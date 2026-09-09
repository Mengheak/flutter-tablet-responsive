# សៀវភៅលំហាត់ៈ ការរចនា UI តាម Best Practices ក្នុង Flutter

> **កម្រិត:** មធ្យម → ខ្ពស់
> **Flutter:** 3.47.1 stable · **Dart:** 3.13.1 (ថ្ងៃសរសេរ៖ កញ្ញា ២០២៦)
> **លំហាត់សរុប:** ២៤ លំហាត់ + ១ Capstone

---

## អំពីសៀវភៅនេះ

សៀវភៅលំហាត់មុន (`flutter_exercises_khmer.md`) ផ្តោតលើ **យន្តការ** របស់ Flutter — widget tree, state, navigation, testing។ សៀវភៅនេះខុសគ្នា៖ វាផ្តោតលើ **ការរចនា (design craft)** — ធ្វើយ៉ាងណាឱ្យ UI មួយមើលទៅជា professional, ស្ថិតស្ថេរ (consistent), ប្រើបានលើគ្រប់ទំហំអេក្រង់, និងអាចប្រើបានដោយអ្នកគ្រប់គ្នា។

កូដដែល "ដំណើរការបាន" មិនមែនជាកូដដែល "រចនាបានល្អ" ទេ។ លំហាត់ទាំងនេះបង្ខំឱ្យអ្នកសរសេរតាមវិន័យ (discipline) ដែលក្រុមការងារពិតប្រាកដប្រើ។

### រចនាសម្ព័ន្ធនៃលំហាត់នីមួយៗ

| និមិត្តសញ្ញា | អត្ថន័យ |
|---|---|
| 🎯 | គោលដៅ — ជំនាញអ្វីដែលអ្នកនឹងទទួលបាន |
| 📋 | តម្រូវការ — អ្វីដែលត្រូវសាងសង់ |
| 🚫 | ច្បាប់តឹងរឹង — អ្វីដែល **ហាមប្រើ** (នេះជាចំណុចសំខាន់បំផុត) |
| ✅ | លក្ខខណ្ឌទទួលយក — របៀបដឹងថាអ្នកធ្វើត្រូវ |
| 💡 | ដំណោះស្រាយ — បើកមើលក្រោយពេលអ្នកព្យាយាមរួច |

**ច្បាប់តឹងរឹង (🚫) គឺជាបេះដូងនៃលំហាត់។** ការសរសេរ UI ស្អាតដោយប្រើ hardcoded values គឺងាយស្រួល។ ការសរសេរ UI ស្អាតដោយ **មិនអនុញ្ញាតឱ្យប្រើ hardcoded values** ទេ — នោះទើបជាការរៀន។

### សូមកុំបើកមើលដំណោះស្រាយមុន

ចំណាយពេលយ៉ាងតិច ២០ នាទីលើលំហាត់នីមួយៗមុននឹងបើក `<details>`។ ការឆ្លងកាត់ការជាប់គាំង (getting stuck) គឺជាកន្លែងដែលការរៀនកើតឡើង។

---

## ការរៀបចំមុនចាប់ផ្តើម (Setup)

```bash
flutter --version   # ត្រូវឃើញ 3.47.1 ឬថ្មីជាង
flutter create ui_design_lab
cd ui_design_lab
```

### ⚠️ ការផ្លាស់ប្តូរធំក្នុង Flutter 3.47 — Material ចេញពី SDK

ចាប់ពី 3.47 មក Material និង Cupertino បានក្លាយជា standalone packages គឺ `material_ui` និង `cupertino_ui` ដែលឡើងដល់ version 1.0 លើ pub.dev។ Libraries ដើមនៅក្នុង core SDK នឹងត្រូវ deprecate ជាផ្លូវការនៅ stable release ខែវិច្ឆិកា ២០២៦។

សម្រាប់ការរចនា UI ថ្មី ខ្ញុំណែនាំឱ្យប្តូរតាំងពីដំបូង៖

```bash
dart fix --apply --code=migrate_design_widgets
```

Command នេះប្តូរ `import 'package:flutter/material.dart'` ទៅជា `import 'package:material_ui/material_ui.dart'` ដោយស្វ័យប្រវត្តិ។ បើ `pubspec.yaml` មិនទាន់ត្រូវបានកែ សូមរត់ `flutter pub add material_ui` ជាមុនសិន។

**ចំណាំ:** កូដក្នុងសៀវភៅនេះសរសេរដោយប្រើ `package:material_ui/material_ui.dart`។ បើអ្នកមិនទាន់ migrate ទេ គ្រាន់តែប្តូរ import ទៅ `package:flutter/material.dart` វិញ — API នៅដដែល។

### Localization ក៏ប្តូរដែរ

```dart
// ចាស់
import 'package:flutter_localizations/flutter_localizations.dart';
localizationsDelegates: const [
  GlobalMaterialLocalizations.delegate,
  GlobalCupertinoLocalizations.delegate,
  GlobalWidgetsLocalizations.delegate,
],

// ថ្មី (3.47+) — មួយជួរគ្រប់គ្រាន់
import 'package:material_ui/material_ui.dart';
localizationsDelegates: GlobalMaterialLocalizations.delegates,
```

### ឧបករណ៍ដែលនឹងប្រើញឹកញាប់

| ឧបករណ៍ | ប្រើសម្រាប់ |
|---|---|
| **Widget Previews** | មើល component ដាច់ដោយឡែក ដោយមិនបាច់ launch app (stable ក្នុង 3.47) |
| **DevTools → Flutter Inspector** | មើល widget tree, constraints, baseline |
| **`debugPaintSizeEnabled`** | មើលព្រំដែន layout ជាពណ៌ |
| **Golden tests** | ចាប់ការប្រែប្រួល UI ដោយចៃដន្យ |
| **Accessibility Scanner** (Android) | វាស់ contrast និង tap target |

---

## មាតិកា

**ផ្នែកទី ១ — មូលដ្ឋានប្រព័ន្ធរចនា (Design System Foundation)**
1. Spacing scale និងក្រឡាចត្រង្គ 8pt
2. ColorScheme និងការលុបបំបាត់ hardcoded colors
3. TextTheme និងអក្សរខ្មែរ
4. ThemeExtension សម្រាប់ token ផ្ទាល់ខ្លួន
5. Shape, elevation និង dark mode parity

**ផ្នែកទី ២ — ប្លង់ និង Constraints**
6. បំបែក build() យក្សទៅជា component
7. ការស្វែងយល់ overflow តាមរយៈ constraints
8. Responsive breakpoints
9. Adaptive navigation
10. SafeArea, insets និង keyboard

**ផ្នែកទី ៣ — Component ដែលអាចប្រើឡើងវិញ**
11. API របស់ component និង variants
12. លំដាប់ព័ត៌មាន (information hierarchy) ក្នុង card
13. ការរចនា form
14. បញ្ជី (lists) និង pagination

**ផ្នែកទី ៤ — ស្ថានភាព UI និង feedback**
15. ស្ថានភាពទាំង ៥ របស់អេក្រង់
16. Skeleton loading
17. SnackBar ទល់នឹង Dialog ទល់នឹង inline
18. ការសរសេរអក្សរក្នុង UI (UX writing)

**ផ្នែកទី ៥ — ចលនា (Motion)**
19. Motion tokens និង reduced motion
20. Hero និង page transitions

**ផ្នែកទី ៦ — Accessibility និងការផ្ទៀងផ្ទាត់**
21. Tap target, contrast, Semantics
22. ការទប់ទល់នឹង text scaling
23. Widget Previews និង golden tests
24. **Capstone** — កែទម្រង់អេក្រង់អាក្រក់មួយ

---

# ផ្នែកទី ១ — មូលដ្ឋានប្រព័ន្ធរចនា

មុនពេលសរសេរអេក្រង់ណាមួយ អ្នកត្រូវមាន **វាក្យសព្ទរចនា (design vocabulary)** សិន។ បើគ្មានវាទេ រាល់អេក្រង់នឹងក្លាយជាការសម្រេចចិត្តថ្មីៗដោយអនាធិបតេយ្យ ហើយ app នឹងមើលទៅមិនឯកភាពគ្នា។

---

### លំហាត់ទី ១ — Spacing scale និងក្រឡាចត្រង្គ 8pt

**🎯 គោលដៅ**

រៀនប្រើ scale មួយចំនួនតូចជំនួសឱ្យលេខចៃដន្យ។ មូលហេតុ៖ ភ្នែកមនុស្សចាប់អារម្មណ៍ចន្លោះមិនស្មើគ្នាភ្លាមៗ (16 ជាមួយ 17 មើលទៅ "ខូច" ដោយអ្នកមិនដឹងមូលហេតុ)។

**📋 តម្រូវការ**

សរសេរឯកសារ `lib/design/spacing.dart` ដែលមាន៖

- Scale ដែលមានតម្លៃមិនលើសពី ៧៖ ផ្អែកលើគុណនៃ 4 (4, 8, 12, 16, 24, 32, 48)
- Helper widget សម្រាប់ចន្លោះបញ្ឈរ និងផ្តេក (`Gap`)
- Constant សម្រាប់ page padding, card padding, និង list item padding

បន្ទាប់មក សរសេរអេក្រង់ profile មួយ (រូបភាព, ឈ្មោះ, bio, ប៊ូតុង ២) ដោយប្រើតែ scale នេះ។

**🚫 ច្បាប់តឹងរឹង**

- ហាមសរសេរលេខណាមួយផ្ទាល់ក្នុង `EdgeInsets` ឬ `SizedBox` (លើកលែងតែ `0`)
- ហាមប្រើ `Spacer()` សម្រាប់គ្រាន់តែបន្ថែមចន្លោះ
- ហាមប្រើ `Padding` ជាន់គ្នាពីរជាន់លើ widget តែមួយ

**✅ លក្ខខណ្ឌទទួលយក**

- ស្វែងរក regex `EdgeInsets.all\([0-9]` ក្នុងឯកសារអេក្រង់ → មិនត្រូវរកឃើញអ្វីទេ
- ប្តូរតម្លៃ `AppSpacing.md` ពី 16 ទៅ 20 → ចន្លោះក្នុងអេក្រង់ទាំងមូលប្តូរតាមស៊ីគ្នា

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/design/spacing.dart
import 'package:material_ui/material_ui.dart';

/// Spacing scale ផ្អែកលើគុណនៃ 4.
/// មិនត្រូវបន្ថែមតម្លៃថ្មីដោយគ្មានការពិភាក្សាក្នុងក្រុមទេ។
abstract final class AppSpacing {
  static const double xxs = 4;
  static const double xs = 8;
  static const double sm = 12;
  static const double md = 16;
  static const double lg = 24;
  static const double xl = 32;
  static const double xxl = 48;

  // Semantic aliases — ប្រើឈ្មោះតាមតួនាទី មិនតាមទំហំ
  static const EdgeInsets page = EdgeInsets.symmetric(
    horizontal: md,
    vertical: lg,
  );
  static const EdgeInsets card = EdgeInsets.all(md);
  static const EdgeInsets listItem = EdgeInsets.symmetric(
    horizontal: md,
    vertical: sm,
  );
}

/// ចន្លោះទទេដែលអានងាយជាង SizedBox(height: ...)
class Gap extends StatelessWidget {
  const Gap(this.size, {super.key}) : _horizontal = false;
  const Gap.horizontal(this.size, {super.key}) : _horizontal = true;

  final double size;
  final bool _horizontal;

  @override
  Widget build(BuildContext context) => SizedBox(
        height: _horizontal ? null : size,
        width: _horizontal ? size : null,
      );
}
```

```dart
// lib/screens/profile_screen.dart
import 'package:material_ui/material_ui.dart';
import '../design/spacing.dart';

class ProfileScreen extends StatelessWidget {
  const ProfileScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('គណនី')),
      body: SingleChildScrollView(
        padding: AppSpacing.page,
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            const Center(
              child: CircleAvatar(
                radius: AppSpacing.xxl,
                child: Icon(Icons.person, size: AppSpacing.xl),
              ),
            ),
            const Gap(AppSpacing.lg),
            Text(
              'ឈៀង មេងហៀក',
              textAlign: TextAlign.center,
              style: Theme.of(context).textTheme.headlineSmall,
            ),
            const Gap(AppSpacing.xxs),
            Text(
              'Frontend developer',
              textAlign: TextAlign.center,
              style: Theme.of(context).textTheme.bodyMedium,
            ),
            const Gap(AppSpacing.lg),
            const _Bio(),
            const Gap(AppSpacing.xl),
            FilledButton(onPressed: () {}, child: const Text('កែប្រែប្រវត្តិរូប')),
            const Gap(AppSpacing.xs),
            OutlinedButton(onPressed: () {}, child: const Text('ចែករំលែក')),
          ],
        ),
      ),
    );
  }
}
```

**មូលហេតុសំខាន់៖** `Gap` ជា `const` បាន ដែលមានន័យថា Flutter មិនបង្កើត widget ថ្មីរាល់ពេល rebuild ទេ។ `Spacer()` មិនអាចជា const បានទេ ហើយវាប្រើ `Flex` ដែលធ្វើឱ្យ layout គិតលើសម្ព័ន្ធ។

</details>

---

### លំហាត់ទី ២ — ColorScheme និងការលុបបំបាត់ hardcoded colors

**🎯 គោលដៅ**

រៀនថា Material 3 ផ្តល់តួនាទីពណ៌ (color roles) ដែលធានាថា contrast ត្រឹមត្រូវដោយស្វ័យប្រវត្តិ។ `Colors.blue` មិនដឹងអ្វីអំពីអក្សរដែលដាក់លើវាទេ។ `colorScheme.primary` ដឹង — ដោយសារវាមានគូ `onPrimary` ជានិច្ច។

**📋 តម្រូវការ**

១. បង្កើត `ThemeData` ពី seed color មួយគត់ ដោយប្រើ `ColorScheme.fromSeed`
២. បង្កើត theme ទាំង light និង dark ពី seed តែមួយ
៣. សរសេរអេក្រង់ "Color role reference" ដែលបង្ហាញ role នីមួយៗ (primary, secondary, tertiary, error, surface, surfaceContainer...) ជា swatch ដោយអក្សរនៅលើវាប្រើ `on*` ត្រូវគ្នា
៤. បន្ថែមប៊ូតុងប្តូរ light/dark

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `Colors.*` ទាំងស្រុងក្នុងកូដ UI (លើកលែងតែ `Colors.transparent`)
- ហាមប្រើ `Color(0xFF...)` ក្រៅពី seed color មួយកន្លែងគត់
- ហាមប្រើ `Theme.of(context).brightness == Brightness.dark ? A : B` ដើម្បីជ្រើសពណ៌

**✅ លក្ខខណ្ឌទទួលយក**

- ប្តូរ seed color មួយជួរ → app ទាំងមូលប្តូរ palette ដោយនៅតែអានបាន
- ប្តូរទៅ dark mode → គ្មានអក្សរណាបាត់ ឬ contrast ទាប
- Grep រក `Colors.` → រកឃើញតែក្នុង theme file

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/design/app_theme.dart
import 'package:material_ui/material_ui.dart';

abstract final class AppTheme {
  /// កន្លែងតែមួយគត់ក្នុង app ដែលមានតម្លៃពណ៌ឆៅ។
  static const Color _seed = Color(0xFF2D6A4F);

  static ThemeData light() => _build(Brightness.light);
  static ThemeData dark() => _build(Brightness.dark);

  static ThemeData _build(Brightness brightness) {
    final scheme = ColorScheme.fromSeed(
      seedColor: _seed,
      brightness: brightness,
    );
    return ThemeData(
      colorScheme: scheme,
      useMaterial3: true,
      scaffoldBackgroundColor: scheme.surface,
    );
  }
}
```

```dart
// lib/screens/color_roles_screen.dart
class ColorRolesScreen extends StatelessWidget {
  const ColorRolesScreen({super.key, required this.onToggleBrightness});

  final VoidCallback onToggleBrightness;

  @override
  Widget build(BuildContext context) {
    final scheme = Theme.of(context).colorScheme;

    // គូ (background, foreground) — ភ្ជាប់ជាមួយគ្នាជានិច្ច
    final roles = <String, (Color, Color)>{
      'primary': (scheme.primary, scheme.onPrimary),
      'primaryContainer': (scheme.primaryContainer, scheme.onPrimaryContainer),
      'secondary': (scheme.secondary, scheme.onSecondary),
      'secondaryContainer': (scheme.secondaryContainer, scheme.onSecondaryContainer),
      'tertiary': (scheme.tertiary, scheme.onTertiary),
      'error': (scheme.error, scheme.onError),
      'errorContainer': (scheme.errorContainer, scheme.onErrorContainer),
      'surface': (scheme.surface, scheme.onSurface),
      'surfaceContainer': (scheme.surfaceContainer, scheme.onSurface),
      'surfaceContainerHighest': (scheme.surfaceContainerHighest, scheme.onSurfaceVariant),
      'inverseSurface': (scheme.inverseSurface, scheme.onInverseSurface),
    };

    return Scaffold(
      appBar: AppBar(
        title: const Text('តួនាទីពណ៌'),
        actions: [
          IconButton(
            onPressed: onToggleBrightness,
            icon: const Icon(Icons.brightness_6_outlined),
            tooltip: 'ប្តូររវាងភ្លឺ និងងងឹត',
          ),
        ],
      ),
      body: ListView.separated(
        padding: AppSpacing.page,
        itemCount: roles.length,
        separatorBuilder: (_, __) => const Gap(AppSpacing.xs),
        itemBuilder: (context, index) {
          final entry = roles.entries.elementAt(index);
          final (background, foreground) = entry.value;
          return Container(
            padding: AppSpacing.card,
            decoration: BoxDecoration(
              color: background,
              borderRadius: BorderRadius.circular(12),
            ),
            child: Text(
              entry.key,
              style: Theme.of(context)
                  .textTheme
                  .titleMedium
                  ?.copyWith(color: foreground),
            ),
          );
        },
      ),
    );
  }
}
```

**ចំណុចសំខាន់៖** ការប្រើ Dart records `(Color, Color)` ធ្វើឱ្យគូ background/foreground មិនអាចបំបែកគ្នាបាន។ បើអ្នករក្សាវាជាបញ្ជីពីរដាច់ដោយឡែក ថ្ងៃណាមួយវានឹងលែងត្រូវគ្នា។

**ការផ្ទៀងផ្ទាត់:** ប្តូរ `_seed` ទៅ `Color(0xFFB3261E)` រួច hot reload។ Palette ទាំងមូលប្តូរ ប៉ុន្តែគ្រប់អក្សរនៅតែអានច្បាស់ — ដោយសារ Material 3 គណនា tone រវាង background និង foreground តាមក្បួន HCT។

</details>

---

### លំហាត់ទី ៣ — TextTheme និងអក្សរខ្មែរ

**🎯 គោលដៅ**

រៀនកំណត់ type scale ដែលមានតែពីរ ឬបីទំហំក្នុងអេក្រង់មួយ ហើយដោះស្រាយបញ្ហាជាក់លាក់របស់អក្សរខ្មែរ។

**📋 តម្រូវការ**

១. កំណត់ `TextTheme` ពេញលេញក្នុង `ThemeData` (display / headline / title / body / label)
២. កំណត់ `height` ឱ្យសមស្របសម្រាប់អក្សរខ្មែរ
៣. សរសេរអេក្រង់អត្ថបទ (article) ដែលមានចំណងជើង, ចំណងជើងរង, កថាខណ្ឌ, caption
៤. បន្ថែម font ខ្មែរ (`Kantumruy Pro` ឬ `Noto Sans Khmer`) តាម `pubspec.yaml`

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `TextStyle(fontSize: ...)` ផ្ទាល់ក្នុងអេក្រង់ — ត្រូវយកពី `Theme.of(context).textTheme` ជានិច្ច
- ហាមប្រើលើសពី ៣ ទំហំអក្សរក្នុងអេក្រង់តែមួយ
- ហាមប្រើ `FontWeight` ខុសពី ២ តម្លៃ (ធម្មតា និងដិត)

**✅ លក្ខខណ្ឌទទួលយក**

- អក្សរខ្មែរដែលមានជើង (ក្បួន, ស្រ្តី, ខ្ញុំ) មិនត្រូវជាន់គ្នាឬត្រូវកាត់ទេ
- បន្ថែម `copyWith` លើ `textTheme` មួយកន្លែង → អក្សរទាំង app ប្តូរ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

**បញ្ហាចម្បង៖** អក្សរខ្មែរមានសញ្ញាខាងលើ (ស្រៈ) និងជើងខាងក្រោម (subscript)។ `height` លំនាំដើមរបស់ Flutter (≈1.2) មិនគ្រប់គ្រាន់ ធ្វើឱ្យជើងអក្សរត្រូវកាត់ ឬបន្ទាត់ជាន់គ្នា។ តម្លៃដែលដំណើរការល្អគឺ **1.6–1.8**។

```yaml
# pubspec.yaml
flutter:
  fonts:
    - family: KantumruyPro
      fonts:
        - asset: assets/fonts/KantumruyPro-Regular.ttf
          weight: 400
        - asset: assets/fonts/KantumruyPro-Bold.ttf
          weight: 700
```

