# ជំពូកបន្ថែម៖ Responsive & Adaptive Layout សម្រាប់ Tablet និងអេក្រង់ធំ

> **កំណែដែលប្រើសរសេរ៖** Flutter stable **3.47.1** (ចេញ ១៩ សីហា ២០២៦) ភ្ជាប់មកជាមួយ Dart **3.13.1**
> **រចនាបទរៀន៖** 📖 គោលគំនិត → 💻 កូដ → 🔬 ភស្តុតាង/ការផ្ទៀងផ្ទាត់

---

## មាតិកា

| # | ចំណងជើង |
|---|---------|
| 20.1 | Responsive ខុសពី Adaptive យ៉ាងដូចម្តេច |
| 20.2 | Window Size Class — ហេតុអ្វីវាស់ទទឹង មិនមែនវាស់ "ឧបករណ៍" |
| 20.3 | របៀបវាស់ឲ្យត្រូវ៖ `MediaQuery.sizeOf` vs `MediaQuery.of` vs `LayoutBuilder` |
| 20.4 | អន្ទាក់ធំបំផុត៖ `Platform.isAndroid` និង `shortestSide` |
| 20.5 | សាងសង់ប្រព័ន្ធ Breakpoint ដែលអាចប្រើឡើងវិញ |
| 20.6 | ការសម្របខ្លួនរបស់ Navigation៖ NavigationBar → NavigationRail → NavigationDrawer |
| 20.7 | List-Detail (Two-Pane) — លំនាំស្នូលរបស់ Tablet |
| 20.8 | Grid ដែលសម្របតាមទំហំ |
| 20.9 | ដែនកំណត់ទទឹងអត្ថបទ និង Typography ខ្មែរនៅលើអេក្រង់ធំ |
| 20.10 | Dialog, Bottom Sheet និង Side Sheet |
| 20.11 | Input៖ Mouse, Keyboard, Hover, Scrollbar |
| 20.12 | Foldable និង `displayFeatures` |
| 20.13 | ការធ្វើតេស្ត៖ Golden Test ច្រើនទំហំ |
| 20.14 | បញ្ជីត្រួតពិនិត្យមុនចេញផ្សាយ |

---

## 20.1 Responsive ខុសពី Adaptive យ៉ាងដូចម្តេច

### 📖 គោលគំនិត

មនុស្សភាគច្រើនប្រើពាក្យពីរនេះជំនួសគ្នា ប៉ុន្តែក្នុងឯកសារផ្លូវការរបស់ Flutter វាមានន័យផ្សេងគ្នាច្បាស់លាស់៖

**Responsive** = UI *ដដែល* ប៉ុន្តែ **រៀបចំទីតាំងឡើងវិញ** តាមទំហំដែលមាន។
ឧទាហរណ៍៖ បញ្ជីកាតមួយជួរឈរនៅលើទូរស័ព្ទ ក្លាយជាបីជួរឈរនៅលើ tablet។ វានៅតែជាកាតដដែល, ទិន្នន័យដដែល, widget ដដែល — គ្រាន់តែរៀបចំខុសគ្នា។

**Adaptive** = UI **ខុសគ្នា** ព្រោះ *វេទិកា* ឬ *របៀបប្រើប្រាស់* ខុសគ្នា។
ឧទាហរណ៍៖ ប្រើ `CupertinoSwitch` នៅលើ iOS តែ `Switch` នៅលើ Android។ ឬការបង្ហាញ menu ពេលចុចខាងស្តាំ (right-click) នៅលើ desktop ដែលមិនមាននៅលើទូរស័ព្ទទាល់តែសោះ។

> **ចំណុចសំខាន់៖** កម្មវិធី tablet ល្អ ត្រូវការ **ទាំងពីរ**។ Responsive ដោះស្រាយបញ្ហា "ទំហំ"។ Adaptive ដោះស្រាយបញ្ហា "អ្នកប្រើអង្គុយនៅឯណា ហើយប្រើអ្វីចុច"។

ការគិតខុសដ៏ទូទៅបំផុតគឺ៖ *"ខ្ញុំបានធ្វើឲ្យវាមិន overflow ហើយ ដូច្នេះវា responsive ហើយ។"*
មិនមែនទេ។ ការមិន overflow គ្រាន់តែជាកម្រិតអប្បបរមា។ បញ្ហាពិតរបស់ tablet គឺ **ចន្លោះទំនេរ** — ពេលអ្នកលាតបញ្ជីទូរស័ព្ទទៅ 1024px ធាតុនីមួយៗវែងឆ្ងាយពេក ភ្នែកអ្នកប្រើត្រូវធ្វើដំណើរឆ្លងកាត់អេក្រង់ទាំងមូលដើម្បីអានបន្ទាត់តែមួយ។ វាមិន overflow ទេ ប៉ុន្តែវាអាក្រក់។

### 💻 កូដ — ភាពខុសគ្នាក្នុងកូដពិត

```dart
// RESPONSIVE — same widgets, different arrangement
class ProductList extends StatelessWidget {
  const ProductList({super.key, required this.products});

  final List<Product> products;

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // Same ProductCard. Only the count of columns changes.
        final columns = switch (constraints.maxWidth) {
          < 600 => 1,
          < 900 => 2,
          < 1200 => 3,
          _ => 4,
        };
        return GridView.builder(
          gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: columns,
            childAspectRatio: 3 / 4,
          ),
          itemCount: products.length,
          itemBuilder: (context, i) => ProductCard(product: products[i]),
        );
      },
    );
  }
}
```

```dart
// ADAPTIVE — different widgets, because the platform expects it
Widget buildToggle(BuildContext context, bool value, ValueChanged<bool> onChanged) {
  final platform = Theme.of(context).platform;
  return switch (platform) {
    TargetPlatform.iOS ||
    TargetPlatform.macOS =>
      CupertinoSwitch(value: value, onChanged: onChanged),
    _ => Switch(value: value, onChanged: onChanged),
  };
}

// Flutter also ships built-in adaptive constructors:
//   Switch.adaptive(...)
//   Slider.adaptive(...)
//   CircularProgressIndicator.adaptive(...)
//   showAdaptiveDialog(...)
//   AlertDialog.adaptive(...)
```

### 🔬 ភស្តុតាង

សរសេរកម្មវិធីតូចមួយនេះ រួចដំណើរការលើ Chrome (`flutter run -d chrome`) ហើយអូសទំហំ browser។ អ្នកនឹងឃើញ `LayoutBuilder` បញ្ចេញ log រាល់ពេលដែលទំហំផ្លាស់ប្តូរ ខណៈពេលដែល `Theme.of(context).platform` មិនផ្លាស់ប្តូរឡើយ — នេះជាភស្តុតាងថាទាំងពីរដំណើរការលើអ័ក្សផ្សេងគ្នា។

```dart
LayoutBuilder(
  builder: (context, constraints) {
    debugPrint('width=${constraints.maxWidth} platform=${Theme.of(context).platform}');
    return const SizedBox.shrink();
  },
)
```

---

## 20.2 Window Size Class — ហេតុអ្វីវាស់ទទឹង មិនមែនវាស់ "ឧបករណ៍"

### 📖 គោលគំនិត

Material 3 កំណត់ **Window Size Class** ដែលជាក្រុមទទឹងស្តង់ដារ។ សូមចាំថាវាវាស់ **ទំហំបង្អួចរបស់កម្មវិធីអ្នក** មិនមែនទំហំអេក្រង់រូបវន្តទេ។

| Size class | ទទឹង (logical pixels) | ឧបករណ៍តំណាង | លំនាំ navigation ដែលណែនាំ |
|---|---|---|---|
| **Compact** | 0 – 599 | ទូរស័ព្ទបញ្ឈរ, tablet ក្នុង split-screen តូច | `NavigationBar` ខាងក្រោម |
| **Medium** | 600 – 839 | tablet តូចបញ្ឈរ, ទូរស័ព្ទផ្តេក | `NavigationRail` (បង្រួម) |
| **Expanded** | 840 – 1199 | tablet ធំផ្តេក | `NavigationRail` + two-pane |
| **Large** | 1200 – 1599 | desktop | `NavigationRail` ពង្រីក ឬ drawer ជាប់ |
| **Extra-large** | ≥ 1600 | monitor ធំ | drawer ជាប់ + ដែនកំណត់ទទឹងមាតិកា |

ហេតុអ្វីត្រូវវាស់បង្អួច មិនមែនអេក្រង់?

១. **Split-screen**៖ អ្នកប្រើ Android tablet អាចដាក់កម្មវិធីអ្នកនៅពាក់កណ្តាលអេក្រង់។ អេក្រង់ 1280px ក្លាយជាបង្អួច 640px។ បើអ្នកសម្រេចចិត្តតាមអេក្រង់ អ្នកនឹងបង្ហាញ layout ធំក្នុងទីតូច។
២. **Foldable**៖ ទំហំផ្លាស់ប្តូរនៅពេលបត់/លា ក្នុងវិនាទីតែមួយ។
៣. **Desktop & Web**៖ អ្នកប្រើអូសបង្អួច។ គ្មានអ្វី "ថេរ" ទេ។
៤. **Picture-in-picture / free-form window**៖ ទំហំអាចតូចជាងទូរស័ព្ទថែមទៀត។

> **ច្បាប់មាស៖** កុំសួរថា *"នេះជា tablet មែនទេ?"* ចូរសួរថា *"ខ្ញុំមានទីធ្លាប៉ុន្មាន?"*

### 💻 កូដ — កំណត់ក្រុមទំហំជា enum

```dart
// lib/core/layout/window_size_class.dart
enum WindowSizeClass {
  compact,     // 0 – 599
  medium,      // 600 – 839
  expanded,    // 840 – 1199
  large,       // 1200 – 1599
  extraLarge;  // 1600+

  static WindowSizeClass fromWidth(double width) => switch (width) {
        < 600 => WindowSizeClass.compact,
        < 840 => WindowSizeClass.medium,
        < 1200 => WindowSizeClass.expanded,
        < 1600 => WindowSizeClass.large,
        _ => WindowSizeClass.extraLarge,
      };

  bool get isCompact => this == WindowSizeClass.compact;

  /// True when there is room for two panes side by side.
  bool get supportsTwoPane => index >= WindowSizeClass.expanded.index;

  /// True when a navigation rail fits (instead of a bottom bar).
  bool get prefersRail => index >= WindowSizeClass.medium.index;
}
```

