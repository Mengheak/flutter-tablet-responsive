# Mr. Passenger — Architecture Documentation

> Capstone project — a ride-booking **passenger** app built in Flutter.
> Dart package name: `scholarar` (inherited from the scholarship-app template this project was forked from).

---

## 1. Overview

Mr. Passenger is an Android-first ride-hailing client. A passenger signs in, picks a pickup and
drop-off point on a Google Map, sends a trip request to the backend, waits for a driver to accept
(the acceptance arrives as a Firebase push notification), then follows the driver on a live map
until the trip completes and is paid for.

| | |
|---|---|
| **Flutter SDK** | `3.22.0-0.3.pre` (beta channel, pinned via `.fvmrc`) |
| **Dart SDK** | `>=3.2.3 <4.0.0` |
| **Package name** | `scholarar` |
| **App display name** | `Authenticator` (`AppConstants.appName` — also a template leftover) |
| **Platforms** | `android/`, `ios/`, `web/` present; **only Android is functional** — most of `main()` is wrapped in `Platform.isAndroid` |
| **State management** | GetX (`GetBuilder` + `update()`), plus global mutable variables |
| **Backend** | REST API at `http://ec2-54-82-25-173.compute-1.amazonaws.com:8000` |
| **Realtime** | Firebase Cloud Messaging (push), polling, and simulated movement — no socket/Firestore |
| **Languages** | English, Khmer, Chinese (`assets/languages/*.json`) |
| **Lints** | `flutter_lints` ^3.0.1, with `avoid_unnecessary_containers` downgraded to ignore |
| **Size** | 106 Dart files, ~16.4k lines under `lib/` |

The app is best understood as **two codebases layered on top of each other**:

1. A clean **GetX MVC + Repository skeleton** inherited from a scholarship/e-learning template
   (DI container, `DioClient`, repositories, controllers, theme tokens, i18n).
2. The **capstone ride-booking feature** grafted on top as large self-contained map screens
   that call `http` directly and coordinate through global variables and push-notification titles.

Roughly half the files in `lib/` belong to group (1) and are now dead code.

---

## 2. Directory layout

```
lib/
├── main.dart                 App entry, Firebase/FCM bootstrap, GetMaterialApp
├── firebase_options.dart     FlutterFire generated config
├── controller/               9 GetxControllers (state + orchestration)
├── data/
│   ├── api/api_client.dart   `DioClient` — HTTP transport (actually package:http)
│   ├── model/
│   │   ├── body/             request DTOs (auth, chat, home, scholarship)
│   │   ├── response/         response DTOs + base ApiResponse/ErrorResponse
│   │   └── TESTAPI/          demo models for fakestoreapi / randomuser
│   ├── repository/           8 repositories (endpoint wrappers)
│   └── globle_variable.dart
├── generated/assets.dart     auto-generated asset constants (unused)
├── helper/
│   ├── get_di.dart           dependency-injection container
│   ├── network_info.dart     connectivity checks
│   ├── responsive_helper.dart breakpoints
│   └── token_helper.dart     token persistence
├── theme/                    light.dart / dark.dart ThemeData
├── util/                     tokens, dialogs, notifications, navigation helpers
└── view/
    ├── app/app_screen.dart   bottom-nav shell
    ├── custom/               shared widgets
    └── screen/
        ├── account/          10 auth screens (only a few reachable)
        ├── booking_driver/   the core booking + tracking flow
        ├── new_home_screen/  home, booking entry, slider, premium
        ├── chat/             chat mockups, contact us, about us
        ├── notification/, profile/, search/, splash/
```

---

## 3. Layer architecture

```
       ┌────────────────────────────────────────────────┐
       │  view/  (screens + custom widgets)             │
       │  GetBuilder<Controller> · setState             │
       └───────────────┬────────────────────────────────┘
                       │ Get.find<XController>()
       ┌───────────────▼────────────────────────────────┐
       │  controller/  GetxController + GetxService     │
       │  private fields → getters → update()           │
       └───────────────┬────────────────────────────────┘
                       │
       ┌───────────────▼────────────────────────────────┐
       │  data/repository/   body maps → dioClient call │
       └───────────────┬────────────────────────────────┘
                       │
       ┌───────────────▼────────────────────────────────┐
       │  data/api/DioClient   headers · timeout · JSON │
       └───────────────┬────────────────────────────────┘
                       │
                  REST backend (EC2)

       ✗ Booking screens bypass all of this and call http.post directly.
```