```dart
// lib/design/app_typography.dart
import 'package:material_ui/material_ui.dart';

abstract final class AppTypography {
  static const String _family = 'KantumruyPro';

  /// អក្សរខ្មែរត្រូវការទំហំបន្ទាត់ខ្ពស់ជាងឡាតាំង ដើម្បីឱ្យជើងអក្សរមានកន្លែង។
  static const double _khmerHeight = 1.7;

  /// អក្សរខ្លីៗ (ប៊ូតុង, label) អាចប្រើ height ទាបជាងបន្តិច
  static const double _khmerHeightTight = 1.4;

  static TextTheme build(ColorScheme scheme) {
    return TextTheme(
      headlineLarge: _style(32, FontWeight.w700, _khmerHeightTight),
      headlineMedium: _style(28, FontWeight.w700, _khmerHeightTight),
      titleLarge: _style(22, FontWeight.w700, _khmerHeightTight),
      titleMedium: _style(16, FontWeight.w700, _khmerHeightTight),
      bodyLarge: _style(16, FontWeight.w400, _khmerHeight),
      bodyMedium: _style(14, FontWeight.w400, _khmerHeight),
      labelLarge: _style(14, FontWeight.w700, _khmerHeightTight),
      labelSmall: _style(12, FontWeight.w400, _khmerHeightTight),
    ).apply(
      bodyColor: scheme.onSurface,
      displayColor: scheme.onSurface,
    );
  }

  static TextStyle _style(double size, FontWeight weight, double height) =>
      TextStyle(
        fontFamily: _family,
        fontSize: size,
        fontWeight: weight,
        height: height,
        // ទុកចន្លោះបន្ថែមខាងលើ/ក្រោមតាមតម្រូវការរបស់ font
        leadingDistribution: TextLeadingDistribution.even,
      );
}
```

ភ្ជាប់ចូល theme៖

```dart
static ThemeData _build(Brightness brightness) {
  final scheme = ColorScheme.fromSeed(seedColor: _seed, brightness: brightness);
  return ThemeData(
    colorScheme: scheme,
    useMaterial3: true,
    textTheme: AppTypography.build(scheme),
  );
}
```

**ការផ្ទៀងផ្ទាត់៖** សរសេរ `Text('ស្រ្តីខ្មែរជាអ្នកគ្រប់គ្រងគ្រួសារ')` ដោយ `height: 1.2` រួច screenshot។ បន្ទាប់មកប្តូរទៅ `1.7` រួច screenshot ម្តងទៀត។ ប្រៀបធៀបជើងអក្សរ `្រ` និង `្ត` — នេះជាភស្តុតាងផ្ទាល់ភ្នែក។

**មូលហេតុនៃច្បាប់ "៣ ទំហំ"៖** អេក្រង់ដែលមាន ៦ ទំហំអក្សរមិនមានលំដាប់ព័ត៌មានទេ — វាមានតែភាពរញ៉េរញ៉ៃ។ លំដាប់កើតឡើងតាមរយៈ **ភាពខុសគ្នាច្បាស់លាស់** មិនមែនតាមរយៈជម្រើសច្រើនទេ។

</details>

---

### លំហាត់ទី ៤ — ThemeExtension សម្រាប់ token ផ្ទាល់ខ្លួន

**🎯 គោលដៅ**

Material ផ្តល់ `colorScheme` និង `textTheme` ប៉ុន្តែ app ពិតប្រាកដត្រូវការ token ផ្សេងទៀត៖ ពណ៌ success/warning, radius scale, duration។ `ThemeExtension` ជាវិធីផ្លូវការក្នុងការបន្ថែមវា។

**📋 តម្រូវការ**

សរសេរ `AppTokens extends ThemeExtension<AppTokens>` ដែលមាន៖
- `success`, `onSuccess`, `warning`, `onWarning`, `info` (ព្រោះ Material 3 មានតែ `error`)
- `radiusSm`, `radiusMd`, `radiusLg`
- `durationFast`, `durationNormal`

បន្ថែម extension method លើ `BuildContext` ដើម្បីអានវាខ្លីៗ។

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ global variable ឬ singleton
- ត្រូវ implement `copyWith` និង `lerp` ឱ្យបានត្រឹមត្រូវ (បើមិនដូច្នេះទេ ការប្តូរ theme នឹងលោត មិន animate)

**✅ លក្ខខណ្ឌទទួលយក**

- `context.tokens.success` ដំណើរការ
- ការប្តូរ light → dark ធ្វើឱ្យពណ៌ success ផ្លាស់ប្តូរ **ដោយរលូន** (មិនលោត)

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/design/app_tokens.dart
import 'package:material_ui/material_ui.dart';

@immutable
class AppTokens extends ThemeExtension<AppTokens> {
  const AppTokens({
    required this.success,
    required this.onSuccess,
    required this.warning,
    required this.onWarning,
    required this.info,
    required this.radiusSm,
    required this.radiusMd,
    required this.radiusLg,
    required this.durationFast,
    required this.durationNormal,
  });

  final Color success;
  final Color onSuccess;
  final Color warning;
  final Color onWarning;
  final Color info;

  final double radiusSm;
  final double radiusMd;
  final double radiusLg;

  final Duration durationFast;
  final Duration durationNormal;

  static const AppTokens light = AppTokens(
    success: Color(0xFF1B6B3A),
    onSuccess: Color(0xFFFFFFFF),
    warning: Color(0xFF8A5A00),
    onWarning: Color(0xFFFFFFFF),
    info: Color(0xFF17557E),
    radiusSm: 8,
    radiusMd: 12,
    radiusLg: 20,
    durationFast: Duration(milliseconds: 150),
    durationNormal: Duration(milliseconds: 250),
  );

  static const AppTokens dark = AppTokens(
    success: Color(0xFF7CD9A0),
    onSuccess: Color(0xFF00391B),
    warning: Color(0xFFF5BF66),
    onWarning: Color(0xFF3F2E00),
    info: Color(0xFF9BCBF0),
    radiusSm: 8,
    radiusMd: 12,
    radiusLg: 20,
    durationFast: Duration(milliseconds: 150),
    durationNormal: Duration(milliseconds: 250),
  );

  @override
  AppTokens copyWith({
    Color? success,
    Color? onSuccess,
    Color? warning,
    Color? onWarning,
    Color? info,
    double? radiusSm,
    double? radiusMd,
    double? radiusLg,
    Duration? durationFast,
    Duration? durationNormal,
  }) {
    return AppTokens(
      success: success ?? this.success,
      onSuccess: onSuccess ?? this.onSuccess,
      warning: warning ?? this.warning,
      onWarning: onWarning ?? this.onWarning,
      info: info ?? this.info,
      radiusSm: radiusSm ?? this.radiusSm,
      radiusMd: radiusMd ?? this.radiusMd,
      radiusLg: radiusLg ?? this.radiusLg,
      durationFast: durationFast ?? this.durationFast,
      durationNormal: durationNormal ?? this.durationNormal,
    );
  }

  /// ត្រូវតែ implement ឱ្យបានពេញលេញ បើមិនដូច្នេះទេ AnimatedTheme នឹងលោត។
  @override
  AppTokens lerp(covariant AppTokens? other, double t) {
    if (other == null) return this;
    return AppTokens(
      success: Color.lerp(success, other.success, t)!,
      onSuccess: Color.lerp(onSuccess, other.onSuccess, t)!,
      warning: Color.lerp(warning, other.warning, t)!,
      onWarning: Color.lerp(onWarning, other.onWarning, t)!,
      info: Color.lerp(info, other.info, t)!,
      radiusSm: lerpDouble(radiusSm, other.radiusSm, t)!,
      radiusMd: lerpDouble(radiusMd, other.radiusMd, t)!,
      radiusLg: lerpDouble(radiusLg, other.radiusLg, t)!,
      durationFast: t < 0.5 ? durationFast : other.durationFast,
      durationNormal: t < 0.5 ? durationNormal : other.durationNormal,
    );
  }
}

/// អានងាយជាង Theme.of(context).extension<AppTokens>()!
extension AppTokensX on BuildContext {
  AppTokens get tokens => Theme.of(this).extension<AppTokens>()!;
  ColorScheme get colors => Theme.of(this).colorScheme;
  TextTheme get text => Theme.of(this).textTheme;
}
```

ភ្ជាប់ចូល theme៖

```dart
ThemeData(
  colorScheme: scheme,
  useMaterial3: true,
  textTheme: AppTypography.build(scheme),
  extensions: [
    brightness == Brightness.light ? AppTokens.light : AppTokens.dark,
  ],
)
```

ការប្រើ៖

```dart
Container(
  decoration: BoxDecoration(
    color: context.tokens.success,
    borderRadius: BorderRadius.circular(context.tokens.radiusMd),
  ),
  child: Text('រក្សាទុកបានជោគជ័យ',
      style: context.text.labelLarge?.copyWith(color: context.tokens.onSuccess)),
)
```

**ចំណាំពី `lerp`៖** មនុស្សភាគច្រើនសរសេរ `lerp` ដោយ `return other ?? this;` ដែលធ្វើឱ្យវាដំណើរការ ប៉ុន្តែពណ៌នឹង **លោត** ភ្លាមៗនៅពេលប្តូរ theme ជំនួសឱ្យការ fade។ នេះជាកំហុសដែលរកឃើញតែពេលមើលដោយភ្នែក។

</details>

---

### លំហាត់ទី ៥ — Shape, elevation និង dark mode parity

**🎯 គោលដៅ**

រៀនថា ក្នុង Material 3 ជម្រៅ (depth) មិនបង្ហាញតាមស្រមោល (shadow) ទេ ប៉ុន្តែតាម **surface tint** និង **surface container levels**។ ស្រមោលធ្ងន់ៗគឺជាសញ្ញានៃការរចនាចាស់។

**📋 តម្រូវការ**

សរសេរអេក្រង់ dashboard ដែលមានស្រទាប់បី៖ ផ្ទៃខាងក្រោយ, card, និង card ជាន់ក្នុង card។ បង្ហាញភាពខុសគ្នាដោយប្រើ `surfaceContainer` levels ជំនួសស្រមោល។

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `BoxShadow` ដោយផ្ទាល់
- ហាមប្រើ `elevation` លើសពី `1` លើ widget ណាមួយ
- ត្រូវប្រើ `borderRadius` តែពី `context.tokens` ប៉ុណ្ណោះ

**✅ លក្ខខណ្ឌទទួលយក**

- ថតរូបអេក្រង់ក្នុង light និង dark → ស្រទាប់ទាំងបីនៅតែបែងចែកបានច្បាស់ក្នុងទាំងពីរ
- បង្កើនពន្លឺទូរសព្ទដល់អតិបរមាក្រៅផ្ទះ → ស្រទាប់នៅតែមើលឃើញ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// Material 3 មាន surface container ៥ កម្រិត — ប្រើវាជា "elevation"
//   surface                     ← ផ្ទៃខាងក្រោយ
//   surfaceContainerLowest
//   surfaceContainerLow
//   surfaceContainer            ← card ធម្មតា
//   surfaceContainerHigh        ← card ជាន់ក្នុង
//   surfaceContainerHighest     ← ធាតុសកម្ម / hover

class DashboardScreen extends StatelessWidget {
  const DashboardScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: context.colors.surface,
      appBar: AppBar(title: const Text('ផ្ទាំងគ្រប់គ្រង')),
      body: ListView(
        padding: AppSpacing.page,
        children: [
          _Surface(
            level: context.colors.surfaceContainer,
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text('ការលក់ខែនេះ', style: context.text.titleMedium),
                const Gap(AppSpacing.sm),
                Text('៤,២៥០,០០០ ៛', style: context.text.headlineMedium),
                const Gap(AppSpacing.md),
                // ស្រទាប់ជាន់ក្នុង — កម្រិតខ្ពស់ជាងមួយថ្នាក់
                _Surface(
                  level: context.colors.surfaceContainerHigh,
                  child: Row(
                    children: [
                      Icon(Icons.trending_up, color: context.tokens.success),
                      const Gap.horizontal(AppSpacing.xs),
                      Expanded(
                        child: Text('កើនឡើង ១២% ធៀបនឹងខែមុន',
                            style: context.text.bodyMedium),
                      ),
                    ],
                  ),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}

class _Surface extends StatelessWidget {
  const _Surface({required this.level, required this.child});

  final Color level;
  final Widget child;

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: AppSpacing.card,
      decoration: BoxDecoration(
        color: level,
        borderRadius: BorderRadius.circular(context.tokens.radiusMd),
      ),
      child: child,
    );
  }
}
```

**មូលហេតុ:** ស្រមោល (`BoxShadow`) ស្ទើរតែមើលមិនឃើញលើផ្ទៃខ្មៅក្នុង dark mode ហើយវាក៏មើលមិនឃើញនៅក្រៅផ្ទះក្រោមពន្លឺថ្ងៃដែរ។ **ភាពខុសគ្នានៃពណ៌ផ្ទៃ (surface tone) ដំណើរការក្នុងគ្រប់ស្ថានភាព។** នេះជាមូលហេតុដែល Material 3 បានផ្លាស់ប្តូរពី elevation-by-shadow ទៅ elevation-by-tone។

**លំហាត់បន្ថែម:** សរសេរ version មួយទៀតដោយប្រើ `BoxShadow(blurRadius: 12, color: Colors.black26)` រួចប្តូរទៅ dark mode។ អ្នកនឹងឃើញថា card បាត់បង់ព្រំដែនទាំងស្រុង។

</details>

---

# ផ្នែកទី ២ — ប្លង់ និង Constraints

Flutter មិនមាន CSS ទេ។ ប្លង់ត្រូវបានកំណត់ដោយក្បួនតែមួយ៖ **constraints ចុះក្រោម, sizes ឡើងលើ, parent កំណត់ទីតាំង**។ វិស្វករដែលមិនយល់ក្បួននេះនឹងចំណាយពេលរាប់ម៉ោងជាមួយ `RenderFlex overflowed by 23 pixels`។

---

### លំហាត់ទី ៦ — បំបែក build() យក្សទៅជា component

**🎯 គោលដៅ**

រៀនថា `build()` វែងមិនត្រឹមតែពិបាកអានទេ — វា **យឺត** ផងដែរ ព្រោះ Flutter rebuild ទាំងអស់ជាមួយគ្នា។

**📋 តម្រូវការ**

នេះជាកូដដែលត្រូវកែ (សរសេរវាចូល project របស់អ្នកជាមុនសិន)៖

```dart
class MessyProductScreen extends StatefulWidget {
  const MessyProductScreen({super.key});
  @override
  State<MessyProductScreen> createState() => _MessyProductScreenState();
}

class _MessyProductScreenState extends State<MessyProductScreen> {
  int quantity = 1;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SingleChildScrollView(
        child: Column(
          children: [
            Image.network('https://picsum.photos/600/400'),
            Padding(
              padding: const EdgeInsets.all(16),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  const Text('កាហ្វេមណ្ឌលគិរី',
                      style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold)),
                  const SizedBox(height: 8),
                  const Text('៨,០០០ ៛', style: TextStyle(fontSize: 20)),
                  const SizedBox(height: 16),
                  const Text('គ្រាប់កាហ្វេ Arabica ដាំដុះនៅខេត្តមណ្ឌលគិរី...'),
                  const SizedBox(height: 16),
                  Row(
                    children: [
                      IconButton(
                        onPressed: () => setState(() => quantity--),
                        icon: const Icon(Icons.remove),
                      ),
                      Text('$quantity'),
                      IconButton(
                        onPressed: () => setState(() => quantity++),
                        icon: const Icon(Icons.add),
                      ),
                    ],
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

កែវាដោយ៖
១. បំបែកទៅជា widget ដាច់ដោយឡែកយ៉ាងតិច ៤
២. ធ្វើឱ្យអ្វីៗដែលមិនអាស្រ័យលើ `quantity` ក្លាយជា `const`
៣. ដាក់ `quantity` ក្នុង widget តូចមួយ ដើម្បីកុំឱ្យអេក្រង់ទាំងមូល rebuild
៤. ជួសជុល bug ដែលអនុញ្ញាតឱ្យ quantity ចុះក្រោម 1

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ method ដែល `return Widget` (ដូចជា `Widget _buildHeader()`) — ត្រូវប្រើ class
- ហាមឱ្យ `build()` របស់អេក្រង់មេវែងលើសពី ២៥ ជួរ
- ត្រូវប្រើ `const` គ្រប់កន្លែងដែលអាចប្រើបាន

**✅ លក្ខខណ្ឌទទួលយក**

- បើក DevTools → Performance → បើក "Track widget rebuilds"។ ចុច + → មានតែ counter widget ប៉ុណ្ណោះដែល rebuild
- `flutter analyze` មិនរាយការណ៍ `prefer_const_constructors`

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
class ProductScreen extends StatelessWidget {
  const ProductScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: SingleChildScrollView(
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            _ProductImage(),
            Padding(
              padding: AppSpacing.card,
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  _ProductHeader(),
                  Gap(AppSpacing.md),
                  _ProductDescription(),
                  Gap(AppSpacing.lg),
                  QuantityStepper(),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class _ProductImage extends StatelessWidget {
  const _ProductImage();

  @override
  Widget build(BuildContext context) {
    return AspectRatio(
      aspectRatio: 3 / 2,
      child: Image.network(
        'https://picsum.photos/600/400',
        fit: BoxFit.cover,
        // តែងតែដោះស្រាយស្ថានភាពកំពុងផ្ទុក និងកំហុស
        loadingBuilder: (context, child, progress) => progress == null
            ? child
            : ColoredBox(color: context.colors.surfaceContainer),
        errorBuilder: (context, _, __) => ColoredBox(
          color: context.colors.surfaceContainer,
          child: Center(
            child: Icon(Icons.image_not_supported_outlined,
                color: context.colors.onSurfaceVariant),
          ),
        ),
      ),
    );
  }
}

class _ProductHeader extends StatelessWidget {
  const _ProductHeader();

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text('កាហ្វេមណ្ឌលគិរី', style: context.text.headlineMedium),
        const Gap(AppSpacing.xxs),
        Text('៨,០០០ ៛',
            style: context.text.titleLarge
                ?.copyWith(color: context.colors.primary)),
      ],
    );
  }
}

class _ProductDescription extends StatelessWidget {
  const _ProductDescription();

  @override
  Widget build(BuildContext context) {
    return Text(
      'គ្រាប់កាហ្វេ Arabica ដាំដុះនៅខេត្តមណ្ឌលគិរី លើកម្ពស់ ៨០០ ម៉ែត្រ។',
      style: context.text.bodyMedium
          ?.copyWith(color: context.colors.onSurfaceVariant),
    );
  }
}

/// State ស្ថិតនៅត្រង់នេះតែប៉ុណ្ណោះ — rebuild មិនឡើងដល់អេក្រង់មេទេ។
class QuantityStepper extends StatefulWidget {
  const QuantityStepper({super.key, this.min = 1, this.max = 99});

  final int min;
  final int max;

  @override
  State<QuantityStepper> createState() => _QuantityStepperState();
}

class _QuantityStepperState extends State<QuantityStepper> {
  int _quantity = 1;

  bool get _canDecrease => _quantity > widget.min;
  bool get _canIncrease => _quantity < widget.max;

  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisSize: MainAxisSize.min,
      children: [
        IconButton.outlined(
          // null = ប៊ូតុងបិទដោយស្វ័យប្រវត្តិ ព្រមទាំងពណ៌ត្រឹមត្រូវ
          onPressed: _canDecrease ? () => setState(() => _quantity--) : null,
          icon: const Icon(Icons.remove),
          tooltip: 'បន្ថយចំនួន',
        ),
        SizedBox(
          width: AppSpacing.xxl,
          child: Text(
            '$_quantity',
            textAlign: TextAlign.center,
            style: context.text.titleMedium,
          ),
        ),
        IconButton.outlined(
          onPressed: _canIncrease ? () => setState(() => _quantity++) : null,
          icon: const Icon(Icons.add),
          tooltip: 'បង្កើនចំនួន',
        ),
      ],
    );
  }
}
```

**មូលហេតុដែលហាមប្រើ `Widget _buildHeader()`៖**

Method មិនបង្កើត Element ថ្មីទេ។ វាគ្រាន់តែបញ្ចូលកូដទៅក្នុង `build()` របស់ parent។ លទ្ធផល៖
- មិនអាចជា `const` បានទេ
- rebuild រាល់ពេលដែល parent rebuild
- មិនបង្ហាញដាច់ដោយឡែកក្នុង DevTools Inspector
- មិនអាចដាក់ `Preview` ឬសរសេរ widget test ដាច់ដោយឡែកបានទេ

Class ដោះស្រាយបញ្ហាទាំង ៤ នេះ។ **នេះជាការកែលម្អដ៏មានតម្លៃបំផុតដែលអ្នកអាចធ្វើលើ codebase Flutter ចាស់មួយ។**

**កំណត់ចំណាំពី `_quantity` ៖** ការប្រើ `onPressed: null` ជំនួសឱ្យការមិនធ្វើអ្វី គឺជា best practice — Flutter ផ្តល់ស្ថានភាព disabled ដែលមានពណ៌ត្រឹមត្រូវ និងប្រាប់ screen reader ដោយស្វ័យប្រវត្តិ។

</details>

---

### លំហាត់ទី ៧ — ស្វែងយល់ overflow តាមរយៈ constraints

