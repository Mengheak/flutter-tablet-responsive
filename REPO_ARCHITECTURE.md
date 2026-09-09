# Architecture Analysis — `palm_ecommerce_app`

> A reference write-up of the app's architecture, intended as a blueprint for reuse in
> another project. Covers the layering, the four core patterns, and the parts that
> should be fixed rather than copied.

---

## 1. What this is

A Flutter restaurant / e-commerce app (157 Dart files) talking to a Laravel-style REST
backend at `palm.moh-dhs.com`, plus:

- **Firebase** (Auth + Firestore) for Google sign-in
- **Bakong / KHQR** for Cambodian QR payments
- **Google Maps + Geolocator** for delivery address selection
- **ARB-based i18n** for English and Khmer

The architecture is **layered MVVM with a Repository pattern, Provider for state
management, and manual constructor dependency injection**. It is a clean, boring, very
copyable skeleton — with a few parts that should not be carried forward as-is.

---

## 2. Layer structure

```
lib/
├── main.dart              ← composition root: builds repos, wires 13 providers
├── data/
│   ├── network/
│   │   ├── fetchingdata.dart   ← raw HTTP helpers (static methods)
│   │   └── api_endpoints.dart  ← ApiConstant: all endpoint paths
│   ├── repository/             ← abstract contracts (ProductRepository, CartRepository…)
│   │   └── https/              ← concrete HTTP implementations
│   └── dto/                    ← JSON ↔ model mappers (only partially adopted)
├── models/                     ← domain entities
│   └── params/                 ← request/parameter objects
├── ui/
│   ├── provider/               ← ChangeNotifier view-models + AsyncValue<T>
│   ├── screens/                ← feature folders, each with a widget/ subfolder
│   └── widget/                 ← shared widgets (bottom nav, loading)
├── l10n/                       ← ARB files (en, km) + generated AppLocalizations
├── theme/                      ← ThemeConfig (light/dark)
└── util/                       ← colors, size config, constants, globals
```

**Dependency direction** is strictly:

```
ui  →  data/repository (abstract)  ←  data/repository/https  →  data/network
```

Screens never import `http` or `FetchingData`. That boundary holds throughout the
codebase — worth preserving.

### Feature inventory

Eleven features follow an identical contract/implementation/provider triple:

`auth` · `product` · `category` · `cart` · `checkout` · `delivery address` · `order` ·
`favorite` · `payment method` · `notification` · `bakong`

---

## 3. The four patterns worth stealing

### 3.1 `AsyncValue<T>` — a four-state result wrapper

`lib/ui/provider/async_values.dart` — 21 lines, and the backbone of the entire UI.

```dart
enum AsyncValueState { loading, error, success, empty }

class AsyncValue<T> {
  final T? data;
  final Object? error;
  final AsyncValueState state;

  AsyncValue._({this.data, this.error, required this.state});

  factory AsyncValue.loading() => AsyncValue._(state: AsyncValueState.loading);
  factory AsyncValue.success(T data) =>
      AsyncValue._(data: data, state: AsyncValueState.success);
  factory AsyncValue.error(Object error) =>
      AsyncValue._(error: error, state: AsyncValueState.error);
  factory AsyncValue.empty() => AsyncValue._(state: AsyncValueState.empty);

  bool get hasError => error != null;
  bool get hasData => data != null;
}
```

Every provider field is an `AsyncValue<Something>`; every screen switches on `.state`:

```dart
switch (provider.cart.state) {
  case AsyncValueState.loading: return const LoadingWidget();
  case AsyncValueState.error:   return ErrorView(provider.cart.error);
  case AsyncValueState.success: return CartList(provider.cart.data!);
  case AsyncValueState.empty:   return const EmptyCartView();
}
```

**The good idea here** is `empty` as a state distinct from `success([])`. "Cart is
empty" and "cart loaded with zero items after an error-ish response" are genuinely
different UI states, and separating them removes a whole class of `if (list.isEmpty)`
branching from widgets.

> **Upgrade for a new project:** make it a Dart 3 `sealed class` with subclasses
> (`Loading` / `Success` / `Failure` / `Empty`) so `switch` becomes exhaustive and
> `data!` bangs disappear.

### 3.2 Contract / implementation split per feature

The abstract contract is tiny and declarative:

```dart
// lib/data/repository/product_repository.dart
abstract class ProductRepository {
  Future<List<ProductDetailModel>> getProducts();
  Future<List<ProductDetailModel>> superHotPromotion();
  Future<List<ProductDetailModel>> getNewArrivalFood();
  Future<List<ProductDetailModel>> getRelatedProduct(String productId);
  Future<List<ProductDetailModel>> searchFoodDishes(String productName, String categoryId);
  Future<List<BannerModel>> getBannerSlideShow();
}
```