សម្គាល់៖ `switch` expression ជាមួយ relational pattern (`< 600`) គឺជាមុខងារ Dart 3 ដែលយើងបានសិក្សាក្នុងជំពូក Dart fundamentals។ វាបង្ខំឲ្យអ្នកគ្របដណ្តប់គ្រប់ករណី (exhaustive) ដូច្នេះ compiler នឹងព្រមានប្រសិនបើអ្នកភ្លេចករណីណាមួយ។

### 🔬 ភស្តុតាង

ដំណើរការនៅលើ Android tablet ពិត រួចបើក split-screen។ ដាក់ `debugPrint` ក្នុង `MediaQuery` builder៖

```dart
@override
Widget build(BuildContext context) {
  final size = MediaQuery.sizeOf(context);
  final view = View.of(context);
  debugPrint(
    'window=${size.width.toStringAsFixed(0)}x${size.height.toStringAsFixed(0)}  '
    'physical=${view.physicalSize.width.toStringAsFixed(0)}  '
    'dpr=${view.devicePixelRatio}',
  );
  return const Placeholder();
}
```

នៅពេលអ្នកចូល split-screen `size.width` នឹងធ្លាក់ចុះមកពាក់កណ្តាល ខណៈពេលដែល `view.physicalSize` នៅដដែល។ នេះជាភស្តុតាងផ្ទាល់ថាការសម្រេចចិត្តត្រូវផ្អែកលើ `MediaQuery` មិនមែនលើទំហំអេក្រង់រូបវន្តទេ។

---

## 20.3 របៀបវាស់ឲ្យត្រូវ

### 📖 គោលគំនិត

មានវិធីបីយ៉ាងក្នុងការដឹងទំហំ ហើយវាមិនស្មើគ្នាទេ៖

**១. `MediaQuery.of(context).size`** — ទទួលបានទំហំបង្អួច **ប៉ុន្តែ** widget របស់អ្នកនឹង rebuild នៅពេល *វាល ណាមួយ* របស់ `MediaQueryData` ផ្លាស់ប្តូរ។ នោះមានន័យថា ការលេចឡើងនៃក្តារចុច (viewInsets), ការប្តូរ text scale, ការប្តូរ brightness, ការប្តូរ padding — ទាំងអស់នេះនឹងធ្វើឲ្យ widget អ្នក rebuild ទោះបីជាទំហំមិនប្តូរក៏ដោយ។

**២. `MediaQuery.sizeOf(context)`** — ចុះឈ្មោះស្តាប់ **តែវាល `size`** ប៉ុណ្ណោះ។ នេះជាវិធីត្រឹមត្រូវចាប់តាំងពី Flutter 3.10 មក។ មានបងប្អូនរបស់វាដែរ៖ `MediaQuery.paddingOf`, `MediaQuery.viewInsetsOf`, `MediaQuery.orientationOf`, `MediaQuery.textScalerOf`, `MediaQuery.platformBrightnessOf`។

**៣. `LayoutBuilder`** — ផ្តល់ **constraints ពីមេផ្ទាល់** មិនមែនទំហំបង្អួចទេ។ នេះសំខាន់ណាស់៖ ប្រសិនបើ widget របស់អ្នកអង្គុយក្នុង pane ខាងស្តាំដែលធំ 400px នៃអេក្រង់ 1200px នោះ `MediaQuery.sizeOf` នឹងប្រាប់ 1200 ខណៈពេលដែល `LayoutBuilder` នឹងប្រាប់ 400។ សម្រាប់ការសម្រេចចិត្តខាងក្នុង component **`LayoutBuilder` ជាចម្លើយត្រឹមត្រូវ**។

> **ច្បាប់ជាក់ស្តែង៖**
> - សម្រេចចិត្តអំពី **រចនាសម្ព័ន្ធកម្មវិធីទាំងមូល** (navigation, two-pane ឬអត់) → `MediaQuery.sizeOf`
> - សម្រេចចិត្តអំពី **ខាងក្នុង component មួយ** (កាតនេះគួរដាក់រូបខាងលើ ឬខាងឆ្វេង?) → `LayoutBuilder`

### 💻 កូដ

```dart
// ❌ BAD — rebuilds whenever the keyboard opens, text scale changes, etc.
class BadHeader extends StatelessWidget {
  const BadHeader({super.key});

  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.of(context).size.width;
    return Text('width: $width');
  }
}

// ✅ GOOD — only subscribes to size changes
class GoodHeader extends StatelessWidget {
  const GoodHeader({super.key});

  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.sizeOf(context).width;
    return Text('width: $width');
  }
}

// ✅ BEST for component-internal decisions — uses the actual space given
class AdaptiveProductCard extends StatelessWidget {
  const AdaptiveProductCard({super.key, required this.product});

  final Product product;

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        // Wide card: image on the left, text on the right.
        if (constraints.maxWidth >= 480) {
          return Row(
            children: [
              SizedBox(width: 160, child: ProductImage(product: product)),
              const SizedBox(width: 16),
              Expanded(child: ProductDetails(product: product)),
            ],
          );
        }
        // Narrow card: image on top, text below.
        return Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: [
            AspectRatio(aspectRatio: 16 / 9, child: ProductImage(product: product)),
            const SizedBox(height: 12),
            ProductDetails(product: product),
          ],
        );
      },
    );
  }
}
```

### 🔬 ភស្តុតាង — បង្ហាញការ rebuild លើសចំនួន

កូដខាងក្រោមរាប់ចំនួន build ពិតប្រាកដ។ ដំណើរការវា រួចចុចលើ `TextField` ដើម្បីបើកក្តារចុច។

```dart
class RebuildCounter extends StatefulWidget {
  const RebuildCounter({super.key, required this.label, required this.readSize});

  final String label;
  final double Function(BuildContext) readSize;

  @override
  State<RebuildCounter> createState() => _RebuildCounterState();
}

class _RebuildCounterState extends State<RebuildCounter> {
  int _builds = 0;

  @override
  Widget build(BuildContext context) {
    _builds++;
    final w = widget.readSize(context);
    return Text('${widget.label}: $_builds builds (w=${w.toStringAsFixed(0)})');
  }
}

// Usage
Column(
  children: [
    RebuildCounter(
      label: 'MediaQuery.of',
      readSize: (c) => MediaQuery.of(c).size.width,
    ),
    RebuildCounter(
      label: 'MediaQuery.sizeOf',
      readSize: (c) => MediaQuery.sizeOf(c).width,
    ),
    const TextField(decoration: InputDecoration(hintText: 'Tap me')),
  ],
)
```

**លទ្ធផលដែលអ្នកនឹងឃើញ៖** នៅពេលក្តារចុចលេចឡើង `MediaQuery.of` នឹងឡើងលេខ ខណៈពេលដែល `MediaQuery.sizeOf` នៅដដែល (លើកលែងតែពេលបង្អួចប្តូរទំហំពិត)។ នេះជាភស្តុតាងផ្ទាល់អំពីភាពខុសគ្នានៃការ subscribe។

**ហេតុអ្វីវាដំណើរការបែបនេះ?** `MediaQuery` ជា `InheritedModel` មិនមែន `InheritedWidget` ធម្មតាទេ។ `InheritedModel` អនុញ្ញាតឲ្យ dependent ចុះឈ្មោះលើ *"aspect"* ជាក់លាក់។ `sizeOf` ហៅ `dependOnInheritedWidgetOfExactType` ជាមួយ aspect `_MediaQueryAspect.size` ហើយ `updateShouldNotifyDependent` នឹងជូនដំណឹងតែពេល aspect នោះប្រែប្រួល។ អ្នកអាចអានកូដប្រភពនៅ `packages/flutter/lib/src/widgets/media_query.dart`។

---

## 20.4 អន្ទាក់ធំបំផុត

### 📖 គោលគំនិត

នេះជាកំហុសដែលកម្មវិធី Flutter ភាគច្រើននៅកម្ពុជា (និងទូទាំងពិភពលោក) ធ្វើ៖

```dart
// ❌❌❌ កុំធ្វើបែបនេះ
final isTablet = MediaQuery.of(context).size.shortestSide >= 600;
if (isTablet) { ... }
```

មូលហេតុដែលវាខូច៖

១. **`shortestSide` ក្នុង split-screen នៅតែធំ** នៅលើឧបករណ៍ខ្លះ ព្រោះវាវាស់ខ្នាតបង្អួច ប៉ុន្តែពេលបង្អួចខ្លីនិងតូច លទ្ធផលអាចយោល។ ការវាស់តាមទទឹងផ្ទាល់ (`size.width`) គឺជាអ្វីដែល Material 3 កំណត់។
២. **វាមិនអាចប្រើលើ desktop/web បានទេ** — បង្អួច browser 500×900 នឹងត្រូវបានចាត់ជា "ទូរស័ព្ទ" ខណៈពេលដែលអ្នកប្រើមាន mouse និង keyboard។
៣. **វាបង្កើត binary** — ក្នុងការពិតមានប្រាំក្រុមទំហំ មិនមែនពីរទេ។
៤. **វារួមបញ្ចូល "ទំហំ" ជាមួយ "របៀបប្រើ"** — tablet ដែលមាន keyboard ភ្ជាប់ជាឧបករណ៍ desktop ដោយពិត។

កំហុសទីពីរគឺការប្រើ `dart:io` Platform៖

```dart
// ❌ វានឹង crash នៅលើ web
import 'dart:io';
if (Platform.isAndroid) { ... }
```

`dart:io` មិនមាននៅលើ web ទេ។ ប្រើ `Theme.of(context).platform` (ដែលអាចត្រូវ override ក្នុងតេស្ត) ឬ `defaultTargetPlatform` ជំនួសវិញ។

### 💻 កូដ — ការជំនួសត្រឹមត្រូវ

```dart
import 'package:flutter/foundation.dart' show defaultTargetPlatform, kIsWeb;

/// Describes *how the user interacts*, not what the device is called.
enum InteractionMode { touch, pointer }

InteractionMode interactionModeOf(BuildContext context) {
  if (kIsWeb) return InteractionMode.pointer;
  return switch (Theme.of(context).platform) {
    TargetPlatform.android || TargetPlatform.iOS => InteractionMode.touch,
    _ => InteractionMode.pointer,
  };
}

/// Combine both axes: how much room, and how the user points.
class LayoutProfile {
  const LayoutProfile({required this.sizeClass, required this.interaction});

  final WindowSizeClass sizeClass;
  final InteractionMode interaction;

  /// Minimum tap target: 48dp for touch, 32dp is acceptable for a mouse.
  double get minTapTarget => interaction == InteractionMode.touch ? 48 : 32;

  bool get showsHoverAffordances => interaction == InteractionMode.pointer;
}
```