**🎯 គោលដៅ**

ឈប់ដោះស្រាយ overflow ដោយការទាយ។ រៀនអានសារកំហុសហើយដឹងភ្លាមថាត្រូវប្រើ `Expanded`, `Flexible`, `IntrinsicWidth` ឬអ្វីផ្សេង។

**📋 តម្រូវការ**

សរសេរអេក្រង់មួយដែលមានករណី overflow ទាំង ៤ ខាងក្រោម រួច**ជួសជុលនីមួយៗ**៖

| ករណី | ស្ថានភាព |
|---|---|
| A | `Row` ដែលមាន `Text` វែងខ្លាំង |
| B | `Column` ក្នុង `Column` ដែលមាន height មិនកំណត់ |
| C | `ListView` ក្នុង `Column` |
| D | `Row` ដែលមាន `TextField` និង `Text` |

សម្រាប់នីមួយៗ សរសេរ comment ពន្យល់ថា **constraint អ្វីដែលបាត់** មិនមែនត្រឹមតែសរសេរដំណោះស្រាយទេ។

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `SingleChildScrollView` ដើម្បីលាក់ overflow
- ហាមប្រើ `MediaQuery.of(context).size.width * 0.6` ដើម្បីទាយទទឹង
- ហាមប្រើ `overflow: TextOverflow.clip` ដើម្បីលាក់បញ្ហា

**✅ លក្ខខណ្ឌទទួលយក**

- បង្វិលឧបករណ៍ទៅ landscape → គ្មាន overflow
- ប្តូរអក្សរទៅភាសាដែលវែងជាង (ឧ. ខ្មែរ → អាល្លឺម៉ង់) → គ្មាន overflow

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// ករណី A — Row ជាមួយ Text វែង
// បញ្ហា: Row ផ្តល់ constraint ទទឹង "មិនកំណត់" ដល់កូន។
//        Text ត្រូវការទទឹងទាំងអស់ដែលអត្ថបទត្រូវការ។
// ដំណោះស្រាយ: Expanded ប្តូរ constraint ជា "ទទឹងជាក់លាក់តាមកន្លែងនៅសល់"។
Row(
  children: [
    const Icon(Icons.info_outline),
    const Gap.horizontal(AppSpacing.xs),
    Expanded(
      child: Text(
        'ការបញ្ជាទិញរបស់អ្នកនឹងត្រូវដឹកជញ្ជូនក្នុងរយៈពេល ៣ ថ្ងៃធ្វើការ',
        // maxLines + ellipsis គឺជាការសម្រេចចិត្តរចនា មិនមែនការលាក់បញ្ហា
        maxLines: 2,
        overflow: TextOverflow.ellipsis,
      ),
    ),
  ],
)

// ករណី B — Column ក្នុង Column
// បញ្ហា: Column ខាងក្រៅផ្តល់កម្ពស់មិនកំណត់ដល់កូន។
//        Column ខាងក្នុងចង់បានកម្ពស់អតិបរមា → មិនអាចគណនាបាន។
// ដំណោះស្រាយ: Expanded (យកកន្លែងនៅសល់) ឬ mainAxisSize: MainAxisSize.min
Column(
  children: [
    const _Header(),
    Expanded(                      // ← ផ្តល់កម្ពស់ជាក់លាក់
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [/* ... */],
      ),
    ),
  ],
)

// ករណី C — ListView ក្នុង Column
// បញ្ហា: ListView ចង់បានកម្ពស់មិនកំណត់។ Column ក៏ផ្តល់កម្ពស់មិនកំណត់។
//        គ្មានអ្នកណាសម្រេចទំហំបានទេ។
// ដំណោះស្រាយទី ១ (ល្អបំផុត): Expanded
Column(
  children: [
    const _SearchBar(),
    Expanded(child: ListView.builder(itemCount: 50, itemBuilder: ...)),
  ],
)
// ដំណោះស្រាយទី ២ (សម្រាប់បញ្ជីខ្លីតែប៉ុណ្ណោះ): shrinkWrap
//   ⚠️ shrinkWrap បង្ខំឱ្យ ListView គណនាកូនទាំងអស់ភ្លាមៗ → បាត់អត្ថប្រយោជន៍ lazy
ListView.builder(
  shrinkWrap: true,
  physics: const NeverScrollableScrollPhysics(),
  itemCount: 5,   // ត្រូវតែតិច!
  itemBuilder: ...,
)
// ដំណោះស្រាយទី ៣ (ល្អបំផុតសម្រាប់ផ្នែកច្រើន): CustomScrollView + Slivers
CustomScrollView(
  slivers: [
    const SliverToBoxAdapter(child: _Header()),
    SliverList.builder(itemCount: 50, itemBuilder: ...),
  ],
)

// ករណី D — Row ជាមួយ TextField
// បញ្ហា: TextField មិនមានទទឹងធម្មជាតិទេ — វាចង់បានទទឹងទាំងអស់។
// ដំណោះស្រាយ: Expanded លើ TextField, Flexible លើ Text
Row(
  children: [
    const Expanded(child: TextField(decoration: InputDecoration(labelText: 'ស្វែងរក'))),
    const Gap.horizontal(AppSpacing.xs),
    // Flexible: បង្រួមបានបើចាំបាច់ ប៉ុន្តែមិនរីកទេ
    Flexible(
      child: Text('លទ្ធផល ១២៣', overflow: TextOverflow.ellipsis),
    ),
  ],
)
```

**តារាងសម្រាប់ចងចាំ៖**

| ត្រូវការ | ប្រើ |
|---|---|
| យកកន្លែងនៅសល់ទាំងអស់ | `Expanded` |
| យកតែអ្វីដែលត្រូវការ តែបង្រួមបានបើតូច | `Flexible(fit: FlexFit.loose)` |
| ដឹងទំហំ parent មុននឹងសម្រេច | `LayoutBuilder` |
| Widget ជាច្រើនត្រូវមានទំហំស្មើគ្នាតាមធាតុធំបំផុត | `IntrinsicHeight` (ថ្លៃ! ប្រើដោយប្រុងប្រយ័ត្ន) |

**ភស្តុតាង:** បន្ថែម `debugPaintSizeEnabled = true;` ក្នុង `main()` រួច hot restart។ អ្នកនឹងឃើញព្រំដែន layout ជាពណ៌ខៀវ និងទិសដៅ flex ជាព្រួញ។ នេះជាឧបករណ៍ដ៏ល្អបំផុតសម្រាប់យល់ constraints។

```dart
import 'package:flutter/rendering.dart';

void main() {
  debugPaintSizeEnabled = true;   // លុបចេញមុន commit!
  runApp(const MyApp());
}
```

</details>

---

### លំហាត់ទី ៨ — Responsive breakpoints

**🎯 គោលដៅ**

រៀនប្រើ breakpoints ផ្លូវការរបស់ Material 3 ជំនួសឱ្យការទាយលេខ ហើយរៀនថា **width មិនមែនជាឧបករណ៍តែមួយគត់** ទេ។

**📋 តម្រូវការ**

១. សរសេរ `enum WindowSize { compact, medium, expanded, large }` តាមស្តង់ដារ Material 3
២. សរសេរ `WindowSize.of(context)` extension
៣. សរសេរ grid ផលិតផលដែលបង្ហាញ ១ ជួរឈរលើ compact, ២ លើ medium, ៣ លើ expanded, ៤ លើ large
៤. Card នីមួយៗត្រូវប្តូររូបរាងផងដែរ — មិនត្រឹមតែចំនួនជួរឈរទេ

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `Platform.isAndroid` ឬ `kIsWeb` ដើម្បីសម្រេចប្លង់
- ហាមប្រើ `MediaQuery.of(context).size` ដោយផ្ទាល់ក្នុង widget (ព្រោះវាបង្ខំ rebuild លើគ្រប់ការប្តូរ keyboard)
- ត្រូវប្រើ `LayoutBuilder` សម្រាប់ការសម្រេចចិត្តក្នុងតំបន់

**✅ លក្ខខណ្ឌទទួលយក**

- រត់លើ web រួចអូសបង្អួច → ប្លង់ប្តូររលូនតាមកម្រិត
- ដាក់ grid ក្នុង panel ចង្អៀត (មិនមែនពេញអេក្រង់) → វានៅតែសម្រេចត្រឹមត្រូវ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/design/window_size.dart
import 'package:material_ui/material_ui.dart';

/// Breakpoints តាមស្តង់ដារ Material 3
enum WindowSize {
  compact,   // < 600  — ទូរសព្ទបញ្ឈរ
  medium,    // 600-839 — ថេប្លេតតូច / ទូរសព្ទផ្តេក
  expanded,  // 840-1199 — ថេប្លេតធំ
  large;     // >= 1200 — desktop

  static WindowSize fromWidth(double width) {
    if (width < 600) return WindowSize.compact;
    if (width < 840) return WindowSize.medium;
    if (width < 1200) return WindowSize.expanded;
    return WindowSize.large;
  }

  /// ប្រើសម្រាប់ការសម្រេចថ្នាក់ app (ឧ. navigation)
  static WindowSize of(BuildContext context) =>
      fromWidth(MediaQuery.sizeOf(context).width);

  int get gridColumns => switch (this) {
        WindowSize.compact => 1,
        WindowSize.medium => 2,
        WindowSize.expanded => 3,
        WindowSize.large => 4,
      };

  bool get isCompact => this == WindowSize.compact;
}
```

```dart
// lib/widgets/product_grid.dart
class ProductGrid extends StatelessWidget {
  const ProductGrid({super.key, required this.products});

  final List<Product> products;

  @override
  Widget build(BuildContext context) {
    // LayoutBuilder វាស់កន្លែងដែល widget នេះទទួលបានពិតប្រាកដ
    // មិនមែនទំហំអេក្រង់ទាំងមូលទេ — ដូច្នេះវាដំណើរការក្នុង panel ចង្អៀតដែរ
    return LayoutBuilder(
      builder: (context, constraints) {
        final size = WindowSize.fromWidth(constraints.maxWidth);

        return GridView.builder(
          padding: AppSpacing.page,
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: size.gridColumns,
            crossAxisSpacing: AppSpacing.md,
            mainAxisSpacing: AppSpacing.md,
            // ផ្តេកលើទូរសព្ទ, បញ្ឈរលើអេក្រង់ធំ
            childAspectRatio: size.isCompact ? 2.4 : 0.78,
          ),
          itemCount: products.length,
          itemBuilder: (context, index) => ProductCard(
            product: products[index],
            // Card ខ្លួនឯងក៏ប្តូររូបរាងដែរ
            layout: size.isCompact ? CardLayout.horizontal : CardLayout.vertical,
          ),
        );
      },
    );
  }
}
```

**មូលហេតុដែលហាមប្រើ `MediaQuery.of(context).size` ដោយផ្ទាល់៖**

`MediaQuery.of(context)` ធ្វើឱ្យ widget របស់អ្នក rebuild នៅពេល **អ្វីមួយ** ក្នុង `MediaQueryData` ប្តូរ — រួមទាំង `viewInsets` ពេល keyboard លេចឡើង, `padding`, `textScaler` ។ល។

`MediaQuery.sizeOf(context)` (Flutter 3.10+) ស្តាប់តែ `size` ប៉ុណ្ណោះ។ វាមាន variant ជាច្រើន៖ `MediaQuery.paddingOf`, `MediaQuery.viewInsetsOf`, `MediaQuery.textScalerOf`, `MediaQuery.platformBrightnessOf`។ **ប្រើ variant ជាក់លាក់ជានិច្ច។**

**មូលហេតុ `LayoutBuilder` ល្អជាង `MediaQuery` សម្រាប់ grid៖** បើថ្ងៃណាមួយអ្នកដាក់ grid នេះក្នុង master-detail layout ដែលមានតែ ៤០% នៃទទឹងអេក្រង់ `MediaQuery` នឹងទាយថាវាធំ ហើយបង្ហាញ ៤ ជួរឈរក្នុងកន្លែងតូច។ `LayoutBuilder` វាស់កន្លែងពិត។

</details>

---

### លំហាត់ទី ៩ — Adaptive navigation

**🎯 គោលដៅ**

Navigation គឺជាកន្លែងដែល responsive design ចាំបាច់បំផុត។ `BottomNavigationBar` លើ desktop មើលទៅដូចជាកំហុស។

**📋 តម្រូវការ**

សរសេរ `AdaptiveScaffold` ដែល៖
- **compact** → `NavigationBar` នៅខាងក្រោម
- **medium** → `NavigationRail` (បង្រួម, តែ icon)
- **expanded / large** → `NavigationRail` ពង្រីក (icon + label) ឬ `NavigationDrawer` អចិន្ត្រៃយ៍

ត្រូវរក្សា **state របស់ tab** ពេលប្តូរទំហំ (កុំឱ្យត្រឡប់ទៅ tab ០ វិញ)។

**🚫 ច្បាប់តឹងរឹង**

- ហាមសរសេរ navigation logic ដដែលៗសម្រាប់ទំហំនីមួយៗ — ត្រូវមានប្រភពទិន្នន័យតែមួយ
- ហាមឱ្យអេក្រង់ខាងក្នុងដឹងអំពីទំហំបង្អួច
- ត្រូវប្រើ `IndexedStack` ឬវិធីផ្សេងដើម្បីរក្សា scroll position

**✅ លក្ខខណ្ឌទទួលយក**

- ជ្រើស tab ទី ៣ → អូសបង្អួចឱ្យធំ → នៅតែស្ថិតលើ tab ទី ៣
- Scroll ចុះក្រោមក្នុង tab មួយ → ប្តូរ tab → ត្រឡប់មកវិញ → scroll position នៅដដែល

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/widgets/adaptive_scaffold.dart
import 'package:material_ui/material_ui.dart';

/// ប្រភពទិន្នន័យតែមួយសម្រាប់ navigation ទាំងអស់។
class NavDestination {
  const NavDestination({
    required this.label,
    required this.icon,
    required this.selectedIcon,
    required this.screen,
  });

  final String label;
  final IconData icon;
  final IconData selectedIcon;
  final Widget screen;
}

const destinations = <NavDestination>[
  NavDestination(
    label: 'ទំព័រដើម',
    icon: Icons.home_outlined,
    selectedIcon: Icons.home,
    screen: HomeScreen(),
  ),
  NavDestination(
    label: 'ស្វែងរក',
    icon: Icons.search_outlined,
    selectedIcon: Icons.search,
    screen: SearchScreen(),
  ),
  NavDestination(
    label: 'កម្ម៉ង់',
    icon: Icons.receipt_long_outlined,
    selectedIcon: Icons.receipt_long,
    screen: OrdersScreen(),
  ),
  NavDestination(
    label: 'គណនី',
    icon: Icons.person_outline,
    selectedIcon: Icons.person,
    screen: ProfileScreen(),
  ),
];

class AdaptiveScaffold extends StatefulWidget {
  const AdaptiveScaffold({super.key});

  @override
  State<AdaptiveScaffold> createState() => _AdaptiveScaffoldState();
}

class _AdaptiveScaffoldState extends State<AdaptiveScaffold> {
  int _index = 0;

  void _select(int i) => setState(() => _index = i);

  @override
  Widget build(BuildContext context) {
    final size = WindowSize.of(context);

    // IndexedStack រក្សា State និង scroll position របស់អេក្រង់ទាំងអស់
    final body = IndexedStack(
      index: _index,
      children: [for (final d in destinations) d.screen],
    );

    return switch (size) {
      WindowSize.compact => Scaffold(
          body: body,
          bottomNavigationBar: NavigationBar(
            selectedIndex: _index,
            onDestinationSelected: _select,
            destinations: [
              for (final d in destinations)
                NavigationDestination(
                  icon: Icon(d.icon),
                  selectedIcon: Icon(d.selectedIcon),
                  label: d.label,
                ),
            ],
          ),
        ),
      WindowSize.medium || WindowSize.expanded || WindowSize.large => Scaffold(
          body: Row(
            children: [
              NavigationRail(
                selectedIndex: _index,
                onDestinationSelected: _select,
                // ពង្រីកតែពេលមានកន្លែងគ្រប់គ្រាន់
                extended: size == WindowSize.large,
                labelType: size == WindowSize.large
                    ? NavigationRailLabelType.none
                    : NavigationRailLabelType.all,
                destinations: [
                  for (final d in destinations)
                    NavigationRailDestination(
                      icon: Icon(d.icon),
                      selectedIcon: Icon(d.selectedIcon),
                      label: Text(d.label),
                    ),
                ],
              ),
              const VerticalDivider(width: 1, thickness: 1),
              Expanded(child: body),
            ],
          ),
        ),
    };
  }
}
```

**ចំណុចសំខាន់ទាំង ៣៖**

១. **`destinations` ជា const list មួយ** — បន្ថែម tab ថ្មី = កែមួយកន្លែង ហើយវាលេចឡើងក្នុងគ្រប់ layout។

២. **`IndexedStack` ទល់នឹង `PageView` ទល់នឹង switch ធម្មតា៖**
   - `IndexedStack` — រក្សា state ទាំងអស់ ប៉ុន្តែសាងសង់អេក្រង់ទាំងអស់តាំងពីដំបូង (សមរម្យសម្រាប់ tab ៣–៥)
   - `PageView` — អនុញ្ញាតឱ្យអូស ប៉ុន្តែពិបាកគ្រប់គ្រង
   - `destinations[_index].screen` ធម្មតា — សន្សំសំចៃបំផុត ប៉ុន្តែ **បាត់ scroll position រាល់ការប្តូរ tab**

៣. **`switch` expression លើ enum** ធានាថាបើអ្នកបន្ថែម `WindowSize` ថ្មី compiler នឹងរាយការណ៍កំហុសភ្លាម (exhaustiveness checking)។

</details>

---

### លំហាត់ទី ១០ — SafeArea, insets និង keyboard

**🎯 គោលដៅ**

រៀនថាអេក្រង់ពិតមិនមែនជាចតុកោណកែងទទេទេ — វាមាន notch, home indicator, status bar, និង keyboard ដែលលេចឡើងលេចចុះ។

**📋 តម្រូវការ**

សរសេរអេក្រង់ chat ដែល៖
១. មានប៊ូតុងអណ្តែត (floating action) នៅជិតបាតអេក្រង់ ដែល **មិនត្រូវជាន់ home indicator**
២. មានប្រអប់វាយអត្ថបទនៅបាត ដែល **ឡើងលើពេល keyboard លេចឡើង**
៣. មាន header ដែលដាក់ពណ៌ពេញរហូតដល់ status bar ប៉ុន្តែអក្សរមិនត្រូវជាន់នាឡិកា
៤. បញ្ជីសារ scroll ទៅបាត ហើយសារចុងក្រោយមិនត្រូវលាក់ក្រោមប្រអប់វាយ

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `SafeArea` រុំអេក្រង់ទាំងមូល (វានឹងធ្វើឱ្យ header មានចន្លោះសនៅខាងលើ)
- ហាមប្រើលេខថេរដូចជា `SizedBox(height: 34)` សម្រាប់ home indicator

**✅ លក្ខខណ្ឌទទួលយក**

- សាកលើ iPhone ដែលមាន notch និង Android ដែលមាន gesture bar → គ្មានការជាន់គ្នា
- បើក keyboard → ប្រអប់វាយឡើងលើ, បញ្ជីរមូរតាម, សារចុងក្រោយនៅតែមើលឃើញ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
class ChatScreen extends StatelessWidget {
  const ChatScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      // លំនាំដើមជា true ហើយ — វាបង្រួម body ពេល keyboard ឡើង
      resizeToAvoidBottomInset: true,
      appBar: AppBar(title: const Text('សន្ទនា')),  // AppBar ដោះស្រាយ status bar ឱ្យស្រាប់
      body: Column(
        children: [
          Expanded(
            child: ListView.builder(
              reverse: true,          // សារថ្មីនៅបាត
              padding: AppSpacing.page,
              itemCount: 30,
              itemBuilder: (context, i) => _MessageBubble(index: i),
            ),
          ),
          // SafeArea តែជុំវិញផ្នែកបាត និងតែគែមខាងក្រោម
          SafeArea(
            top: false,
            child: _MessageComposer(),
          ),
        ],
      ),
    );
  }
}

class _MessageComposer extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      padding: AppSpacing.listItem,
      decoration: BoxDecoration(
        color: context.colors.surfaceContainer,
        border: Border(
          top: BorderSide(color: context.colors.outlineVariant),
        ),
      ),
      child: Row(
        crossAxisAlignment: CrossAxisAlignment.end,
        children: [
          Expanded(
            child: TextField(
              minLines: 1,
              maxLines: 5,          // រីកតាមអត្ថបទ ប៉ុន្តែមានដែនកំណត់
              textInputAction: TextInputAction.newline,
              decoration: const InputDecoration(
                hintText: 'សរសេរសារ...',
                border: InputBorder.none,
                isDense: true,
              ),
            ),
          ),
          const Gap.horizontal(AppSpacing.xs),
          IconButton.filled(
            onPressed: () {},
            icon: const Icon(Icons.send),
            tooltip: 'ផ្ញើសារ',
          ),
        ],
      ),
    );
  }
}
```

សម្រាប់ **FAB អណ្តែតលើបញ្ជី** ដែលមិនត្រូវជាន់ home indicator៖