The implementation (`data/repository/https/product_api_repository.dart`, ~293 lines)
owns header construction, status-code handling, response parsing, and logging.

Providers depend only on the **abstract** type, so swapping in a mock, a fake, or a
local-cache implementation requires zero UI changes. This is the single biggest payoff
of the structure.

### 3.3 Manual DI with `main.dart` as composition root

No `get_it`, no code generation. Repositories are constructed once and injected into
providers via `MultiProvider`.

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  if (Firebase.apps.isEmpty) await Firebase.initializeApp();

  final authRepository     = AuthenticationApiRepository();
  final productRepository  = ProductApiRepository()..repository = authRepository;
  final categoryRepository = CategoryApiRepository(authRepository);
  final cartRepository     = CartApiRepository()..repository = authRepository;
  // … 11 repositories total

  runApp(MultiProvider(
    providers: [
      ChangeNotifierProvider(create: (_) => AppProvider()),
      ChangeNotifierProvider(create: (_) => LanguageProvider()),
      ChangeNotifierProvider(create: (_) => ProductProvider(repository: productRepository)),
      // … 13 providers total
    ],
    child: const MyApp(),
  ));
}
```

**The shared-auth trick:** `AuthenticationApiRepository` is built first and handed to
every other repository, so token retrieval lives in exactly one place. Each repo then
does:

```dart
final token = await repository.getCurrentToken();
if (token.isEmpty) throw Exception('No authentication token available.');
final response = await FetchingData.getData(ApiConstant.products, _getAuthHeaders(token));
```

with a shared private helper:

```dart
final _baseHeaders = {'Accept': 'application/json', 'Content-Type': 'application/json'};
Map<String, String> _getAuthHeaders(String token) =>
    {..._baseHeaders, 'Authorization': 'Bearer $token'};