### 3.1 Dependency injection — [`lib/helper/get_di.dart`](lib/helper/get_di.dart)

A service-locator built on `Get.lazyPut`, called once from `main()`:

```
SharedPreferences ─┬─> DioClient ──┬─> AuthRepository ─────> AuthController
                   │               ├─> TrackingRepository ─> TrackingController
NetworkInfo        │               ├─> HomeRepository ─────> HomeController
(Connectivity)     │               ├─> CourseRepository ───> CourseController
                   │               ├─> BookStoreRepository > BookStoreController
                   │               ├─> ScholarshipRepository
                   │               └─> TestRepository ─────> TestController
                   ├─> LocalizationController
                   └─> ThemeController
                        SplashController, LanguageRepository
```

`init()` also loads the three language JSON bundles from assets and returns them to `main()`.

> `get_it` is listed in `pubspec.yaml` but never used — GetX's locator does all the work.
> `di.init()` is called **twice** in `main()` ([main.dart:46](lib/main.dart:46) and
> [main.dart:48](lib/main.dart:48)), so every registration and all language parsing runs twice.

### 3.2 Controllers — `lib/controller/`

All follow the same shape: `class XController extends GetxController implements GetxService`,
constructor-injected repository + `SharedPreferences`, private `_field` + public getter, and
`update()` to notify `GetBuilder`. Rx/`.obs` is used in exactly one place
(`AuthController.hidePassword`) — the codebase is effectively non-reactive GetX.

| Controller | Responsibility | Status |
|---|---|---|
| `AuthController` (675 ln) | login / register / profile / signout / device-token upload | **Active** |
| `SplashController` | holds bottom-nav index | **Active** |
| `ThemeController` | light/dark flag, persisted | Registered, never toggled |
| `LocalizationController` | locale + persistence | Registered, switcher not wired |
| `TrackingController` | `updatePassengerToken` | Partly active |
| `HomeController`, `CourseController`, `BookStoreController`, `TestController` | template leftovers | **Dead** |

### 3.3 Repositories — `lib/data/repository/`

Thin wrappers: build a `Map` body, call `dioClient`, return the raw GetX `Response`. No model
mapping, no error normalisation — controllers index into `response.body['...']` directly.
`AuthRepository` (368 ln) is the only substantial one, and about a third of its methods point at
empty endpoint constants (`AppConstants.signOut = ""`, `changePassword = ""`, …), meaning they
would POST to the bare base URL.

### 3.4 HTTP client — [`lib/data/api/api_client.dart`](lib/data/api/api_client.dart)

- Named `DioClient`, but implemented with `package:http`; `dio` is a dependency it doesn't use.
- Extends `GetxService`. Holds `_mainHeaders` (`Accept`, `Content-Type`, `Authorization: Bearer …`).
- Reads the token from `SharedPreferences` in its constructor; `updateHeader()` refreshes it after login.
- 30-second timeout, `getData` / `postData` / `putData` / `deleteData` / `postMultipartData`.
- `handleResponse()` decodes JSON, unwraps `{errors: [...]}` / `{message: ...}` bodies into GetX
  `Response.statusText`, and logs every request and response via `debugPrint`.
- Network failures are swallowed into `Response(statusCode: 1, statusText: noInternetMessage)`,
  so callers cannot distinguish a timeout from a DNS failure from a malformed body.

### 3.5 Models — `lib/data/model/`

Hand-written `fromJson`/`toJson` DTOs, no code generation. `body/` holds request payloads and some
UI seed data (chat avatars, home sections); `response/` holds `country_model`, `language_model` and
the `base/` envelope types; `TESTAPI/` holds demo models for `fakestoreapi.com` and `randomuser.me`.
The booking feature does not use models at all — it works on raw `Map<String, dynamic>`.

---

## 4. Application bootstrap & navigation

### 4.1 Startup sequence — [`lib/main.dart`](lib/main.dart)