```dart
// អានចន្លោះសុវត្ថិភាពដោយផ្ទាល់
final bottomSafe = MediaQuery.paddingOf(context).bottom;
final keyboardHeight = MediaQuery.viewInsetsOf(context).bottom;

Positioned(
  right: AppSpacing.md,
  // ឡើងលើពេល keyboard លេច និងគោរពចន្លោះសុវត្ថិភាព
  bottom: AppSpacing.md + bottomSafe + keyboardHeight,
  child: FloatingActionButton(onPressed: () {}, child: const Icon(Icons.add)),
)
```

**ភាពខុសគ្នារវាង `padding` និង `viewInsets` និង `viewPadding`៖**

| Property | អត្ថន័យ |
|---|---|
| `padding` | ចន្លោះដែលប្រព័ន្ធកាន់កាប់ (notch, home indicator) — **ក្លាយជា 0 ពេល keyboard គ្របលើវា** |
| `viewInsets` | ចន្លោះដែលត្រូវបាន "លេប" ទាំងស្រុង — ភាគច្រើនគឺ keyboard |
| `viewPadding` | ដូច `padding` ប៉ុន្តែ **មិនប្តូរពេល keyboard ឡើង** |

នេះជាមូលហេតុដែលរូបមន្ត `padding.bottom + viewInsets.bottom` ដំណើរការត្រឹមត្រូវ៖ ពេលគ្មាន keyboard អ្នកទទួលបាន home indicator; ពេលមាន keyboard `padding.bottom` ក្លាយជា 0 ហើយ `viewInsets.bottom` ក្លាយជាកម្ពស់ keyboard។ គ្មានការរាប់ទ្វេទេ។

**ការសាកល្បង:** បើក **Settings → Accessibility → Display Size** ធំបំផុត រួចសាកម្តងទៀត។ បញ្ហា layout ភាគច្រើនលេចឡើងត្រង់ចំណុចនេះ។

</details>

---

# ផ្នែកទី ៣ — Component ដែលអាចប្រើឡើងវិញ

Component ល្អមិនមែនជា component ដែលធ្វើបានច្រើនទេ — វាជា component ដែល **គេប្រើខុសមិនបាន**។ ការរចនា API ជាការរចនា UI។

---

### លំហាត់ទី ១១ — API របស់ component និង variants

**🎯 គោលដៅ**

រៀនរចនា API ដែលមិនអនុញ្ញាតឱ្យមានស្ថានភាពមិនត្រឹមត្រូវ (impossible states)។

**📋 តម្រូវការ**

សរសេរ `AppButton` ដែលមាន variants ៖ `primary`, `secondary`, `destructive`, `text`។ វាត្រូវទ្រទ្រង់៖
- ស្ថានភាព loading (បង្ហាញ spinner, បិទការចុច)
- Icon ខាងឆ្វេង ជាជម្រើស
- ទំហំពេញទទឹង ជាជម្រើស

**🚫 ច្បាប់តឹងរឹង**

- ហាមមាន parameter `Color? color` — variant ត្រូវសម្រេចពណ៌
- ហាមអនុញ្ញាតឱ្យ `isLoading: true` និង `onPressed` ដំណើរការក្នុងពេលតែមួយ
- ហាមប្រើ `bool isPrimary, bool isDestructive` (boolean ច្រើនអនុញ្ញាតឱ្យមានស្ថានភាពមិនសមហេតុផល)
- Constructor ត្រូវជា `const` បាន

**✅ លក្ខខណ្ឌទទួលយក**

- មិនអាចសរសេរ `AppButton` ដែលមានពណ៌ខុសពី design system បានទេ
- ពេល loading ការចុចមិនធ្វើអ្វីទេ ហើយទំហំប៊ូតុង **មិនប្តូរ** (មិនលោត)

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/widgets/app_button.dart
import 'package:material_ui/material_ui.dart';

enum AppButtonVariant { primary, secondary, destructive, text }

class AppButton extends StatelessWidget {
  const AppButton({
    super.key,
    required this.label,
    required this.onPressed,
    this.variant = AppButtonVariant.primary,
    this.icon,
    this.isLoading = false,
    this.fullWidth = false,
  });

  final String label;
  /// null = បិទ។ ក្នុងស្ថានភាព loading វាត្រូវបានមិនអើពើដោយស្វ័យប្រវត្តិ។
  final VoidCallback? onPressed;
  final AppButtonVariant variant;
  final IconData? icon;
  final bool isLoading;
  final bool fullWidth;

  @override
  Widget build(BuildContext context) {
    // ច្បាប់តែមួយកន្លែង: loading = បិទជានិច្ច
    final effectiveOnPressed = isLoading ? null : onPressed;

    final child = _ButtonContent(
      label: label,
      icon: icon,
      isLoading: isLoading,
    );

    final button = switch (variant) {
      AppButtonVariant.primary =>
        FilledButton(onPressed: effectiveOnPressed, child: child),
      AppButtonVariant.secondary =>
        OutlinedButton(onPressed: effectiveOnPressed, child: child),
      AppButtonVariant.destructive => FilledButton(
          onPressed: effectiveOnPressed,
          style: FilledButton.styleFrom(
            backgroundColor: context.colors.error,
            foregroundColor: context.colors.onError,
          ),
          child: child,
        ),
      AppButtonVariant.text =>
        TextButton(onPressed: effectiveOnPressed, child: child),
    };

    return fullWidth
        ? SizedBox(width: double.infinity, child: button)
        : button;
  }
}

class _ButtonContent extends StatelessWidget {
  const _ButtonContent({
    required this.label,
    required this.icon,
    required this.isLoading,
  });

  final String label;
  final IconData? icon;
  final bool isLoading;

  @override
  Widget build(BuildContext context) {
    // Stack រក្សាទំហំដើម → ប៊ូតុងមិនលោតពេលចូល loading
    return Stack(
      alignment: Alignment.center,
      children: [
        Opacity(
          opacity: isLoading ? 0 : 1,
          child: Row(
            mainAxisSize: MainAxisSize.min,
            children: [
              if (icon != null) ...[
                Icon(icon, size: 18),
                const Gap.horizontal(AppSpacing.xs),
              ],
              Text(label),
            ],
          ),
        ),
        if (isLoading)
          SizedBox(
            width: 18,
            height: 18,
            child: CircularProgressIndicator(
              strokeWidth: 2,
              // ទទួលពណ៌ត្រឹមត្រូវពី ButtonStyle ដែលកំពុងអនុវត្ត
              color: DefaultTextStyle.of(context).style.color,
            ),
          ),
      ],
    );
  }
}
```

**មូលហេតុហាមប្រើ boolean ច្រើន៖**

```dart
// អាក្រក់ — មាន 2³ = ៨ ស្ថានភាព ដែល ៥ មិនសមហេតុផល
AppButton(isPrimary: true, isDestructive: true, isText: true)  // អ្វី?

// ល្អ — មាន ៤ ស្ថានភាព ហើយទាំងអស់សុទ្ធតែត្រឹមត្រូវ
AppButton(variant: AppButtonVariant.destructive)
```

នេះជាគោលការណ៍ **"make illegal states unrepresentable"**។ Enum និង sealed class ជាឧបករណ៍ចម្បង។

**មូលហេតុ `Stack` ជំនួស ternary៖** បើអ្នកសរសេរ `isLoading ? CircularProgressIndicator() : Text(label)` ទំហំប៊ូតុងនឹងប្តូរភ្លាមៗ ធ្វើឱ្យធាតុជុំវិញលោត។ អ្នកប្រើប្រាស់ដែលកំពុងចង់ចុចអ្វីមួយផ្សេងអាចចុចខុស។ **UI មិនត្រូវផ្លាស់ទីក្រោមម្រាមដៃអ្នកប្រើប្រាស់ទេ។**

**ការធ្វើតេស្ត:**

```dart
testWidgets('ប៊ូតុងមិនហៅ onPressed ពេល loading', (tester) async {
  var pressed = false;
  await tester.pumpWidget(MaterialApp(
    home: Scaffold(
      body: AppButton(
        label: 'រក្សាទុក',
        isLoading: true,
        onPressed: () => pressed = true,
      ),
    ),
  ));
  await tester.tap(find.byType(AppButton));
  expect(pressed, isFalse);
});
```

</details>

---

### លំហាត់ទី ១២ — លំដាប់ព័ត៌មានក្នុង card

**🎯 គោលដៅ**

រៀនថាការរចនាល្អ = **ការសម្រេចថាអ្វីសំខាន់បំផុត** រួចធ្វើឱ្យវាលេចធ្លោបំផុត។ Card ដែលអ្វីៗគ្រប់យ៉ាងសំខាន់ស្មើគ្នា គឺជា card ដែលគ្មានអ្វីសំខាន់។

**📋 តម្រូវការ**

សរសេរ card សម្រាប់បញ្ជីកម្មង់អាហារ ដែលមានទិន្នន័យ៖ ឈ្មោះភោជនីយដ្ឋាន, រូបភាព, តម្លៃសរុប, ស្ថានភាព (កំពុងចម្អិន/កំពុងដឹក/បានដល់), ពេលវេលា, លេខកម្មង់, ចំនួនម្ហូប។

**រៀបចំវាតាមលំដាប់អាទិភាព៖**
- **កម្រិត ១** (មើលឃើញក្នុង ០.៥ វិនាទី) — ធាតុតែមួយ
- **កម្រិត ២** (មើលឃើញពេលមើលបន្តិច) — ធាតុ ២–៣
- **កម្រិត ៣** (ត្រូវអានទើបឃើញ) — នៅសល់

**🚫 ច្បាប់តឹងរឹង**

- ធាតុត្រឹមតែមួយប៉ុណ្ណោះអាចជាកម្រិត ១
- ហាមប្រើអក្សរដិត (bold) លើសពី ២ កន្លែង
- ហាមប្រើពណ៌លេចធ្លោលើសពី ១ កន្លែង
- Card ត្រូវអាចចុចបានទាំងមូល ជាមួយ ripple ត្រឹមត្រូវ

**✅ លក្ខខណ្ឌទទួលយក**

- បង្ហាញ card ដល់មិត្តភក្តិក្នុងរយៈពេល ១ វិនាទី រួចសួរថា "ឃើញអ្វី?" → ពួកគេត្រូវនិយាយពីធាតុកម្រិត ១
- បំបែក card ជា grayscale → លំដាប់នៅតែច្បាស់ (មិនពឹងលើពណ៌តែម្យ៉ាង)

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
class OrderCard extends StatelessWidget {
  const OrderCard({super.key, required this.order, required this.onTap});

  final Order order;
  final VoidCallback onTap;

  @override
  Widget build(BuildContext context) {
    return Card(
      clipBehavior: Clip.antiAlias,   // ធានាថា ripple នៅក្នុងជ្រុងកោង
      margin: EdgeInsets.zero,
      child: InkWell(
        onTap: onTap,
        child: Padding(
          padding: AppSpacing.card,
          child: Row(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              ClipRRect(
                borderRadius: BorderRadius.circular(context.tokens.radiusSm),
                child: Image.network(order.imageUrl,
                    width: 64, height: 64, fit: BoxFit.cover),
              ),
              const Gap.horizontal(AppSpacing.sm),
              Expanded(
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    // ━━━ កម្រិត ១ — ឈ្មោះភោជនីយដ្ឋាន ━━━
                    Text(
                      order.restaurantName,
                      style: context.text.titleMedium,   // តែមួយគត់ដែលធំ+ដិត
                      maxLines: 1,
                      overflow: TextOverflow.ellipsis,
                    ),
                    const Gap(AppSpacing.xxs),

                    // ━━━ កម្រិត ២ — ស្ថានភាព និងតម្លៃ ━━━
                    Row(
                      children: [
                        _StatusChip(status: order.status),  // ពណ៌លេចធ្លោតែមួយ
                        const Gap.horizontal(AppSpacing.xs),
                        Text('${order.total} ៛',
                            style: context.text.titleMedium),  // ដិតទីពីរ
                      ],
                    ),
                    const Gap(AppSpacing.xs),

                    // ━━━ កម្រិត ៣ — ព័ត៌មានលម្អិត ━━━
                    Text(
                      '#${order.id} · ម្ហូប ${order.itemCount} · ${order.timeAgo}',
                      style: context.text.labelSmall?.copyWith(
                        color: context.colors.onSurfaceVariant,  // ស្រអាប់
                      ),
                    ),
                  ],
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class _StatusChip extends StatelessWidget {
  const _StatusChip({required this.status});

  final OrderStatus status;

  @override
  Widget build(BuildContext context) {
    final (background, foreground, label) = switch (status) {
      OrderStatus.cooking => (
          context.tokens.warning,
          context.tokens.onWarning,
          'កំពុងចម្អិន'
        ),
      OrderStatus.delivering => (
          context.colors.primaryContainer,
          context.colors.onPrimaryContainer,
          'កំពុងដឹក'
        ),
      OrderStatus.delivered => (
          context.tokens.success,
          context.tokens.onSuccess,
          'បានដល់'
        ),
    };

    return Container(
      padding: const EdgeInsets.symmetric(
          horizontal: AppSpacing.xs, vertical: AppSpacing.xxs),
      decoration: BoxDecoration(
        color: background,
        borderRadius: BorderRadius.circular(context.tokens.radiusSm),
      ),
      child: Text(label,
          style: context.text.labelSmall?.copyWith(color: foreground)),
    );
  }
}
```

**ឧបករណ៍បង្កើតលំដាប់ (មិនមែនតែទំហំអក្សរទេ)៖**

| ឧបករណ៍ | កម្លាំង |
|---|---|
| ទំហំ | ខ្លាំងបំផុត |
| ទម្ងន់ (bold) | ខ្លាំង |
| ពណ៌ផ្ទុយគ្នា (contrast) | ខ្លាំង |
| ចន្លោះជុំវិញ (whitespace) | មធ្យម តែមើលមិនឃើញច្បាស់ |
| ទីតាំង (ខាងលើ/ឆ្វេង) | មធ្យម |
| ពណ៌ស្រអាប់ (`onSurfaceVariant`) | បន្ថយកម្រិត |

**ការធ្វើតេស្ត grayscale៖** ថតរូប card រួចបំបែកពណ៌ (Photoshop, ឬ Android Developer Options → Simulate color space → Monochromacy)។ បើលំដាប់បាត់ អ្នកកំពុងពឹងលើពណ៌ខ្លាំងពេក — ហើយអ្នកប្រើប្រាស់ដែលពិបាកមើលពណ៌ (ប្រុសប្រហែល ៨%) នឹងឃើញដូចនោះ។

</details>

---

### លំហាត់ទី ១៣ — ការរចនា form

**🎯 គោលដៅ**

Form គឺជាកន្លែងដែល UI អាក្រក់បង្កើតការខាតបង់ជាក់ស្តែង។ រៀនក្បួនផ្ទៀងផ្ទាត់ (validation) ដែលមិនធ្វើឱ្យអ្នកប្រើប្រាស់ខឹង។

**📋 តម្រូវការ**

សរសេរ form ចុះឈ្មោះ (ឈ្មោះ, អ៊ីមែល, លេខទូរស័ព្ទកម្ពុជា, ពាក្យសម្ងាត់, បញ្ជាក់ពាក្យសម្ងាត់) ដែល៖

១. ផ្ទៀងផ្ទាត់ **ពេលចាកចេញពី field (on blur)** មិនមែនពេលកំពុងវាយទេ
២. ប៊ូតុង "បន្ត" នៅតែចុចបាន សូម្បីតែ form មិនត្រឹមត្រូវ — ចុចវាបង្ហាញកំហុសទាំងអស់
៣. `TextInputAction.next` ភ្ជាប់គ្នាតាមលំដាប់ត្រឹមត្រូវ
៤. Autofill hints គ្រប់ field
៥. Keyboard type ត្រឹមត្រូវ (email, phone, ...)
៦. បង្ហាញ/លាក់ពាក្យសម្ងាត់
៧. សារកំហុសប្រាប់ **វិធីជួសជុល** មិនត្រឹមតែថាខុសទេ

**🚫 ច្បាប់តឹងរឹង**

- ហាមបិទ (disable) ប៊ូតុង submit
- ហាមបង្ហាញកំហុសពេលអ្នកប្រើប្រាស់វាយអក្សរទីមួយ
- ហាមប្រើសារកំហុសដូចជា "Invalid input" ឬ "ទិន្នន័យមិនត្រឹមត្រូវ"
- ហាមប្រើ `Form` ដោយគ្មាន `AutofillGroup`

**✅ លក្ខខណ្ឌទទួលយក**

- Password manager (Google, iCloud) ស្នើរក្សាទុកពាក្យសម្ងាត់បន្ទាប់ពី submit
- ចុច "next" លើ keyboard → ទៅ field បន្ទាប់តាមលំដាប់មើលឃើញ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
class SignUpForm extends StatefulWidget {
  const SignUpForm({super.key});

  @override
  State<SignUpForm> createState() => _SignUpFormState();
}

class _SignUpFormState extends State<SignUpForm> {
  final _formKey = GlobalKey<FormState>();
  final _passwordController = TextEditingController();
  bool _obscurePassword = true;

  // ចាប់ផ្តើមដោយ disabled → ប្តូរទៅ onUserInteraction ក្រោយពេលចុច submit
  AutovalidateMode _autovalidate = AutovalidateMode.disabled;

  @override
  void dispose() {
    _passwordController.dispose();
    super.dispose();
  }