### 🔬 ភស្តុតាង

សាកល្បងជាក់ស្តែង៖ បើក emulator tablet មួយ (ឧ. Pixel Tablet) រួចដំណើរការកូដនេះ។ បន្ទាប់មកចូល split-screen ជាមួយកម្មវិធីមួយទៀត។

```dart
Builder(
  builder: (context) {
    final size = MediaQuery.sizeOf(context);
    return Text(
      'shortestSide=${size.shortestSide.toStringAsFixed(0)}\n'
      'width=${size.width.toStringAsFixed(0)}\n'
      'sizeClass=${WindowSizeClass.fromWidth(size.width)}',
      style: const TextStyle(fontSize: 20, height: 1.7),
    );
  },
)
```

អ្នកនឹងឃើញថាការសម្រេចចិត្តតាម `width` ផ្តល់លទ្ធផលត្រឹមត្រូវ (compact ពេលបង្អួចតូច) ខណៈពេលដែលការសម្រេចចិត្តតាម `shortestSide` អាចនៅតែរាយការណ៍ថា "tablet" ហើយបង្ហាញ layout ពីរ pane ក្នុងចន្លោះតូចចង្អៀត។

---

## 20.5 សាងសង់ប្រព័ន្ធ Breakpoint ដែលអាចប្រើឡើងវិញ

### 📖 គោលគំនិត

កុំបាចយ `if (width > 600)` ទៅគ្រប់ទីកន្លែងក្នុងកូដ។ ពេលអ្នកចង់ប្តូរ breakpoint ពី 600 ទៅ 640 អ្នកនឹងត្រូវស្វែងរកគ្រប់ឯកសារ។ ជំនួសវិញ សូមធ្វើឲ្យវាក្លាយជា **ផ្នែកមួយនៃ context** តាមរយៈ `InheritedWidget` ដែលគណនាតែម្តង។

មានយុទ្ធសាស្ត្រពីរ៖

**(ក) InheritedWidget នៅឫសកម្មវិធី** — គណនាម្តងនៅ `MaterialApp.builder` រួចចែកចាយចុះក្រោម។ សាមញ្ញ លឿន ត្រឹមត្រូវសម្រាប់ការសម្រេចចិត្តកម្រិតកម្មវិធី។

**(ខ) ThemeExtension** — ដាក់តម្លៃដែលអាស្រ័យលើទំហំ (padding, ចម្ងាយ, ទំហំអក្សរ) ចូលក្នុង `ThemeData` ដើម្បីឲ្យ widget អានតាមរយៈ `Theme.of(context)` ធម្មតា។

### 💻 កូដ — យុទ្ធសាស្ត្រ (ក)

```dart
// lib/core/layout/layout_scope.dart
import 'package:flutter/material.dart';

class LayoutScope extends InheritedWidget {
  const LayoutScope({
    super.key,
    required this.sizeClass,
    required this.interaction,
    required super.child,
  });

  final WindowSizeClass sizeClass;
  final InteractionMode interaction;

  static LayoutScope of(BuildContext context) {
    final scope = context.dependOnInheritedWidgetOfExactType<LayoutScope>();
    assert(scope != null, 'LayoutScope.of() called with no LayoutScope above.');
    return scope!;
  }

  @override
  bool updateShouldNotify(LayoutScope oldWidget) =>
      sizeClass != oldWidget.sizeClass || interaction != oldWidget.interaction;
}

/// Install once, at the very top of the app.
class LayoutScopeProvider extends StatelessWidget {
  const LayoutScopeProvider({super.key, required this.child});

  final Widget child;

  @override
  Widget build(BuildContext context) {
    final width = MediaQuery.sizeOf(context).width;
    return LayoutScope(
      sizeClass: WindowSizeClass.fromWidth(width),
      interaction: interactionModeOf(context),
      child: child,
    );
  }
}
```

ភ្ជាប់វាក្នុង `MaterialApp`៖

```dart
MaterialApp.router(
  routerConfig: appRouter,
  builder: (context, child) => LayoutScopeProvider(child: child!),
)
```

ឥឡូវនេះ widget ណាមួយអាចសួរ៖

```dart
final layout = LayoutScope.of(context);
if (layout.sizeClass.supportsTwoPane) { ... }
```

**ចំណុចសំខាន់ខាងរចនាសម្ព័ន្ធ៖** ព្រោះ `updateShouldNotify` ប្រៀបធៀប **enum** មិនមែន `double` ទេ widget ដែលអាស្រ័យនឹង rebuild **តែពេលឆ្លងកាត់ breakpoint** មិនមែនរាល់ pixel ដែលអ្នកអូសបង្អួចទេ។ នេះជាការសន្សំសំចៃដ៏ធំនៅលើ desktop និង web។

### 💻 កូដ — យុទ្ធសាស្ត្រ (ខ) ThemeExtension

```dart
@immutable
class Dimens extends ThemeExtension<Dimens> {
  const Dimens({
    required this.pagePadding,
    required this.gutter,
    required this.cardRadius,
    required this.maxContentWidth,
  });

  final double pagePadding;
  final double gutter;
  final double cardRadius;
  final double maxContentWidth;

  factory Dimens.forSizeClass(WindowSizeClass sc) => switch (sc) {
        WindowSizeClass.compact => const Dimens(
            pagePadding: 16, gutter: 8, cardRadius: 12, maxContentWidth: 600),
        WindowSizeClass.medium => const Dimens(
            pagePadding: 24, gutter: 12, cardRadius: 16, maxContentWidth: 720),
        WindowSizeClass.expanded => const Dimens(
            pagePadding: 32, gutter: 16, cardRadius: 16, maxContentWidth: 840),
        WindowSizeClass.large || WindowSizeClass.extraLarge => const Dimens(
            pagePadding: 40, gutter: 24, cardRadius: 20, maxContentWidth: 960),
      };

  @override
  Dimens copyWith({
    double? pagePadding,
    double? gutter,
    double? cardRadius,
    double? maxContentWidth,
  }) =>
      Dimens(
        pagePadding: pagePadding ?? this.pagePadding,
        gutter: gutter ?? this.gutter,
        cardRadius: cardRadius ?? this.cardRadius,
        maxContentWidth: maxContentWidth ?? this.maxContentWidth,
      );

  @override
  Dimens lerp(Dimens? other, double t) {
    if (other is! Dimens) return this;
    return Dimens(
      pagePadding: lerpDouble(pagePadding, other.pagePadding, t)!,
      gutter: lerpDouble(gutter, other.gutter, t)!,
      cardRadius: lerpDouble(cardRadius, other.cardRadius, t)!,
      maxContentWidth: lerpDouble(maxContentWidth, other.maxContentWidth, t)!,
    );
  }
}

// Convenience accessor
extension DimensX on BuildContext {
  Dimens get dimens => Theme.of(this).extension<Dimens>()!;
}
```

ដំឡើងវាតាមទំហំបច្ចុប្បន្ន៖

```dart
MaterialApp.router(
  builder: (context, child) {
    final sc = WindowSizeClass.fromWidth(MediaQuery.sizeOf(context).width);
    final base = Theme.of(context);
    return Theme(
      data: base.copyWith(extensions: [Dimens.forSizeClass(sc)]),
      child: LayoutScopeProvider(child: child!),
    );
  },
)
```

ការប្រើ៖

```dart
Padding(
  padding: EdgeInsets.all(context.dimens.pagePadding),
  child: const Body(),
)
```

**អត្ថប្រយោជន៍មួយទៀត៖** ព្រោះ `Dimens` អនុវត្ត `lerp` ត្រឹមត្រូវ `AnimatedTheme` នឹងធ្វើ animation រលូនរវាង breakpoint ដោយស្វ័យប្រវត្តិ។

### 🔬 ភស្តុតាង

សរសេរតេស្តដែលបញ្ជាក់ថា breakpoint រៀបចំត្រឹមត្រូវ៖

```dart
// test/layout/window_size_class_test.dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  test('boundaries map to the documented Material 3 classes', () {
    expect(WindowSizeClass.fromWidth(0), WindowSizeClass.compact);
    expect(WindowSizeClass.fromWidth(599.9), WindowSizeClass.compact);
    expect(WindowSizeClass.fromWidth(600), WindowSizeClass.medium);
    expect(WindowSizeClass.fromWidth(839.9), WindowSizeClass.medium);
    expect(WindowSizeClass.fromWidth(840), WindowSizeClass.expanded);
    expect(WindowSizeClass.fromWidth(1199.9), WindowSizeClass.expanded);
    expect(WindowSizeClass.fromWidth(1200), WindowSizeClass.large);
    expect(WindowSizeClass.fromWidth(1600), WindowSizeClass.extraLarge);
  });

  test('two-pane support starts at expanded', () {
    expect(WindowSizeClass.medium.supportsTwoPane, isFalse);
    expect(WindowSizeClass.expanded.supportsTwoPane, isTrue);
  });
}
```

នេះជាតេស្តដែលរត់ក្នុងរយៈពេលមិនដល់មួយវិនាទី ហើយវាការពារកុំឲ្យអ្នកណាម្នាក់ក្នុងក្រុមប្តូរលេខដោយចៃដន្យ។

---

## 20.6 ការសម្របខ្លួនរបស់ Navigation

### 📖 គោលគំនិត

នេះជាការផ្លាស់ប្តូរដែលអ្នកប្រើមើលឃើញច្បាស់បំផុត។ Material 3 កំណត់លំដាប់នេះ៖

```
Compact (< 600)     →  NavigationBar        (ខាងក្រោម, មេដៃងាយចុច)
Medium (600–839)    →  NavigationRail       (ខាងឆ្វេង, បង្រួម, icon + label តូច)
Expanded (840–1199) →  NavigationRail       (ខាងឆ្វេង, អាចពង្រីក)
Large (1200+)       →  NavigationDrawer     (ជាប់ជានិច្ច, មិនអាចបិទ)
```

ហេតុអ្វី bottom bar មិនល្អនៅលើ tablet? ព្រោះនៅលើ tablet ផ្តេកទំហំ 1024×768 គែមខាងក្រោមឃ្លាតឆ្ងាយពីមេដៃ ហើយ bottom bar ដែលលាតពេញ 1024px មើលទៅដូចជាការខ្ជះខ្ជាយទីធ្លា។ `NavigationRail` នៅខាងឆ្វេងជិតនឹងកន្លែងដៃកាន់ ហើយវាទុកទីធ្លាបញ្ឈរឲ្យមាតិកា។