```
main()
 ├── WidgetsFlutterBinding.ensureInitialized()
 ├── if (Android):
 │     Firebase.initializeApp()
 │     1-second artificial delay
 │     FirebaseAPI().initNotifications()      → requests permission, caches FCM token
 │     Crashlytics wiring (FlutterError.onError, PlatformDispatcher.onError)
 ├── lock orientation to portraitUp
 ├── di.init()                                 (called twice)
 ├── HttpOverrides.global = MyHttpOverrides()  ← accepts ANY TLS certificate
 └── runApp(MyApp(languages))

MyApp (StatefulWidget, WidgetsBindingObserver)
 ├── initState → FCM token fetch, notification permission, foreground listeners,
 │               app-badge updates, local-notification channel init
 └── build → GetBuilder<ThemeController>
              └── GetBuilder<LocalizationController>
                   └── GetMaterialApp(theme, translations, locale, home: SplashScreen())
```

### 4.2 Routing

There are **no named routes and no `GetPage` table**. Navigation is imperative, through
[`lib/util/next_screen.dart`](lib/util/next_screen.dart), used in 18 files:

| Helper | Behaviour |
|---|---|
| `nextScreen(context, page)` | `Get.to` with right-to-left transition |
| `nextScreenReplace(context, page)` | `Navigator.pushReplacement` |
| `nextScreenNoReturn(context, page)` | `pushAndRemoveUntil` — clears the stack (used post-login) |
| `nextScreenIOS(context, page)` | `CupertinoPageRoute` |

Note the mix: `nextScreen` uses the GetX navigator while the others use `Navigator`, and most calls
pass `Get.context!` rather than the widget's own `BuildContext`.

### 4.3 Screen flow

```
SplashScreen ──> AppScreen (always; token only decides whether profile is fetched)
                    │
   ┌────────────────┼──────────────────────────┐
   │                │                          │
NewHomeScreen   DisplayScreen             SettingScreen
(tab 0)         (tab 1, local history)    (tab 2)
   │                                          ├─ ProfileScreen
   ▼                                          ├─ NotificationScreen
BookingDriver ── POST /api/trips/createTrip    ├─ DeveloperScreen (about us)
   │             120s countdown timer          └─ ContactUs (tel / telegram / mail)
   │
   ▼  (FCM push: "ការកក់បានបញ្ជាក់")
DriverPick (dri_to_pas.dart) — driver → passenger leg
   │
   ▼  (FCM push: "ចាប់ផ្តើម")
BookingScreen (start_to_end.dart) — pickup → destination leg, call driver, PayPal
   │
   ▼  (FCM push: "ការធ្វើដំណើរបានបញ្ចប់")
AppScreen
```

- [`SplashScreen`](lib/view/screen/splash/splash_screen.dart) calls
  `getPassengerInfoController()` and reads the token, but **every branch lands on `AppScreen`** —
  there is no auth gate. Screens that need a session check it themselves.
- [`AppScreen`](lib/view/app/app_screen.dart) is the shell: a `PageView` with
  `NeverScrollableScrollPhysics` driven by a `PageController`, plus a Material 3 `NavigationBar`
  whose selected index lives in `SplashController`. It declares
  `DefaultTabController(length: 4)` while providing 3 destinations.

---

## 5. The booking & tracking flow (core feature)

This is the capstone work, and it deliberately sidesteps the repository layer.

### Step 1 — Request a trip
[`booking_driver_screen.dart`](lib/view/screen/booking_driver/booking_driver_screen.dart) (930 ln)
and its near-duplicate [`booking_passapp.dart`](lib/view/screen/new_home_screen/booking_passapp.dart) (892 ln):

- `GoogleMap` + `geolocator` for the current position, `geocoding` (`placemarkFromCoordinates`)
  to turn taps into human-readable pickup/drop-off addresses.
- Custom marker bitmaps are produced at runtime by decoding an asset with `package:image`,
  resizing it, re-encoding to PNG and calling `BitmapDescriptor.fromBytes`.
- Submits directly:

```
POST http://ec2-…:8000/api/trips/createTrip
{
  "passenger_id":  authController.userPassengerMap['userDetails']['_id'],
  "start_location": [longCur, latCur],
  "end_location":   [longDir, latDir],
  "passenger_name": …, "passenger_phone_number": …
}
```