  void _submit() {
    // ចុចហើយទើបបើកការផ្ទៀងផ្ទាត់ស្វ័យប្រវត្តិ
    setState(() => _autovalidate = AutovalidateMode.onUserInteraction);

    if (_formKey.currentState?.validate() ?? false) {
      TextInput.finishAutofillContext();   // ប្រាប់ password manager ឱ្យរក្សាទុក
      // ... បញ្ជូនទិន្នន័យ
    }
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      autovalidateMode: _autovalidate,
      child: AutofillGroup(
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            TextFormField(
              decoration: const InputDecoration(
                labelText: 'ឈ្មោះពេញ',
                hintText: 'ឧទាហរណ៍៖ ឈៀង មេងហៀក',
              ),
              textInputAction: TextInputAction.next,
              textCapitalization: TextCapitalization.words,
              autofillHints: const [AutofillHints.name],
              validator: (v) => (v == null || v.trim().isEmpty)
                  ? 'សូមបញ្ចូលឈ្មោះរបស់អ្នក'
                  : null,
            ),
            const Gap(AppSpacing.md),

            TextFormField(
              decoration: const InputDecoration(
                labelText: 'អ៊ីមែល',
                hintText: 'name@example.com',
              ),
              keyboardType: TextInputType.emailAddress,
              textInputAction: TextInputAction.next,
              autofillHints: const [AutofillHints.email],
              autocorrect: false,
              validator: (v) {
                if (v == null || v.isEmpty) return 'សូមបញ្ចូលអ៊ីមែល';
                if (!v.contains('@') || !v.contains('.')) {
                  // ប្រាប់ពីវិធីជួសជុល មិនត្រឹមតែថា "ខុស"
                  return 'អ៊ីមែលត្រូវមានសញ្ញា @ ដូចជា name@example.com';
                }
                return null;
              },
            ),
            const Gap(AppSpacing.md),

            TextFormField(
              decoration: const InputDecoration(
                labelText: 'លេខទូរស័ព្ទ',
                hintText: '០១២ ៣៤៥ ៦៧៨',
                prefixText: '+855 ',
              ),
              keyboardType: TextInputType.phone,
              textInputAction: TextInputAction.next,
              autofillHints: const [AutofillHints.telephoneNumber],
              validator: (v) {
                final digits = v?.replaceAll(RegExp(r'\D'), '') ?? '';
                if (digits.isEmpty) return 'សូមបញ្ចូលលេខទូរស័ព្ទ';
                if (digits.length < 8 || digits.length > 9) {
                  return 'លេខទូរស័ព្ទកម្ពុជាមាន ៨ ឬ ៩ ខ្ទង់';
                }
                return null;
              },
            ),
            const Gap(AppSpacing.md),

            TextFormField(
              controller: _passwordController,
              obscureText: _obscurePassword,
              decoration: InputDecoration(
                labelText: 'ពាក្យសម្ងាត់',
                helperText: 'យ៉ាងតិច ៨ តួអក្សរ',   // ប្រាប់ច្បាប់ជាមុន
                suffixIcon: IconButton(
                  onPressed: () =>
                      setState(() => _obscurePassword = !_obscurePassword),
                  icon: Icon(_obscurePassword
                      ? Icons.visibility_outlined
                      : Icons.visibility_off_outlined),
                  tooltip: _obscurePassword
                      ? 'បង្ហាញពាក្យសម្ងាត់'
                      : 'លាក់ពាក្យសម្ងាត់',
                ),
              ),
              textInputAction: TextInputAction.next,
              autofillHints: const [AutofillHints.newPassword],
              validator: (v) => (v == null || v.length < 8)
                  ? 'ពាក្យសម្ងាត់ត្រូវមានយ៉ាងតិច ៨ តួអក្សរ'
                  : null,
            ),
            const Gap(AppSpacing.md),

            TextFormField(
              obscureText: true,
              decoration: const InputDecoration(labelText: 'បញ្ជាក់ពាក្យសម្ងាត់'),
              textInputAction: TextInputAction.done,
              autofillHints: const [AutofillHints.newPassword],
              onFieldSubmitted: (_) => _submit(),
              validator: (v) => v != _passwordController.text
                  ? 'ពាក្យសម្ងាត់ទាំងពីរមិនដូចគ្នា'
                  : null,
            ),
            const Gap(AppSpacing.xl),

            AppButton(
              label: 'បង្កើតគណនី',
              onPressed: _submit,      // មិនដែល null
              fullWidth: true,
            ),
          ],
        ),
      ),
    );
  }
}
```

**មូលហេតុហាមបិទប៊ូតុង submit៖**

នេះជា pattern ដែលមើលទៅ "ការពារ" ប៉ុន្តែជាក់ស្តែងវាធ្វើទុក្ខអ្នកប្រើប្រាស់៖ ពួកគេឃើញប៊ូតុងប្រផេះ **ដោយមិនដឹងថាហេតុអ្វី**។ បើពួកគេចុចវាបានហើយឃើញកំហុសទាំង ៣ ភ្លាមៗ ពួកគេដឹងច្បាស់ថាត្រូវធ្វើអ្វី។

**ក្បួន `AutovalidateMode` ៖**
- `disabled` មុនចុច submit លើកទីមួយ — កុំស្តីបន្ទោសមុនពេលគេវាយចប់
- `onUserInteraction` ក្រោយចុច submit — ឥឡូវកំហុសបាត់ភ្លាមៗពេលគេកែ

**`TextInput.finishAutofillContext()`** ជាចំណុចដែលមនុស្សភាគច្រើនភ្លេច។ បើគ្មានវាទេ password manager មិនដឹងថា form ចប់ហើយ ដូច្នេះវាមិនស្នើរក្សាទុកទេ។

</details>

---

### លំហាត់ទី ១៤ — បញ្ជី និង pagination

**🎯 គោលដៅ**

បញ្ជីជាអេក្រង់ដែលអ្នកប្រើប្រាស់ចំណាយពេលច្រើនបំផុត។ រៀនធ្វើឱ្យវារលូន និងច្បាស់លាស់។

**📋 តម្រូវការ**

សរសេរបញ្ជីផលិតផលដែលមាន៖
១. `ListView.separated` ជាមួយបន្ទាត់ខណ្ឌចែក
២. Pull-to-refresh
៣. Infinite scroll — ផ្ទុកទំព័របន្ថែមមុនពេលដល់បាត
៤. Footer ដែលបង្ហាញ ៣ ស្ថានភាព៖ កំពុងផ្ទុក, កំហុស (ជាមួយប៊ូតុងព្យាយាមម្តងទៀត), ចប់
៥. រក្សា scroll position ពេលត្រឡប់មកពីអេក្រង់លម្អិត

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `ListView(children: [...])` សម្រាប់ទិន្នន័យថាមវន្ត
- ហាមផ្ទុកទំព័របន្ថែមក្នុង `itemBuilder` (វាហៅច្រើនដងមិនអាចទាយបាន)
- ហាមឱ្យបញ្ជីលោតពេលទំព័រថ្មីមកដល់

**✅ លក្ខខណ្ឌទទួលយក**

- Scroll យ៉ាងលឿនតាមធាតុ ៥០០ → គ្មាន jank (មើលក្នុង DevTools Performance overlay)
- ផ្តាច់អ៊ីនធឺណិត → scroll ដល់បាត → ឃើញកំហុស + ប៊ូតុងព្យាយាមម្តងទៀត

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
class ProductListScreen extends StatefulWidget {
  const ProductListScreen({super.key});

  @override
  State<ProductListScreen> createState() => _ProductListScreenState();
}

class _ProductListScreenState extends State<ProductListScreen> {
  final _scrollController = ScrollController();
  final _products = <Product>[];

  bool _isLoadingMore = false;
  bool _hasMore = true;
  Object? _loadMoreError;

  @override
  void initState() {
    super.initState();
    _scrollController.addListener(_onScroll);
    _loadMore();
  }

  @override
  void dispose() {
    _scrollController.removeListener(_onScroll);
    _scrollController.dispose();
    super.dispose();
  }

  void _onScroll() {
    // ផ្ទុកមុនដល់បាត ៥០០px → អ្នកប្រើប្រាស់មិនដែលឃើញ spinner
    final threshold = _scrollController.position.maxScrollExtent - 500;
    if (_scrollController.position.pixels >= threshold) {
      _loadMore();
    }
  }

  Future<void> _loadMore() async {
    if (_isLoadingMore || !_hasMore) return;
    setState(() {
      _isLoadingMore = true;
      _loadMoreError = null;
    });
    try {
      final page = await api.fetchProducts(offset: _products.length);
      setState(() {
        _products.addAll(page.items);
        _hasMore = page.hasMore;
      });
    } catch (e) {
      setState(() => _loadMoreError = e);
    } finally {
      setState(() => _isLoadingMore = false);
    }
  }

  Future<void> _refresh() async {
    final page = await api.fetchProducts(offset: 0);
    setState(() {
      _products
        ..clear()
        ..addAll(page.items);
      _hasMore = page.hasMore;
      _loadMoreError = null;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('ផលិតផល')),
      body: RefreshIndicator(
        onRefresh: _refresh,
        child: ListView.separated(
          controller: _scrollController,
          padding: AppSpacing.page,
          // +1 សម្រាប់ footer
          itemCount: _products.length + 1,
          separatorBuilder: (_, __) => const Divider(height: AppSpacing.lg),
          itemBuilder: (context, index) {
            if (index == _products.length) {
              return _ListFooter(
                isLoading: _isLoadingMore,
                error: _loadMoreError,
                hasMore: _hasMore,
                onRetry: _loadMore,
              );
            }
            return ProductTile(
              product: _products[index],
              // ValueKey ជួយ Flutter កំណត់អត្តសញ្ញាណធាតុពេលបញ្ជីប្តូរ
              key: ValueKey(_products[index].id),
            );
          },
        ),
      ),
    );
  }
}

class _ListFooter extends StatelessWidget {
  const _ListFooter({
    required this.isLoading,
    required this.error,
    required this.hasMore,
    required this.onRetry,
  });

  final bool isLoading;
  final Object? error;
  final bool hasMore;
  final VoidCallback onRetry;

  @override
  Widget build(BuildContext context) {
    if (error != null) {
      return Padding(
        padding: const EdgeInsets.symmetric(vertical: AppSpacing.lg),
        child: Column(
          children: [
            Text('មិនអាចផ្ទុកបន្ថែមបានទេ',
                style: context.text.bodyMedium),
            const Gap(AppSpacing.xs),
            AppButton(
              label: 'ព្យាយាមម្តងទៀត',
              variant: AppButtonVariant.text,
              onPressed: onRetry,
            ),
          ],
        ),
      );
    }
    if (isLoading) {
      return const Padding(
        padding: EdgeInsets.symmetric(vertical: AppSpacing.lg),
        child: Center(child: CircularProgressIndicator()),
      );
    }
    if (!hasMore) {
      return Padding(
        padding: const EdgeInsets.symmetric(vertical: AppSpacing.lg),
        child: Center(
          child: Text('អស់ហើយ',
              style: context.text.labelSmall
                  ?.copyWith(color: context.colors.onSurfaceVariant)),
        ),
      );
    }
    return const SizedBox.shrink();
  }
}
```

**មូលហេតុហាមផ្ទុកក្នុង `itemBuilder`៖**

```dart
// អាក្រក់ — itemBuilder ត្រូវបានហៅឡើងវិញពេល scroll ថយក្រោយ, ពេល rebuild,
// ពេលវាស់ទំហំ... អ្នកនឹងផ្ញើសំណើដដែលៗ ១០ ដង។
itemBuilder: (context, index) {
  if (index == products.length - 1) loadMore();   // ❌
  return ProductTile(...);
}
```

`ScrollController` listener ត្រូវបានហៅតែពេល scroll ពិតប្រាកដ ហើយ `_isLoadingMore` guard ការពារការហៅជាន់គ្នា។

**មូលហេតុ threshold 500px៖** បើអ្នករង់ចាំរហូតដល់បាតទើបផ្ទុក អ្នកប្រើប្រាស់នឹងឃើញ spinner ជានិច្ច។ ការផ្ទុកមុន ៥០០px មានន័យថាទិន្នន័យមកដល់មុនពេលពួកគេ scroll ដល់ — **ការផ្ទុកដ៏ល្អបំផុតគឺការផ្ទុកដែលមើលមិនឃើញ។**

</details>

---

# ផ្នែកទី ៤ — ស្ថានភាព UI និង feedback

Developer ភាគច្រើនរចនាតែ "ស្ថានភាពសប្បាយ" (happy path)។ អ្នកប្រើប្រាស់ចំណាយពេលច្រើនក្នុងស្ថានភាពផ្សេងទៀត។

---

### លំហាត់ទី ១៥ — ស្ថានភាពទាំង ៥ របស់អេក្រង់

**🎯 គោលដៅ**

រៀនថាអេក្រង់មួយមិនមែនមានតែ "មានទិន្នន័យ" ទេ។ រៀនប្រើ sealed class ដើម្បីធានាថាអ្នកមិនភ្លេចស្ថានភាពណាមួយ។

**📋 តម្រូវការ**

សរសេរអេក្រង់ស្វែងរកដែលដោះស្រាយស្ថានភាពទាំង ៥៖

| ស្ថានភាព | នៅពេលណា | ត្រូវបង្ហាញអ្វី |
|---|---|---|
| `initial` | មុនស្វែងរក | ការណែនាំ ឬពាក្យពេញនិយម |
| `loading` | កំពុងស្វែងរក | skeleton (មិនមែន spinner) |
| `empty` | រកមិនឃើញ | មូលហេតុ + សកម្មភាពបន្ទាប់ |
| `error` | បរាជ័យ | អ្វីខុស + ប៊ូតុងព្យាយាមម្តងទៀត |
| `success` | មានលទ្ធផល | បញ្ជី |

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `bool isLoading; String? error; List? data;` (វាអនុញ្ញាតឱ្យមានស្ថានភាព "loading + error + data" ដែលមិនសមហេតុផល)
- ត្រូវប្រើ `switch` expression ដែល exhaustive — គ្មាន `default` ទេ
- អេក្រង់ទទេហាមបង្ហាញត្រឹមតែពាក្យ "គ្មានទិន្នន័យ"

**✅ លក្ខខណ្ឌទទួលយក**

- បន្ថែម state ថ្មីទៅ sealed class → compiler បង្ហាញកំហុសគ្រប់កន្លែងដែលត្រូវកែ
- គ្រប់ស្ថានភាពមានប៊ូតុងសកម្មភាព (អ្នកប្រើប្រាស់មិនដែលជាប់គាំង)

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/features/search/search_state.dart

/// ស្ថានភាពទាំង ៥ — មិនអាចមានពីរក្នុងពេលតែមួយបានទេ។
sealed class SearchState {
  const SearchState();
}

final class SearchInitial extends SearchState {
  const SearchInitial({required this.suggestions});
  final List<String> suggestions;
}

final class SearchLoading extends SearchState {
  const SearchLoading();
}

final class SearchEmpty extends SearchState {
  const SearchEmpty({required this.query});
  final String query;
}

final class SearchError extends SearchState {
  const SearchError({required this.message, required this.isRetryable});
  final String message;
  final bool isRetryable;
}

final class SearchSuccess extends SearchState {
  const SearchSuccess({required this.results});
  final List<Product> results;
}
```

```dart
// lib/features/search/search_screen.dart
class SearchBody extends StatelessWidget {
  const SearchBody({
    super.key,
    required this.state,
    required this.onRetry,
    required this.onSearch,
    required this.onClearFilters,
  });

  final SearchState state;
  final VoidCallback onRetry;
  final ValueChanged<String> onSearch;
  final VoidCallback onClearFilters;

  @override
  Widget build(BuildContext context) {
    // គ្មាន default → បើបន្ថែម state ថ្មី compiler នឹងបរាជ័យត្រង់នេះ
    return switch (state) {
      SearchInitial(:final suggestions) => _SuggestionsView(
          suggestions: suggestions,
          onTap: onSearch,
        ),

      SearchLoading() => const _SearchSkeleton(),

      SearchEmpty(:final query) => EmptyStateView(
          icon: Icons.search_off_outlined,
          title: 'រកមិនឃើញលទ្ធផលសម្រាប់ "$query"',
          // ប្រាប់ពីវិធីដោះស្រាយ មិនត្រឹមតែរាយការណ៍
          message: 'សាកល្បងពាក្យខ្លីជាង ឬលុបតម្រងចេញ។',
          action: AppButton(
            label: 'លុបតម្រងទាំងអស់',
            variant: AppButtonVariant.secondary,
            onPressed: onClearFilters,
          ),
        ),

      SearchError(:final message, :final isRetryable) => EmptyStateView(
          icon: Icons.cloud_off_outlined,
          title: 'មិនអាចស្វែងរកបានទេ',
          message: message,
          action: isRetryable
              ? AppButton(label: 'ព្យាយាមម្តងទៀត', onPressed: onRetry)
              : null,
        ),

      SearchSuccess(:final results) => ProductList(products: results),
    };
  }
}
```

```dart
/// Widget តែមួយសម្រាប់ស្ថានភាពទទេ និងកំហុស — រក្សាភាពស៊ីគ្នា
class EmptyStateView extends StatelessWidget {
  const EmptyStateView({
    super.key,
    required this.icon,
    required this.title,
    required this.message,
    this.action,
  });

  final IconData icon;
  final String title;
  final String message;
  final Widget? action;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Padding(
        padding: const EdgeInsets.all(AppSpacing.xl),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Icon(icon, size: 48, color: context.colors.onSurfaceVariant),
            const Gap(AppSpacing.md),
            Text(title,
                textAlign: TextAlign.center, style: context.text.titleMedium),
            const Gap(AppSpacing.xs),
            Text(
              message,
              textAlign: TextAlign.center,
              style: context.text.bodyMedium
                  ?.copyWith(color: context.colors.onSurfaceVariant),
            ),
            if (action != null) ...[
              const Gap(AppSpacing.lg),
              action!,
            ],
          ],
        ),
      ),
    );
  }
}
```

**មូលហេតុ sealed class ល្អជាង boolean៖**

```dart
// អាក្រក់ — តើនេះមានន័យយ៉ាងណា?
bool isLoading = true;
String? error = 'Network failed';
List<Product>? data = [product1, product2];
```

ជាមួយ `sealed class` រដ្ឋមិនត្រឹមត្រូវ **មិនអាចសរសេរបាន**។ ហើយ Dart 3 pattern matching (`SearchEmpty(:final query)`) បំបែកទិន្នន័យចេញដោយសុវត្ថិភាព។

**មូលហេតុហាមប្រើ "គ្មានទិន្នន័យ"៖** អេក្រង់ទទេជាឱកាសរចនា មិនមែនជាការរាយការណ៍កំហុសទេ។ វាត្រូវឆ្លើយសំណួរ ៣៖ *មានអ្វីកើតឡើង?* *ហេតុអ្វី?* *ខ្ញុំត្រូវធ្វើអ្វីបន្ទាប់?*

</details>

---

### លំហាត់ទី ១៦ — Skeleton loading

**🎯 គោលដៅ**

រៀនថា spinner ធ្វើឱ្យការរង់ចាំមានអារម្មណ៍យូរជាង skeleton ព្រោះ skeleton បង្ហាញ **រូបរាងនៃអ្វីដែលនឹងមកដល់** ជាមុន។

**📋 តម្រូវការ**

១. សរសេរ `Shimmer` widget ដោយប្រើ `AnimationController` + `ShaderMask` (មិនប្រើ package ខាងក្រៅ)
២. សរសេរ skeleton សម្រាប់ product card ដែលមានរូបរាង **ដូចគ្នាបេះបិទ** នឹង card ពិត
៣. រក្សា layout មិនឱ្យលោតពេលទិន្នន័យពិតមកជំនួស

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ package `shimmer` ឬស្រដៀង (លំហាត់នេះសម្រាប់រៀន)
- Skeleton ត្រូវមានកម្ពស់ដូច card ពិត ± 2px
- ត្រូវគោរព `MediaQuery.disableAnimationsOf(context)`

**✅ លក្ខខណ្ឌទទួលយក**

- ថតវីដេអូការផ្លាស់ប្តូរពី skeleton ទៅទិន្នន័យ → គ្មានការលោត
- បើក "Remove animations" ក្នុង accessibility settings → shimmer ឈប់ តែ skeleton នៅ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/widgets/shimmer.dart
import 'package:material_ui/material_ui.dart';

class Shimmer extends StatefulWidget {
  const Shimmer({super.key, required this.child});

  final Widget child;

  @override
  State<Shimmer> createState() => _ShimmerState();
}

class _ShimmerState extends State<Shimmer> with SingleTickerProviderStateMixin {
  late final AnimationController _controller = AnimationController(
    vsync: this,
    duration: const Duration(milliseconds: 1400),
  );

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    // គោរពការកំណត់ accessibility
    if (MediaQuery.disableAnimationsOf(context)) {
      _controller.stop();
    } else if (!_controller.isAnimating) {
      _controller.repeat();
    }
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    final base = context.colors.surfaceContainerHighest;
    final highlight = context.colors.surfaceContainerLow;

    return AnimatedBuilder(
      animation: _controller,
      // child ត្រូវបានសាងសង់តែម្តង — មិន rebuild រាល់ frame
      child: widget.child,
      builder: (context, child) {
        return ShaderMask(
          blendMode: BlendMode.srcATop,
          shaderCallback: (bounds) {
            final t = _controller.value;
            return LinearGradient(
              begin: Alignment.centerLeft,
              end: Alignment.centerRight,
              colors: [base, highlight, base],
              // ផ្លាស់ទីពី -1 ទៅ 2 ដើម្បីឱ្យពន្លឺឆ្លងកាត់ទាំងស្រុង
              stops: [
                (t * 3 - 1).clamp(0.0, 1.0),
                (t * 3 - 0.5).clamp(0.0, 1.0),
                (t * 3).clamp(0.0, 1.0),
              ],
            ).createShader(bounds);
          },
          child: child,
        );
      },
    );
  }
}

/// រូបរាងមូលដ្ឋានសម្រាប់ skeleton
class SkeletonBox extends StatelessWidget {
  const SkeletonBox({
    super.key,
    this.width,
    required this.height,
    this.radius = 8,
  });

  final double? width;
  final double height;
  final double radius;

  @override
  Widget build(BuildContext context) {
    return Container(
      width: width,
      height: height,
      decoration: BoxDecoration(
        color: context.colors.surfaceContainerHighest,
        borderRadius: BorderRadius.circular(radius),
      ),
    );
  }
}
```

```dart
/// Skeleton ត្រូវឆ្លុះបញ្ចាំង ProductCard ពិតៗ
class ProductCardSkeleton extends StatelessWidget {
  const ProductCardSkeleton({super.key});

  @override
  Widget build(BuildContext context) {
    return Shimmer(
      child: Padding(
        padding: AppSpacing.card,
        child: Row(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            const SkeletonBox(width: 64, height: 64),   // ដូចរូបភាពពិត
            const Gap.horizontal(AppSpacing.sm),
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  const SkeletonBox(height: 16, width: 160),  // ចំណងជើង
                  const Gap(AppSpacing.xs),
                  const SkeletonBox(height: 14, width: 90),   // តម្លៃ
                  const Gap(AppSpacing.xs),
                  const SkeletonBox(height: 12),              // ពិពណ៌នា
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

**គន្លឹះសម្រាប់កុំឱ្យលោត៖** បើកឯកសារ `ProductCard` និង `ProductCardSkeleton` ចំហៀងគ្នា។ គ្រប់ `Gap`, `Padding`, និងកម្ពស់ត្រូវតែដូចគ្នា។ វិធីល្អបំផុតគឺបំបែក layout ចេញ៖

```dart
/// Layout តែមួយ — ខ្លឹមសារពីរ
class ProductCardLayout extends StatelessWidget {
  const ProductCardLayout({
    super.key,
    required this.leading,
    required this.title,
    required this.subtitle,
  });