**ចំណុចបច្ចេកទេសសំខាន់បំផុត៖ រក្សា state ពេលប្តូរ layout។**
បើអ្នកសាងសង់ layout ថ្មីទាំងស្រុងពេលឆ្លង breakpoint នោះ `Element` tree នឹងត្រូវបំផ្លាញ ហើយ scroll position, form input, animation ទាំងអស់នឹងបាត់។ ដំណោះស្រាយគឺរក្សា subtree មាតិកាឲ្យដដែល ហើយប្តូរតែស្បែក navigation ជុំវិញវា។

### 💻 កូដ — Scaffold ដែលសម្របតាមទំហំ ដោយរក្សា state

```dart
// lib/core/layout/adaptive_scaffold.dart
import 'package:flutter/material.dart';

class NavDestination {
  const NavDestination({
    required this.icon,
    required this.selectedIcon,
    required this.label,
  });

  final IconData icon;
  final IconData selectedIcon;
  final String label;
}

class AdaptiveNavigationScaffold extends StatelessWidget {
  const AdaptiveNavigationScaffold({
    super.key,
    required this.destinations,
    required this.selectedIndex,
    required this.onDestinationSelected,
    required this.body,
    this.floatingActionButton,
  });

  final List<NavDestination> destinations;
  final int selectedIndex;
  final ValueChanged<int> onDestinationSelected;
  final Widget body;
  final Widget? floatingActionButton;

  @override
  Widget build(BuildContext context) {
    final sizeClass = LayoutScope.of(context).sizeClass;

    // IMPORTANT: `body` is created once, outside the switch, so the same
    // widget instance is reused across breakpoints. Flutter can then match
    // Elements and preserve State (scroll offsets, text fields, animations).
    return switch (sizeClass) {
      WindowSizeClass.compact => Scaffold(
          body: body,
          floatingActionButton: floatingActionButton,
          bottomNavigationBar: NavigationBar(
            selectedIndex: selectedIndex,
            onDestinationSelected: onDestinationSelected,
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
      WindowSizeClass.medium || WindowSizeClass.expanded => Scaffold(
          floatingActionButton: floatingActionButton,
          body: Row(
            children: [
              NavigationRail(
                selectedIndex: selectedIndex,
                onDestinationSelected: onDestinationSelected,
                // Show labels from `expanded` upward; icons only on `medium`.
                labelType: sizeClass == WindowSizeClass.expanded
                    ? NavigationRailLabelType.all
                    : NavigationRailLabelType.selected,
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
      WindowSizeClass.large || WindowSizeClass.extraLarge => Scaffold(
          floatingActionButton: floatingActionButton,
          body: Row(
            children: [
              NavigationDrawer(
                selectedIndex: selectedIndex,
                onDestinationSelected: onDestinationSelected,
                children: [
                  const Padding(
                    padding: EdgeInsets.fromLTRB(28, 16, 16, 10),
                    child: Text('ម៉ឺនុយ', style: TextStyle(fontSize: 18, height: 1.7)),
                  ),
                  for (final d in destinations)
                    NavigationDrawerDestination(
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

### 🔬 ភស្តុតាង — បញ្ជាក់ថា State រស់រានពេលឆ្លង breakpoint

នេះជាតេស្តដែលបង្ហាញច្បាស់ថាតើ state ត្រូវបានរក្សាឬអត់៖

```dart
// test/layout/adaptive_scaffold_test.dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

void main() {
  testWidgets('scroll offset survives a compact → expanded transition',
      (tester) async {
    // Start compact.
    tester.view.physicalSize = const Size(400 * 3, 800 * 3);
    tester.view.devicePixelRatio = 3.0;
    addTearDown(tester.view.reset);

    await tester.pumpWidget(const MyApp());

    // Scroll the body down.
    await tester.drag(find.byType(ListView), const Offset(0, -500));
    await tester.pumpAndSettle();

    final controller = tester
        .widget<Scrollable>(find.byType(Scrollable).first)
        .controller;
    final offsetBefore = controller?.offset ?? 0;
    expect(offsetBefore, greaterThan(0));

    // Resize to expanded — this is the moment where naive code loses state.
    tester.view.physicalSize = const Size(1000 * 3, 800 * 3);
    await tester.pumpAndSettle();

    final offsetAfter = tester
            .widget<Scrollable>(find.byType(Scrollable).first)
            .controller
            ?.offset ??
        0;

    expect(offsetAfter, closeTo(offsetBefore, 1.0),
        reason: 'Body subtree was rebuilt from scratch — State was lost.');
  });
}
```

ប្រសិនបើអ្នកសរសេរ `AdaptiveNavigationScaffold` ខុស (ឧ. បង្កើត `body` ឡើងវិញក្នុងសាខានីមួយៗនៃ `switch`) តេស្តនេះនឹងធ្លាក់។ នេះជាភស្តុតាងដែលអាចត្រួតពិនិត្យបានដោយស្វ័យប្រវត្តិក្នុង CI។

---

## 20.7 List-Detail (Two-Pane) — លំនាំស្នូលរបស់ Tablet

### 📖 គោលគំនិត

ប្រសិនបើអ្នកអនុវត្តតែមួយពីជំពូកនេះ សូមអនុវត្តលំនាំនេះ។ វាជាអ្វីដែលធ្វើឲ្យកម្មវិធីមួយ "មានអារម្មណ៍ថាបានធ្វើសម្រាប់ tablet"។

នៅលើទូរស័ព្ទ៖ ចុចធាតុមួយ → រុញទំព័រថ្មីចូល (push) → ចុចថយក្រោយដើម្បីត្រឡប់។
នៅលើ tablet៖ បញ្ជីនៅខាងឆ្វេង, ព័ត៌មានលម្អិតនៅខាងស្តាំ, ទាំងពីរមើលឃើញព្រមគ្នា។

មានការសម្រេចចិត្តរចនាបីដែលអ្នកត្រូវធ្វើ៖

**១. តើ URL គួរមើលទៅដូចម្តេច?**
ចម្លើយត្រឹមត្រូវ៖ URL ដដែលសម្រាប់ layout ទាំងពីរ។ `/messages/42` គួរបង្ហាញសារលេខ 42 ជាទំព័រពេញនៅលើទូរស័ព្ទ ហើយបង្ហាញបញ្ជី + សារលេខ 42 នៅលើ tablet។ នេះមានន័យថា **layout ជាមុខងារនៃ (route, ទំហំ)** មិនមែនជា state ដាច់ដោយឡែកទេ។

**២. តើត្រូវធ្វើអ្វីនៅពេលអ្នកប្រើបង្វិល tablet ពីផ្តេកទៅបញ្ឈរខណៈពេលកំពុងអានលម្អិត?**
រក្សាការជ្រើសរើស។ បង្ហាញទំព័រលម្អិតជាទំព័រពេញ ហើយប៊ូតុងថយក្រោយត្រឡប់ទៅបញ្ជី។ ការបោះបង់ការជ្រើសរើសគឺជាការធ្វើឲ្យអ្នកប្រើខូចចិត្ត។

**៣. តើត្រូវបង្ហាញអ្វីនៅ pane ស្តាំពេលមិនទាន់ជ្រើសរើសអ្វី?**
មិនមែនអេក្រង់សរួបទេ — បង្ហាញ "empty state" ដែលមានន័យ៖ រូបតំណាង + ការណែនាំខ្លី។

### 💻 កូដ — សម្របជាមួយ go_router

យើងប្រើ `StatefulShellRoute` របស់ go_router ដើម្បីរក្សា state របស់ pane បញ្ជី។

```dart
// lib/routing/app_router.dart
import 'package:go_router/go_router.dart';

final appRouter = GoRouter(
  initialLocation: '/messages',
  routes: [
    GoRoute(
      path: '/messages',
      builder: (context, state) => const MessagesPage(selectedId: null),
      routes: [
        GoRoute(
          path: ':id',
          builder: (context, state) => MessagesPage(
            selectedId: state.pathParameters['id'],
          ),
        ),
      ],
    ),
  ],
);
```

សម្គាល់ថា **route ទាំងពីរបង្កើត `MessagesPage` ដដែល** ដោយផ្លាស់ប្តូរតែ `selectedId`។ `MessagesPage` ជាអ្នកសម្រេចថាតើត្រូវបង្ហាញមួយ pane ឬពីរ។

```dart
// lib/features/messages/messages_page.dart
class MessagesPage extends StatelessWidget {
  const MessagesPage({super.key, required this.selectedId});

  final String? selectedId;

  @override
  Widget build(BuildContext context) {
    final twoPane = LayoutScope.of(context).sizeClass.supportsTwoPane;

    if (twoPane) {
      return Scaffold(
        body: Row(
          children: [
            // Fixed-width list pane. 360 is a comfortable list width;
            // Material recommends 320–400 for a supporting pane.
            SizedBox(
              width: 360,
              child: MessageListPane(
                selectedId: selectedId,
                onSelect: (id) => context.go('/messages/$id'),
              ),
            ),
            const VerticalDivider(width: 1, thickness: 1),
            Expanded(
              child: selectedId == null
                  ? const _NoSelectionPlaceholder()
                  : MessageDetailPane(
                      // Key by id so switching messages resets detail State
                      // (scroll offset, reply draft, etc.).
                      key: ValueKey(selectedId),
                      id: selectedId!,
                    ),
            ),
          ],
        ),
      );
    }

    // Compact: show whichever half the route asks for.
    if (selectedId != null) {
      return Scaffold(
        appBar: AppBar(title: const Text('សារ')),
        body: MessageDetailPane(key: ValueKey(selectedId), id: selectedId!),
      );
    }

    return Scaffold(
      appBar: AppBar(title: const Text('សារទាំងអស់')),
      body: MessageListPane(
        selectedId: null,
        onSelect: (id) => context.go('/messages/$id'),
      ),
    );
  }
}

class _NoSelectionPlaceholder extends StatelessWidget {
  const _NoSelectionPlaceholder();

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);
    return Center(
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          Icon(Icons.mark_email_unread_outlined,
              size: 64, color: theme.colorScheme.outline),
          const SizedBox(height: 16),
          Text(
            'ជ្រើសរើសសារមួយដើម្បីអាន',
            style: theme.textTheme.bodyLarge?.copyWith(height: 1.7),
          ),
        ],
      ),
    );
  }
}
```

### 💻 កូដ — សមាសភាគ TwoPane ទូទៅ

ជំនួសឲ្យការសរសេរ logic នេះឡើងវិញក្នុងគ្រប់មុខងារ សូមទាញយកវាចេញ៖

```dart
// lib/core/layout/two_pane.dart
class TwoPane extends StatelessWidget {
  const TwoPane({
    super.key,
    required this.start,
    required this.end,
    required this.showEndOnly,
    this.startWidth = 360,
    this.startFlex,
    this.endFlex,
  });