- On `201` it stores the response in the global `postBookingInfo`, sets `isWaiting = true`, and
  starts a `Timer.periodic(1s)` counting down from 120 to give up on finding a driver.

### Step 2 — Driver accepts (push)
The backend sends an FCM message. [`main.dart:103`](lib/main.dart:103) listens on
`FirebaseMessaging.onMessage`, raises `customNotificationDialog`, and routes by **matching the
notification title string** (Khmer literals) to a screen:

| Push title | Destination |
|---|---|
| `ការកក់បានបញ្ជាក់` (booking confirmed) | `DriverPick` |
| `ចាប់ផ្តើម` (start) | `BookingScreen` |
| `ការធ្វើដំណើរបានបញ្ចប់` (trip finished) | `AppScreen` |

### Step 3 — Driver on the way
[`dri_to_pas.dart`](lib/view/screen/booking_driver/dri_to_pas.dart) (649 ln):

- `GET /api/trips/{postBookingInfo['_id']}` to fetch the accepted trip and the driver's coordinates.
- Draws the route with `flutter_polyline_points` against the Google Directions API.
- Subscribes to `Geolocator.getPositionStream()` and animates the camera.
- **Simulates driver movement**: `Timer.periodic(5s)` nudges the driver marker by a random
  ±0.0005° offset ([dri_to_pas.dart:343](lib/view/screen/booking_driver/dri_to_pas.dart:343)) —
  the driver's real position is never re-fetched after the initial GET.
- Appends the trip JSON to a `driverTrips` list in `SharedPreferences`.

### Step 4 — Trip in progress
[`start_to_end.dart`](lib/view/screen/booking_driver/start_to_end.dart) (1175 ln, the largest file;
roughly the first 600 lines are a commented-out earlier version of the same screen):

- Same map/polyline/position-stream machinery for the pickup → destination leg.
- `flutter_phone_direct_caller` / `url_launcher` to call the driver.
- `_startPayPalPayment()` ([start_to_end.dart:1108](lib/view/screen/booking_driver/start_to_end.dart:1108))
  opens `flutter_paypal_payment` for the fare.

### Step 5 — History
[`booking_history_display.dart`](lib/view/screen/booking_driver/booking_history_display.dart) reads
the `driverTrips` key back out of `SharedPreferences`, reverses it, reverse-geocodes the coordinates
and renders expandable cards. **There is no history endpoint — clearing app data erases all trips.**

---

## 6. State & persistence

### 6.1 Three parallel mechanisms

1. **GetX controllers** — `update()` + `GetBuilder`, for auth/profile state.
2. **`setState`** — inside the booking screens, which hold their own HTTP, timers and map state.
3. **Global mutable top-level variables** in
   [`app_constants.dart:4-21`](lib/util/app_constants.dart:4) — the actual channel between
   booking screens:

```dart
late double latCur, longCur, latDir, longDir;      // selected pickup / drop-off
late double latitudePas, longitudePas, latitudeDri, // resolved trip coordinates
            longitudeDri, latitudePasDir, longitudePasDir;
bool isWaiting, driAccept;                          // booking state machine
String selectedFromAddress, selectedToAddress;      // geocoded labels
Map newUserInfo, postBookingInfo;                   // profile + created trip
Map<String, dynamic> getDriverTrip;                 // fetched trip
String? frmTokenPublic;                             // FCM token
```

Because several are `late double` with no initialiser, reaching a tracking screen without having
gone through the booking screen first throws `LateInitializationError`.

### 6.2 Storage

| Store | Contents |
|---|---|
| `SharedPreferences` | `token`, `language_code`, `country_code`, `isSelectNumber`, `authenticator_theme`, `driverTrips` (full trip history JSON) |
| `flutter_secure_storage` | `token`, `uuid` |

[`TokenHelper`](lib/helper/token_helper.dart) writes the token to **both** stores on every save
(and calls `storage.deleteAll()` first); `DioClient` only ever reads the `SharedPreferences` copy,
so the secure copy is effectively write-only.

---

## 7. Firebase & notifications