  final Widget leading;
  final Widget title;
  final Widget subtitle;
  // ... build() តែមួយ ប្រើដោយទាំង card ពិត និង skeleton
}
```

**ភស្តុតាង:** ថតវីដេអូអេក្រង់ ៦០fps រួចមើលម្តងមួយ frame។ បើអក្សរផ្លាស់ទីសូម្បីតែ 1px អ្នកនឹងឃើញ។

</details>

---

### លំហាត់ទី ១៧ — SnackBar ទល់នឹង Dialog ទល់នឹង inline

**🎯 គោលដៅ**

រៀនជ្រើសរើសកម្រិតនៃការរំខាន (interruption level) ឱ្យត្រូវនឹងសារៈសំខាន់នៃសារ។

**📋 តម្រូវការ**

សរសេរអេក្រង់គ្រប់គ្រងឯកសារដែលមានសកម្មភាព ៦ ហើយជ្រើស feedback ត្រឹមត្រូវសម្រាប់នីមួយៗ៖

| សកម្មភាព | Feedback អ្វី? |
|---|---|
| រក្សាទុកជោគជ័យ | ? |
| លុបឯកសារ (បញ្ច្រាស់បាន) | ? |
| លុបគណនី (បញ្ច្រាស់មិនបាន) | ? |
| បណ្តាញដាច់ពេលរក្សាទុក | ? |
| ឈ្មោះឯកសារខុសទម្រង់ | ? |
| ការផ្ទុកឯកសារធំកំពុងដំណើរការ | ? |

សរសេរកូដសម្រាប់ទាំង ៦ ជាមួយហេតុផលក្នុង comment។

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ dialog សម្រាប់សារជោគជ័យ
- ហាមប្រើ SnackBar សម្រាប់សកម្មភាពដែលបញ្ច្រាស់មិនបាន
- Dialog បំផ្លិចបំផ្លាញត្រូវទាមទារការបញ្ជាក់ដែលមិនអាចចុចខុសបាន

**✅ លក្ខខណ្ឌទទួលយក**

- ចុច "លុប" ១០ ដងលឿនៗ → SnackBar មិនត្រួតគ្នា
- Dialog លុបគណនីមិនអាចបិទដោយចុចក្រៅបានទេ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// ១. រក្សាទុកជោគជ័យ → SnackBar ខ្លី
//    មូលហេតុ: អ្នកប្រើប្រាស់ដឹងស្រាប់ថាគេចុចរក្សាទុក។ កុំបង្ខំគេចុច "OK"។
void _onSaved(BuildContext context) {
  ScaffoldMessenger.of(context)
    ..hideCurrentSnackBar()          // ការពារការត្រួតគ្នា
    ..showSnackBar(const SnackBar(
      content: Text('បានរក្សាទុក'),
      duration: Duration(seconds: 2),
    ));
}

// ២. លុបឯកសារ (បញ្ច្រាស់បាន) → SnackBar ជាមួយ "មិនធ្វើវិញ"
//    មូលហេតុ: undo ល្អជាងការសួរបញ្ជាក់។ វាលឿនជាង ហើយសុវត្ថិភាពដូចគ្នា។
void _onDeleteFile(BuildContext context, File file) {
  _repository.delete(file);
  ScaffoldMessenger.of(context)
    ..hideCurrentSnackBar()
    ..showSnackBar(SnackBar(
      content: Text('បានលុប ${file.name}'),
      duration: const Duration(seconds: 6),   // វែងជាង ដើម្បីឱ្យទាន់ undo
      action: SnackBarAction(
        label: 'មិនធ្វើវិញ',
        onPressed: () => _repository.restore(file),
      ),
    ));
}

// ៣. លុបគណនី (បញ្ច្រាស់មិនបាន) → Dialog ជាមួយការវាយបញ្ជាក់
//    មូលហេតុ: undo មិនអាចធ្វើបាន ដូច្នេះការបញ្ជាក់ត្រូវតែពិបាកចុចខុស។
Future<void> _onDeleteAccount(BuildContext context) async {
  final confirmed = await showDialog<bool>(
    context: context,
    barrierDismissible: false,        // បិទដោយចុចក្រៅមិនបាន
    builder: (context) => const _DeleteAccountDialog(),
  );
  if (confirmed ?? false) { /* ... */ }
}

class _DeleteAccountDialog extends StatefulWidget {
  const _DeleteAccountDialog();
  @override
  State<_DeleteAccountDialog> createState() => _DeleteAccountDialogState();
}

class _DeleteAccountDialogState extends State<_DeleteAccountDialog> {
  final _controller = TextEditingController();
  static const _phrase = 'លុបគណនី';

  @override
  void dispose() { _controller.dispose(); super.dispose(); }

  @override
  Widget build(BuildContext context) {
    final canDelete = _controller.text.trim() == _phrase;
    return AlertDialog(
      icon: Icon(Icons.warning_amber_rounded, color: context.colors.error),
      title: const Text('លុបគណនីជាអចិន្ត្រៃយ៍?'),
      content: Column(
        mainAxisSize: MainAxisSize.min,
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          // ប្រាប់ផលវិបាកជាក់លាក់ មិនត្រឹមតែថា "ប្រាកដទេ?"
          const Text('ឯកសារទាំង ១២៤ និងប្រវត្តិទាំងអស់នឹងត្រូវលុប។ '
              'សកម្មភាពនេះមិនអាចត្រឡប់វិញបានទេ។'),
          const Gap(AppSpacing.md),
          TextField(
            controller: _controller,
            onChanged: (_) => setState(() {}),
            decoration: const InputDecoration(
              labelText: 'វាយ "$_phrase" ដើម្បីបញ្ជាក់',
            ),
          ),
        ],
      ),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context, false),
          child: const Text('បោះបង់'),
        ),
        AppButton(
          label: 'លុបគណនី',
          variant: AppButtonVariant.destructive,
          onPressed: canDelete ? () => Navigator.pop(context, true) : null,
        ),
      ],
    );
  }
}

// ៤. បណ្តាញដាច់ពេលរក្សាទុក → Banner ជាប់ (persistent) ជាមួយប៊ូតុងព្យាយាម
//    មូលហេតុ: បញ្ហានៅតែមាន។ SnackBar នឹងបាត់ ហើយអ្នកប្រើប្រាស់នឹងបាត់ការងារ។
void _onSaveFailed(BuildContext context) {
  ScaffoldMessenger.of(context).showMaterialBanner(
    MaterialBanner(
      leading: Icon(Icons.cloud_off, color: context.colors.onErrorContainer),
      backgroundColor: context.colors.errorContainer,
      content: const Text('ការផ្លាស់ប្តូរមិនទាន់រក្សាទុកទេ — គ្មានការតភ្ជាប់'),
      actions: [
        TextButton(onPressed: _retry, child: const Text('ព្យាយាមម្តងទៀត')),
      ],
    ),
  );
}

// ៥. ឈ្មោះឯកសារខុសទម្រង់ → កំហុស inline នៅជាប់ field
//    មូលហេតុ: កំហុសត្រូវនៅជិតកន្លែងកែ។ SnackBar នឹងបាត់មុនពេលគេអានចប់។
TextFormField(
  decoration: InputDecoration(
    labelText: 'ឈ្មោះឯកសារ',
    errorText: _nameError,   // ← inline
  ),
)

// ៦. ការផ្ទុកកំពុងដំណើរការ → progress inline ក្នុងជួរបញ្ជី
//    មូលហេតុ: វាជាស្ថានភាពមិនមែនព្រឹត្តិការណ៍។ វាត្រូវមើលឃើញរហូតដល់ចប់។
ListTile(
  leading: const Icon(Icons.insert_drive_file_outlined),
  title: Text(file.name),
  subtitle: LinearProgressIndicator(value: file.uploadProgress),
  trailing: IconButton(
    onPressed: _cancelUpload,
    icon: const Icon(Icons.close),
    tooltip: 'បោះបង់ការផ្ទុក',
  ),
)
```

**តារាងសម្រេចចិត្ត៖**

| សំណួរ | ចម្លើយ → ប្រើ |
|---|---|
| តើវាបញ្ច្រាស់បានទេ? | បាន → SnackBar + Undo |
| តើវាបញ្ច្រាស់មិនបានទេ? | មិនបាន → Dialog + បញ្ជាក់ |
| តើវាទាក់ទងនឹង field ជាក់លាក់? | បាទ → inline `errorText` |
| តើវាជាស្ថានភាពបន្ត? | បាទ → Banner ឬ inline progress |
| តើអ្នកប្រើប្រាស់ស្នើសុំវាមែនទេ? | មែន → feedback ស្រាល ឬគ្មានសោះ |

**គោលការណ៍មាស:** *កម្រិតនៃការរំខានត្រូវសមាមាត្រនឹងផលវិបាកនៃការមិនអាន។*

</details>

---

### លំហាត់ទី ១៨ — ការសរសេរអក្សរក្នុង UI (UX writing)

**🎯 គោលដៅ**

អក្សរជាផ្នែកនៃការរចនា។ អេក្រង់ស្អាតដែលមានពាក្យអន់ គឺជាអេក្រង់អន់។

**📋 តម្រូវការ**

កែពាក្យខាងក្រោមទាំងអស់ ហើយសរសេរហេតុផលសម្រាប់នីមួយៗ៖

| ដើម | បញ្ហា |
|---|---|
| "Error: null" | ? |
| "Submit" | ? |
| "សូមអភ័យទោស! មានអ្វីមួយខុសប្រក្រតី 😢" | ? |
| "គ្មានទិន្នន័យ" | ? |
| "Are you sure?" | ? |
| "ដំណើរការ..." | ? |
| "Invalid email" | ? |

**🚫 ច្បាប់តឹងរឹង**

- ប៊ូតុងត្រូវប្រើកិរិយាសព្ទដែលប្រាប់ថាមានអ្វីកើតឡើង
- សារកំហុសហាមសុំទោស
- ហាមប្រើវាក្យស័ព្ទបច្ចេកទេស (null, timeout, 500, exception)
- ពាក្យលើប៊ូតុងត្រូវត្រូវគ្នាជាមួយចំណងជើងអេក្រង់បន្ទាប់

**✅ លក្ខខណ្ឌទទួលយក**

- បង្ហាញអេក្រង់ដល់មនុស្សដែលមិនចេះកុំព្យូទ័រ → គាត់ដឹងថាត្រូវធ្វើអ្វី

<details>
<summary>💡 ដំណោះស្រាយ</summary>

| ដើម | កែជា | ហេតុផល |
|---|---|---|
| `Error: null` | `មិនអាចផ្ទុកកម្មង់បានទេ។ ពិនិត្យការតភ្ជាប់រួចព្យាយាមម្តងទៀត។` | អ្នកប្រើប្រាស់មិនដឹងថា `null` ជាអ្វី។ ប្រាប់ថាអ្វីបរាជ័យ និងត្រូវធ្វើអ្វី។ |
| `Submit` | `បង្កើតគណនី` | ប៊ូតុងត្រូវប្រាប់ថា **មានអ្វីកើតឡើង** ក្រោយចុច។ "Submit" ជាពាក្យរបស់ប្រព័ន្ធ មិនមែនរបស់មនុស្ស។ |
| `សូមអភ័យទោស! មានអ្វីមួយខុសប្រក្រតី 😢` | `មិនអាចរក្សាទុកបានទេ។ ការផ្លាស់ប្តូររបស់អ្នកនៅតែមាននៅទីនេះ។` | ការសុំទោសមិនជួយអ្វីទេ។ វាចំណាយកន្លែងដែលគួរប្រាប់ព័ត៌មានមានប្រយោជន៍។ ហើយ emoji ធ្វើឱ្យបញ្ហាធ្ងន់ធ្ងរមើលទៅមិនធ្ងន់ធ្ងរ។ |
| `គ្មានទិន្នន័យ` | `អ្នកមិនទាន់មានកម្មង់នៅឡើយ។ រកមើលភោជនីយដ្ឋានជិតអ្នក។` + ប៊ូតុង | អេក្រង់ទទេជាការអញ្ជើញឱ្យធ្វើសកម្មភាព។ |
| `Are you sure?` | `លុបរូបភាព ១២ សន្លឹក?` | ត្រូវប្រាប់ថាកំពុងបញ្ជាក់អ្វី។ អ្នកប្រើប្រាស់អាចភ្លេចថាចុចអ្វី។ |
| `ដំណើរការ...` | `កំពុងផ្ទុករូបភាព ៣ ក្នុងចំណោម ១២` | ភាពជាក់លាក់បន្ថយអារម្មណ៍រង់ចាំ។ |
| `Invalid email` | `អ៊ីមែលត្រូវមានសញ្ញា @ ដូចជា name@example.com` | ប្រាប់ក្បួន និងឧទាហរណ៍ មិនត្រឹមតែថា "ខុស"។ |

**ក្បួន ៥ សម្រាប់ការសរសេរ UI ខ្មែរ៖**

១. **ប្រើកិរិយាសព្ទសកម្ម** — "រក្សាទុកការផ្លាស់ប្តូរ" មិនមែន "ការផ្លាស់ប្តូរនឹងត្រូវរក្សាទុក"
២. **រក្សាភាពស៊ីគ្នា** — បើប៊ូតុងសរសេរ "បោះផ្សាយ" សារជោគជ័យត្រូវសរសេរ "បានបោះផ្សាយ" មិនមែន "រក្សាទុករួចរាល់"
៣. **កុំបកប្រែពាក្យបច្ចេកទេសពាក្យក្នុងពាក្យ** — "Cache cleared" ជា "សម្អាតទិន្នន័យបណ្តោះអាសន្នរួច" មិនមែន "ឃ្លាំងសម្ងាត់បានសម្អាត"
៤. **វាល្អជាងបើប្រើពាក្យដែលមនុស្សនិយាយ** — "លុប" មិនមែន "លុបបំបាត់"
៥. **ធាតុនីមួយៗធ្វើការងារតែមួយ** — label ប្រាប់ថាជាអ្វី, hint ផ្តល់ឧទាហរណ៍, helper ប្រាប់ក្បួន, error ប្រាប់វិធីជួសជុល។ កុំដាក់ទាំងអស់ក្នុងកន្លែងតែមួយ។

</details>

---

# ផ្នែកទី ៥ — ចលនា (Motion)

ចលនាមិនមែនសម្រាប់ធ្វើឱ្យស្អាតទេ។ វាសម្រាប់ **ពន្យល់ថាមានអ្វីផ្លាស់ប្តូរ**។ ចលនាដែលមិនពន្យល់អ្វី គឺជាការពន្យាពេល។

---

### លំហាត់ទី ១៩ — Motion tokens និង reduced motion

**🎯 គោលដៅ**

រៀនប្រើ duration និង curve ជាបទដ្ឋាន ហើយរៀនគោរពអ្នកប្រើប្រាស់ដែលបិទ animation។

**📋 តម្រូវការ**

១. បន្ថែម motion tokens ទៅ `AppTokens` (duration ៣ កម្រិត, curve ៣ ប្រភេទ)
២. សរសេរ list item ដែលពង្រីក/បង្រួម ដោយប្រើ `AnimatedSize`
៣. សរសេរប៊ូតុងចូលចិត្ត (like) ដែលមាន micro-interaction
៤. សរសេរការប្តូររវាង loading និងខ្លឹមសារដោយ `AnimatedSwitcher`
៥. **ទាំងអស់ត្រូវគោរព `MediaQuery.disableAnimationsOf(context)`**

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `Duration(milliseconds: ...)` ផ្ទាល់ក្នុង widget
- ហាមប្រើ animation លើសពី ៥០០ms សម្រាប់ការឆ្លើយតបលើការចុច
- ហាមប្រើ `Curves.bounceOut` ឬ `elasticOut` លើ UI ធម្មតា

**✅ លក្ខខណ្ឌទទួលយក**

- បើក Settings → Accessibility → Remove animations → UI ប្តូរភ្លាមៗដោយគ្មាន animation តែនៅតែដំណើរការត្រឹមត្រូវ
- គ្រប់ animation ចប់ក្នុងរយៈពេលក្រោម ៣០០ms សម្រាប់ការឆ្លើយតបផ្ទាល់

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// បន្ថែមទៅ AppTokens
class AppTokens extends ThemeExtension<AppTokens> {
  // ...
  final Duration motionFast;      // 150ms — micro-interaction (ripple, checkbox)
  final Duration motionNormal;    // 250ms — ការប្តូរធាតុ (expand, switch)
  final Duration motionSlow;      // 400ms — ការប្តូរអេក្រង់

  // Curves តាមស្តង់ដារ Material 3
  Curve get curveStandard => Curves.easeInOutCubicEmphasized;  // ចេញ+ចូល
  Curve get curveDecelerate => Curves.easeOutCubic;            // ធាតុចូល
  Curve get curveAccelerate => Curves.easeInCubic;             // ធាតុចេញ
}

/// Helper ដែលធ្វើឱ្យ duration ក្លាយជាសូន្យពេលអ្នកប្រើប្រាស់បិទ animation
extension MotionX on BuildContext {
  Duration motion(Duration d) =>
      MediaQuery.disableAnimationsOf(this) ? Duration.zero : d;
}
```

```dart
// ២. List item ដែលពង្រីកបាន
class ExpandableTile extends StatefulWidget {
  const ExpandableTile({super.key, required this.title, required this.details});
  final String title;
  final String details;

  @override
  State<ExpandableTile> createState() => _ExpandableTileState();
}

class _ExpandableTileState extends State<ExpandableTile> {
  bool _expanded = false;

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: () => setState(() => _expanded = !_expanded),
      child: Padding(
        padding: AppSpacing.card,
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              children: [
                Expanded(child: Text(widget.title, style: context.text.titleMedium)),
                // ព្រួញបង្វិល — ប្រាប់ទិសដៅនៃសកម្មភាព
                AnimatedRotation(
                  turns: _expanded ? 0.5 : 0,
                  duration: context.motion(context.tokens.motionNormal),
                  curve: context.tokens.curveStandard,
                  child: const Icon(Icons.expand_more),
                ),
              ],
            ),
            AnimatedSize(
              duration: context.motion(context.tokens.motionNormal),
              curve: context.tokens.curveStandard,
              alignment: Alignment.topCenter,
              child: _expanded
                  ? Padding(
                      padding: const EdgeInsets.only(top: AppSpacing.xs),
                      child: Text(widget.details, style: context.text.bodyMedium),
                    )
                  : const SizedBox(width: double.infinity),
            ),
          ],
        ),
      ),
    );
  }
}
```

```dart
// ៣. ប៊ូតុងចូលចិត្ត — micro-interaction
class LikeButton extends StatefulWidget {
  const LikeButton({super.key, required this.isLiked, required this.onChanged});
  final bool isLiked;
  final ValueChanged<bool> onChanged;

  @override
  State<LikeButton> createState() => _LikeButtonState();
}

class _LikeButtonState extends State<LikeButton>
    with SingleTickerProviderStateMixin {
  late final _controller = AnimationController(
    vsync: this,
    duration: const Duration(milliseconds: 200),
  );

  late final _scale = TweenSequence<double>([
    TweenSequenceItem(tween: Tween(begin: 1.0, end: 1.3), weight: 40),
    TweenSequenceItem(tween: Tween(begin: 1.3, end: 1.0), weight: 60),
  ]).animate(CurvedAnimation(parent: _controller, curve: Curves.easeOut));

  @override
  void dispose() { _controller.dispose(); super.dispose(); }

  void _handleTap() {
    widget.onChanged(!widget.isLiked);
    if (!widget.isLiked && !MediaQuery.disableAnimationsOf(context)) {
      _controller.forward(from: 0);
    }
    HapticFeedback.lightImpact();   // ការឆ្លើយតបផ្លូវកាយ
  }

  @override
  Widget build(BuildContext context) {
    return IconButton(
      onPressed: _handleTap,
      tooltip: widget.isLiked ? 'ដកការចូលចិត្ត' : 'ចូលចិត្ត',
      icon: ScaleTransition(
        scale: _scale,
        child: Icon(
          widget.isLiked ? Icons.favorite : Icons.favorite_border,
          color: widget.isLiked ? context.colors.error : null,
        ),
      ),
    );
  }
}
```

```dart
// ៤. ការប្តូររវាង loading និងខ្លឹមសារ
AnimatedSwitcher(
  duration: context.motion(context.tokens.motionNormal),
  // fade + slide តូច = អារម្មណ៍ថាខ្លឹមសារ "មកដល់" មិនមែន "លោតចេញ"
  transitionBuilder: (child, animation) => FadeTransition(
    opacity: animation,
    child: SlideTransition(
      position: Tween(
        begin: const Offset(0, 0.03),
        end: Offset.zero,
      ).animate(animation),
      child: child,
    ),
  ),
  child: isLoading
      // Key ចាំបាច់ — បើគ្មានវាទេ AnimatedSwitcher មិនដឹងថាខ្លឹមសារប្តូរ
      ? const ProductCardSkeleton(key: ValueKey('skeleton'))
      : ProductCard(key: ValueKey(product.id), product: product),
)
```

**មូលហេតុហាមប្រើ `bounceOut`៖** ចលនាលោតធ្វើឱ្យ UI មើលទៅដូចជាល្បែង។ វាក៏ពន្យាពេលការឆ្លើយតបផងដែរ — អ្នកប្រើប្រាស់ត្រូវរង់ចាំរហូតដល់ការលោតចប់ទើបដឹងថាការចុចបានដំណើរការ។ Material 3 ប្រើ `easeInOutCubicEmphasized` ដែលចេញលឿន ចូលយឺត — មើលទៅ**ឆាប់រហ័ស** តែនៅតែរលូន។

**មូលហេតុគោរព reduced motion៖** អ្នកប្រើប្រាស់ខ្លះមានបញ្ហា vestibular ដែលធ្វើឱ្យចលនាបង្កឱ្យវិលមុខ ឬចង់ក្អួត។ សម្រាប់ពួកគេនេះមិនមែនជាចំណូលចិត្តទេ — វាជាសុខភាព។

</details>

---

### លំហាត់ទី ២០ — Hero និង page transitions

**🎯 គោលដៅ**

រៀនប្រើ shared element transition ដើម្បីរក្សាបរិបទ (context) នៅពេលអ្នកប្រើប្រាស់ផ្លាស់ទីរវាងអេក្រង់។

**📋 តម្រូវការ**

១. Grid ផលិតផល → អេក្រង់លម្អិត ជាមួយ `Hero` លើរូបភាព
២. ជួសជុលបញ្ហា Hero ធម្មតា៖ រូបភាពខូចទ្រង់ទ្រាយពេលកំពុងហោះ
៣. សរសេរ page transition ផ្ទាល់ខ្លួន (shared axis) សម្រាប់ការប្តូរ tab
៤. ធានាថាការចុចថយក្រោយ (back) មាន transition ត្រឡប់វិញត្រឹមត្រូវ

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `Hero` លើ widget ដែលមានអត្ថបទ (វានឹងភ្លឹបភ្លែត)
- ត្រូវប្រើ tag តែមួយគត់ក្នុងអេក្រង់នីមួយៗ
- Transition ត្រូវអាចបញ្ច្រាស់បាន

**✅ លក្ខខណ្ឌទទួលយក**

- ថតវីដេអូ slow motion → រូបភាពមិនលាតឬបង្រួមខុសទ្រង់ទ្រាយ
- អូសពីគែម (iOS back gesture) → Hero ដើរតាមម្រាមដៃ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// Grid — រូបភាពរុំដោយ Hero
class ProductGridTile extends StatelessWidget {
  const ProductGridTile({super.key, required this.product});
  final Product product;

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: () => Navigator.push(
        context,
        MaterialPageRoute(builder: (_) => ProductDetailScreen(product: product)),
      ),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Hero(
            // tag ត្រូវតែដូចគ្នាបេះបិទក្នុងអេក្រង់ទាំងពីរ
            tag: 'product-image-${product.id}',
            // flightShuttleBuilder ជួសជុលការខូចទ្រង់ទ្រាយពេលហោះ
            flightShuttleBuilder: (_, animation, __, ___, ____) {
              return AspectRatio(
                aspectRatio: 1,
                child: Image.network(product.imageUrl, fit: BoxFit.cover),
              );
            },
            child: AspectRatio(
              aspectRatio: 1,
              child: ClipRRect(
                borderRadius: BorderRadius.circular(context.tokens.radiusMd),
                child: Image.network(product.imageUrl, fit: BoxFit.cover),
              ),
            ),
          ),
          const Gap(AppSpacing.xs),
          // អត្ថបទ *មិន* ស្ថិតក្នុង Hero — វានឹងភ្លឹបភ្លែតពេលទំហំប្តូរ
          Text(product.name, maxLines: 1, overflow: TextOverflow.ellipsis),
        ],
      ),
    );
  }
}
```