  /// The list / supporting pane.
  final Widget start;

  /// The detail / focus pane.
  final Widget end;

  /// In compact mode: true → show `end` full screen, false → show `start`.
  final bool showEndOnly;

  /// Fixed width for the start pane. Ignored when [startFlex] is set.
  final double startWidth;
  final int? startFlex;
  final int? endFlex;

  @override
  Widget build(BuildContext context) {
    final canSplit = LayoutScope.of(context).sizeClass.supportsTwoPane;

    if (!canSplit) return showEndOnly ? end : start;

    final startPane = startFlex != null
        ? Expanded(flex: startFlex!, child: start)
        : SizedBox(width: startWidth, child: start);

    return Row(
      children: [
        startPane,
        const VerticalDivider(width: 1, thickness: 1),
        Expanded(flex: endFlex ?? 1, child: end),
      ],
    );
  }
}
```

### 📖 ការគ្រប់គ្រងប៊ូតុងថយក្រោយ (back button)

នេះជាចំណុចដែលមនុស្សភាគច្រើនធ្វើខុស។ នៅក្នុងរបៀបពីរ pane ការចុចថយក្រោយ **មិនគួរ** ចាកចេញពីទំព័របញ្ជីទេ ព្រោះបញ្ជីនៅតែមើលឃើញ។ វាគួរតែលុបការជ្រើសរើសវិញ ឬបញ្ជូនចេញពីមុខងារទាំងមូល។

```dart
PopScope(
  // In two-pane mode with a selection, intercept the pop to clear selection.
  canPop: !twoPane || selectedId == null,
  onPopInvokedWithResult: (didPop, result) {
    if (didPop) return;
    context.go('/messages'); // clears selection, keeps the list
  },
  child: scaffold,
)
```

> **ចំណាំ៖** `WillPopScope` ត្រូវបានលុបចោលហើយ។ ចាប់ពី Flutter 3.12 មក សូមប្រើ `PopScope` ជាមួយ `canPop` និង `onPopInvokedWithResult`។ API ចាស់ `onPopInvoked` ក៏ត្រូវបានជំនួសដោយកំណែដែលមាន result ដែរ។

### 🔬 ភស្តុតាង

តេស្ត widget ដែលបញ្ជាក់ថា route ដដែលបង្ហាញ layout ខុសគ្នាតាមទំហំ៖

```dart
testWidgets('/messages/7 shows one pane on phone, two panes on tablet',
    (tester) async {
  Future<void> pumpAt(Size logical) async {
    tester.view.devicePixelRatio = 1.0;
    tester.view.physicalSize = logical;
    await tester.pumpWidget(const MyApp());
    await tester.pumpAndSettle();
  }
  addTearDown(tester.view.reset);

  // Phone
  await pumpAt(const Size(400, 800));
  appRouter.go('/messages/7');
  await tester.pumpAndSettle();
  expect(find.byType(MessageListPane), findsNothing);
  expect(find.byType(MessageDetailPane), findsOneWidget);

  // Tablet — same URL
  await pumpAt(const Size(1024, 768));
  await tester.pumpAndSettle();
  expect(find.byType(MessageListPane), findsOneWidget);
  expect(find.byType(MessageDetailPane), findsOneWidget);
});
```

---

## 20.8 Grid ដែលសម្របតាមទំហំ

### 📖 គោលគំនិត

មានវិធីពីរក្នុងការធ្វើ grid ហើយភាគច្រើនជ្រើសរើសខុស។

**`SliverGridDelegateWithFixedCrossAxisCount`** — អ្នកកំណត់ *ចំនួនជួរឈរ*។ ទទឹងកាតត្រូវបានគណនាចេញ។ បញ្ហា៖ នៅ 1600px ជាមួយ 4 ជួរឈរ កាតនីមួយៗនឹងធំ 400px — ធំពេក។

**`SliverGridDelegateWithMaxCrossAxisExtent`** — អ្នកកំណត់ *ទទឹងអតិបរមារបស់កាត*។ Flutter គណនាចំនួនជួរឈរដោយខ្លួនឯង។ នេះជាជម្រើសដែលត្រឹមត្រូវសម្រាប់ករណីភាគច្រើន ព្រោះវារក្សាទំហំកាតឲ្យថេរ ហើយបន្ថែមជួរឈរនៅពេលមានទីធ្លា — ដូចជា CSS `repeat(auto-fill, minmax(...))`។

រូបមន្តខាងក្នុងគឺ៖
```
crossAxisCount = (crossAxisExtent / (maxCrossAxisExtent + crossAxisSpacing)).ceil()
```
ដូច្នេះ `maxCrossAxisExtent` គឺជា *អតិបរមា* ពិតប្រាកដ — កាតនឹងតូចជាងឬស្មើតម្លៃនោះជានិច្ច។

### 💻 កូដ

```dart
// ✅ Preferred: card size stays constant, column count adapts.
GridView.builder(
  padding: EdgeInsets.all(context.dimens.pagePadding),
  gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
    maxCrossAxisExtent: 320,      // a card is never wider than 320
    mainAxisSpacing: context.dimens.gutter,
    crossAxisSpacing: context.dimens.gutter,
    childAspectRatio: 3 / 4,
  ),
  itemCount: products.length,
  itemBuilder: (context, i) => ProductCard(product: products[i]),
);
```

សម្រាប់ layout ស្មុគស្មាញដែលមានធាតុទំហំខុសគ្នា ចូរប្រើ `SliverGrid` ក្នុង `CustomScrollView` រួមជាមួយ sliver ផ្សេងទៀត — ដូចដែលយើងបានសិក្សាក្នុងជំពូក slivers៖

```dart
CustomScrollView(
  slivers: [
    SliverAppBar.large(title: const Text('ផលិតផល')),
    SliverPadding(
      padding: EdgeInsets.symmetric(horizontal: context.dimens.pagePadding),
      sliver: SliverGrid.builder(
        gridDelegate: const SliverGridDelegateWithMaxCrossAxisExtent(
          maxCrossAxisExtent: 320,
          mainAxisSpacing: 16,
          crossAxisSpacing: 16,
          childAspectRatio: 3 / 4,
        ),
        itemCount: products.length,
        itemBuilder: (context, i) => ProductCard(product: products[i]),
      ),
    ),
  ],
);
```

### 🔬 ភស្តុតាង — ផ្ទៀងផ្ទាត់រូបមន្តជួរឈរ

```dart
test('max-extent delegate computes column count as documented', () {
  const delegate = SliverGridDelegateWithMaxCrossAxisExtent(
    maxCrossAxisExtent: 320,
    crossAxisSpacing: 16,
    mainAxisSpacing: 16,
    childAspectRatio: 1,
  );

  SliverGridLayout layoutFor(double width) => delegate.getLayout(
        SliverConstraints(
          axisDirection: AxisDirection.down,
          growthDirection: GrowthDirection.forward,
          userScrollDirection: ScrollDirection.idle,
          scrollOffset: 0,
          precedingScrollExtent: 0,
          overlap: 0,
          remainingPaintExtent: 800,
          crossAxisExtent: width,
          crossAxisDirection: AxisDirection.right,
          viewportMainAxisExtent: 800,
          remainingCacheExtent: 800,
          cacheOrigin: 0,
        ),
      );

  // 400 / (320 + 16) = 1.19 → ceil → 2 columns
  expect(layoutFor(400).getMaxChildIndexForScrollOffset(0), 1);
  // 1024 / 336 = 3.04 → ceil → 4 columns
  expect(layoutFor(1024).getMaxChildIndexForScrollOffset(0), 3);
});
```

ការអានកូដប្រភពដោយផ្ទាល់ក៏ជាភស្តុតាងដែរ៖ `packages/flutter/lib/src/rendering/sliver_grid.dart`, class `SliverGridDelegateWithMaxCrossAxisExtent.getLayout`។

---

## 20.9 ដែនកំណត់ទទឹងអត្ថបទ និង Typography ខ្មែរនៅលើអេក្រង់ធំ

### 📖 គោលគំនិត

នេះជាបញ្ហាដែលមើលមិនឃើញភ្លាមៗ ប៉ុន្តែធ្វើឲ្យកម្មវិធីអានពិបាកបំផុត។

ការស្រាវជ្រាវ typography បង្ហាញថាបន្ទាត់ដែលអានស្រួលបំផុតមានប្រហែល **45–75 តួអក្សរ** សម្រាប់អក្សរឡាតាំង។ សម្រាប់អក្សរខ្មែរ ព្រោះតួអក្សរមានទទឹងធំជាង និងមានជើងអក្សរ (subscript) ដែលធ្វើឲ្យបន្ទាត់ក្រាស់ជាង ចន្លោះ **40–60 តួអក្សរ** មានអារម្មណ៍ស្រួលជាង។ នៅ 16sp នេះស្មើនឹងប្រហែល **600–720 logical pixels**។

បើអ្នកទុកអត្ថបទឲ្យលាតពេញ 1440px ភ្នែកអ្នកអានត្រូវធ្វើដំណើរឆ្ងាយពេក ហើយពេលចុះបន្ទាត់ថ្មីវានឹងវង្វេង។

**បញ្ហាខ្មែរជាក់លាក់ដែលអ្នកត្រូវដឹង៖**

១. **`height` ត្រូវតែប្រហែល 1.7** — អក្សរខ្មែរមានស្រៈលើ (េ ើ ៊ ់) និងជើងអក្សរខាងក្រោម (្ក ្រ ្ត)។ ជាមួយ `height` លំនាំដើម (ដែលអាស្រ័យលើ font) ជើងអក្សរបន្ទាត់មួយនឹងប៉ះស្រៈបន្ទាត់បន្ទាប់។ នេះកាន់តែធ្ងន់ធ្ងរនៅលើ tablet ដែលបន្ទាត់វែងជាង ហើយកថាខណ្ឌក្រាស់ជាង។

២. **ការកាត់បន្ទាត់ (line breaking)** — ខ្មែរមិនប្រើដកឃ្លារវាងពាក្យទេ។ Flutter ពឹងលើ ICU line-break algorithm សម្រាប់ការកាត់។ នៅលើអេក្រង់ធំដែលមានទីធ្លាច្រើន បញ្ហានេះកម្រលេចធ្លោ ប៉ុន្តែក្នុង pane ចង្អៀត (ឧ. list pane 360px) អ្នកអាចឃើញការកាត់ខុសកន្លែង។ ដំណោះស្រាយជាក់ស្តែងគឺបញ្ចូល **zero-width space (U+200B)** នៅចន្លោះពាក្យក្នុងអត្ថបទដែលអ្នកគ្រប់គ្រង។

៣. **កុំកំណត់ `maxLines` ដោយពឹងលើការរាប់តួអក្សរ** — សូមប្រើ `TextOverflow.ellipsis` ជាមួយ `maxLines` ហើយទុកឲ្យ engine កាត់។

### 💻 កូដ

```dart
// lib/core/layout/readable_width.dart
class ReadableWidth extends StatelessWidget {
  const ReadableWidth({super.key, required this.child, this.maxWidth});