| Service | Where | Use |
|---|---|---|
| `firebase_core` | `main()` | init (Android only) |
| `firebase_messaging` | [`util/firebase_api.dart`](lib/util/firebase_api.dart) | permission, token, foreground/background handlers |
| `flutter_local_notifications` | [`util/notification_service.dart`](lib/util/notification_service.dart) | `high_importance_channel`, shows heads-up notifications |
| `firebase_crashlytics` | `main()` | `FlutterError.onError` + `PlatformDispatcher.onError` |
| `firebase_analytics` | [`util/firebase_analytic_api.dart`](lib/util/firebase_analytic_api.dart) | event logging |
| `flutter_app_badger` | `main()` | badge count on message arrival |

The FCM token is cached into the global `frmTokenPublic` by `FirebaseAPI.initNotifications()` and
uploaded after login via `AuthController.postDeviceToken()` →
`POST /api/trips/updatePassengerToken`.

`flutter_geofire` is a dependency but is never imported — there is no Firestore or Realtime
Database usage anywhere in `lib/`.

---

## 8. Design system & reusable infrastructure

A full token/utility layer ships with the project (from the template). It is well organised but
only partly adopted.

| Concern | File | Adoption |
|---|---|---|
| Colour tokens | [`util/color_resources.dart`](lib/util/color_resources.dart) | **35 files** — the real source of truth |
| Spacing / font / icon tokens | [`util/dimensions.dart`](lib/util/dimensions.dart) | **4 files** |
| Breakpoints | [`helper/responsive_helper.dart`](lib/helper/responsive_helper.dart) | consumed only by `Dimensions` |
| Text styles | [`util/style.dart`](lib/util/style.dart) | ~12 files |
| Themes | [`theme/light_theme.dart`](lib/theme/light_theme.dart), [`theme/dark_theme.dart`](lib/theme/dark_theme.dart) | wired to `GetMaterialApp`, never toggled |
| i18n | [`util/messages.dart`](lib/util/messages.dart) + `assets/languages/*.json` | `.tr` used widely; switcher not wired |
| Asset constants | [`generated/assets.dart`](lib/generated/assets.dart) | **0 usages** |
| Shared widgets | `lib/view/custom/` | `CustomButtonWidget` 7, `CustomTextFieldWidget` 4, `customShowSnackBar` 8; `CustomAppbarWidget` and the setting list-tile: **0** |
| Dialogs / helpers | `loading_dialog.dart`, `alert_dialog.dart`, `permission_util.dart`, `network_info.dart` | active |

### 8.1 Known inconsistencies in this layer

- **Two competing primary colours.** `light.primaryColor` is gold `0xFFE1BB17`;
  `ColorResources.primaryColor` is red `0xFFE85B5B`. Screens use the latter, so `ThemeData` is
  largely decorative.
- **The themed font does not exist.** Both themes set `fontFamily: 'Mulish'`, but `pubspec.yaml`
  declares only one family — `Inter` — whose asset is `ArimaKoshi-Regular.otf`
  ([pubspec.yaml:111](pubspec.yaml:111)). `style.dart` asks for `'Inter'`, `CustomButtonWidget`
  asks `GoogleFonts.montserrat`. Three different type systems coexist.
- **Dark mode is unreachable.** `ThemeController.toggleTheme()` has no caller, and screens hardcode
  `Colors.white` (13 occurrences in `start_to_end.dart` alone), so dark would render broken anyway.
- **Language switching is unreachable.** `LocalizationController.setNumberLanguage()` has no caller;
  the locale is whatever was persisted, defaulting to English — while much of the booking UI is
  hardcoded Khmer strings rather than `.tr` keys.
- **`Dimensions` is static-initialised.** Its `static double` fields are computed once from
  `Get.width` at first access, so they never react to rotation or resize; with the app locked to
  portrait on phones, every ternary collapses to the mobile value.
- **Styling is mostly inlined anyway** — 15 hardcoded `fontSize:` literals in `register_screen.dart`,
  12 in `start_to_end.dart`.

---

## 9. Dead / template code

Sizeable parts of `lib/` belong to the original scholarship app and are unreachable:

- **Controllers/repositories:** `course`, `book_store`, `scholarship`, `home`, `test` — pointing at
  `/api/courses`, `/api/scholarships`, `/api/books`, `/api/categories`.
- **Models:** `body_scholarship_model.dart/`, `body_home_model/`, `TESTAPI/` (fakestoreapi,
  randomuser, newsapi).