```dart
// Shared axis transition សម្រាប់ការប្តូរ tab
class SharedAxisPageRoute<T> extends PageRouteBuilder<T> {
  SharedAxisPageRoute({required this.child, this.reverse = false})
      : super(
          transitionDuration: const Duration(milliseconds: 300),
          reverseTransitionDuration: const Duration(milliseconds: 300),
          pageBuilder: (_, __, ___) => child,
        );

  final Widget child;
  final bool reverse;

  @override
  Widget buildTransitions(
    BuildContext context,
    Animation<double> animation,
    Animation<double> secondaryAnimation,
    Widget child,
  ) {
    if (MediaQuery.disableAnimationsOf(context)) return child;

    final direction = reverse ? -1.0 : 1.0;
    return SlideTransition(
      position: Tween(
        begin: Offset(0.15 * direction, 0),
        end: Offset.zero,
      ).animate(CurvedAnimation(
        parent: animation,
        curve: Curves.easeOutCubic,
        reverseCurve: Curves.easeInCubic,
      )),
      child: FadeTransition(opacity: animation, child: child),
    );
  }
}
```

**បញ្ហា Hero ធម្មតា ៣ និងដំណោះស្រាយ៖**

| បញ្ហា | មូលហេតុ | ដំណោះស្រាយ |
|---|---|---|
| រូបភាពលាតខុសទ្រង់ទ្រាយពេលហោះ | ទំហំដើម និងទំហំចុងមាន aspect ratio ខុសគ្នា | `flightShuttleBuilder` ដែលរក្សា ratio ថេរ |
| អក្សរភ្លឹបភ្លែត | Text rebuild រាល់ frame ជាមួយ constraint ខុសៗគ្នា | កុំដាក់ Text ក្នុង Hero |
| `There are multiple heroes with tag X` | Tag ដដែលលេចឡើងពីរដងក្នុងអេក្រង់ | ប្រើ ID ពិត (`'product-${product.id}'`) មិនមែន index |

**ការធ្វើតេស្ត:** បើក **Settings → Developer options → Animator duration scale → 10x** លើ Android។ ចលនានឹងយឺតខ្លាំង ហើយបញ្ហាទាំងអស់នឹងលេចឡើងច្បាស់។ នេះជាឧបករណ៍ដ៏ល្អបំផុតសម្រាប់បំបាត់កំហុស animation។

</details>

---

# ផ្នែកទី ៦ — Accessibility និងការផ្ទៀងផ្ទាត់

Accessibility មិនមែនជាមុខងារបន្ថែមទេ។ វាជាការធានាថាការរចនារបស់អ្នកដំណើរការសម្រាប់មនុស្សពិត — រួមទាំងអ្នកដែលដៃញ័រ, ភ្នែកស្រវាំង, ឬកំពុងកាន់កូនក្នុងដៃម្ខាង។

---

### លំហាត់ទី ២១ — Tap target, contrast និង Semantics

**🎯 គោលដៅ**

រៀនក្បួនវាស់វែងបី ដែលដោះស្រាយបញ្ហា accessibility ភាគច្រើន។

**📋 តម្រូវការ**

សរសេរ toolbar ដែលមានប៊ូតុង icon ៦ រួច៖

១. ធានាថាគ្រប់គោលដៅចុចមានទំហំយ៉ាងតិច **48×48 dp** ទោះបីជា icon តូចជាងក៏ដោយ
២. ធានាថាអត្ថបទទាំងអស់មាន contrast ratio យ៉ាងតិច **4.5:1** (3:1 សម្រាប់អក្សរធំ)
៣. បន្ថែម `Semantics` label ដល់គ្រប់ធាតុដែលមានតែ icon
៤. សរសេរ widget test ដែលពិនិត្យ accessibility ដោយស្វ័យប្រវត្តិ

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `GestureDetector` លើ icon តូចដោយគ្មានការពង្រីកគោលដៅ
- ហាមប្រើ `Semantics` លើអ្វីដែលមាន label ស្រាប់ (វានឹងអានពីរដង)
- ហាមប្រើពណ៌ជាមធ្យោបាយតែមួយគត់ក្នុងការបញ្ជូនព័ត៌មាន

**✅ លក្ខខណ្ឌទទួលយក**

- Widget test ជាមួយ `meetsGuideline(androidTapTargetGuideline)` ជាប់
- Widget test ជាមួយ `meetsGuideline(textContrastGuideline)` ជាប់
- បើក TalkBack/VoiceOver → គ្រប់ប៊ូតុងអានបានច្បាស់

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
class AccessibleToolbar extends StatelessWidget {
  const AccessibleToolbar({super.key});

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        // IconButton មាន tap target 48x48 ជាស្រាប់ — ប្រើវាជំនួស GestureDetector
        IconButton(
          onPressed: () {},
          icon: const Icon(Icons.bookmark_border),
          tooltip: 'រក្សាទុក',      // tooltip ក្លាយជា semantic label ស្វ័យប្រវត្តិ
        ),
        IconButton(
          onPressed: () {},
          icon: const Icon(Icons.share_outlined),
          tooltip: 'ចែករំលែក',
        ),
        const Spacer(),
        // សម្រាប់ធាតុផ្ទាល់ខ្លួន — បង្ខំទំហំអប្បបរមា
        _CustomTapTarget(
          onTap: () {},
          semanticLabel: 'បន្ថែមទៅកន្ត្រក',
          child: const Icon(Icons.add_shopping_cart, size: 20),
        ),
      ],
    );
  }
}

class _CustomTapTarget extends StatelessWidget {
  const _CustomTapTarget({
    required this.onTap,
    required this.semanticLabel,
    required this.child,
  });

  final VoidCallback onTap;
  final String semanticLabel;
  final Widget child;

  @override
  Widget build(BuildContext context) {
    return Semantics(
      label: semanticLabel,
      button: true,
      child: InkWell(
        onTap: onTap,
        customBorder: const CircleBorder(),
        child: ConstrainedBox(
          // ក្បួន Material: 48dp ជាទំហំអប្បបរមា
          constraints: const BoxConstraints(minWidth: 48, minHeight: 48),
          child: Center(child: child),
        ),
      ),
    );
  }
}
```

**ការធ្វើតេស្តដោយស្វ័យប្រវត្តិ៖**

```dart
testWidgets('toolbar គោរពក្បួន accessibility', (tester) async {
  final handle = tester.ensureSemantics();

  await tester.pumpWidget(
    MaterialApp(
      theme: AppTheme.light(),
      home: const Scaffold(body: AccessibleToolbar()),
    ),
  );

  // ក្បួនទាំង ៤ ដែល Flutter ពិនិត្យបាន
  await expectLater(tester, meetsGuideline(androidTapTargetGuideline));
  await expectLater(tester, meetsGuideline(iOSTapTargetGuideline));
  await expectLater(tester, meetsGuideline(textContrastGuideline));
  await expectLater(tester, meetsGuideline(labeledTapTargetGuideline));

  handle.dispose();
});
```

**ក្បួន contrast (WCAG AA)៖**

| ប្រភេទអត្ថបទ | Ratio អប្បបរមា |
|---|---|
| អត្ថបទធម្មតា (< 18pt) | 4.5:1 |
| អត្ថបទធំ (≥ 18pt ឬ ≥ 14pt ដិត) | 3:1 |
| ធាតុ UI (ព្រំដែន, icon) | 3:1 |

**ដំណឹងល្អ:** បើអ្នកបានធ្វើលំហាត់ទី ២ ត្រឹមត្រូវ `ColorScheme.fromSeed` រក្សា contrast ទាំងនេះឱ្យអ្នកស្រាប់សម្រាប់គូ `primary`/`onPrimary` ។ល។ បញ្ហាកើតឡើងតែពេលអ្នកលាយពណ៌ដោយខ្លួនឯង។

**ក្បួន "កុំប្រើពណ៌តែម្យ៉ាង"៖**

```dart
// អាក្រក់ — អ្នកមិនមើលឃើញពណ៌នឹងមិនដឹងអ្វីទាំងអស់
Text('សកម្ម', style: TextStyle(color: Colors.green))

// ល្អ — ពណ៌ + icon + អត្ថបទ
Row(children: [
  Icon(Icons.check_circle, size: 16, color: context.tokens.success),
  const Gap.horizontal(AppSpacing.xxs),
  Text('សកម្ម'),
])
```

</details>

---

### លំហាត់ទី ២២ — ការទប់ទល់នឹង text scaling

**🎯 គោលដៅ**

អ្នកប្រើប្រាស់ជាច្រើន (ជាពិសេសមនុស្សវ័យចំណាស់) កំណត់ទំហំអក្សរប្រព័ន្ធធំជាងធម្មតា ២០០%។ UI ភាគច្រើនបែកបាក់ត្រង់នេះ។

**📋 តម្រូវការ**

១. យកអេក្រង់ណាមួយពីលំហាត់មុន រួចសាកលើ text scale 0.85, 1.0, 1.5, និង 2.0
២. ជួសជុលបញ្ហាទាំងអស់ដោយ **មិនកំណត់ដែនកំណត់ scale ទាំងស្រុង**
៣. សម្រាប់កន្លែងដែលចាំបាច់ ដាក់ដែនកំណត់ដោយប្រុងប្រយ័ត្ន (ឧ. អតិបរមា 1.5 លើ badge តូច)
៤. ប្តូរ layout ពី Row ទៅ Column ដោយស្វ័យប្រវត្តិនៅពេលអក្សរធំពេក

**🚫 ច្បាប់តឹងរឹង**

- ហាមប្រើ `MediaQuery(data: ...copyWith(textScaler: TextScaler.noScaling))` លើ app ទាំងមូល
- ហាមប្រើកម្ពស់ថេរលើ container ដែលមានអត្ថបទ
- ហាមកាត់អក្សរដោយ `TextOverflow.clip`

**✅ លក្ខខណ្ឌទទួលយក**

- នៅ scale 2.0 អត្ថបទទាំងអស់នៅតែអានបាន និងចុចបាន
- គ្មាន overflow នៅ scale ណាមួយ

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// ឧបករណ៍សាកល្បង — រុំអេក្រង់ក្នុងវានៅពេលអភិវឌ្ឍ
class TextScalePreview extends StatelessWidget {
  const TextScalePreview({super.key, required this.child, required this.scale});
  final Widget child;
  final double scale;

  @override
  Widget build(BuildContext context) {
    return MediaQuery(
      data: MediaQuery.of(context).copyWith(
        textScaler: TextScaler.linear(scale),
      ),
      child: child,
    );
  }
}
```

**បញ្ហាទី ១ — Container កម្ពស់ថេរ**

```dart
// អាក្រក់ — អក្សរនឹងលើសនៅ scale 1.5
Container(height: 56, child: Center(child: Text(label)))

// ល្អ — កម្ពស់អប្បបរមា តែរីកបាន
ConstrainedBox(
  constraints: const BoxConstraints(minHeight: 56),
  child: Padding(
    padding: AppSpacing.listItem,
    child: Text(label),
  ),
)
```

**បញ្ហាទី ២ — Row ដែលមានធាតុពីរដែលមានអត្ថបទ**

```dart
/// ប្តូរពី Row ទៅ Column ដោយស្វ័យប្រវត្តិពេលអក្សរធំពេក។
class ResponsiveRow extends StatelessWidget {
  const ResponsiveRow({super.key, required this.children});
  final List<Widget> children;

  @override
  Widget build(BuildContext context) {
    // អានទំហំអក្សរជាក់ស្តែងបន្ទាប់ពី scaling
    final scaledSize = MediaQuery.textScalerOf(context).scale(14);
    final needsStacking = scaledSize > 20;   // ≈ scale 1.4

    return needsStacking
        ? Column(crossAxisAlignment: CrossAxisAlignment.start, children: children)
        : Row(children: children);
  }
}
```

**បញ្ហាទី ៣ — កន្លែងដែលចាំបាច់ត្រូវដាក់ដែនកំណត់**

```dart
// Badge លេខតូច — ការពង្រីក ២០០% នឹងគ្របលើ UI ទាំងមូល
// ដាក់ដែនកំណត់ *តែត្រង់នេះ* មិនមែនលើ app ទាំងមូល
MediaQuery(
  data: MediaQuery.of(context).copyWith(
    textScaler: MediaQuery.textScalerOf(context).clamp(
      minScaleFactor: 1.0,
      maxScaleFactor: 1.3,
    ),
  ),
  child: Badge(label: Text('$count')),
)
```

**បញ្ហាទី ៤ — Icon មិនរីកតាមអក្សរ**

```dart
// នៅ scale 2.0 អក្សរធំទ្វេ តែ icon នៅដដែល → មើលទៅមិនស៊ីគ្នា
Icon(
  Icons.star,
  // រីកតាមអក្សរ ប៉ុន្តែមានដែនកំណត់
  size: MediaQuery.textScalerOf(context).clamp(maxScaleFactor: 1.6).scale(20),
)
```

**មូលហេតុហាមបិទ scaling ទាំងស្រុង៖**

`TextScaler.noScaling` ធ្វើឱ្យ app របស់អ្នកមិនអាចប្រើបានសម្រាប់អ្នកដែលភ្នែកមិនល្អ។ វាដូចជាការសាងសង់អគារដោយគ្មានផ្លូវរទេះរុញ។ ការជួសជុល layout ពិបាកជាង តែវាជាការងារត្រឹមត្រូវ។

**កំណត់ចំណាំពី `TextScaler`៖** នៅក្នុង Flutter ថ្មី `textScaleFactor` (double) ត្រូវបាន deprecate ជំនួសដោយ `TextScaler` (class)។ មូលហេតុ៖ Android 14+ ប្រើ **non-linear scaling** — អក្សរតូចរីកច្រើន អក្សរធំរីកតិច។ `TextScaler` អាចតំណាងវាបាន ចំណែក double មិនអាចទេ។

</details>

---

### លំហាត់ទី ២៣ — Widget Previews និង golden tests

**🎯 គោលដៅ**

រៀនផ្ទៀងផ្ទាត់ការរចនាដោយស្វ័យប្រវត្តិ ដើម្បីកុំឱ្យវាបែកបាក់ដោយចៃដន្យ។

**📋 តម្រូវការ**

១. បន្ថែម `@Preview` ដល់ component ទាំងអស់ដែលអ្នកបានសរសេរ
២. សរសេរ `MultiPreview` ផ្ទាល់ខ្លួនដែលបង្កើត preview ជាច្រើនក្នុងពេលតែមួយ៖ light/dark × text scale 1.0/2.0
៣. សរសេរ golden test សម្រាប់ `AppButton` គ្រប់ variant
៤. រត់ `flutter test --update-goldens` រួច commit រូបភាព

**🚫 ច្បាប់តឹងរឹង**

- Preview មិនត្រូវទាមទារ network ឬ `dart:io`
- Golden test ត្រូវប្រើ font ជាក់លាក់ (មិនមែន font លំនាំដើមរបស់ test environment)

**✅ លក្ខខណ្ឌទទួលយក**

- `flutter widget-preview start` បង្ហាញ component ទាំងអស់
- ប្តូរ padding មួយកន្លែង → golden test បរាជ័យ ហើយបង្ហាញភាពខុសគ្នា

<details>
<summary>💡 ដំណោះស្រាយ</summary>

```dart
// lib/widgets/app_button.dart (ក្រោមកូដ component)
import 'package:flutter/widget_previews.dart';

@Preview(name: 'ប៊ូតុងចម្បង', group: 'Buttons')
Widget primaryButtonPreview() => AppButton(
      label: 'បញ្ជាទិញ',
      onPressed: () {},
    );

@Preview(name: 'ប៊ូតុងកំពុងផ្ទុក', group: 'Buttons')
Widget loadingButtonPreview() => AppButton(
      label: 'បញ្ជាទិញ',
      isLoading: true,
      onPressed: () {},
    );

@Preview(name: 'ប៊ូតុងបំផ្លាញ', group: 'Buttons')
Widget destructiveButtonPreview() => AppButton(
      label: 'លុបគណនី',
      variant: AppButtonVariant.destructive,
      onPressed: () {},
    );
```

**Preview មួយដែលបង្ហាញគ្រប់ variant ក្នុងពេលតែមួយ៖**

```dart
@Preview(name: 'Variants ទាំងអស់', group: 'Buttons', size: Size(360, 400))
Widget allButtonVariantsPreview() => Padding(
      padding: AppSpacing.page,
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          for (final variant in AppButtonVariant.values) ...[
            AppButton(
              label: variant.name,
              variant: variant,
              onPressed: () {},
              fullWidth: true,
            ),
            const Gap(AppSpacing.xs),
          ],
        ],
      ),
    );
```

**MultiPreview ផ្ទាល់ខ្លួន — matrix នៃ light/dark៖**

```dart
// lib/design/previews.dart
import 'package:flutter/widget_previews.dart';

/// បង្កើត preview ពីរ (ភ្លឺ + ងងឹត) ដោយប្រើ theme របស់ app ពិត។
final class AppPreview extends MultiPreview {
  const AppPreview({required this.name});

  final String name;

  @override
  List<Preview> get previews => const [
        Preview(brightness: Brightness.light, theme: _appTheme),
        Preview(brightness: Brightness.dark, theme: _appTheme),
      ];

  @override
  List<Preview> transform() {
    return super.transform().map((preview) {
      final builder = preview.toBuilder()
        ..group = 'Components'
        ..name = '$name — ${preview.brightness!.name}';
      return builder.toPreview();
    }).toList();
  }
}

/// Callback ត្រូវតែជា top-level និង const-accessible
PreviewThemeData _appTheme() => PreviewThemeData(
      materialLight: AppTheme.light(),
      materialDark: AppTheme.dark(),
    );
```

ការប្រើ៖

```dart
@AppPreview(name: 'Card កម្មង់')
Widget orderCardPreview() => OrderCard(
      order: Order.sample(),   // ទិន្នន័យសាកល្បង — មិនត្រូវហៅ network
      onTap: () {},
    );
```

**Golden test៖**

```dart
// test/golden/app_button_golden_test.dart
void main() {
  testWidgets('AppButton variants', (tester) async {
    await tester.pumpWidget(
      MaterialApp(
        theme: AppTheme.light(),
        home: Scaffold(
          body: Center(
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                for (final v in AppButtonVariant.values)
                  Padding(
                    padding: const EdgeInsets.all(AppSpacing.xs),
                    child: AppButton(
                      label: v.name,
                      variant: v,
                      onPressed: () {},
                    ),
                  ),
              ],
            ),
          ),
        ),
      ),
    );

    await expectLater(
      find.byType(Column),
      matchesGoldenFile('goldens/app_button_variants.png'),
    );
  });
}
```

Golden test ត្រូវការ font ពិត បើមិនដូច្នេះទេវានឹងបង្ហាញតែប្រអប់ទទេ៖