  final Widget child;
  final double? maxWidth;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: ConstrainedBox(
        constraints: BoxConstraints(
          maxWidth: maxWidth ?? context.dimens.maxContentWidth,
        ),
        child: child,
      ),
    );
  }
}
```

Theme សម្រាប់អក្សរខ្មែរ៖

```dart
// lib/core/theme/khmer_typography.dart
import 'package:flutter/material.dart';
import 'package:google_fonts/google_fonts.dart';

TextTheme khmerTextTheme(TextTheme base) {
  // height: 1.7 gives Khmer subscripts and superscript vowels room to render.
  TextStyle? kh(TextStyle? s, {double height = 1.7}) =>
      s?.copyWith(height: height, fontFamily: 'NotoSansKhmer');

  return base.copyWith(
    displayLarge: kh(base.displayLarge, height: 1.4),
    displayMedium: kh(base.displayMedium, height: 1.4),
    displaySmall: kh(base.displaySmall, height: 1.4),
    headlineLarge: kh(base.headlineLarge, height: 1.5),
    headlineMedium: kh(base.headlineMedium, height: 1.5),
    headlineSmall: kh(base.headlineSmall, height: 1.5),
    titleLarge: kh(base.titleLarge, height: 1.6),
    titleMedium: kh(base.titleMedium, height: 1.6),
    titleSmall: kh(base.titleSmall, height: 1.6),
    bodyLarge: kh(base.bodyLarge),
    bodyMedium: kh(base.bodyMedium),
    bodySmall: kh(base.bodySmall),
    labelLarge: kh(base.labelLarge, height: 1.5),
    labelMedium: kh(base.labelMedium, height: 1.5),
    labelSmall: kh(base.labelSmall, height: 1.5),
  );
}
```

សម្គាល់៖ ចំណងជើងធំ (`display*`) ប្រើ `height` តូចជាង ព្រោះនៅទំហំធំចន្លោះបន្ទាត់ 1.7 មើលទៅឃ្លាតពេក។

**ការសម្របទំហំអក្សរតាមទំហំអេក្រង់** — កុំគុណទំហំអក្សរដោយផ្ទាល់តាមទទឹងអេក្រង់ (`fontSize: width * 0.05`)។ វាបំផ្លាញការកំណត់ភាពងាយស្រួលរបស់អ្នកប្រើ។ ជំនួសវិញ សូមប្រើកម្រិត typography ខុសគ្នា៖

```dart
Text(
  article.title,
  style: switch (LayoutScope.of(context).sizeClass) {
    WindowSizeClass.compact => Theme.of(context).textTheme.headlineSmall,
    WindowSizeClass.medium => Theme.of(context).textTheme.headlineMedium,
    _ => Theme.of(context).textTheme.headlineLarge,
  },
)
```

**គោរពការកំណត់របស់អ្នកប្រើ** — ចាប់ពី Flutter 3.16 មក `textScaleFactor` ត្រូវបានលុបចោល ជំនួសដោយ `TextScaler`៖

```dart
// ❌ Deprecated
final scale = MediaQuery.of(context).textScaleFactor;

// ✅ Current
final scaler = MediaQuery.textScalerOf(context);
final scaledSize = scaler.scale(16);

// Clamp instead of disabling, so the layout survives 200% scaling
// without ignoring accessibility settings entirely.
MediaQuery(
  data: MediaQuery.of(context).copyWith(
    textScaler: MediaQuery.textScalerOf(context).clamp(
      minScaleFactor: 1.0,
      maxScaleFactor: 1.6,
    ),
  ),
  child: child,
)
```

### 🔬 ភស្តុតាង — Golden test សម្រាប់ការធ្លាក់ជើងអក្សរ

```dart
testWidgets('Khmer paragraph does not clip subscripts at height 1.7',
    (tester) async {
  await tester.pumpWidget(
    MaterialApp(
      home: Scaffold(
        body: Center(
          child: SizedBox(
            width: 600,
            child: Text(
              'ការសរសេរកម្មវិធីដោយប្រើ Flutter គឺជាការបង្កើតកម្មវិធីដែល'
              'អាចដំណើរការលើប្រព័ន្ធប្រតិបត្តិការច្រើនក្នុងកូដតែមួយ។',
              style: TextStyle(fontSize: 18, height: 1.7),
            ),
          ),
        ),
      ),
    ),
  );

  await expectLater(
    find.byType(Text),
    matchesGoldenFile('goldens/khmer_paragraph_600.png'),
  );
});
```

ដំណើរការ `flutter test --update-goldens` ម្តង រួចមើលរូបភាពដោយភ្នែក។ ប្តូរ `height` ទៅ `1.0` រួចដំណើរការ `flutter test` ម្តងទៀត — តេស្តនឹងធ្លាក់ ហើយ Flutter នឹងបង្កើតឯកសារ diff ដែលបង្ហាញច្បាស់ថាជើងអក្សរត្រូវបានកាត់។ នេះជាភស្តុតាងដែលមើលឃើញផ្ទាល់ភ្នែក។

---

## 20.10 Dialog, Bottom Sheet និង Side Sheet

### 📖 គោលគំនិត

នៅលើទូរស័ព្ទ modal bottom sheet ជាមធ្យោបាយស្តង់ដារ។ នៅលើ tablet វាមានបញ្ហា៖ sheet ដែលលាតពេញ 1280px ហើយខ្ពស់តែ 200px មើលទៅដូចជាបន្ទះចម្លែក ហើយវាឆ្ងាយពីកន្លែងដែលអ្នកប្រើកំពុងសម្លឹង។

ការណែនាំ Material 3៖

| ទំហំ | ជម្រើសល្អបំផុត |
|---|---|
| Compact | `showModalBottomSheet` (លាតពេញទទឹង) |
| Medium | `showModalBottomSheet` ជាមួយ `constraints` កំណត់ទទឹង |
| Expanded+ | `showDialog` (កណ្តាល) ឬ **side sheet** (រុញចេញពីគែម) |

### 💻 កូដ — មុខងារបង្ហាញដែលសម្របតាមទំហំ

```dart
// lib/core/layout/adaptive_modal.dart
Future<T?> showAdaptiveSheet<T>({
  required BuildContext context,
  required WidgetBuilder builder,
  String? title,
}) {
  final sizeClass = LayoutScope.of(context).sizeClass;

  return switch (sizeClass) {
    WindowSizeClass.compact => showModalBottomSheet<T>(
        context: context,
        isScrollControlled: true,
        useSafeArea: true,
        builder: builder,
      ),
    WindowSizeClass.medium => showModalBottomSheet<T>(
        context: context,
        isScrollControlled: true,
        useSafeArea: true,
        // Material caps a bottom sheet at 640 on medium windows.
        constraints: const BoxConstraints(maxWidth: 640),
        builder: builder,
      ),
    _ => showDialog<T>(
        context: context,
        builder: (context) => Dialog(
          child: ConstrainedBox(
            constraints: const BoxConstraints(maxWidth: 560, maxHeight: 720),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                if (title != null)
                  Padding(
                    padding: const EdgeInsets.fromLTRB(24, 24, 24, 8),
                    child: Text(
                      title,
                      style: Theme.of(context).textTheme.headlineSmall,
                    ),
                  ),
                Flexible(child: builder(context)),
              ],
            ),
          ),
        ),
      ),
  };
}
```

### 💻 កូដ — Side Sheet ជាប់ (persistent)

Side sheet គឺជា pane ទីបីដែលរុញចេញពីគែមខាងស្តាំ ដោយ **មិន** បិទបាំងមាតិកា។ វាល្អសម្រាប់ការតម្រង (filters) ឬព័ត៌មានបន្ថែម។

```dart
class SideSheetScaffold extends StatelessWidget {
  const SideSheetScaffold({
    super.key,
    required this.body,
    required this.sheet,
    required this.isOpen,
    this.sheetWidth = 320,
  });

  final Widget body;
  final Widget sheet;
  final bool isOpen;
  final double sheetWidth;

  @override
  Widget build(BuildContext context) {
    final canDock = LayoutScope.of(context).sizeClass.supportsTwoPane;

    // On small windows the sheet must be modal — there is no room to dock it.
    if (!canDock) return body;

    return Row(
      children: [
        Expanded(child: body),
        // AnimatedContainer + ClipRect gives a smooth slide without
        // overflow errors during the transition.
        ClipRect(
          child: AnimatedContainer(
            duration: const Duration(milliseconds: 250),
            curve: Curves.easeInOutCubic,
            width: isOpen ? sheetWidth : 0,
            child: OverflowBox(
              alignment: Alignment.centerLeft,
              maxWidth: sheetWidth,
              child: SizedBox(width: sheetWidth, child: sheet),
            ),
          ),
        ),
      ],
    );
  }
}
```

សម្គាល់ការប្រើ `OverflowBox` — បើគ្មានវាទេ មាតិកា sheet នឹងត្រូវបង្ខំឲ្យតូចទៅ 0 ក្នុងអំឡុង animation ហើយបង្កើត layout error។ `OverflowBox` អនុញ្ញាតឲ្យកូនរក្សាទទឹងពេញ ខណៈពេលដែល `ClipRect` លាក់ផ្នែកដែលហួស។

### 🔬 ភស្តុតាង

```dart
testWidgets('sheet is a bottom sheet on phone and a dialog on tablet',
    (tester) async {
  Future<void> open(Size size) async {
    tester.view.devicePixelRatio = 1.0;
    tester.view.physicalSize = size;
    await tester.pumpWidget(const MyApp());
    await tester.tap(find.byKey(const Key('open-filters')));
    await tester.pumpAndSettle();
  }
  addTearDown(tester.view.reset);

  await open(const Size(400, 800));
  expect(find.byType(BottomSheet), findsOneWidget);
  expect(find.byType(Dialog), findsNothing);

  await open(const Size(1100, 800));
  expect(find.byType(Dialog), findsOneWidget);
  expect(find.byType(BottomSheet), findsNothing);
});
```

---

## 20.11 Input៖ Mouse, Keyboard, Hover, Scrollbar

### 📖 គោលគំនិត

Tablet ជាច្រើនត្រូវបានប្រើជាមួយ keyboard ភ្ជាប់ (iPad Magic Keyboard, Android tablet ជាមួយ Bluetooth keyboard) ហើយកម្មវិធីដដែលនោះក៏ដំណើរការលើ desktop និង web។ ការមិនគាំទ្រ input ទាំងនេះធ្វើឲ្យកម្មវិធីមានអារម្មណ៍ថា "គ្រាន់តែជាកម្មវិធីទូរស័ព្ទដែលពង្រីក"។

អ្វីដែលអ្នកត្រូវបន្ថែម៖

១. **Hover state** — កាតគួរបន្លិចនៅពេល mouse ឆ្លងកាត់
២. **Focus traversal** — ចុច Tab គួរផ្លាស់ទីត្រឹមត្រូវ
៣. **Keyboard shortcuts** — Ctrl+F ស្វែងរក, Esc បិទ, ព្រួញឡើង/ចុះក្នុងបញ្ជី
៤. **Scrollbar ដែលមើលឃើញ** — នៅលើ desktop/tablet ដែលមាន mouse អ្នកប្រើរំពឹងឃើញ scrollbar
៥. **ការចុចខាងស្តាំ** សម្រាប់ context menu

### 💻 កូដ — Shortcuts និង Actions

```dart
// lib/features/messages/message_shortcuts.dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