- **Auth screens:** `lib/view/screen/account/` holds ten login/register/verification screens
  (`login_screen`, `signin_screen`, `sing_in_account_screen`, `signup_screen`,
  `singup_account_screen`, `register_screen`, `set_password_screen`, `forget_password_screen`,
  `verification_code_screen`, `edite_profile_screen`) of which only a couple are on a live path.
- **Chat feature:** `chat_screen`, `sender_chat_screen` render static seed data from
  `AvartarBodyChatModel` — there is no messaging backend.
- **Other:** `premium.dart` (static "Premium Plan" page), `search_screen`, `qr`/`scan`/`encrypt`/
  `pod_player`/`carousel` dependencies, `driver_accepted_unused.dart`, `generated/assets.dart`,
  `duplicate developer_screen.dart` in two folders, and ~600 commented-out lines at the top of
  `start_to_end.dart`.
- `TrackingRepository.getTracking()` calls `dioClient.getData(AppConstants.google_key_api)` — it
  passes a Google API *key* as a URL path, then discards the result.

---

## 10. Security & robustness notes

These are worth addressing before any real deployment:

1. **TLS verification is globally disabled.** `MyHttpOverrides` ([main.dart:182](lib/main.dart:182))
   returns `true` from `badCertificateCallback` for every host. Combined with the plain-`http`
   base URL, all traffic — including login credentials — is in the clear and trivially interceptable.
2. **Secrets committed in source.** Google Maps key at
   [`app_constants.dart:58`](lib/util/app_constants.dart:58) (and in `AndroidManifest.xml`);
   a NewsAPI key at [`app_constants.dart:48`](lib/util/app_constants.dart:48).
3. **Trip endpoints are unauthenticated.** `createTrip`, `updatePassengerToken` and
   `GET /api/trips/{id}` are called with only `Content-Type` — no bearer token — and take
   `passenger_id` straight from the client, so any client can create or read trips for any id.
4. **`ACCESS_BACKGROUND_LOCATION`** is requested in the manifest; Play Store review requires a
   documented justification for it.
5. **Push-driven navigation keys on localised display strings** — changing a notification's wording
   server-side silently breaks the client's routing.
6. **Error handling collapses everything into `print`** — the user sees a generic snackbar (often a
   hardcoded Khmer one) regardless of cause, and `DioClient` cannot distinguish failure modes.
7. **Timers/streams leak.** `BookingDriver.dispose()` disposes the `PageController` but not the
   120-second `_timer`; `setState` is called from those timers without a `mounted` guard.
8. **`di.init()` runs twice** at startup, duplicating registrations and asset parsing.

---

## 11. What is worth reusing

If this scaffolding is carried into another project, the genuinely portable pieces are:

- The DI + repository + controller skeleton: `helper/get_di.dart`, `data/api/api_client.dart`,
  `data/repository/*`, the `GetxController implements GetxService` pattern.
- `helper/token_helper.dart`, `helper/network_info.dart`, `util/permission_util.dart`.
- The token layer: `util/color_resources.dart`, `util/dimensions.dart`,
  `helper/responsive_helper.dart`, `util/style.dart` — after unifying the font and colour sources.
- i18n: `util/messages.dart`, `data/model/response/language_model.dart`, `LocalizationController`
  and the `assets/languages/*.json` convention.
- UI helpers: `util/next_screen.dart`, `util/loading_dialog.dart`, `util/alert_dialog.dart`,
  `view/custom/custom_show_snakbar.dart`, `custom_textfield_widget.dart`.

### Highest-value cleanups, in order

1. Point `ThemeData` at `ColorResources.primaryColor` and fix the font-family declaration, so the
   theme layer becomes meaningful instead of ornamental.
2. Move the three direct `http` calls in the booking screens into a `TripRepository` +
   `BookingController`, and replace the globals in `app_constants.dart` with controller state.
3. Remove the `MyHttpOverrides` certificate bypass and move the backend to HTTPS.
4. Delete the scholarship/course/book-store/test layers and the unreachable auth screens.
5. Send the bearer token on the trip endpoints and derive `passenger_id` server-side.

---

*Generated from a read-through of the `lib/` tree, `pubspec.yaml` and the Android manifest.
File references are clickable: `path:line`.*