```

Token storage is two-tier: an in-memory `_cachedToken` in the auth repository, backed by
`SharedPreferences` under the key `storeToken`.

### 3.4 A uniform provider method shape

Every fetch is the same five moves — set loading, notify, await repo, set
success/error, notify:

```dart
Future<void> fetchProducts() async {
  _products = AsyncValue.loading();
  notifyListeners();
  try {
    _cachedProduct = await repository.getProducts();
    _products = AsyncValue.success(_cachedProduct);
  } catch (e) {
    _products = AsyncValue.error(e);
  }
  notifyListeners();
}
```

`CartProvider.addToCart` adds a useful variation — **optimistic update, then server
reconcile**:

1. Mutate the local `CartModel` with `copyWith` (increment qty or append item)
2. `notifyListeners()` immediately so the UI responds instantly
3. `await repository.addToCart(...)`
4. Re-`GET` the cart and replace local state with the server's truth

The consistency is the real strength here: after reading two repositories you can
predict the contents of the eleventh.

---

## 4. Supporting pieces

| Concern | Approach |
|---|---|
| **i18n** | ARB files (`app_en.arb`, `app_km.arb`) → generated `AppLocalizations`. A `LanguageProvider` gates the whole app: `MaterialApp` renders a spinner until `isInitialized` is true, then supplies `locale`. |
| **Data loading trigger** | Screens fetch in `initState` via `WidgetsBinding.instance.addPostFrameCallback((_) => fetchData())`, so `context.read<T>()` is safe. |
| **Navigation** | Imperative `Navigator.push` + `MaterialPageRoute` (~70 call sites). No named routes, no router. |
| **Theming** | `AppProvider` (a `ChangeNotifier`) persists a light/dark choice to `SharedPreferences` via `ThemeConfig`. |
| **Global keys** | `AppProvider` also holds a `navigatorKey` and a `key` for `MaterialApp`, enabling full-app rebuilds on language/theme change. |
| **Error surfacing** | `_handleErrorResponse()` in the auth repo normalizes Laravel's `errors` map / `message` / `error` shapes into a single `Exception`. |
| **Payments** | KHQR generated via `khqr_sdk`; a deeplink endpoint plus an MD5-based `check_transaction_by_md5` call to `api-bakong.nbc.gov.kh` confirms payment. |

---

## 5. What to fix rather than copy

### Security — address before reusing anything

| Issue | Location |
|---|---|
| **Bakong JWT hardcoded and committed** | `lib/data/network/api_endpoints.dart:65` — `static const String token = 'eyJ…'`. Rotate this token; move to a server-side proxy or env config. |
| **`.env` not gitignored** | Contains `GOOGLE_MAPS_API_KEY`. Add `.env` to `.gitignore` and rotate the key. |
| **No 401 → refresh/logout path** | Each repo throws `Exception('Authentication failed')` independently. Nothing globally clears the token or routes to login. |

### Structural

| Issue | Where | Do instead |
|---|---|---|
| **~35 mutable globals** (`token`, `usetoken`, `savePhone`, `listNew`, `lat`, `long`, …) | `lib/util/data.dart` | This is a parallel, untracked state system that fights Provider — widgets read *and write* it mid-build. Delete it; move state into providers. |
| **Base URL is a compile-time const** | `fetchingdata.dart:7` | Env-based config with dev/staging/prod flavors. |
| **Tenant IDs `PALM-0006` / `PALM-00060001` scattered across repos** | product / category / search repos | A single injected config object. |
| **DTO layer only used by 3 of 11 repos** | `lib/data/dto/` | Pick one convention: DTOs everywhere, or `fromJson` on models everywhere. Currently it's both, so the layer's purpose is unclear. |
| **Dark theme is dead code** | `AppProvider.theme` is computed and persisted, but `MaterialApp` in `main.dart` has no `theme:` parameter | Wire it up or remove the machinery. |
| **Double caching with no invalidation** | Repo-level `_productCache` map *and* provider-level `_cachedX` lists | One cache layer, with a TTL or explicit invalidation. Today a stale product can never be evicted without a restart. |
| **`FetchingData` has 9 near-duplicate static methods** | `postData`, `postHeader`, `postHeaderFile`, `postHeaderMd5`, `postMultipart`, `getData`, `getHeader`, `getDataPar`, `deleteCart`, `updateCartQuantity` | One `request()` method with a method enum. Timeouts are currently inconsistent — 30s, 10s, and none, depending on which helper you happen to call. |
| **Two empty files** | `ui/provider/connectivity_provider.dart`, `util/connectivity_service.dart` (0 bytes each) | Implement or delete. |
| **No tests** | `test/` does not exist, though `test` and `mocktail` are declared dependencies | The abstract repositories make this straightforward — it's the payoff of the whole pattern. |
| **`get: ^4.7.2` declared but essentially unused** | 1 usage vs. 70 raw `Navigator` calls | Drop the dependency, or adopt `go_router` for typed, deep-linkable routes. |
| **`print()` mixed with `Logger`** | product repo, several providers | Standardize on one logging path. |
| **README is boilerplate from a different project** | `README.md` says "tourism_app" | — |

---

## 6. The skeleton, condensed

For a new project, the reusable core is about five files:

1. **`ui/provider/async_value.dart`** — copy nearly verbatim; upgrade to a sealed class.
2. **`data/network/http_client.dart`** — *one* generic request method (not nine), with a
   single timeout policy and centralized 401 handling.
3. **`data/repository/<feature>_repository.dart`** — abstract contract per feature.
4. **`data/repository/api/<feature>_api_repository.dart`** — implementation: headers,
   parsing, typed exceptions.
5. **`ui/provider/<feature>_provider.dart`** — `ChangeNotifier` holding `AsyncValue<T>`
   fields, one method per operation in the five-move shape.

Then `main.dart` builds repositories top-down and hands them to `MultiProvider`.

**Adding a feature is always:** contract → implementation → provider → register.

### One structural upgrade worth making

Put the auth token in an **HTTP-client interceptor** rather than threading
`authRepository` into every other repository. That:

- removes the one place where the layering gets tangled (every repo currently depends on
  the auth repo),
- gives you a single spot for `401 → refresh or logout`, which the app currently lacks,
- and lets each repository depend only on the HTTP client, not on a sibling repository.

### Optional modernizations

- **State:** `provider` still works fine, but `riverpod` gives you `AsyncValue` for free
  (with the same four states), plus compile-time-safe DI and auto-disposal.
- **Models:** `freezed` + `json_serializable` replaces the hand-written `fromJson` /
  `copyWith` / DTO code entirely, and removes the DTO-vs-model inconsistency by fiat.
- **Routing:** `go_router` for deep links and typed navigation.
- **Errors:** typed exception hierarchy (`NetworkException`, `AuthException`,
  `ServerException`) instead of `Exception('string message')` — currently `CartProvider`
  does substring matching on error text (`errorMsg.contains('empty')`) to decide whether
  an error is really an empty cart.