class NextMessageIntent extends Intent {
  const NextMessageIntent();
}

class PreviousMessageIntent extends Intent {
  const PreviousMessageIntent();
}

class SearchIntent extends Intent {
  const SearchIntent();
}

class MessageShortcuts extends StatelessWidget {
  const MessageShortcuts({
    super.key,
    required this.child,
    required this.onNext,
    required this.onPrevious,
    required this.onSearch,
  });

  final Widget child;
  final VoidCallback onNext;
  final VoidCallback onPrevious;
  final VoidCallback onSearch;

  @override
  Widget build(BuildContext context) {
    return Shortcuts(
      shortcuts: <ShortcutActivator, Intent>{
        const SingleActivator(LogicalKeyboardKey.arrowDown):
            const NextMessageIntent(),
        const SingleActivator(LogicalKeyboardKey.arrowUp):
            const PreviousMessageIntent(),
        // meta on macOS/iOS, control elsewhere — SingleActivator handles
        // neither automatically, so declare both.
        const SingleActivator(LogicalKeyboardKey.keyF, control: true):
            const SearchIntent(),
        const SingleActivator(LogicalKeyboardKey.keyF, meta: true):
            const SearchIntent(),
      },
      child: Actions(
        actions: <Type, Action<Intent>>{
          NextMessageIntent: CallbackAction<NextMessageIntent>(
            onInvoke: (_) {
              onNext();
              return null;
            },
          ),
          PreviousMessageIntent: CallbackAction<PreviousMessageIntent>(
            onInvoke: (_) {
              onPrevious();
              return null;
            },
          ),
          SearchIntent: CallbackAction<SearchIntent>(
            onInvoke: (_) {
              onSearch();
              return null;
            },
          ),
        },
        child: Focus(autofocus: true, child: child),
      ),
    );
  }
}
```

### 💻 កូដ — Hover និង Scrollbar

```dart
class HoverableCard extends StatefulWidget {
  const HoverableCard({super.key, required this.child, required this.onTap});

  final Widget child;
  final VoidCallback onTap;

  @override
  State<HoverableCard> createState() => _HoverableCardState();
}

class _HoverableCardState extends State<HoverableCard> {
  bool _hovered = false;

  @override
  Widget build(BuildContext context) {
    // Only pointer devices get hover affordances.
    final showHover = LayoutScope.of(context).interaction == InteractionMode.pointer;

    return MouseRegion(
      cursor: SystemMouseCursors.click,
      onEnter: (_) => setState(() => _hovered = true),
      onExit: (_) => setState(() => _hovered = false),
      child: AnimatedContainer(
        duration: const Duration(milliseconds: 150),
        transform: Matrix4.translationValues(
          0, showHover && _hovered ? -2 : 0, 0,
        ),
        decoration: BoxDecoration(
          borderRadius: BorderRadius.circular(context.dimens.cardRadius),
          boxShadow: showHover && _hovered
              ? [BoxShadow(blurRadius: 12, color: Colors.black.withValues(alpha: 0.12))]
              : const [],
        ),
        child: InkWell(
          onTap: widget.onTap,
          borderRadius: BorderRadius.circular(context.dimens.cardRadius),
          child: widget.child,
        ),
      ),
    );
  }
}
```

> **ចំណាំ API៖** `Color.withOpacity()` ត្រូវបានលុបចោលនៅ Flutter 3.27 ជំនួសដោយ `withValues(alpha: ...)` ដែលមិនបាត់បង់ភាពជាក់លាក់ក្នុង wide-gamut color space។

Scrollbar ដែលមើលឃើញជានិច្ចនៅលើឧបករណ៍ pointer៖

```dart
final controller = ScrollController();

Scrollbar(
  controller: controller,
  thumbVisibility:
      LayoutScope.of(context).interaction == InteractionMode.pointer,
  child: ListView.builder(
    controller: controller,   // must be the SAME controller
    itemBuilder: ...,
  ),
)
```

**កំហុសទូទៅ៖** ការភ្លេចបញ្ជូន `controller` ដដែលទៅទាំង `Scrollbar` និង `ListView` នឹងបង្កើត assertion error នៅ runtime។

### 🔬 ភស្តុតាង

```dart
testWidgets('arrow down selects the next message', (tester) async {
  await tester.pumpWidget(const MyApp());
  await tester.pumpAndSettle();

  await tester.sendKeyEvent(LogicalKeyboardKey.arrowDown);
  await tester.pumpAndSettle();

  expect(find.text('សារទី ២'), findsOneWidget);
});

testWidgets('hover raises the card only on pointer devices', (tester) async {
  await tester.pumpWidget(const MyApp());

  final gesture = await tester.createGesture(kind: PointerDeviceKind.mouse);
  await gesture.addPointer(location: Offset.zero);
  addTearDown(gesture.removePointer);

  await gesture.moveTo(tester.getCenter(find.byType(HoverableCard).first));
  await tester.pumpAndSettle();

  final container = tester.widget<AnimatedContainer>(
    find.descendant(
      of: find.byType(HoverableCard).first,
      matching: find.byType(AnimatedContainer),
    ),
  );
  expect((container.decoration as BoxDecoration).boxShadow, isNotEmpty);
});
```

---

## 20.12 Foldable និង `displayFeatures`

### 📖 គោលគំនិត

ឧបករណ៍បត់បាន (Galaxy Fold, Pixel Fold, Surface Duo) បង្ហាញបញ្ហាថ្មី៖ អេក្រង់មួយអាចមាន **ស្នាមបត់ (hinge/fold)** ឆ្លងកាត់កណ្តាល។ ការដាក់ប៊ូតុងសំខាន់នៅត្រង់ស្នាមបត់មានន័យថាអ្នកប្រើមិនអាចចុចវាបានត្រឹមត្រូវ។

Flutter បង្ហាញព័ត៌មាននេះតាមរយៈ `MediaQuery.of(context).displayFeatures` ដែលជាបញ្ជីនៃ `DisplayFeature` ដែលនីមួយៗមាន `bounds`, `type` (`fold` ឬ `cutout`) និង `state` (`postureFlat` ឬ `postureHalfOpened`)។

`Scaffold`, `showDialog`, និង `showModalBottomSheet` ទទួលយក `anchorPoint` ដែលប្រាប់ Flutter ថាត្រូវដាក់មាតិកានៅផ្នែកណានៃអេក្រង់ដែលបែងចែក។

### 💻 កូដ

```dart
class FoldAwareTwoPane extends StatelessWidget {
  const FoldAwareTwoPane({super.key, required this.start, required this.end});

  final Widget start;
  final Widget end;

  @override
  Widget build(BuildContext context) {
    final mq = MediaQuery.of(context);
    final size = mq.size;

    // Find a vertical fold/hinge that splits the window left/right.
    final hinge = mq.displayFeatures.where((f) {
      return f.bounds.top <= 0 &&
          f.bounds.bottom >= size.height &&
          (f.type == DisplayFeatureType.fold ||
              f.type == DisplayFeatureType.hinge);
    }).firstOrNull;

    if (hinge != null) {
      // Lay the panes out around the hinge so nothing sits underneath it.
      return Row(
        children: [
          SizedBox(width: hinge.bounds.left, child: start),
          SizedBox(width: hinge.bounds.width),  // dead zone
          SizedBox(width: size.width - hinge.bounds.right, child: end),
        ],
      );
    }

    // No hinge — fall back to the normal breakpoint logic.
    return TwoPane(start: start, end: end, showEndOnly: false);
  }
}
```

សម្រាប់ dialog៖

```dart
showDialog(
  context: context,
  // Place the dialog in the left half of a folded device.
  anchorPoint: const Offset(0, 0),
  builder: (context) => const MyDialog(),
);
```

### 🔬 ភស្តុតាង

អ្នកមិនចាំបាច់មានឧបករណ៍បត់បានពិតដើម្បីតេស្តទេ។ ក្នុងតេស្តអ្នកអាចបញ្ចូល `displayFeatures` ដោយផ្ទាល់៖

```dart
testWidgets('panes avoid the hinge', (tester) async {
  await tester.pumpWidget(
    MediaQuery(
      data: const MediaQueryData(
        size: Size(1000, 800),
        displayFeatures: [
          DisplayFeature(
            bounds: Rect.fromLTRB(490, 0, 510, 800),
            type: DisplayFeatureType.hinge,
            state: DisplayFeatureState.postureFlat,
          ),
        ],
      ),
      child: const MaterialApp(
        home: FoldAwareTwoPane(start: Placeholder(), end: Placeholder()),
      ),
    ),
  );

  final panes = tester.widgetList<SizedBox>(find.byType(SizedBox)).toList();
  expect(panes[0].width, 490);   // left of the hinge
  expect(panes[1].width, 20);    // the hinge itself — kept empty
  expect(panes[2].width, 490);   // right of the hinge
});
```

Android Studio ក៏មាន emulator profile សម្រាប់ **7.6" Fold-in with outer display** និង **8" Fold-out** ដែលបញ្ចេញ `displayFeatures` ពិត។

---

## 20.13 ការធ្វើតេស្ត៖ Golden Test ច្រើនទំហំ

### 📖 គោលគំនិត

Layout responsive ខូចដោយស្ងាត់ៗ។ គ្មាន exception, គ្មាន crash — គ្រាន់តែធាតុមួយឃ្លាតទីតាំង ហើយគ្មាននរណាកត់សម្គាល់រហូតដល់អ្នកប្រើត្អូញត្អែរ។ ដំណោះស្រាយគឺ **golden test នៅទំហំសំខាន់ៗ** ដែលរត់ក្នុង CI។

ជ្រើសរើសទំហំតំណាងចំនួន ៤–៥ មិនមែន ២០ ទេ៖

| ឈ្មោះ | ទំហំ | តំណាង |
|---|---|---|
| phone-portrait | 400 × 800 | ទូរស័ព្ទធម្មតា |
| phone-landscape | 800 × 400 | ទូរស័ព្ទផ្តេក / split-screen |
| tablet-portrait | 800 × 1280 | tablet បញ្ឈរ |
| tablet-landscape | 1280 × 800 | tablet ផ្តេក (ករណីសំខាន់បំផុត) |
| desktop | 1600 × 1000 | បង្អួច desktop |

### 💻 កូដ — Helper សម្រាប់ Golden ច្រើនទំហំ

```dart
// test/helpers/golden_helpers.dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