```dart
// test/flutter_test_config.dart
import 'dart:async';
import 'package:flutter_test/flutter_test.dart';

Future<void> testExecutable(FutureOr<void> Function() testMain) async {
  // ផ្ទុក font ពិតសម្រាប់ golden tests
  await loadAppFonts();
  return testMain();
}

Future<void> loadAppFonts() async {
  TestWidgetsFlutterBinding.ensureInitialized();
  final fontLoader = FontLoader('KantumruyPro')
    ..addFont(rootBundle.load('assets/fonts/KantumruyPro-Regular.ttf'))
    ..addFont(rootBundle.load('assets/fonts/KantumruyPro-Bold.ttf'));
  await fontLoader.load();
}
```

```bash
flutter test --update-goldens   # បង្កើតរូបភាពមូលដ្ឋាន
git add test/golden/goldens/    # commit វា
flutter test                    # ការរត់ក្រោយៗត្រូវប្រៀបធៀបនឹងវា
```

**ដែនកំណត់នៃ Widget Previewer ដែលត្រូវដឹង៖**
- Previewer សាងសង់ដោយ Flutter Web ដូច្នេះ `dart:io` និង `dart:ffi` មិនដំណើរការទេ
- Asset ត្រូវប្រើផ្លូវបែប package (`packages/my_package/assets/x.png`)
- Callback ក្នុង annotation ត្រូវជា public និង const
- Widget ដែលគ្មាន constraint នឹងត្រូវបានកំណត់ទំហំដោយស្វ័យប្រវត្តិ — ប្រើ `size:` ជំនួស

**ហេតុអ្វី preview និង golden ជាគូល្អ៖** Preview ជួយអ្នក **ក្នុងពេលកំពុងសរសេរ** (loop លឿន)។ Golden test ការពារអ្នក **ក្រោយពេលសរសេររួច** (regression)។ ទាំងពីរប្រើ widget ដដែល ដូច្នេះការសរសេរ preview មួយធ្វើឱ្យការសរសេរ golden test ងាយស្រួល។

</details>

---

### លំហាត់ទី ២៤ — Capstone: កែទម្រង់អេក្រង់អាក្រក់

**🎯 គោលដៅ**

អនុវត្តគ្រប់លំហាត់ទាំង ២៣ លើកូដតែមួយ។

**📋 កូដដែលត្រូវកែ**

```dart
// ⚠️ កូដនេះមានបញ្ហារចនាយ៉ាងតិច ២៥។ រកឱ្យឃើញទាំងអស់។
import 'package:flutter/material.dart';

class SettingsScreen extends StatefulWidget {
  @override
  _SettingsScreenState createState() => _SettingsScreenState();
}

class _SettingsScreenState extends State<SettingsScreen> {
  bool notifications = true;
  bool darkMode = false;
  String name = "";

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Color(0xFFF5F5F5),
      appBar: AppBar(
        backgroundColor: Colors.blue,
        title: Text("Settings", style: TextStyle(color: Colors.white, fontSize: 22)),
      ),
      body: Container(
        padding: EdgeInsets.all(15),
        child: Column(
          children: [
            Container(
              height: 60,
              color: Colors.white,
              padding: EdgeInsets.all(10),
              child: Row(
                children: [
                  Text("ឈ្មោះអ្នកប្រើប្រាស់", style: TextStyle(fontSize: 17)),
                  SizedBox(width: 30),
                  TextField(
                    onChanged: (v) => setState(() => name = v),
                    decoration: InputDecoration(hintText: "Enter name"),
                  ),
                ],
              ),
            ),
            SizedBox(height: 13),
            Container(
              height: 55,
              color: Colors.white,
              child: Row(
                children: [
                  SizedBox(width: 10),
                  Text("ការជូនដំណឹង", style: TextStyle(fontSize: 17)),
                  Spacer(),
                  Switch(
                    value: notifications,
                    onChanged: (v) => setState(() => notifications = v),
                  ),
                ],
              ),
            ),
            SizedBox(height: 13),
            Container(
              height: 55,
              color: Colors.white,
              child: Row(
                children: [
                  SizedBox(width: 10),
                  Text("Dark Mode", style: TextStyle(fontSize: 17)),
                  Spacer(),
                  Switch(
                    value: darkMode,
                    onChanged: (v) => setState(() => darkMode = v),
                  ),
                ],
              ),
            ),
            SizedBox(height: 40),
            GestureDetector(
              onTap: () {
                ScaffoldMessenger.of(context).showSnackBar(
                  SnackBar(content: Text("Saved!")),
                );
              },
              child: Container(
                width: 200,
                height: 45,
                color: Colors.blue,
                child: Center(
                  child: Text("Save", style: TextStyle(color: Colors.white)),
                ),
              ),
            ),
            SizedBox(height: 20),
            GestureDetector(
              onTap: () {},
              child: Icon(Icons.delete, color: Colors.red, size: 20),
            ),
          ],
        ),
      ),
    );
  }
}
```

**📋 តម្រូវការ**

១. រាយបញ្ជីបញ្ហាទាំងអស់ដែលអ្នករកឃើញ **មុនពេលកែ** (សរសេរវាចុះ)
២. កែវាដោយអនុវត្តគ្រប់គោលការណ៍ពីលំហាត់ ១–២៣
៣. បន្ថែម `@Preview` និង golden test
៤. រត់ accessibility test

**✅ លក្ខខណ្ឌទទួលយក**

គ្រប់ចំណុចក្នុងបញ្ជីត្រួតពិនិត្យខាងក្រោមត្រូវជាប់។

<details>
<summary>💡 បញ្ជីបញ្ហា (មើលក្រោយពេលអ្នករាយរួច)</summary>

**ប្រព័ន្ធរចនា**
1. `Color(0xFFF5F5F5)` — hardcoded background ជំនួស `colorScheme.surface`
2. `Colors.blue` លើ AppBar — មិនតាម theme
3. `Colors.white` លើ card — បែកបាក់ក្នុង dark mode
4. `Colors.red` លើ icon លុប — គួរប្រើ `colorScheme.error`
5. `fontSize: 22`, `17` — hardcoded ជំនួស `textTheme`
6. `EdgeInsets.all(15)`, `SizedBox(height: 13)` — លេខមិនស្ថិតលើ scale
7. គ្មាន `height` លើ TextStyle — អក្សរខ្មែរនឹងជាន់គ្នា
8. `darkMode` switch មិនធ្វើអ្វីទាំងអស់ — មុខងារក្លែងក្លាយ

**រចនាសម្ព័ន្ធ**
9. `build()` វែងជាង ៨០ ជួរ ដោយគ្មាន component
10. គ្មាន `const` សូម្បីតែមួយ
11. `_SettingsScreenState` ជា private class name ដែលចាប់ផ្តើមដោយ underscore តែ `SettingsScreen` គ្មាន `super.key`
12. `createState` គ្មាន return type ច្បាស់លាស់តាមស្ទីលថ្មី

**ប្លង់**
13. `TextField` ក្នុង `Row` គ្មាន `Expanded` → overflow
14. `Container(height: 60)` ថេរ → បែកបាក់នៅ text scale 1.5
15. `SizedBox(width: 30)` រវាង label និង field → មិនបត់បែន
16. `SizedBox(width: 10)` សម្រាប់ padding → គួរប្រើ `Padding`
17. គ្មាន `SafeArea` ឬ scroll → បញ្ហានៅលើអេក្រង់តូច

**Component**
18. `GestureDetector` + `Container` ជំនួសប៊ូតុង → គ្មាន ripple, គ្មាន focus, គ្មាន disabled state
19. Icon លុបទំហំ 20 ក្នុង `GestureDetector` → tap target តូចជាង 48dp
20. គ្មាន `tooltip` ឬ `Semantics` លើ icon លុប → screen reader មិនអាន
21. `Switch` គ្មាន label ភ្ជាប់ → ចុចលើអក្សរមិនបាន (គួរប្រើ `SwitchListTile`)

**ខ្លឹមសារ និង feedback**
22. `"Settings"`, `"Save"`, `"Enter name"` — ភាសាលាយគ្នា
23. `"Saved!"` — សញ្ញាឧទាន និងមិនប្រាប់ថារក្សាទុកអ្វី
24. ការលុបគ្មានការបញ្ជាក់ → សកម្មភាពបំផ្លាញដោយចុចម្តង
25. គ្មានស្ថានភាព loading ពេលរក្សាទុក

**Accessibility**
26. `Colors.blue` លើសផ្ទៃពណ៌ស → contrast ratio ប្រហែល 3.1:1 (ធ្លាក់ក្រោម 4.5:1)
27. ពណ៌ក្រហមតែម្យ៉ាងសម្រាប់សកម្មភាពគ្រោះថ្នាក់ → មិនគ្រប់គ្រាន់

</details>

<details>
<summary>💡 ដំណោះស្រាយពេញលេញ</summary>

```dart
import 'package:material_ui/material_ui.dart';
import 'package:flutter/widget_previews.dart';

class SettingsScreen extends StatefulWidget {
  const SettingsScreen({super.key});

  @override
  State<SettingsScreen> createState() => _SettingsScreenState();
}

class _SettingsScreenState extends State<SettingsScreen> {
  final _nameController = TextEditingController();
  bool _notificationsEnabled = true;
  bool _isSaving = false;

  @override
  void dispose() {
    _nameController.dispose();
    super.dispose();
  }

  Future<void> _save() async {
    setState(() => _isSaving = true);
    try {
      await settingsRepository.save(name: _nameController.text);
      if (!mounted) return;
      ScaffoldMessenger.of(context)
        ..hideCurrentSnackBar()
        ..showSnackBar(const SnackBar(content: Text('បានរក្សាទុកការកំណត់')));
    } catch (_) {
      if (!mounted) return;
      ScaffoldMessenger.of(context)
        ..hideCurrentSnackBar()
        ..showSnackBar(SnackBar(
          content: const Text('មិនអាចរក្សាទុកបានទេ'),
          action: SnackBarAction(label: 'ព្យាយាមម្តងទៀត', onPressed: _save),
        ));
    } finally {
      if (mounted) setState(() => _isSaving = false);
    }
  }

  Future<void> _confirmDelete() async {
    final confirmed = await showDialog<bool>(
      context: context,
      builder: (context) => AlertDialog(
        icon: Icon(Icons.warning_amber_rounded, color: context.colors.error),
        title: const Text('លុបគណនី?'),
        content: const Text('ទិន្នន័យទាំងអស់នឹងត្រូវលុប។ '
            'សកម្មភាពនេះមិនអាចត្រឡប់វិញបានទេ។'),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context, false),
            child: const Text('បោះបង់'),
          ),
          AppButton(
            label: 'លុបគណនី',
            variant: AppButtonVariant.destructive,
            onPressed: () => Navigator.pop(context, true),
          ),
        ],
      ),
    );
    if (confirmed ?? false) { /* ... */ }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('ការកំណត់')),
      body: ListView(
        padding: AppSpacing.page,
        children: [
          _SettingsSection(
            title: 'គណនី',
            children: [
              // Field ពេញទទឹង — label នៅខាងលើ មិននៅចំហៀង
              TextField(
                controller: _nameController,
                decoration: const InputDecoration(
                  labelText: 'ឈ្មោះអ្នកប្រើប្រាស់',
                  hintText: 'ឧទាហរណ៍៖ មេងហៀក',
                ),
                textCapitalization: TextCapitalization.words,
                autofillHints: const [AutofillHints.name],
              ),
            ],
          ),
          const Gap(AppSpacing.lg),

          _SettingsSection(
            title: 'ចំណូលចិត្ត',
            children: [
              // SwitchListTile: ចុចលើអក្សរក៏បានដែរ + tap target ត្រឹមត្រូវ
              SwitchListTile(
                value: _notificationsEnabled,
                onChanged: (v) => setState(() => _notificationsEnabled = v),
                title: const Text('ការជូនដំណឹង'),
                subtitle: const Text('ទទួលដំណឹងអំពីកម្មង់ថ្មី'),
                contentPadding: EdgeInsets.zero,
              ),
              const ThemeModeSelector(),   // ប្តូរ theme ពិតប្រាកដ
            ],
          ),
          const Gap(AppSpacing.xl),

          AppButton(
            label: 'រក្សាទុកការកំណត់',
            onPressed: _save,
            isLoading: _isSaving,
            fullWidth: true,
          ),
          const Gap(AppSpacing.md),

          AppButton(
            label: 'លុបគណនី',
            variant: AppButtonVariant.text,
            icon: Icons.delete_outline,
            onPressed: _confirmDelete,
            fullWidth: true,
          ),
        ],
      ),
    );
  }
}

class _SettingsSection extends StatelessWidget {
  const _SettingsSection({required this.title, required this.children});

  final String title;
  final List<Widget> children;

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(
          title,
          style: context.text.titleMedium?.copyWith(color: context.colors.primary),
        ),
        const Gap(AppSpacing.xs),
        Container(
          padding: AppSpacing.card,
          decoration: BoxDecoration(
            color: context.colors.surfaceContainer,
            borderRadius: BorderRadius.circular(context.tokens.radiusMd),
          ),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: children,
          ),
        ),
      ],
    );
  }
}

@AppPreview(name: 'អេក្រង់ការកំណត់')
Widget settingsScreenPreview() => const SettingsScreen();
```

**បញ្ជីត្រួតពិនិត្យចុងក្រោយ៖**

- [ ] គ្មាន `Colors.*` ក្រៅពី theme file
- [ ] គ្មានលេខ spacing ផ្ទាល់
- [ ] គ្មាន `TextStyle(fontSize:)` ក្នុងអេក្រង់
- [ ] គ្មាន `Container(height:)` ជុំវិញអត្ថបទ
- [ ] គ្មាន `GestureDetector` សម្រាប់សកម្មភាពដែលគួរជាប៊ូតុង
- [ ] គ្រប់ tap target ≥ 48dp
- [ ] គ្រប់ icon-only មាន tooltip ឬ Semantics
- [ ] សកម្មភាពបំផ្លាញមានការបញ្ជាក់
- [ ] ស្ថានភាព loading មាន ហើយប៊ូតុងមិនលោត
- [ ] ភាសាតែមួយ ស៊ីគ្នា
- [ ] ដំណើរការនៅ text scale 2.0
- [ ] ដំណើរការក្នុង dark mode
- [ ] មាន `@Preview`
- [ ] `flutter analyze` ស្អាត

</details>

---

# ឧបសម្ព័ន្ធ

## ក. បញ្ជីត្រួតពិនិត្យសម្រាប់ការពិនិត្យ UI (UI Review Checklist)

ប្រើវាមុនពេលបញ្ជូន pull request ណាមួយដែលប៉ះ UI។

**ប្រព័ន្ធរចនា**
- [ ] គ្មាន hardcoded color, spacing, ឬ font size
- [ ] Token ថ្មីៗត្រូវបានបន្ថែមទៅ design system មិនមែនក្នុងអេក្រង់
- [ ] ដំណើរការទាំង light និង dark

**ប្លង់**
- [ ] គ្មាន overflow នៅទំហំណាមួយ (សាក 320px ដល់ 1920px)
- [ ] គោរព SafeArea និង keyboard insets
- [ ] Landscape ដំណើរការ

**ស្ថានភាព**
- [ ] រចនាស្ថានភាព loading, empty, error មិនត្រឹមតែ success
- [ ] គ្រប់ស្ថានភាពមានផ្លូវចេញ (សកម្មភាពបន្ទាប់)
- [ ] សកម្មភាពបំផ្លាញអាចបញ្ច្រាស់ ឬត្រូវការការបញ្ជាក់

**Accessibility**
- [ ] Tap target ≥ 48dp
- [ ] Contrast ≥ 4.5:1 សម្រាប់អត្ថបទធម្មតា
- [ ] Icon-only មាន label
- [ ] ដំណើរការនៅ text scale 2.0
- [ ] គោរព reduced motion
- [ ] ព័ត៌មានមិនបញ្ជូនតាមពណ៌តែម្យ៉ាង

**ខ្លឹមសារ**
- [ ] ប៊ូតុងប្រើកិរិយាសព្ទដែលប្រាប់ថាមានអ្វីកើតឡើង
- [ ] សារកំហុសប្រាប់វិធីជួសជុល
- [ ] គ្មានវាក្យស័ព្ទបច្ចេកទេស
- [ ] ភាសាស៊ីគ្នាទាំង app

**គុណភាពកូដ**
- [ ] គ្មាន `Widget _buildX()` method
- [ ] `const` គ្រប់កន្លែងដែលអាច
- [ ] មាន `@Preview`
- [ ] `flutter analyze` ស្អាត

---

## ខ. តារាង Anti-pattern → ដំណោះស្រាយ

| Anti-pattern | ហេតុអ្វីអាក្រក់ | ជំនួសដោយ |
|---|---|---|
| `Colors.blue` | បែកបាក់ក្នុង dark mode, គ្មាន contrast guarantee | `colorScheme.primary` |
| `EdgeInsets.all(15)` | មិនស៊ីគ្នាទូទាំង app | `AppSpacing.md` |
| `TextStyle(fontSize: 17)` | មិនគោរព text scaling និង theme | `context.text.titleMedium` |
| `Widget _buildHeader()` | មិនអាច const, rebuild ជានិច្ច | `class _Header extends StatelessWidget` |
| `GestureDetector` + `Container` | គ្មាន ripple, focus, disabled | `FilledButton`, `InkWell` |
| `Container(height: 56)` ជុំវិញអត្ថបទ | បែកបាក់នៅ text scale ធំ | `ConstrainedBox(minHeight:)` |
| `MediaQuery.of(context).size` | rebuild លើគ្រប់ការប្តូរ | `MediaQuery.sizeOf(context)` |
| `BoxShadow` សម្រាប់ជម្រៅ | មើលមិនឃើញក្នុង dark mode | `surfaceContainer` levels |
| `bool isLoading; String? error;` | អនុញ្ញាតស្ថានភាពមិនត្រឹមត្រូវ | `sealed class` + `switch` |
| ប៊ូតុង submit disabled | អ្នកប្រើប្រាស់មិនដឹងហេតុអ្វី | បើកជានិច្ច + បង្ហាញកំហុសពេលចុច |
| `"Something went wrong"` | គ្មានព័ត៌មានប្រើប្រាស់បាន | ប្រាប់អ្វីបរាជ័យ + វិធីជួសជុល |
| Dialog សម្រាប់សារជោគជ័យ | រំខានលើសហេតុ | SnackBar |
| SnackBar សម្រាប់ការលុបជាអចិន្ត្រៃយ៍ | បាត់មុនអ្នកប្រើប្រាស់ទាន់អាន | Dialog + បញ្ជាក់ |
| `Curves.bounceOut` លើ UI | យឺត និងមើលទៅមិនជាក់លាក់ | `Curves.easeOutCubic` |
| `TextScaler.noScaling` | បំបែក accessibility | ជួសជុល layout ជំនួស |
| `Hero` លើ `Text` | ភ្លឹបភ្លែតពេលហោះ | `Hero` លើរូបភាពតែប៉ុណ្ណោះ |
| `ListView(children: [...])` សម្រាប់ទិន្នន័យ | សាងសង់ធាតុទាំងអស់ភ្លាមៗ | `ListView.builder` |
| `shrinkWrap: true` លើបញ្ជីវែង | បាត់ lazy loading | `Expanded` ឬ slivers |

---

## គ. ផែនការអនុវត្ត ៦ សប្តាហ៍

| សប្តាហ៍ | លំហាត់ | គោលដៅ |
|---|---|---|
| ១ | ១–៥ | បង្កើត design system ពេញលេញរបស់អ្នក — យកទៅប្រើក្នុងគ្រប់ project ក្រោយៗ |
| ២ | ៦–១០ | យល់ constraints ដល់កម្រិតដែលអ្នកមិនចាំបាច់ទាយទៀត |
| ៣ | ១១–១៤ | បង្កើត component library តូចមួយ (button, card, list, form) |
| ៤ | ១៥–១៨ | ចាប់ផ្តើមគិតពីស្ថានភាពទាំង ៥ ជាទម្លាប់ |
| ៥ | ១៩–២២ | បន្ថែម polish និង accessibility |
| ៦ | ២៣–២៤ | ការផ្ទៀងផ្ទាត់ស្វ័យប្រវត្តិ + capstone |

**បន្ទាប់ពីនេះ:** យក design system និង component library ដែលអ្នកបានសាងសង់ រួចប្រើវាដើម្បីសរសេរ app ពិតមួយ។ នេះជាចំណុចដែលអ្វីៗចាប់ផ្តើមមានន័យ។

---

## ឃ. ឯកសារយោង

| ប្រធានបទ | តំណ |
|---|---|
| Material 3 design system | `m3.material.io` |
| Flutter adaptive & responsive | `docs.flutter.dev/ui/adaptive-responsive` |
| Understanding constraints | `docs.flutter.dev/ui/layout/constraints` |
| Accessibility | `docs.flutter.dev/ui/accessibility` |
| Widget Previewer | `docs.flutter.dev/tools/widget-previewer` |
| Performance best practices | `docs.flutter.dev/perf/best-practices` |
| WCAG contrast checker | `webaim.org/resources/contrastchecker` |

---

*សរសេរសម្រាប់ Flutter 3.47.1 / Dart 3.13.1 — កញ្ញា ២០២៦*