const goldenSizes = <String, Size>{
  'phone_portrait': Size(400, 800),
  'phone_landscape': Size(800, 400),
  'tablet_portrait': Size(800, 1280),
  'tablet_landscape': Size(1280, 800),
  'desktop': Size(1600, 1000),
};

/// Renders [builder] at every golden size and compares against
/// `goldens/<name>__<sizeName>.png`.
Future<void> expectGoldenAcrossSizes(
  WidgetTester tester,
  String name,
  WidgetBuilder builder, {
  Map<String, Size> sizes = goldenSizes,
}) async {
  for (final entry in sizes.entries) {
    tester.view.devicePixelRatio = 1.0;
    tester.view.physicalSize = entry.value;

    await tester.pumpWidget(
      MaterialApp(
        debugShowCheckedModeBanner: false,
        theme: appTheme,
        home: Builder(builder: builder),
      ),
    );
    await tester.pumpAndSettle();

    await expectLater(
      find.byType(MaterialApp),
      matchesGoldenFile('goldens/${name}__${entry.key}.png'),
    );
  }
  addTearDown(tester.view.reset);
}
```

ការប្រើ៖

```dart
// test/features/messages/messages_page_golden_test.dart
void main() {
  testWidgets('messages page across all size classes', (tester) async {
    await loadAppFonts();   // from golden_toolkit, or load Khmer font manually
    await expectGoldenAcrossSizes(
      tester,
      'messages_page',
      (context) => const MessagesPage(selectedId: '7'),
    );
  });
}
```

**សំខាន់សម្រាប់អក្សរខ្មែរ៖** ក្នុងបរិស្ថានតេស្ត Flutter ប្រើ font placeholder (ប្រអប់បួនជ្រុង) តាមលំនាំដើម។ ដើម្បីឲ្យ golden បង្ហាញអក្សរខ្មែរពិត អ្នកត្រូវផ្ទុក font ដោយដៃ៖

```dart
// test/flutter_test_config.dart
import 'dart:async';
import 'dart:io';
import 'package:flutter/services.dart';
import 'package:flutter_test/flutter_test.dart';

Future<void> testExecutable(FutureOr<void> Function() testMain) async {
  TestWidgetsFlutterBinding.ensureInitialized();

  final loader = FontLoader('NotoSansKhmer');
  loader.addFont(
    File('assets/fonts/NotoSansKhmer-Regular.ttf')
        .readAsBytes()
        .then((b) => ByteData.view(b.buffer)),
  );
  await loader.load();

  return testMain();
}
```

ឯកសារ `test/flutter_test_config.dart` ត្រូវបានរកឃើញដោយស្វ័យប្រវត្តិដោយ `flutter test` ហើយដំណើរការមុនតេស្តទាំងអស់។

### 💻 កូដ — CI

```yaml
# .github/workflows/goldens.yml
name: Golden tests
on: [pull_request]

jobs:
  goldens:
    # Golden files are pixel-comparisons — the OS must match the one that
    # generated them, otherwise font rendering differs by a few pixels.
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: subosito/flutter-action@v2
        with:
          channel: stable
          flutter-version: 3.47.1
      - run: flutter pub get
      - run: flutter test --tags golden
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: golden-failures
          path: '**/failures/*.png'
```

### 🔬 ភស្តុតាង

បន្ទាប់ពីអ្នករៀបចំរួច សូមធ្វើការពិសោធន៍នេះ៖ ប្តូរ breakpoint `supportsTwoPane` ពី `expanded` ទៅ `large` រួចដំណើរការ `flutter test`។ Golden `tablet_landscape` នឹងធ្លាក់ ហើយ Flutter នឹងសរសេរឯកសារបីទៅក្នុង `failures/`៖ `*_masterImage.png`, `*_testImage.png`, និង `*_isolatedDiff.png`។ ឯកសារ diff បង្ហាញច្បាស់ថា layout បានប្តូរពីពីរ pane ទៅមួយ pane។

---

## 20.14 បញ្ជីត្រួតពិនិត្យមុនចេញផ្សាយ

### សំណួរដែលត្រូវសួរខ្លួនឯង

**រចនាសម្ព័ន្ធ**
- [ ] គ្មាន `if (isTablet)` នៅក្នុងកូដ — មានតែ `WindowSizeClass` ប៉ុណ្ណោះ
- [ ] គ្មាន `dart:io` `Platform` ត្រូវបានប្រើសម្រាប់ការសម្រេចចិត្ត UI (វា crash លើ web)
- [ ] Breakpoint ត្រូវបានកំណត់នៅកន្លែងតែមួយ
- [ ] `MediaQuery.sizeOf` ត្រូវបានប្រើ មិនមែន `MediaQuery.of(context).size`
- [ ] `LayoutBuilder` ត្រូវបានប្រើសម្រាប់ការសម្រេចចិត្តខាងក្នុង component

**Layout**
- [ ] មានលំនាំ list-detail នៅ `expanded` ឡើងទៅ
- [ ] Navigation ប្តូរពី bar → rail → drawer
- [ ] អត្ថបទវែងមានដែនកំណត់ទទឹង (600–960)
- [ ] Grid ប្រើ `maxCrossAxisExtent` មិនមែន `fixedCrossAxisCount`
- [ ] គ្មានតម្លៃ pixel ថេរដែលធំជាង 400 សម្រាប់ធាតុដែលត្រូវលាត

**State**
- [ ] ការអូសបង្អួច (ឬបង្វិលឧបករណ៍) មិនបាត់បង់ scroll position
- [ ] ការអូសបង្អួច មិនបាត់បង់អត្ថបទដែលវាយក្នុង form
- [ ] ការជ្រើសរើសនៅតែរក្សា ពេលឆ្លងកាត់ breakpoint
- [ ] ប៊ូតុងថយក្រោយប្រព្រឹត្តត្រឹមត្រូវក្នុងរបៀបពីរ pane

**Input**
- [ ] Tab ធ្វើដំណើរតាមលំដាប់ដែលសមហេតុផល
- [ ] មាន keyboard shortcut យ៉ាងតិចសម្រាប់សកម្មភាពញឹកញាប់
- [ ] Scrollbar មើលឃើញនៅលើឧបករណ៍ pointer
- [ ] គោលដៅចុចយ៉ាងតិច 48dp លើឧបករណ៍ប៉ះ

**អក្សរខ្មែរ**
- [ ] `height: 1.7` សម្រាប់ style តួអត្ថបទ (និង 1.4–1.6 សម្រាប់ចំណងជើង)
- [ ] គ្មានជើងអក្សរត្រូវបានកាត់នៅគ្រប់ទំហំ
- [ ] `TextScaler` ត្រូវបាន clamp មិនមែនបិទ
- [ ] Font ខ្មែរត្រូវបានផ្ទុកក្នុងតេស្ត golden

**ការធ្វើតេស្ត**
- [ ] Golden test នៅទំហំយ៉ាងតិច ៤
- [ ] តេស្ត state-preservation នៅពេលឆ្លង breakpoint
- [ ] តេស្តលើ split-screen ពិតលើ Android tablet
- [ ] តេស្តលើឧបករណ៍បត់បាន (emulator profile គ្រប់គ្រាន់)

---

## សេចក្តីសង្ខេប

រឿងសំខាន់បំផុតបីយ៉ាងពីជំពូកនេះ៖

**១. វាស់ទីធ្លា មិនមែនវាស់ឧបករណ៍។** ការសម្រេចចិត្តគ្រប់យ៉ាងគួរផ្អែកលើទទឹងបង្អួចបច្ចុប្បន្ន តាមរយៈ `WindowSizeClass`។ ពាក្យថា "tablet" មិនគួរលេចនៅក្នុង logic កូដរបស់អ្នកទាល់តែសោះ។

**២. រក្សា State ពេលឆ្លង breakpoint។** នេះជាភាពខុសគ្នារវាងកម្មវិធីដែលមានអារម្មណ៍ថារឹងមាំ និងកម្មវិធីដែលមានអារម្មណ៍ថាខូច។ សរសេរតេស្តសម្រាប់វា — កុំពឹងលើការចាំ។

**៣. Layout ជាមុខងារនៃ (route, ទំហំ)។** URL ដដែលគួរដំណើរការនៅគ្រប់ទំហំ។ នេះធ្វើឲ្យ deep link, ការចែករំលែក, និងការស្តារឡើងវិញ ដំណើរការត្រឹមត្រូវដោយស្វ័យប្រវត្តិ។

---

## ការអានបន្ថែម (ប្រភពផ្លូវការ)

- `docs.flutter.dev` → General → Adaptive & responsive design
- `m3.material.io` → Foundations → Layout → Applying layout (window size classes)
- Flutter source: `packages/flutter/lib/src/widgets/media_query.dart` (`InheritedModel` aspects)
- Flutter source: `packages/flutter/lib/src/rendering/sliver_grid.dart` (រូបមន្តជួរឈរ)
- Package: `flutter_adaptive_scaffold` (ដោយក្រុម Flutter — ជាជម្រើសបើអ្នកមិនចង់សរសេរ scaffold ខ្លួនឯង)
