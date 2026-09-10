# 📚 សៀវភៅមេ: Widget ដែលមានស្រាប់ទាំងអស់ក្នុង Flutter

> **កំណែឯកសារ:** Flutter stable `3.47.1` · Dart `3.13.1` (ចេញផ្សាយ ១៩ សីហា ២០២៦)
> **ភាសា:** ការពន្យល់ជាភាសាខ្មែរ · កូដឧទាហរណ៍ជាភាសាអង់គ្លេស
> **គោលបំណង:** ជាសៀវភៅយោង (reference) ពេញលេញសម្រាប់ built-in widgets ទាំងអស់ក្នុង Flutter SDK

---

## 🧭 របៀបប្រើសៀវភៅនេះ

សៀវភៅនេះ **មិនមែន** សម្រាប់អានពីដើមដល់ចប់ក្នុងមួយថ្ងៃទេ។ វាត្រូវបានរៀបចំជា **កាតាឡុក (catalog)** ដែលអ្នកអាចបើកមើលពេលអ្នកត្រូវការ widget ណាមួយ។

រាល់ widget នីមួយៗនឹងមានទម្រង់ដូចនេះ:

| ផ្នែក | អត្ថន័យ |
|---|---|
| **📖 វាជាអ្វី** | ការពន្យល់ខ្លីៗថា widget នេះធ្វើអ្វី និងពេលណាគួរប្រើ |
| **⚙️ Parameters សំខាន់** | property ដែលអ្នកនឹងប្រើញឹកញាប់បំផុត |
| **💻 កូដ** | ឧទាហរណ៍ដែលអាចដំណើរការបាន (runnable) |
| **⚠️ អន្ទាក់** | កំហុសទូទៅ ឬ gotcha ដែលអ្នកគួរដឹងជាមុន |

---

## 📑 មាតិកា

- [ជំពូក ០ — គ្រឹះ: Widget ជាអ្វីពិតប្រាកដ?](#ជំពូក-០--គ្រឹះ-widget-ជាអ្វីពិតប្រាកដ)
- [ជំពូក ១ — Widgets មូលដ្ឋាន (Basics)](#ជំពូក-១--widgets-មូលដ្ឋាន-basics)
- [ជំពូក ២ — Layout: Single-child](#ជំពូក-២--layout-single-child)
- [ជំពូក ៣ — Layout: Multi-child](#ជំពូក-៣--layout-multi-child)
- [ជំពូក ៤ — Text និង Typography](#ជំពូក-៤--text-និង-typography)
- [ជំពូក ៥ — Images, Icons និង Assets](#ជំពូក-៥--images-icons-និង-assets)
- [ជំពូក ៦ — Input និង Forms](#ជំពូក-៦--input-និង-forms)
- [ជំពូក ៧ — Scrolling និង Slivers](#ជំពូក-៧--scrolling-និង-slivers)
- [ជំពូក ៨ — Material Components](#ជំពូក-៨--material-components)
- [ជំពូក ៩ — Cupertino (iOS style)](#ជំពូក-៩--cupertino-ios-style)
- [ជំពូក ១០ — Animation និង Motion](#ជំពូក-១០--animation-និង-motion)
- [ជំពូក ១១ — Interaction: Gestures និង Navigation](#ជំពូក-១១--interaction-gestures-និង-navigation)
- [ជំពូក ១២ — Painting និង Visual Effects](#ជំពូក-១២--painting-និង-visual-effects)
- [ជំពូក ១៣ — Async Widgets](#ជំពូក-១៣--async-widgets)
- [ជំពូក ១៤ — Accessibility និង Semantics](#ជំពូក-១៤--accessibility-និង-semantics)
- [ជំពូក ១៥ — Widgets កម្រប្រើ តែសង្គ្រោះជីវិត](#ជំពូក-១៥--widgets-កម្រប្រើ-តែសង្គ្រោះជីវិត)
- [ឧបសម្ព័ន្ធ — តារាងជ្រើសរើសរហ័ស](#ឧបសម្ព័ន្ធ--តារាងជ្រើសរើសរហ័ស)

---

# ជំពូក ០ — គ្រឹះ: Widget ជាអ្វីពិតប្រាកដ?

មុននឹងចូលទៅមើល widget នីមួយៗ អ្នកត្រូវយល់ថា **Flutter មាន widget តែ ៥ ប្រភេទប៉ុណ្ណោះ** នៅកម្រិតឫសគល់។ រាល់ widget ទាំង ៤០០+ ក្នុងសៀវភៅនេះ គឺជាកូនចៅរបស់ប្រភេទណាមួយក្នុងចំណោម ៥ នេះ។

```
Widget (abstract)
│
├── StatelessWidget ────── គ្មាន state ផ្ទាល់ខ្លួន · build() ម្តងហើយចប់
├── StatefulWidget ─────── មាន State object ដាច់ដោយឡែក · អាច setState() បាន
├── ProxyWidget ────────── រុំកូនដោយមិនប្តូររូបរាង
│   ├── InheritedWidget ── បញ្ជូនទិន្នន័យចុះក្រោមតាមមែកធាង (O(1) lookup)
│   └── ParentDataWidget ─ ដាក់ metadata ឲ្យ parent (ឧ. Expanded, Positioned)
└── RenderObjectWidget ─── បង្កើត RenderObject ពិតប្រាកដដែលគូរលើអេក្រង់
    ├── LeafRenderObjectWidget ────── គ្មានកូន (ឧ. Text ខាងក្នុង)
    ├── SingleChildRenderObjectWidget ─ កូនមួយ (ឧ. Padding, Align)
    └── MultiChildRenderObjectWidget ── កូនច្រើន (ឧ. Row, Column, Stack)
```

### 🔬 ភស្តុតាង — ពិនិត្យដោយខ្លួនឯង

```dart
import 'package:flutter/material.dart';

void main() {
  // Padding គឺជា SingleChildRenderObjectWidget
  print(const Padding(padding: EdgeInsets.zero) is RenderObjectWidget); // true
  print(const Padding(padding: EdgeInsets.zero) is StatelessWidget);    // false

  // ចំណែក Container ជា StatelessWidget ធម្មតា — វាគ្រាន់តែ compose widget ដទៃ!
  print(Container() is StatelessWidget);                                 // true
  print(Container() is RenderObjectWidget);                              // false
}
```

> 💡 **ចំណុចសំខាន់:** `Container` **មិនមែន** ជា widget ពិតប្រាកដទេ។ វាគ្រាន់តែជា "កញ្ចប់រុំ" ដែលបញ្ចូល `Padding` + `DecoratedBox` + `ConstrainedBox` + `Align` + `Transform` ចូលគ្នា។ ដូច្នេះបើអ្នកប្រើ `Container` ដើម្បីតែដាក់ padding អ្នកកំពុងបង្កើត widget ៥ ជាន់ដោយឥតប្រយោជន៍។

---

## 🎯 វិន័យ ៣ យ៉ាងសម្រាប់អ្នកចាប់ផ្តើម

**១. ប្រើ widget ដែលចង្អុលច្បាស់ ជាជាង Container**

```dart
// ❌ អាក្រក់ — បង្កើត widget ៣ ជាន់ដោយឥតប្រយោជន៍
Container(padding: EdgeInsets.all(8), child: Text('សួស្តី'))

// ✅ ល្អ — widget តែមួយ និងជា const បានទៀត
const Padding(padding: EdgeInsets.all(8), child: Text('សួស្តី'))
```

**២. ដាក់ `const` គ្រប់ទីកន្លែងដែលអាចដាក់បាន**

`const` widget ត្រូវបាន canonicalize ដោយ Dart។ ពេល `build()` ដំណើរការឡើងវិញ Flutter ប្រៀបធៀបដោយ `identical()` រួចរំលងផ្នែកនោះទាំងស្រុង។

**៣. ភាសាខ្មែរត្រូវការ `height` ក្នុង TextStyle**

```dart
// ✅ ចាំបាច់សម្រាប់អក្សរខ្មែរ — បើមិនដាក់ ជើងអក្សរនឹងត្រូវកាត់
const TextStyle(fontSize: 16, height: 1.7)
```

---
# ជំពូក ១ — Widgets មូលដ្ឋាន (Basics)

នេះជា widget ១១ ដែលអ្នកនឹងប្រើក្នុងគ្រប់ app។ បើអ្នកចេះតែប៉ុណ្ណេះ អ្នកអាចសង់ app សាមញ្ញបានហើយ។

---

### `Container`

**📖 វាជាអ្វី**
ជា widget "ចេះគ្រប់យ៉ាង" — វាអាចដាក់ padding, margin, ពណ៌, border, ស្រមោល, កំណត់ទំហំ និង transform ក្នុងពេលតែមួយ។ ខាងក្នុងវាគឺជាការ compose widget តូចៗជាច្រើនចូលគ្នា។

**⚙️ Parameters សំខាន់**

| Parameter | អត្ថន័យ |
|---|---|
| `padding` | គម្លាតខាង**ក្នុង** រវាងស៊ុម និងកូន |
| `margin` | គម្លាតខាង**ក្រៅ** ស៊ុម |
| `color` | ពណ៌ផ្ទៃខាងក្រោយ (មិនអាចប្រើជាមួយ `decoration`) |
| `decoration` | `BoxDecoration` — border, radius, gradient, shadow |
| `width` / `height` | ទំហំថេរ |
| `constraints` | ព្រំដែនអប្បបរមា/អតិបរមា |
| `alignment` | តម្រឹមកូនខាងក្នុង |
| `transform` | Matrix4 សម្រាប់បង្វិល/ពង្រីក |

**💻 កូដ**

```dart
Container(
  margin: const EdgeInsets.symmetric(horizontal: 16, vertical: 8),
  padding: const EdgeInsets.all(20),
  decoration: BoxDecoration(
    gradient: const LinearGradient(
      colors: [Color(0xFF6A11CB), Color(0xFF2575FC)],
      begin: Alignment.topLeft,
      end: Alignment.bottomRight,
    ),
    borderRadius: BorderRadius.circular(16),
    boxShadow: [
      BoxShadow(
        color: Colors.black.withValues(alpha: 0.2),
        blurRadius: 12,
        offset: const Offset(0, 4),
      ),
    ],
  ),
  child: const Text(
    'សួស្តី ពិភពលោក',
    style: TextStyle(color: Colors.white, fontSize: 18, height: 1.7),
  ),
)
```

**⚠️ អន្ទាក់**
- ដាក់ `color` និង `decoration` ព្រមគ្នា = **crash**។ ដាក់ពណ៌ក្នុង `BoxDecoration(color: ...)` វិញ។
- `Container` គ្មានកូន និងគ្មានទំហំកំណត់ → វានឹង**ពង្រីកអស់ទំហំដែលមាន**។ តែបើវាមានកូន វានឹងតូចប៉ុនកូន។ ឥរិយាបថនេះច្រឡំមនុស្សច្រើនណាស់។
- ជៀសវាងប្រើ `Container` ដើម្បីតែធ្វើការមួយយ៉ាង។ ប្រើ `Padding`, `ColoredBox`, `SizedBox`, ឬ `DecoratedBox` វិញ — ពួកវាអាចជា `const`។

---

### `Row`

**📖 វាជាអ្វី**
រៀបកូនៗ **ផ្ដេក** (ពីឆ្វេងទៅស្តាំ)។

**⚙️ Parameters សំខាន់**

| Parameter | អត្ថន័យ |
|---|---|
| `mainAxisAlignment` | តម្រឹមតាមអក្សផ្តេក (start, end, center, spaceBetween, spaceAround, spaceEvenly) |
| `crossAxisAlignment` | តម្រឹមតាមអក្សបញ្ឈរ (start, end, center, stretch, baseline) |
| `mainAxisSize` | `max` (ពេញ) ឬ `min` (តូចប៉ុនកូន) |
| `spacing` | គម្លាតរវាងកូន — **ថ្មីតាំងពី Flutter 3.27** |

**💻 កូដ**

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.spaceBetween,
  crossAxisAlignment: CrossAxisAlignment.center,
  spacing: 12, // លែងត្រូវការ SizedBox រវាងកូនទៀតហើយ
  children: [
    const Icon(Icons.person),
    const Expanded(
      child: Text('ឈ្មោះអ្នកប្រើ', style: TextStyle(height: 1.7)),
    ),
    IconButton(icon: const Icon(Icons.more_vert), onPressed: () {}),
  ],
)
```

**⚠️ អន្ទាក់**
- ដាក់ `Text` វែងក្នុង `Row` ដោយគ្មាន `Expanded` ឬ `Flexible` → **overflow ពណ៌លឿង-ខ្មៅ**។ នេះជាកំហុសលេខ ១ របស់អ្នកចាប់ផ្តើម។
- ដាក់ scrollable (ឧ. `ListView`) ក្នុង `Row` ដោយផ្ទាល់ → error "unbounded width"។ ត្រូវរុំដោយ `Expanded`។

---

### `Column`

**📖 វាជាអ្វី**
ដូច `Row` តែរៀបកូនៗ **បញ្ឈរ** (ពីលើចុះក្រោម)។ ចំណាំថា main axis គឺបញ្ឈរ ចំណែក cross axis គឺផ្ដេក — ផ្ទុយពី `Row`។

**💻 កូដ**

```dart
Column(
  mainAxisSize: MainAxisSize.min,       // តូចប៉ុនកូន មិនពេញអេក្រង់
  crossAxisAlignment: CrossAxisAlignment.start,
  spacing: 8,
  children: const [
    Text('ចំណងជើង', style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold, height: 1.7)),
    Text('អត្ថបទរងខាងក្រោម', style: TextStyle(color: Colors.grey, height: 1.7)),
  ],
)
```

**⚠️ អន្ទាក់**
- `Column` ក្នុង `SingleChildScrollView` ← ត្រឹមត្រូវ។ ប៉ុន្តែ `Column` ដែលមាន `Expanded` ក្នុង `SingleChildScrollView` ← **crash** ព្រោះកម្ពស់គ្មានព្រំដែន។
- បើអ្នកចង់ឲ្យវា scroll បាន ហើយមានកូនច្រើន → ប្រើ `ListView` វិញ។

---

### `Stack`

**📖 វាជាអ្វី**
ដាក់កូនៗ **ត្រួតលើគ្នា** ដូចជាការជង់ក្រដាស។ កូនចុងក្រោយក្នុង `children` នៅ**លើគេ**។

**💻 កូដ**

```dart
Stack(
  alignment: Alignment.center,
  clipBehavior: Clip.none, // អនុញ្ញាតឲ្យកូនហូរចេញក្រៅ
  children: [
    Image.network('https://picsum.photos/400/300', fit: BoxFit.cover),
    Positioned(
      bottom: 12,
      left: 12,
      child: Container(
        padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 4),
        color: Colors.black54,
        child: const Text('រូបភាព', style: TextStyle(color: Colors.white, height: 1.7)),
      ),
    ),
  ],
)
```

**⚠️ អន្ទាក់**
- កូនដែល**មិន**ត្រូវបានរុំដោយ `Positioned` ហៅថា *non-positioned* — ទំហំ `Stack` កំណត់ដោយកូនទាំងនេះ។ បើកូនទាំងអស់ជា `Positioned` `Stack` នឹងតូចបំផុតតាមដែលអាច។
- `Positioned` ត្រូវតែជាកូន**ផ្ទាល់**របស់ `Stack`។ រុំវាដោយ `Padding` មុន → crash។
- Like `Positioned` must be direct child of `Stack`

---

### `Text`

**📖 វាជាអ្វី**
បង្ហាញអត្ថបទមួយស្ទីល។ បើត្រូវការស្ទីលច្រើនក្នុងប្រយោគតែមួយ ប្រើ `Text.rich` ជាមួយ `TextSpan`។

**💻 កូដ**

```dart
// អត្ថបទធម្មតា
const Text(
  'ការសរសេរកម្មវិធីជាសិល្បៈមួយ',
  style: TextStyle(fontSize: 16, height: 1.7), // height សំខាន់ណាស់សម្រាប់ខ្មែរ
  maxLines: 2,
  overflow: TextOverflow.ellipsis,
  textAlign: TextAlign.start,
)

// អត្ថបទច្រើនស្ទីល
Text.rich(
  TextSpan(
    style: const TextStyle(height: 1.7, color: Colors.black87),
    children: [
      const TextSpan(text: 'តម្លៃសរុប: '),
      TextSpan(
        text: '\$29.99',
        style: TextStyle(
          fontWeight: FontWeight.bold,
          color: Colors.green.shade700,
        ),
      ),
    ],
  ),
)
```

**⚠️ អន្ទាក់**
- បើគ្មាន `height: 1.7` អក្សរខ្មែរដែលមានជើង (ក្ដ, ង្គ) និងស្រៈលើ នឹង**ត្រូវកាត់**។
- `overflow: TextOverflow.ellipsis` ដំណើរការតែពេលអត្ថបទមានទទឹងកំណត់ (ឧ. ក្នុង `Expanded`)។

---

### `Icon`

**📖 វាជាអ្វី**
គូររូបតំណាងពី icon font។ Flutter ភ្ជាប់មកជាមួយ Material Icons (`Icons.*`) និង Cupertino Icons (`CupertinoIcons.*`)។

**💻 កូដ**

```dart
const Icon(
  Icons.favorite_rounded,
  size: 32,
  color: Colors.redAccent,
  semanticLabel: 'ចូលចិត្ត', // សម្រាប់ screen reader
)
```

**⚠️ អន្ទាក់**
- មាន ៥ ស្ទីល: `Icons.home`, `.home_outlined`, `.home_rounded`, `.home_sharp`, `.home_filled`។ ជ្រើសមួយឲ្យស៊ីគ្នាទាំង app។
- បើមិនដាក់ `color` វានឹងយកពី `IconTheme` ដែលនៅជិតបំផុត។

---

### `Image`

**📖 វាជាអ្វី**
បង្ហាញរូបភាព។ មាន named constructor ៤ តាមប្រភពទិន្នន័យ។

**💻 កូដ**

```dart
// ១. ពី network — មាន placeholder និង error handling
Image.network(
  'https://picsum.photos/300',
  width: 300,
  height: 200,
  fit: BoxFit.cover,
  loadingBuilder: (context, child, progress) {
    if (progress == null) return child;
    return const Center(child: CircularProgressIndicator());
  },
  errorBuilder: (context, error, stack) =>
      const Icon(Icons.broken_image, size: 48),
)

// ២. ពី assets (ត្រូវប្រកាសក្នុង pubspec.yaml)
Image.asset('assets/images/logo.png', width: 120)

// ៣. ពី File
// Image.file(File('/path/to/photo.jpg'))

// ៤. ពី bytes ក្នុងអង្គចងចាំ
// Image.memory(uint8ListData)
```

**⚠️ អន្ទាក់**
- `Image.network` **គ្មាន disk cache**។ សម្រាប់ production ប្រើ package `cached_network_image` វិញ។
- `BoxFit` ដែលប្រើញឹកញាប់: `cover` (ពេញស៊ុម អាចកាត់), `contain` (ឃើញទាំងអស់ អាចមានចន្លោះ), `fill` (បង្ខំពេញ អាចខូចរូបរាង)។

---

### `Scaffold`

**📖 វាជាអ្វី**
ជាគ្រោងឆ្អឹងរបស់អេក្រង់មួយតាមស្តង់ដារ Material — មាន slot សម្រាប់ AppBar, body, FAB, drawer, bottom bar។

**💻 កូដ**

```dart
Scaffold(
  appBar: AppBar(title: const Text('ទំព័រដើម')),
  body: const Center(child: Text('មាតិកា')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: const Icon(Icons.add),
  ),
  drawer: const Drawer(child: Text('ម៉ឺនុយ')),
  bottomNavigationBar: NavigationBar(
    destinations: const [
      NavigationDestination(icon: Icon(Icons.home), label: 'ដើម'),
      NavigationDestination(icon: Icon(Icons.settings), label: 'កំណត់'),
    ],
  ),
  resizeToAvoidBottomInset: true, // រុញ body ឡើងលើពេលក្តារចុចលេចឡើង
)
```

**⚠️ អន្ទាក់**
- `ScaffoldMessenger.of(context).showSnackBar(...)` — មិនមែន `Scaffold.of()` ទៀតទេ (ចាប់តាំងពី Flutter 2.0)។
- ត្រូវការ `Scaffold` ថ្មីសម្រាប់ **រាល់អេក្រង់** មិនមែនតែមួយសម្រាប់ទាំង app ទេ។

---

### `AppBar`

**📖 វាជាអ្វី**
របារខាងលើដែលមានចំណងជើង ប៊ូតុងថយក្រោយ និងសកម្មភាព។

**💻 កូដ**

```dart
AppBar(
  title: const Text('ការកំណត់'),
  centerTitle: true,
  elevation: 0,
  scrolledUnderElevation: 3, // ស្រមោលលេចឡើងពេល scroll
  leading: IconButton(
    icon: const Icon(Icons.arrow_back),
    onPressed: () => Navigator.maybePop(context),
  ),
  actions: [
    IconButton(icon: const Icon(Icons.search), onPressed: () {}),
  ],
  bottom: const TabBar(tabs: [Tab(text: 'ថ្មី'), Tab(text: 'ចាស់')]),
)
```

---

### `ElevatedButton`

**📖 វាជាអ្វី**
ប៊ូតុងសំខាន់ (primary action) ដែលមានស្រមោល។ គ្រួសារប៊ូតុង Material មាន ៦: `ElevatedButton`, `FilledButton`, `FilledButton.tonal`, `OutlinedButton`, `TextButton`, `IconButton`។

**💻 កូដ**

```dart
ElevatedButton.icon(
  onPressed: () => debugPrint('ចុចហើយ'),
  icon: const Icon(Icons.save),
  label: const Text('រក្សាទុក'),
  style: ElevatedButton.styleFrom(
    backgroundColor: Colors.indigo,
    foregroundColor: Colors.white,
    padding: const EdgeInsets.symmetric(horizontal: 24, vertical: 14),
    shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
  ),
)
```

**⚠️ អន្ទាក់**
- `onPressed: null` = ប៊ូតុង**ដាច់** (disabled) ដោយស្វ័យប្រវត្តិ។ នេះជាវិធីត្រឹមត្រូវ — កុំប្រើ `IgnorePointer`។

---

### `Placeholder`

**📖 វាជាអ្វី**
គូរប្រអប់មានឆ្នូតឆ្លងកាត់ — ប្រើពេលអ្នកកំពុងសង់ layout ហើយមិនទាន់មានមាតិកា។

```dart
const Placeholder(color: Colors.deepOrange, strokeWidth: 2, fallbackHeight: 200)
```

---

### `FlutterLogo`

**📖 វាជាអ្វី**
រូបសញ្ញា Flutter ដែលអាចធ្វើ animation បាន។ មានប្រយោជន៍ពេលធ្វើ demo។

```dart
const FlutterLogo(size: 100, style: FlutterLogoStyle.horizontal)
```

---
# ជំពូក ២ — Layout: Single-child

Widget ក្នុងក្រុមនេះមានកូន**តែមួយ** ហើយធ្វើការមួយយ៉ាងច្បាស់លាស់។ ពួកវាភាគច្រើនអាចជា `const` ដែលមានន័យថាលឿនជាង `Container`។

> **🧠 ច្បាប់មាស:** *"Constraints go down. Sizes go up. Parent sets position."*
> Parent ប្រាប់ព្រំដែនទៅកូន → កូនសម្រេចទំហំខ្លួន → parent ដាក់ទីតាំង។ គ្រប់បញ្ហា layout ក្នុង Flutter ដោះស្រាយបានដោយច្បាប់នេះ។

---

### `Padding`

**📖 វាជាអ្វី** ដាក់គម្លាតជុំវិញកូន។ ស្រាល និងអាចជា `const`។

```dart
const Padding(
  padding: EdgeInsets.only(left: 16, top: 8, right: 16, bottom: 24),
  child: Text('មាតិកា', style: TextStyle(height: 1.7)),
)
```

**ប្រភេទ `EdgeInsets`:**
```dart
EdgeInsets.all(16)                                  // ទាំង ៤ ជ្រុង
EdgeInsets.symmetric(horizontal: 16, vertical: 8)   // ឆ្វេង-ស្តាំ, លើ-ក្រោម
EdgeInsets.only(left: 16)                           // ជ្រុងណាមួយ
EdgeInsets.fromLTRB(16, 8, 16, 8)                   // ឆ្វេង លើ ស្តាំ ក្រោម
EdgeInsetsDirectional.only(start: 16)               // គោរព RTL/LTR
```

---

### `Center`

**📖 វាជាអ្វី** ដាក់កូនកណ្តាល។ វាគឺជា `Align(alignment: Alignment.center)` ខ្លីៗ។

```dart
const Center(child: CircularProgressIndicator())
```

**⚠️** `Center` ពង្រីកខ្លួនអស់ទំហំដែលមាន។ បើមិនចង់ ប្រើ `widthFactor` / `heightFactor`។

---

### `Align`

**📖 វាជាអ្វី** ដាក់កូនតាមទីតាំងណាមួយក្នុងខ្លួន។

```dart
Align(
  alignment: Alignment.bottomRight,          // ឬ Alignment(0.5, -0.3) ជាកូអរដោនេ
  child: FloatingActionButton(onPressed: () {}, child: const Icon(Icons.add)),
)
```

**កូអរដោនេ `Alignment`:** `(-1,-1)` = លើឆ្វេង · `(0,0)` = កណ្តាល · `(1,1)` = ក្រោមស្តាំ។

---

### `SizedBox`

**📖 វាជាអ្វី** កំណត់ទំហំថេរ ឬបង្កើតចន្លោះទទេ។

```dart
const SizedBox(height: 16)                        // ចន្លោះបញ្ឈរ
const SizedBox(width: 200, height: 100, child: Card())
const SizedBox.expand(child: ColoredBox(color: Colors.blue))  // ពេញទាំងអស់
const SizedBox.shrink()                           // ទំហំសូន្យ — ជំនួស widget ទទេ
const SizedBox.square(dimension: 48, child: Icon(Icons.star))
```

**⚠️** បើ constraint ពី parent តឹងជាង `SizedBox` នឹងត្រូវបង្ខំតាម parent។ ត្រូវការ `UnconstrainedBox` ដើម្បីរត់ចេញ។

---

### `ConstrainedBox`

**📖 វាជាអ្វី** ដាក់ព្រំដែនអប្បបរមា/អតិបរមាទៅលើកូន។

```dart
ConstrainedBox(
  constraints: const BoxConstraints(
    minWidth: 100,
    maxWidth: 300,
    minHeight: 48,
    maxHeight: double.infinity,
  ),
  child: const Text('អត្ថបទដែលមិនអាចធំជាង ៣០០px', style: TextStyle(height: 1.7)),
)

// ខ្លីៗ
const BoxConstraints.tightFor(width: 200)     // បង្ខំទទឹងឲ្យប៉ុន ២០០
const BoxConstraints.loose(Size(300, 200))    // អតិបរមា មិនបង្ខំ
const BoxConstraints.expand()                 // ពេញទាំងអស់
```

---

### `UnconstrainedBox`

**📖 វាជាអ្វី** លុប constraint ពី parent ចោល ដើម្បីឲ្យកូនធំតាមចិត្តវា។ ប្រើពេលអ្នកចង់ដាក់ widget ធំក្នុងកន្លែងតូច ដោយអនុញ្ញាតឲ្យវាហូរចេញ។

```dart
UnconstrainedBox(
  constrainedAxis: Axis.vertical, // លុបតែ constraint ផ្ដេក
  child: Container(width: 500, height: 50, color: Colors.amber),
)
```

**⚠️** ជាញឹកញាប់នេះជាសញ្ញាថា layout របស់អ្នកមានបញ្ហារចនាសម្ព័ន្ធ។ ប្រើដោយប្រុងប្រយ័ត្ន។

---

### `AspectRatio`

**📖 វាជាអ្វី** បង្ខំសមាមាត្រ ទទឹង÷កម្ពស់។

```dart
AspectRatio(
  aspectRatio: 16 / 9,   // វីដេអូ
  child: Image.network('https://picsum.photos/640/360', fit: BoxFit.cover),
)
```

**តម្លៃទូទៅ:** `1/1` ការ៉េ · `4/3` រូបថតចាស់ · `16/9` វីដេអូ · `21/9` ភាពយន្ត។

---

### `FractionallySizedBox`

**📖 វាជាអ្វី** កំណត់ទំហំជា**ភាគរយ**នៃ parent។

```dart
const FractionallySizedBox(
  widthFactor: 0.8,   // ៨០% នៃទទឹង parent
  heightFactor: 0.5,  // ៥០% នៃកម្ពស់ parent
  child: ColoredBox(color: Colors.teal),
)
```

---

### `FittedBox`

**📖 វាជាអ្វី** បង្រួម/ពង្រីកកូនឲ្យស៊ីនឹងទំហំដែលមាន។ ល្អសម្រាប់ការពារ text overflow។

```dart
const FittedBox(
  fit: BoxFit.scaleDown,     // បង្រួមតែពេលចាំបាច់ មិនពង្រីក
  child: Text('អត្ថបទវែងខ្លាំងណាស់', style: TextStyle(fontSize: 40, height: 1.7)),
)
```

**⚠️** `FittedBox` **scale** មិនមែន **wrap** ទេ។ អក្សរនឹងតូចទៅ មិនមែនចុះបន្ទាត់ថ្មី។

---

### `IntrinsicHeight` / `IntrinsicWidth`

**📖 វាជាអ្វី** បង្ខំកូនៗឲ្យមានទំហំស្មើនឹងកូនធំបំផុត។ ឧទាហរណ៍បុរាណ: ធ្វើឲ្យ card ពីរក្នុង `Row` មានកម្ពស់ស្មើគ្នា។

```dart
IntrinsicHeight(
  child: Row(
    crossAxisAlignment: CrossAxisAlignment.stretch,
    children: [
      Expanded(child: Card(child: Text('អត្ថបទខ្លី' * 1))),
      const VerticalDivider(width: 1),
      Expanded(child: Card(child: Text('អត្ថបទវែងជាង' * 5))),
    ],
  ),
)
```

**⚠️ ថ្លៃណាស់!** វាធ្វើ layout pass **ពីរដង** (O(N²) ក្នុងករណីអាក្រក់)។ ជៀសវាងក្នុង `ListView` ដែលមានធាតុច្រើន។

---

### `LimitedBox`

**📖 វាជាអ្វី** ដាក់ព្រំដែន **តែពេលកូនស្ថិតក្នុងលំហគ្មានព្រំដែន** (unbounded)។ បើ parent មានព្រំដែនហើយ វាគ្មានឥទ្ធិពល។

```dart
ListView(
  children: [
    LimitedBox(
      maxHeight: 200,  // ការពារកម្ពស់មិនកំណត់ក្នុង scroll direction
      child: Container(color: Colors.pink),
    ),
  ],
)
```

---

### `OverflowBox` / `SizedOverflowBox`

**📖 វាជាអ្វី** អនុញ្ញាតឲ្យកូនធំជាង parent ដោយហូរចេញក្រៅ។

```dart
SizedBox(
  width: 100, height: 100,
  child: OverflowBox(
    maxWidth: 300, maxHeight: 300,
    child: Container(color: Colors.purple.withValues(alpha: 0.4)),
  ),
)
```

---

### `Offstage`

**📖 វាជាអ្វី** លាក់កូនដោយ **មិនគូរ មិនកាន់ទំហំ តែនៅរក្សា state**។ ខុសពី `Visibility` ដែលអាចដក widget ចោលទាំងស្រុង។

```dart
Offstage(
  offstage: !_isVisible,
  child: const VideoPlayerWidget(), // state មិនបាត់ពេលលាក់
)
```

**⚠️** `Offstage` នៅតែ build និង layout កូន — ខាតធនធាន។ បើមិនត្រូវការរក្សា state ប្រើ `if (cond) widget` វិញ។

---

### `Visibility`

**📖 វាជាអ្វី** គ្រប់គ្រងការបង្ហាញយ៉ាងលម្អិត — អាចជ្រើសថាតើរក្សាទំហំ រក្សា state ឬដក់ចោល។

```dart
Visibility(
  visible: _showDetails,
  maintainState: true,      // រក្សា State object
  maintainSize: true,       // នៅតែកាន់ទំហំ
  maintainAnimation: true,
  replacement: const SizedBox.shrink(),
  child: const Text('ព័ត៌មានលម្អិត', style: TextStyle(height: 1.7)),
)
```

---

### `Transform`

**📖 វាជាអ្វី** អនុវត្ត matrix transformation (បង្វិល ពង្រីក រំកិល ទ្រេត) **ក្រោយពេល layout** — ដូច្នេះវាមិនប៉ះពាល់ទីតាំងកូនផ្សេងទេ។

```dart
Transform.rotate(angle: 0.2, child: const Card(child: Text('ទ្រេត')))
Transform.scale(scale: 1.2, child: const Icon(Icons.star))
Transform.translate(offset: const Offset(20, -10), child: const Text('រំកិល'))
Transform.flip(flipX: true, child: const Icon(Icons.arrow_forward))

// Matrix ផ្ទាល់ — បែប 3D
Transform(
  alignment: Alignment.center,
  transform: Matrix4.identity()
    ..setEntry(3, 2, 0.001)   // perspective
    ..rotateY(0.5),
  child: const FlutterLogo(size: 100),
)
```

---

### `Baseline`

**📖 វាជាអ្វី** ដាក់កូនតាមបន្ទាត់គោល (baseline) របស់អក្សរ។ ប្រើពេលត្រូវតម្រឹមអក្សរទំហំខុសគ្នាឲ្យអង្គុយលើបន្ទាត់តែមួយ។

```dart
Baseline(
  baseline: 40,
  baselineType: TextBaseline.alphabetic,
  child: const Text('អក្សរ', style: TextStyle(fontSize: 30, height: 1.7)),
)
```

---

### `CustomSingleChildLayout`

**📖 វាជាអ្វី** ផ្តល់ការគ្រប់គ្រងពេញលេញលើ layout របស់កូនតែមួយ តាមរយៈ `SingleChildLayoutDelegate`។

```dart
class _MyDelegate extends SingleChildLayoutDelegate {
  @override
  BoxConstraints getConstraintsForChild(BoxConstraints constraints) =>
      constraints.loosen();

  @override
  Offset getPositionForChild(Size size, Size childSize) =>
      Offset(size.width - childSize.width, 0); // ជាប់ស្តាំ

  @override
  bool shouldRelayout(_MyDelegate old) => false;
}
```

---

### `ColoredBox` និង `DecoratedBox`

**📖 វាជាអ្វី** ជម្រើសស្រាល (និង `const`-able) ជំនួស `Container` សម្រាប់ពណ៌ និងការតុបតែង។

```dart
const ColoredBox(color: Colors.amber, child: Padding(
  padding: EdgeInsets.all(8), child: Text('លឿន', style: TextStyle(height: 1.7)),
))

DecoratedBox(
  decoration: BoxDecoration(
    border: Border.all(color: Colors.blue, width: 2),
    borderRadius: BorderRadius.circular(8),
  ),
  child: const Padding(padding: EdgeInsets.all(12), child: Text('ស៊ុម')),
)
```

---

### `SafeArea`

**📖 វាជាអ្វី** ដាក់ padding ដោយស្វ័យប្រវត្តិដើម្បីជៀស notch, status bar, home indicator។

```dart
const SafeArea(
  top: true, bottom: true, left: true, right: true,
  minimum: EdgeInsets.all(8),
  child: Text('មិនត្រូវ notch បាំង', style: TextStyle(height: 1.7)),
)
```

**⚠️** កុំរុំ `Scaffold` ទាំងមូលដោយ `SafeArea` — `Scaffold` ចាត់ការ `AppBar` រួចហើយ។ រុំតែ `body` វិញ។

---
# ជំពូក ៣ — Layout: Multi-child

---

### `Flex`

**📖 វាជាអ្វី** ជា**ឪពុក**របស់ `Row` និង `Column`។ ប្រើវាដោយផ្ទាល់ពេលទិសដៅត្រូវផ្លាស់ប្តូរតាម runtime។

```dart
Flex(
  direction: isWide ? Axis.horizontal : Axis.vertical,
  spacing: 12,
  children: const [Card(), Card()],
)
```

---

### `Expanded`

**📖 វាជាអ្វី** ជា `ParentDataWidget` ដែលបង្ខំកូន**យកទំហំទំនេរដែលនៅសល់**។ ត្រូវប្រើក្នុង `Row`, `Column`, ឬ `Flex` តែប៉ុណ្ណោះ។

```dart
Row(
  children: [
    Expanded(flex: 2, child: Container(color: Colors.red)),   // ២ ភាគ
    Expanded(flex: 1, child: Container(color: Colors.blue)),  // ១ ភាគ
  ],
)
// លទ្ធផល: ក្រហមទទឹង ២/៣, ខៀវ ១/៣
```

---

### `Flexible`

**📖 វាជាអ្វី** ដូច `Expanded` តែ**មិនបង្ខំ**កូនឲ្យពេញ។ ភាពខុសគ្នាស្ថិតលើ `fit`:

| Widget | `fit` | អត្ថន័យ |
|---|---|---|
| `Expanded` | `FlexFit.tight` | **ត្រូវតែ**ពេញទំហំដែលបែងចែក |
| `Flexible` | `FlexFit.loose` | **អាច**តូចជាង បើកូនចង់ |

```dart
Row(
  children: [
    Flexible(child: Text('អត្ថបទនេះនឹងចុះបន្ទាត់ថ្មីជំនួសឲ្យ overflow', style: TextStyle(height: 1.7))),
    const Icon(Icons.check),
  ],
)
```

---

### `Spacer`

**📖 វាជាអ្វី** បង្កើតចន្លោះទទេដែលអាចបត់បែន។ គឺជា `Expanded(child: SizedBox.shrink())` ខ្លីៗ។

```dart
Row(
  children: const [
    Text('ឆ្វេង', style: TextStyle(height: 1.7)),
    Spacer(),              // រុញទៅចុងទាំងសងខាង
    Text('ស្តាំ', style: TextStyle(height: 1.7)),
  ],
)

// Spacer មាន flex ដែរ
Row(children: const [Text('A'), Spacer(flex: 2), Text('B'), Spacer(flex: 1), Text('C')])
```

---

### `Wrap`

**📖 វាជាអ្វី** ដូច `Row`/`Column` តែពេលអស់ទីកន្លែង វា**ចុះបន្ទាត់ថ្មី**ជំនួសឲ្យ overflow។ ល្អឥតខ្ចោះសម្រាប់ chips និង tags។

```dart
Wrap(
  spacing: 8,           // គម្លាតរវាងធាតុក្នុងបន្ទាត់តែមួយ
  runSpacing: 8,        // គម្លាតរវាងបន្ទាត់
  alignment: WrapAlignment.start,
  crossAxisAlignment: WrapCrossAlignment.center,
  children: const [
    Chip(label: Text('Flutter')),
    Chip(label: Text('Dart')),
    Chip(label: Text('Spring Boot')),
    Chip(label: Text('Kotlin')),
  ],
)
```

---

### `Table`

**📖 វាជាអ្វី** តារាងដែលទទឹងជួរឈរស្របគ្នាទាំងអស់។ ខុសពី `Row` ក្នុង `Column` ដែលជួរឈរនីមួយៗឯករាជ្យ។

```dart
Table(
  border: TableBorder.all(color: Colors.grey.shade300),
  columnWidths: const {
    0: FlexColumnWidth(2),
    1: FlexColumnWidth(1),
    2: FixedColumnWidth(80),
  },
  defaultVerticalAlignment: TableCellVerticalAlignment.middle,
  children: const [
    TableRow(children: [
      Padding(padding: EdgeInsets.all(8), child: Text('ឈ្មោះ', style: TextStyle(height: 1.7))),
      Padding(padding: EdgeInsets.all(8), child: Text('អាយុ', style: TextStyle(height: 1.7))),
      Padding(padding: EdgeInsets.all(8), child: Text('ស្ថានភាព', style: TextStyle(height: 1.7))),
    ]),
  ],
)
```

**ប្រភេទទទឹងជួរឈរ:** `FlexColumnWidth` (សមាមាត្រ) · `FixedColumnWidth` (px) · `FractionColumnWidth` (%) · `IntrinsicColumnWidth` (តាមមាតិកា — យឺត)។

---

### `IndexedStack`

**📖 វាជាអ្វី** ដូច `Stack` តែបង្ហាញកូន**តែមួយ**តាម index។ កូនផ្សេងនៅរក្សា state។ ល្អសម្រាប់ tab navigation។

```dart
IndexedStack(
  index: _currentTab,
  children: const [HomePage(), SearchPage(), ProfilePage()],
)
```

**⚠️** កូន**ទាំងអស់**ត្រូវបាន build និង layout ទោះជាមិនបង្ហាញក្តី។ បើអ្នកមាន tab ១០ ដែលធ្ងន់ វានឹងយឺត។ ប្រើ `PageView` ជាមួយ `AutomaticKeepAliveClientMixin` វិញ។

---

### `Flow`

**📖 វាជាអ្វី** layout ដែលមានប្រសិទ្ធភាពខ្ពស់បំផុត — វាដាក់ទីតាំងកូនតាម transformation matrix ក្នុង **paint phase** ដូច្នេះការផ្លាស់ប្តូរទីតាំង**មិនបង្ក relayout** ទេ។ ប្រើសម្រាប់ animation ស្មុគស្មាញ។

```dart
class _FanDelegate extends FlowDelegate {
  final Animation<double> anim;
  _FanDelegate(this.anim) : super(repaint: anim);

  @override
  void paintChildren(FlowPaintingContext context) {
    for (var i = 0; i < context.childCount; i++) {
      final dx = context.getChildSize(i)!.width * i * anim.value;
      context.paintChild(i, transform: Matrix4.translationValues(dx, 0, 0));
    }
  }

  @override
  bool shouldRepaint(_FanDelegate old) => anim != old.anim;
}

Flow(delegate: _FanDelegate(_controller), children: const [Icon(Icons.a), Icon(Icons.b)])
```

---

### `CustomMultiChildLayout`

**📖 វាជាអ្វី** គ្រប់គ្រង layout របស់កូនច្រើនដោយដៃ តាមរយៈ `MultiChildLayoutDelegate`។ កូននីមួយៗត្រូវមាន `LayoutId`។

```dart
class _MyLayout extends MultiChildLayoutDelegate {
  @override
  void performLayout(Size size) {
    final headerSize = layoutChild('header', BoxConstraints.loose(size));
    positionChild('header', Offset.zero);
    layoutChild('body', BoxConstraints.tight(
      Size(size.width, size.height - headerSize.height),
    ));
    positionChild('body', Offset(0, headerSize.height));
  }

  @override
  bool shouldRelayout(_MyLayout old) => false;
}

CustomMultiChildLayout(
  delegate: _MyLayout(),
  children: const [
    LayoutId(id: 'header', child: Text('ក្បាល')),
    LayoutId(id: 'body', child: Text('តួ')),
  ],
)
```

---

### `ListBody`

**📖 វាជាអ្វី** រៀបកូនតាមលំដាប់ក្នុងទិសមួយ ដោយឲ្យកូននីមួយៗ**ពេញទទឹង** តែកម្ពស់តាមមាតិកា។ ស្រាលជាង `Column` ព្រោះមិនចាត់ការ flex។

```dart
const ListBody(
  mainAxis: Axis.vertical,
  children: [Text('មួយ'), Text('ពីរ'), Text('បី')],
)
```

---

### `LayoutBuilder`

**📖 វាជាអ្វី** ផ្តល់ `BoxConstraints` របស់ parent ឲ្យអ្នក ដើម្បីសម្រេចថាត្រូវ build អ្វី។ នេះជាឧបករណ៍សំខាន់បំផុតសម្រាប់ **responsive design**។

```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth > 900) {
      return const _DesktopLayout();
    } else if (constraints.maxWidth > 600) {
      return const _TabletLayout();
    }
    return const _MobileLayout();
  },
)
```

**⚠️** `LayoutBuilder` build កូន**ក្នុង layout phase** — ដូច្នេះកុំហៅ `setState` ខាងក្នុងវា។ ហើយវាទាមទារឲ្យ parent មាន constraint ច្បាស់លាស់។

---

### `OrientationBuilder`

**📖 វាជាអ្វី** ដូច `LayoutBuilder` តែផ្តល់ `Orientation.portrait` ឬ `.landscape` វិញ។

```dart
OrientationBuilder(
  builder: (context, orientation) => GridView.count(
    crossAxisCount: orientation == Orientation.portrait ? 2 : 4,
    children: items,
  ),
)
```

---

### `Positioned` និង `PositionedDirectional`

**📖 វាជាអ្វី** `ParentDataWidget` សម្រាប់ដាក់ទីតាំងកូនក្នុង `Stack`។

```dart
Stack(children: [
  const ColoredBox(color: Colors.grey),
  Positioned(top: 10, right: 10, width: 50, height: 50, child: Container(color: Colors.red)),
  Positioned.fill(child: Container(color: Colors.black12)),           // ពេញទាំងអស់
  const PositionedDirectional(start: 10, child: Text('គោរព RTL')),
])
```

**⚠️** ក្នុងអក្ស៍មួយ អ្នកអាចដាក់តម្លៃ **២ ក្នុងចំណោម ៣** ប៉ុណ្ណោះ (`left`, `right`, `width`)។ ដាក់ទាំង ៣ → crash។

---
# ជំពូក ៤ — Text និង Typography

---

### `RichText`

**📖 វាជាអ្វី** កម្រិតទាបជាង `Text.rich` — វា **មិន**យក style ពី `DefaultTextStyle` ទេ ដូច្នេះអ្នកត្រូវផ្តល់ style ដោយខ្លួនឯងទាំងស្រុង។

```dart
RichText(
  text: TextSpan(
    style: const TextStyle(color: Colors.black, fontSize: 16, height: 1.7),
    children: [
      const TextSpan(text: 'អាន'),
      TextSpan(
        text: 'លក្ខខណ្ឌប្រើប្រាស់',
        style: const TextStyle(color: Colors.blue, decoration: TextDecoration.underline),
        recognizer: TapGestureRecognizer()..onTap = () => debugPrint('ចុច'),
      ),
      const WidgetSpan(child: Icon(Icons.open_in_new, size: 14)),
    ],
  ),
)
```

**⚠️** `TapGestureRecognizer` ត្រូវតែ `dispose()` ក្នុង `State.dispose()` បើមិនដូច្នេះនឹង memory leak។ ជាទូទៅប្រើ `Text.rich` វិញនឹងងាយស្រួលជាង។

---

### `TextSpan` និង `WidgetSpan`

**📖 វាជាអ្វី** `TextSpan` ជាបំណែកអត្ថបទដែលមានស្ទីលរៀងខ្លួន។ `WidgetSpan` អនុញ្ញាតឲ្យបញ្ចូល **widget ណាមួយ**ចូលក្នុងខ្សែអត្ថបទ។

```dart
Text.rich(TextSpan(children: [
  const TextSpan(text: 'តម្លៃ '),
  WidgetSpan(
    alignment: PlaceholderAlignment.middle,
    child: Container(
      padding: const EdgeInsets.symmetric(horizontal: 6),
      decoration: BoxDecoration(color: Colors.green.shade100, borderRadius: BorderRadius.circular(4)),
      child: const Text('-20%', style: TextStyle(height: 1.7)),
    ),
  ),
  const TextSpan(text: ' សម្រាប់ថ្ងៃនេះ'),
]))
```

---

### `DefaultTextStyle`

**📖 វាជាអ្វី** ជា `InheritedWidget` ដែលកំណត់ស្ទីលលំនាំដើមសម្រាប់ `Text` ទាំងអស់ខាងក្រោមវា។

```dart
DefaultTextStyle(
  style: const TextStyle(fontSize: 16, height: 1.7, color: Colors.white),
  textAlign: TextAlign.center,
  child: Column(
    children: const [
      Text('ទាំងអស់នេះនឹងជាពណ៌ស'),
      Text('ដោយមិនចាំបាច់សរសេរ style ម្តងទៀត'),
    ],
  ),
)

// merge ជាមួយស្ទីលដែលមានស្រាប់
DefaultTextStyle.merge(
  style: const TextStyle(fontWeight: FontWeight.bold),
  child: const Text('ដិត តែរក្សាទំហំ និងពណ៌ដើម'),
)
```

---

### `SelectableText`

**📖 វាជាអ្វី** អត្ថបទដែលអ្នកប្រើអាចរើស និងចម្លងបាន។

```dart
const SelectableText(
  'អត្ថបទនេះអាចរើសបាន',
  style: TextStyle(height: 1.7),
  showCursor: true,
  cursorWidth: 2,
  toolbarOptions: ToolbarOptions(copy: true, selectAll: true),
)

SelectableText.rich(TextSpan(children: [/* ... */]))
```

---

### `SelectionArea`

**📖 វាជាអ្វី** ធ្វើឲ្យ **subtree ទាំងមូល** អាចរើសបាន — ជាវិធីទំនើបជាង `SelectableText` ព្រោះអ្នកអាចរើសឆ្លងកាត់ widget ច្រើន។

```dart
SelectionArea(
  child: Column(
    children: const [
      Text('ចំណងជើង', style: TextStyle(fontSize: 22, height: 1.7)),
      Text('កថាខណ្ឌទី១ ...', style: TextStyle(height: 1.7)),
      Text('កថាខណ្ឌទី២ ...', style: TextStyle(height: 1.7)),
    ],
  ),
)
```

---

# ជំពូក ៥ — Images, Icons និង Assets

---

### `FadeInImage`

**📖 វាជាអ្វី** បង្ហាញ placeholder រហូតដល់រូបពិតផ្ទុករួច រួច fade ចូល។

```dart
FadeInImage.assetNetwork(
  placeholder: 'assets/images/loading.gif',
  image: 'https://picsum.photos/400',
  fadeInDuration: const Duration(milliseconds: 300),
  fit: BoxFit.cover,
  imageErrorBuilder: (c, e, s) => const Icon(Icons.error),
)

// កំណែស្រាលបំផុត — placeholder ថ្លា គ្មាន asset
FadeInImage.memoryNetwork(
  placeholder: kTransparentImage, // ពី package transparent_image
  image: url,
)
```

---

### `ImageIcon`

**📖 វាជាអ្វី** ប្រើ `ImageProvider` (ឧ. PNG) ជា icon — វានឹងទទួលពណ៌ពី `IconTheme` ដូច `Icon` ដែរ។

```dart
const ImageIcon(AssetImage('assets/icons/custom.png'), size: 24, color: Colors.blue)
```

---

### `RawImage`

**📖 វាជាអ្វី** កម្រិតទាបបំផុត — គូរ `dart:ui.Image` ដោយផ្ទាល់។ ប្រើពេលអ្នកបានដំណើរការ pixel ដោយខ្លួនឯង។

```dart
RawImage(image: myDartUiImage, fit: BoxFit.contain)
```

---

### `DefaultAssetBundle` និង `AssetBundle`

**📖 វាជាអ្វី** ចូលទៅកាន់ file ក្នុង assets ដោយផ្ទាល់ (JSON, text, binary)។

```dart
final jsonStr = await DefaultAssetBundle.of(context).loadString('assets/data/config.json');
final bytes = await rootBundle.load('assets/fonts/khmer.ttf');
```

---

### `CircleAvatar`

**📖 វាជាអ្វី** រូបតំណាងអ្នកប្រើជារង្វង់ មាន fallback ជាអក្សរ។

```dart
CircleAvatar(
  radius: 28,
  backgroundImage: const NetworkImage('https://i.pravatar.cc/100'),
  backgroundColor: Colors.grey.shade200,
  onBackgroundImageError: (e, s) {},
  child: const Text('ម', style: TextStyle(height: 1.7)), // បង្ហាញពេលគ្មានរូប
)
```

---

### `ImageFiltered` និង `BackdropFilter`

**📖 វាជាអ្វី** អនុវត្ត filter (blur, បំលែងពណ៌) — `ImageFiltered` លើ**កូន** ចំណែក `BackdropFilter` លើ**អ្វីដែលនៅពីក្រោយ**។

```dart
// ធ្វើឲ្យកូនព្រិល
ImageFiltered(
  imageFilter: ui.ImageFilter.blur(sigmaX: 4, sigmaY: 4),
  child: Image.network(url),
)

// កញ្ចក់ព្រិល (frosted glass) — ត្រូវនៅក្នុង Stack ឬ ClipRect
ClipRRect(
  borderRadius: BorderRadius.circular(16),
  child: BackdropFilter(
    filter: ui.ImageFilter.blur(sigmaX: 12, sigmaY: 12),
    child: Container(
      color: Colors.white.withValues(alpha: 0.15),
      padding: const EdgeInsets.all(20),
      child: const Text('កញ្ចក់ព្រិល', style: TextStyle(height: 1.7)),
    ),
  ),
)
```

**⚠️** `BackdropFilter` **ថ្លៃខ្លាំង**លើឧបករណ៍ចាស់។ ត្រូវរុំដោយ `ClipRect` បើមិនដូច្នេះវានឹងព្រិលទាំងអេក្រង់។

---
# ជំពូក ៦ — Input និង Forms

---

### `TextField`

**📖 វាជាអ្វី** ប្រអប់បញ្ចូលអត្ថបទមូលដ្ឋាន។

```dart
TextField(
  controller: _controller,
  focusNode: _focusNode,
  decoration: InputDecoration(
    labelText: 'អ៊ីមែល',
    hintText: 'you@example.com',
    helperText: 'យើងនឹងមិនចែករំលែកអ៊ីមែលរបស់អ្នកទេ',
    prefixIcon: const Icon(Icons.email_outlined),
    suffixIcon: IconButton(icon: const Icon(Icons.clear), onPressed: _controller.clear),
    border: OutlineInputBorder(borderRadius: BorderRadius.circular(12)),
    filled: true,
    fillColor: Colors.grey.shade50,
    counterText: '', // លាក់អក្សររាប់
  ),
  keyboardType: TextInputType.emailAddress,
  textInputAction: TextInputAction.next,
  autofillHints: const [AutofillHints.email],
  maxLength: 100,
  obscureText: false,
  onChanged: (v) => setState(() => _email = v),
  onSubmitted: (v) => _focusNode.nextFocus(),
  inputFormatters: [FilteringTextInputFormatter.deny(RegExp(r'\s'))],
)
```

**⚠️** `TextEditingController` និង `FocusNode` **ត្រូវតែ** `dispose()`:

```dart
@override
void dispose() {
  _controller.dispose();
  _focusNode.dispose();
  super.dispose();
}
```

---

### `TextFormField` និង `Form`

**📖 វាជាអ្វី** `TextFormField` = `TextField` + ការផ្ទៀងផ្ទាត់ (validation)។ `Form` ជាកុងតឺន័រដែលគ្រប់គ្រង field ទាំងអស់ជាក្រុម។

```dart
final _formKey = GlobalKey<FormState>();

Form(
  key: _formKey,
  autovalidateMode: AutovalidateMode.onUserInteraction,
  child: Column(children: [
    TextFormField(
      decoration: const InputDecoration(labelText: 'ពាក្យសម្ងាត់'),
      obscureText: true,
      validator: (value) {
        if (value == null || value.isEmpty) return 'សូមបញ្ចូលពាក្យសម្ងាត់';
        if (value.length < 8) return 'ត្រូវមានយ៉ាងតិច ៨ តួ';
        return null; // null = ត្រឹមត្រូវ
      },
      onSaved: (v) => _password = v,
    ),
    ElevatedButton(
      onPressed: () {
        if (_formKey.currentState!.validate()) {
          _formKey.currentState!.save();
          // បញ្ជូនទិន្នន័យ
        }
      },
      child: const Text('ចូល'),
    ),
  ]),
)
```

---

### `Checkbox` និង `CheckboxListTile`

```dart
Checkbox(
  value: _agreed,
  tristate: false,  // true = អាចមានតម្លៃ null
  onChanged: (v) => setState(() => _agreed = v ?? false),
)

CheckboxListTile(
  value: _agreed,
  onChanged: (v) => setState(() => _agreed = v!),
  title: const Text('ខ្ញុំយល់ព្រមតាមលក្ខខណ្ឌ', style: TextStyle(height: 1.7)),
  subtitle: const Text('សូមអានមុនយល់ព្រម'),
  controlAffinity: ListTileControlAffinity.leading, // ប្រអប់នៅឆ្វេង
)
```

---

### `Radio` និង `RadioListTile`

```dart
enum Payment { cash, card, aba }

Column(children: [
  for (final p in Payment.values)
    RadioListTile<Payment>(
      value: p,
      groupValue: _selected,
      onChanged: (v) => setState(() => _selected = v!),
      title: Text(p.name, style: const TextStyle(height: 1.7)),
    ),
])
```

---

### `Switch` និង `SwitchListTile`

```dart
SwitchListTile(
  value: _darkMode,
  onChanged: (v) => setState(() => _darkMode = v),
  title: const Text('រចនាបទងងឹត', style: TextStyle(height: 1.7)),
  secondary: const Icon(Icons.dark_mode),
  thumbIcon: WidgetStateProperty.resolveWith((states) =>
      states.contains(WidgetState.selected) ? const Icon(Icons.check) : null),
)
```

---

### `Slider` និង `RangeSlider`

```dart
Slider(
  value: _volume,
  min: 0, max: 100,
  divisions: 20,               // ចំណុចឈប់ — លុបចេញបើចង់រំកិលរលូន
  label: '${_volume.round()}%',
  onChanged: (v) => setState(() => _volume = v),
  onChangeEnd: (v) => _save(v), // ហៅតែពេលលែងដៃ
)

RangeSlider(
  values: _priceRange,
  min: 0, max: 1000,
  labels: RangeLabels('\$${_priceRange.start.round()}', '\$${_priceRange.end.round()}'),
  onChanged: (v) => setState(() => _priceRange = v),
)
```

---

### `DropdownButton` និង `DropdownMenu`

**📖 វាជាអ្វី** `DropdownButton` ជាកំណែចាស់។ `DropdownMenu` (Material 3) ថ្មីជាង មានប្រអប់ស្វែងរកភ្ជាប់មក។

```dart
// កំណែចាស់
DropdownButton<String>(
  value: _province,
  hint: const Text('ជ្រើសរើសខេត្ត'),
  isExpanded: true,
  items: const [
    DropdownMenuItem(value: 'PP', child: Text('ភ្នំពេញ', style: TextStyle(height: 1.7))),
    DropdownMenuItem(value: 'SR', child: Text('សៀមរាប', style: TextStyle(height: 1.7))),
  ],
  onChanged: (v) => setState(() => _province = v),
)

// កំណែ Material 3 — មានការស្វែងរក
DropdownMenu<String>(
  initialSelection: 'PP',
  label: const Text('ខេត្ត'),
  enableFilter: true,
  onSelected: (v) => setState(() => _province = v),
  dropdownMenuEntries: const [
    DropdownMenuEntry(value: 'PP', label: 'ភ្នំពេញ'),
    DropdownMenuEntry(value: 'SR', label: 'សៀមរាប'),
  ],
)
```

---

### `SegmentedButton`

**📖 វាជាអ្វី** ក្រុមប៊ូតុងជាប់គ្នាសម្រាប់ជ្រើសរើស ១ ឬ ច្រើន (Material 3)។

```dart
SegmentedButton<String>(
  segments: const [
    ButtonSegment(value: 'day', label: Text('ថ្ងៃ'), icon: Icon(Icons.today)),
    ButtonSegment(value: 'week', label: Text('សប្តាហ៍')),
    ButtonSegment(value: 'month', label: Text('ខែ')),
  ],
  selected: {_view},
  multiSelectionEnabled: false,
  onSelectionChanged: (s) => setState(() => _view = s.first),
)
```

---

### `Autocomplete` និង `RawAutocomplete`

```dart
Autocomplete<String>(
  optionsBuilder: (TextEditingValue value) {
    if (value.text.isEmpty) return const Iterable<String>.empty();
    return _allProvinces.where(
      (p) => p.toLowerCase().contains(value.text.toLowerCase()),
    );
  },
  onSelected: (selection) => debugPrint('ជ្រើស: $selection'),
  fieldViewBuilder: (context, controller, focusNode, onSubmit) => TextField(
    controller: controller,
    focusNode: focusNode,
    decoration: const InputDecoration(labelText: 'ស្វែងរកខេត្ត'),
  ),
)
```

---

### `SearchAnchor` និង `SearchBar`

**📖 វាជាអ្វី** ការស្វែងរកតាមស្តង់ដារ Material 3 — មាន overlay បង្ហាញលទ្ធផលដោយស្វ័យប្រវត្តិ។

```dart
SearchAnchor.bar(
  barHintText: 'ស្វែងរកផលិតផល',
  suggestionsBuilder: (context, controller) async {
    final results = await api.search(controller.text);
    return results.map((r) => ListTile(
      title: Text(r.name, style: const TextStyle(height: 1.7)),
      onTap: () => controller.closeView(r.name),
    ));
  },
)
```

---

### `Focus`, `FocusScope`, `FocusTraversalGroup`

**📖 វាជាអ្វី** គ្រប់គ្រងការផ្តោត (focus) — សំខាន់សម្រាប់ក្តារចុច និង accessibility។

```dart
Focus(
  onFocusChange: (hasFocus) => debugPrint('ផ្តោត: $hasFocus'),
  onKeyEvent: (node, event) {
    if (event.logicalKey == LogicalKeyboardKey.escape) {
      Navigator.pop(context);
      return KeyEventResult.handled;
    }
    return KeyEventResult.ignored;
  },
  child: const TextField(),
)

// បិទក្តារចុចពេលចុចខាងក្រៅ
GestureDetector(
  onTap: () => FocusScope.of(context).unfocus(),
  child: const Scaffold(/* ... */),
)

// កំណត់លំដាប់ Tab
FocusTraversalGroup(
  policy: OrderedTraversalPolicy(),
  child: Column(children: const [
    FocusTraversalOrder(order: NumericFocusOrder(2), child: TextField()),
    FocusTraversalOrder(order: NumericFocusOrder(1), child: TextField()),
  ]),
)
```

---

### `Shortcuts` និង `Actions`

**📖 វាជាអ្វី** ភ្ជាប់បន្សំគ្រាប់ចុចទៅនឹងសកម្មភាព — ស្ថាបត្យកម្មផ្លូវការសម្រាប់ keyboard shortcuts។

```dart
class SaveIntent extends Intent { const SaveIntent(); }

Shortcuts(
  shortcuts: <ShortcutActivator, Intent>{
    SingleActivator(LogicalKeyboardKey.keyS, control: true): const SaveIntent(),
  },
  child: Actions(
    actions: <Type, Action<Intent>>{
      SaveIntent: CallbackAction<SaveIntent>(onInvoke: (_) => _save()),
    },
    child: const Focus(autofocus: true, child: MyEditor()),
  ),
)
```

---

### `KeyboardListener`

```dart
KeyboardListener(
  focusNode: _node,
  onKeyEvent: (event) {
    if (event is KeyDownEvent) debugPrint('ចុច: ${event.logicalKey}');
  },
  child: const SizedBox(),
)
```

---

### `AutofillGroup`

**📖 វាជាអ្វី** ប្រាប់ប្រព័ន្ធថា field ណាខ្លះជាក្រុមតែមួយ ដើម្បីឲ្យ password manager បំពេញបានត្រឹមត្រូវ។

```dart
AutofillGroup(
  child: Column(children: [
    TextField(autofillHints: const [AutofillHints.username]),
    TextField(autofillHints: const [AutofillHints.password], obscureText: true),
  ]),
)
```

---
# ជំពូក ៧ — Scrolling និង Slivers

> **🧠 គំនិតគន្លឹះ:** *Sliver* គឺជាផ្នែកមួយនៃតំបន់ដែល scroll បាន។ `ListView` ពិតជា `CustomScrollView` + `SliverList` ដែលបានរុំស្អាតៗ។ ពេលអ្នកត្រូវការ scroll ស្មុគស្មាញ (header បង្រួម, បញ្ជីច្រើនប្រភេទក្នុងទំព័រតែមួយ) អ្នកត្រូវចុះទៅ sliver ដោយផ្ទាល់។

---

### `SingleChildScrollView`

**📖 វាជាអ្វី** ធ្វើឲ្យកូន**តែមួយ** scroll បាន។ ប្រើពេលមាតិកា**មានចំនួនកំណត់** និងតូច។

```dart
SingleChildScrollView(
  padding: const EdgeInsets.all(16),
  physics: const BouncingScrollPhysics(),
  child: Column(children: const [/* form fields */]),
)
```

**⚠️** វា build កូន**ទាំងអស់ក្នុងពេលតែមួយ** — គ្មាន lazy loading។ ធាតុលើសពី ~២០ ត្រូវប្រើ `ListView` វិញ។

---

### `ListView`

**📖 វាជាអ្វី** បញ្ជីដែល scroll បាន ហើយ **build តែធាតុដែលមើលឃើញ** (lazy)។

```dart
// ១. .builder — សម្រាប់បញ្ជីវែង ឬមិនកំណត់ ★ ប្រើវាភាគច្រើន
ListView.builder(
  itemCount: items.length,
  itemExtent: 72,          // ប្រាប់កម្ពស់ជាមុន = លឿនជាង
  padding: const EdgeInsets.symmetric(vertical: 8),
  itemBuilder: (context, index) => ListTile(
    title: Text(items[index].name, style: const TextStyle(height: 1.7)),
  ),
)

// ២. .separated — មានបន្ទាត់ខណ្ឌចែក
ListView.separated(
  itemCount: items.length,
  itemBuilder: (c, i) => ListTile(title: Text(items[i].name)),
  separatorBuilder: (c, i) => const Divider(height: 1),
)

// ៣. constructor ធម្មតា — សម្រាប់ធាតុតិច និងថេរ
ListView(children: const [Text('មួយ'), Text('ពីរ')])

// ៤. .custom — គ្រប់គ្រង SliverChildDelegate ដោយខ្លួនឯង
```

**Parameters សំខាន់:**

| Parameter | អត្ថន័យ |
|---|---|
| `shrinkWrap: true` | កម្ពស់តាមមាតិកា — **យឺត** ព្រោះបាត់ lazy loading |
| `physics` | `NeverScrollableScrollPhysics()` បិទ scroll · `AlwaysScrollableScrollPhysics()` បើកជានិច្ច |
| `reverse: true` | ចាប់ផ្តើមពីក្រោម (ល្អសម្រាប់ chat) |
| `controller` | `ScrollController` សម្រាប់អាន/កំណត់ទីតាំង |
| `cacheExtent` | ចម្ងាយបន្ថែមដែល build មុន (px) |

**⚠️** `ListView` ក្នុង `Column` → **crash** ("unbounded height")។ ដំណោះស្រាយ: រុំដោយ `Expanded`។

---

### `GridView`

```dart
// កំណត់ចំនួនជួរឈរ
GridView.count(
  crossAxisCount: 2,
  mainAxisSpacing: 12,
  crossAxisSpacing: 12,
  childAspectRatio: 0.75,
  children: products.map((p) => ProductCard(p)).toList(),
)

// កំណត់ទទឹងអតិបរមា — responsive ដោយស្វ័យប្រវត្តិ ★ ល្អបំផុត
GridView.builder(
  gridDelegate: const SliverGridDelegateWithMaxCrossAxisExtent(
    maxCrossAxisExtent: 200,
    mainAxisSpacing: 12,
    crossAxisSpacing: 12,
  ),
  itemCount: products.length,
  itemBuilder: (c, i) => ProductCard(products[i]),
)

// GridView.extent — ដូច .count តែកំណត់តាមទទឹង
```

---

### `PageView`

**📖 វាជាអ្វី** scroll មួយទំព័រម្តងៗ ដូច onboarding ឬ image carousel។

```dart
PageView.builder(
  controller: PageController(viewportFraction: 0.9), // ឃើញទំព័របន្ទាប់បន្តិច
  scrollDirection: Axis.horizontal,
  itemCount: 3,
  onPageChanged: (i) => setState(() => _page = i),
  itemBuilder: (c, i) => OnboardingSlide(index: i),
)
```

---

### `CustomScrollView` និងគ្រួសារ Sliver

**📖 វាជាអ្វី** តំបន់ scroll ដែលអ្នកផ្គុំពី sliver ច្រើនប្រភេទ។

```dart
CustomScrollView(
  slivers: [
    // ១. AppBar ដែលបង្រួម/ពង្រីកតាម scroll
    SliverAppBar.large(
      title: const Text('ព័ត៌មាន'),
      pinned: true,        // នៅជាប់លើពេល scroll
      floating: false,     // លេចមកវិញភ្លាមពេល scroll ឡើង
      snap: false,
      expandedHeight: 220,
      flexibleSpace: FlexibleSpaceBar(
        background: Image.network(url, fit: BoxFit.cover),
        collapseMode: CollapseMode.parallax,
      ),
    ),

    // ២. widget ធម្មតាតែមួយក្នុងលំហ sliver
    const SliverToBoxAdapter(
      child: Padding(padding: EdgeInsets.all(16), child: Text('ណែនាំ')),
    ),

    // ៣. padding ជុំវិញ sliver
    SliverPadding(
      padding: const EdgeInsets.symmetric(horizontal: 16),
      sliver: SliverList.builder(
        itemCount: 50,
        itemBuilder: (c, i) => ListTile(title: Text('ធាតុ $i')),
      ),
    ),

    // ៤. grid ក្នុង sliver
    SliverGrid.count(crossAxisCount: 3, children: const [/* ... */]),

    // ៥. បំពេញទំហំដែលនៅសល់ (ល្អសម្រាប់ empty state)
    const SliverFillRemaining(
      hasScrollBody: false,
      child: Center(child: Text('គ្មានទិន្នន័យ', style: TextStyle(height: 1.7))),
    ),
  ],
)
```

**Sliver ផ្សេងទៀត:**

| Sliver | ការប្រើប្រាស់ |
|---|---|
| `SliverFixedExtentList` | កម្ពស់ធាតុថេរ — លឿនបំផុត |
| `SliverPrototypeExtentList` | កម្ពស់យកតាម widget គំរូ |
| `SliverPersistentHeader` | header ផ្ទាល់ខ្លួនដែលបង្រួម/ជាប់ |
| `SliverAnimatedList` | បញ្ជីមាន animation ពេលបន្ថែម/លុប |
| `SliverReorderableList` | អូសរៀបលំដាប់ឡើងវិញ |
| `SliverFillViewport` | ធាតុនីមួយៗពេញអេក្រង់ |
| `SliverOpacity`, `SliverIgnorePointer`, `SliverVisibility` | កំណែ sliver នៃ widget ធម្មតា |
| `SliverMainAxisGroup`, `SliverCrossAxisGroup` | ដាក់ sliver ច្រើនជាក្រុម (Flutter 3.16+) |

---

### `NestedScrollView`

**📖 វាជាអ្វី** សម្របសម្រួល scroll ខាងក្រៅ (header) ជាមួយ scroll ខាងក្នុង (TabBarView)។

```dart
NestedScrollView(
  headerSliverBuilder: (context, innerBoxScrolled) => [
    SliverAppBar(
      pinned: true,
      expandedHeight: 200,
      forceElevated: innerBoxScrolled,
      bottom: const TabBar(tabs: [Tab(text: 'ថ្មី'), Tab(text: 'ពេញនិយម')]),
    ),
  ],
  body: const TabBarView(children: [FeedList(), PopularList()]),
)
```

---

### `RefreshIndicator`

```dart
RefreshIndicator(
  onRefresh: () async => await _reload(),   // ត្រូវ return Future
  displacement: 40,
  child: ListView.builder(
    physics: const AlwaysScrollableScrollPhysics(), // ចាំបាច់ពេលបញ្ជីខ្លី
    itemCount: items.length,
    itemBuilder: (c, i) => ListTile(title: Text(items[i])),
  ),
)
```

---

### `Scrollbar` និង `ScrollConfiguration`

```dart
Scrollbar(
  controller: _scrollController,
  thumbVisibility: true,
  trackVisibility: true,
  child: ListView(controller: _scrollController, children: const [/* ... */]),
)

// លុប glow effect លើ Android ទាំង app
ScrollConfiguration(
  behavior: const ScrollBehavior().copyWith(overscroll: false),
  child: const ListView(children: []),
)
```

---

### `ReorderableListView`

```dart
ReorderableListView.builder(
  itemCount: tasks.length,
  onReorder: (oldIndex, newIndex) => setState(() {
    if (newIndex > oldIndex) newIndex -= 1;
    tasks.insert(newIndex, tasks.removeAt(oldIndex));
  }),
  itemBuilder: (c, i) => ListTile(
    key: ValueKey(tasks[i].id),  // ★ key ចាំបាច់ណាស់
    title: Text(tasks[i].title, style: const TextStyle(height: 1.7)),
    trailing: ReorderableDragStartListener(index: i, child: const Icon(Icons.drag_handle)),
  ),
)
```

---

### `ListWheelScrollView`

**📖 វាជាអ្វី** បញ្ជីបែបកង់វិល ៣ វិមាត្រ ដូច picker របស់ iOS។

```dart
ListWheelScrollView.useDelegate(
  itemExtent: 48,
  diameterRatio: 2.0,
  perspective: 0.005,
  physics: const FixedExtentScrollPhysics(),
  onSelectedItemChanged: (i) => setState(() => _year = 2000 + i),
  childDelegate: ListWheelChildBuilderDelegate(
    childCount: 50,
    builder: (c, i) => Center(child: Text('${2000 + i}')),
  ),
)
```

---

### `DraggableScrollableSheet`

**📖 វាជាអ្វី** សន្លឹកខាងក្រោមដែលអ្នកប្រើអាចអូសឡើង-ចុះ ដូច Google Maps។

```dart
DraggableScrollableSheet(
  initialChildSize: 0.3,
  minChildSize: 0.15,
  maxChildSize: 0.9,
  snap: true,
  snapSizes: const [0.3, 0.6, 0.9],
  builder: (context, scrollController) => Container(
    decoration: const BoxDecoration(
      color: Colors.white,
      borderRadius: BorderRadius.vertical(top: Radius.circular(24)),
    ),
    child: ListView.builder(
      controller: scrollController, // ★ ត្រូវប្រើ controller នេះ
      itemCount: 30,
      itemBuilder: (c, i) => ListTile(title: Text('ធាតុ $i')),
    ),
  ),
)
```

---

### `NotificationListener`

**📖 វាជាអ្វី** ស្តាប់ព្រឹត្តិការណ៍ដែលឡើងតាមមែកធាង (bubble up) — ជាពិសេស `ScrollNotification`។

```dart
NotificationListener<ScrollNotification>(
  onNotification: (notification) {
    if (notification is ScrollEndNotification &&
        notification.metrics.extentAfter < 200) {
      _loadMore();   // infinite scroll
    }
    return false;    // false = អនុញ្ញាតឲ្យបន្តឡើងលើ
  },
  child: ListView.builder(/* ... */),
)
```

---

### `InteractiveViewer`

**📖 វាជាអ្វី** អនុញ្ញាតឲ្យ pinch-zoom, pan, និង scale លើកូនណាមួយ។

```dart
InteractiveViewer(
  minScale: 0.5,
  maxScale: 4.0,
  boundaryMargin: const EdgeInsets.all(80),
  constrained: false,
  child: Image.network(url),
)
```

---
# ជំពូក ៨ — Material Components

---

### `MaterialApp`

**📖 វាជាអ្វី** ឫសគល់នៃ app បែប Material — ផ្តល់ theme, navigation, localization, និង directionality។

```dart
MaterialApp(
  title: 'កម្មវិធីរបស់ខ្ញុំ',
  debugShowCheckedModeBanner: false,
  theme: ThemeData(
    colorScheme: ColorScheme.fromSeed(seedColor: Colors.indigo),
    useMaterial3: true,
    fontFamily: 'NotoSansKhmer',
    textTheme: const TextTheme(
      bodyMedium: TextStyle(height: 1.7),   // ★ សម្រាប់អក្សរខ្មែរ
    ),
  ),
  darkTheme: ThemeData.dark(useMaterial3: true),
  themeMode: ThemeMode.system,
  locale: const Locale('km'),
  supportedLocales: const [Locale('km'), Locale('en')],
  localizationsDelegates: const [
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
    GlobalCupertinoLocalizations.delegate,
  ],
  home: const HomePage(),
  // ឬប្រើ router: MaterialApp.router(routerConfig: _router)
)
```

---

### `Material` និង `Ink`

**📖 វាជាអ្វី** `Material` ជាផ្ទៃដែល ink splash អាចលេចឡើងបាន។ ប្រសិនបើ ripple របស់អ្នកមិនលេច គឺដោយសារគ្មាន `Material` នៅពីក្រោម។

```dart
Material(
  color: Colors.white,
  elevation: 2,
  borderRadius: BorderRadius.circular(12),
  clipBehavior: Clip.antiAlias,   // ★ បង្ខំ ripple ឲ្យស៊ីនឹងជ្រុងមូល
  child: InkWell(
    onTap: () {},
    child: const Padding(padding: EdgeInsets.all(16), child: Text('ចុចខ្ញុំ')),
  ),
)

// Ink — គូរផ្ទៃខាងក្រោយដែល ripple គោរព
Ink.image(
  image: const NetworkImage('https://picsum.photos/200'),
  height: 200, fit: BoxFit.cover,
  child: InkWell(onTap: () {}, child: const SizedBox.expand()),
)
```

**⚠️** ប្រសិនបើអ្នកដាក់ `Container(color: ...)` នៅ**លើ** `InkWell` ripple នឹងត្រូវបាំង។ ប្រើ `Ink(decoration: ...)` វិញ។

---

### `InkWell` និង `InkResponse`

```dart
InkWell(
  onTap: () {},
  onLongPress: () {},
  onDoubleTap: () {},
  borderRadius: BorderRadius.circular(8),
  splashColor: Colors.blue.withValues(alpha: 0.2),
  highlightColor: Colors.transparent,
  child: const Padding(padding: EdgeInsets.all(12), child: Icon(Icons.share)),
)
```

`InkResponse` ដូចគ្នា តែ splash ជារង្វង់ ហើយអាចហូរចេញក្រៅព្រំដែន (`containedInkWell: false`)។

---

### `Card`

```dart
Card(
  elevation: 2,
  margin: const EdgeInsets.symmetric(horizontal: 16, vertical: 8),
  shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(16)),
  clipBehavior: Clip.antiAlias,
  child: Column(mainAxisSize: MainAxisSize.min, children: const [
    ListTile(leading: Icon(Icons.album), title: Text('ចំណងជើង'), subtitle: Text('អត្ថបទរង')),
  ]),
)

// កំណែ Material 3
Card.filled(child: /* ... */)
Card.outlined(child: /* ... */)
```

---

### `ListTile`

**📖 វាជាអ្វី** ជួរបញ្ជីតាមស្តង់ដារ — មាន slot សម្រាប់រូបខាងឆ្វេង ចំណងជើង អត្ថបទរង និងធាតុខាងស្តាំ។

```dart
ListTile(
  leading: const CircleAvatar(child: Icon(Icons.person)),
  title: const Text('ចន សុភា', style: TextStyle(height: 1.7)),
  subtitle: const Text('អ្នកអភិវឌ្ឍន៍កម្មវិធី', style: TextStyle(height: 1.7)),
  trailing: const Icon(Icons.chevron_right),
  onTap: () {},
  selected: _isSelected,
  selectedTileColor: Colors.indigo.shade50,
  dense: false,
  contentPadding: const EdgeInsets.symmetric(horizontal: 16),
  shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
)
```

**គ្រួសារ:** `CheckboxListTile` · `RadioListTile` · `SwitchListTile` · `ExpansionTile` · `AboutListTile`

---

### `ExpansionTile` និង `ExpansionPanelList`

```dart
ExpansionTile(
  leading: const Icon(Icons.help_outline),
  title: const Text('សំណួរញឹកញាប់', style: TextStyle(height: 1.7)),
  initiallyExpanded: false,
  onExpansionChanged: (open) => debugPrint('បើក: $open'),
  children: const [
    Padding(padding: EdgeInsets.all(16), child: Text('ចម្លើយ...', style: TextStyle(height: 1.7))),
  ],
)

ExpansionPanelList(
  expansionCallback: (i, isOpen) => setState(() => _open[i] = isOpen),
  children: [
    ExpansionPanel(
      isExpanded: _open[0],
      headerBuilder: (c, isOpen) => const ListTile(title: Text('ក្បាល')),
      body: const Padding(padding: EdgeInsets.all(16), child: Text('តួ')),
    ),
  ],
)
```

---

### `Chip` និងគ្រួសារ

```dart
Chip(label: const Text('ស្លាក'), onDeleted: () {}, avatar: const Icon(Icons.tag))
ActionChip(label: const Text('សកម្មភាព'), onPressed: () {})
FilterChip(label: const Text('តម្រង'), selected: _on, onSelected: (v) => setState(() => _on = v))
ChoiceChip(label: const Text('ជម្រើស'), selected: _sel, onSelected: (v) {})
InputChip(label: const Text('បញ្ចូល'), onDeleted: () {}, onPressed: () {})
```

---

### ប្រអប់សន្ទនា (Dialogs)

```dart
// AlertDialog
showDialog<bool>(
  context: context,
  barrierDismissible: false,
  builder: (context) => AlertDialog(
    icon: const Icon(Icons.warning_amber),
    title: const Text('លុបទិន្នន័យ?'),
    content: const Text('សកម្មភាពនេះមិនអាចត្រឡប់វិញបានទេ។', style: TextStyle(height: 1.7)),
    actions: [
      TextButton(onPressed: () => Navigator.pop(context, false), child: const Text('បោះបង់')),
      FilledButton(onPressed: () => Navigator.pop(context, true), child: const Text('លុប')),
    ],
  ),
);

// SimpleDialog — បញ្ជីជម្រើស
showDialog(context: context, builder: (c) => SimpleDialog(
  title: const Text('ជ្រើសរើសភាសា'),
  children: [
    SimpleDialogOption(onPressed: () => Navigator.pop(c, 'km'), child: const Text('ខ្មែរ')),
    SimpleDialogOption(onPressed: () => Navigator.pop(c, 'en'), child: const Text('English')),
  ],
));

// ប្រអប់ថ្ងៃខែ និងម៉ោង
final date = await showDatePicker(
  context: context,
  initialDate: DateTime.now(),
  firstDate: DateTime(2020),
  lastDate: DateTime(2030),
);
final time = await showTimePicker(context: context, initialTime: TimeOfDay.now());

// AboutDialog
showAboutDialog(context: context, applicationName: 'App', applicationVersion: '1.0.0');
```

---

### `BottomSheet`

```dart
// Modal — បិទផ្ទៃខាងក្រោយ
showModalBottomSheet(
  context: context,
  isScrollControlled: true,          // អនុញ្ញាតកម្ពស់លើសពាក់កណ្តាល
  useSafeArea: true,
  showDragHandle: true,
  shape: const RoundedRectangleBorder(
    borderRadius: BorderRadius.vertical(top: Radius.circular(24)),
  ),
  builder: (context) => Padding(
    padding: EdgeInsets.only(bottom: MediaQuery.viewInsetsOf(context).bottom),
    child: const _EditForm(),
  ),
);

// Persistent — នៅជាមួយ Scaffold
Scaffold.of(context).showBottomSheet((c) => const _Player());
```

---

### `SnackBar` និង `MaterialBanner`

```dart
ScaffoldMessenger.of(context).showSnackBar(
  SnackBar(
    content: const Text('រក្សាទុករួចរាល់', style: TextStyle(height: 1.7)),
    behavior: SnackBarBehavior.floating,
    duration: const Duration(seconds: 3),
    action: SnackBarAction(label: 'មិនធ្វើវិញ', onPressed: _undo),
  ),
);

ScaffoldMessenger.of(context).showMaterialBanner(
  MaterialBanner(
    content: const Text('គ្មានការតភ្ជាប់អ៊ីនធឺណិត'),
    leading: const Icon(Icons.wifi_off),
    actions: [TextButton(onPressed: () {}, child: const Text('ព្យាយាមម្តងទៀត'))],
  ),
);
```

---

### Navigation Components

```dart
// NavigationBar (Material 3) — ជំនួស BottomNavigationBar
NavigationBar(
  selectedIndex: _index,
  onDestinationSelected: (i) => setState(() => _index = i),
  destinations: const [
    NavigationDestination(icon: Icon(Icons.home_outlined),
        selectedIcon: Icon(Icons.home), label: 'ដើម'),
    NavigationDestination(icon: Badge(label: Text('3'), child: Icon(Icons.notifications)),
        label: 'ជូនដំណឹង'),
  ],
)

// NavigationRail — សម្រាប់អេក្រង់ធំ
NavigationRail(
  selectedIndex: _index,
  onDestinationSelected: (i) => setState(() => _index = i),
  labelType: NavigationRailLabelType.all,
  extended: false,
  destinations: const [
    NavigationRailDestination(icon: Icon(Icons.home), label: Text('ដើម')),
  ],
)

// NavigationDrawer (Material 3)
NavigationDrawer(
  selectedIndex: _index,
  onDestinationSelected: (i) {},
  children: const [
    DrawerHeader(child: Text('ម៉ឺនុយ')),
    NavigationDrawerDestination(icon: Icon(Icons.home), label: Text('ដើម')),
  ],
)

// Drawer បែបចាស់
Drawer(child: ListView(children: const [
  UserAccountsDrawerHeader(
    accountName: Text('ចន សុភា'),
    accountEmail: Text('sophea@example.com'),
    currentAccountPicture: CircleAvatar(child: Text('ស')),
  ),
  ListTile(leading: Icon(Icons.settings), title: Text('ការកំណត់')),
]))
```

---

### `TabBar`, `TabBarView`, `DefaultTabController`

```dart
DefaultTabController(
  length: 3,
  child: Scaffold(
    appBar: AppBar(
      bottom: const TabBar(
        isScrollable: true,
        tabAlignment: TabAlignment.start,
        tabs: [Tab(text: 'ថ្មី'), Tab(text: 'ពេញនិយម'), Tab(icon: Icon(Icons.star))],
      ),
    ),
    body: const TabBarView(children: [NewFeed(), Popular(), Favorites()]),
  ),
)
```

---

### `Stepper`

```dart
Stepper(
  currentStep: _step,
  type: StepperType.vertical,
  onStepContinue: () => setState(() => _step++),
  onStepCancel: () => setState(() => _step--),
  steps: const [
    Step(title: Text('ព័ត៌មានផ្ទាល់ខ្លួន'), content: Text('...'), isActive: true),
    Step(title: Text('អាសយដ្ឋាន'), content: Text('...')),
    Step(title: Text('បញ្ជាក់'), content: Text('...'), state: StepState.complete),
  ],
)
```

---

### `DataTable` និង `PaginatedDataTable`

```dart
DataTable(
  sortColumnIndex: 0,
  sortAscending: true,
  columns: const [
    DataColumn(label: Text('ឈ្មោះ')),
    DataColumn(label: Text('តម្លៃ'), numeric: true),
  ],
  rows: products.map((p) => DataRow(
    selected: p.selected,
    onSelectChanged: (v) {},
    cells: [DataCell(Text(p.name)), DataCell(Text('\$${p.price}'))],
  )).toList(),
)
```

---

### Progress Indicators

```dart
const CircularProgressIndicator(strokeWidth: 3)
const CircularProgressIndicator(value: 0.65)          // កំណត់ភាគរយ
const LinearProgressIndicator(value: 0.4, minHeight: 6)
const RefreshProgressIndicator()
```

---

### `Tooltip`, `Badge`, `Divider`

```dart
Tooltip(
  message: 'លុបធាតុនេះ',
  waitDuration: const Duration(milliseconds: 500),
  child: IconButton(icon: const Icon(Icons.delete), onPressed: () {}),
)

const Badge(label: Text('9+'), child: Icon(Icons.mail))
const Badge.count(count: 128, child: Icon(Icons.notifications))

const Divider(height: 32, thickness: 1, indent: 16, endIndent: 16)
const VerticalDivider(width: 1)
```

---

### `PopupMenuButton`

```dart
PopupMenuButton<String>(
  icon: const Icon(Icons.more_vert),
  onSelected: (value) => _handle(value),
  itemBuilder: (context) => const [
    PopupMenuItem(value: 'edit', child: ListTile(leading: Icon(Icons.edit), title: Text('កែសម្រួល'))),
    PopupMenuDivider(),
    PopupMenuItem(value: 'delete', child: ListTile(leading: Icon(Icons.delete), title: Text('លុប'))),
  ],
)
```

---

### `FloatingActionButton`

```dart
FloatingActionButton(onPressed: () {}, child: const Icon(Icons.add))
FloatingActionButton.small(onPressed: () {}, child: const Icon(Icons.edit))
FloatingActionButton.large(onPressed: () {}, child: const Icon(Icons.camera))
FloatingActionButton.extended(
  onPressed: () {},
  icon: const Icon(Icons.add),
  label: const Text('បង្កើតថ្មី'),
)
```

---

### `Theme` និង `MediaQuery`

```dart
// អានពី theme បច្ចុប្បន្ន
final colors = Theme.of(context).colorScheme;
final text = Theme.of(context).textTheme;

// ប្តូរ theme សម្រាប់ subtree មួយ
Theme(
  data: Theme.of(context).copyWith(
    colorScheme: Theme.of(context).colorScheme.copyWith(primary: Colors.red),
  ),
  child: const MySection(),
)

// MediaQuery — ★ ប្រើ method ជាក់លាក់ ដើម្បីជៀស rebuild ដោយឥតប្រយោជន៍
final size = MediaQuery.sizeOf(context);
final padding = MediaQuery.paddingOf(context);
final insets = MediaQuery.viewInsetsOf(context);      // កម្ពស់ក្តារចុច
final brightness = MediaQuery.platformBrightnessOf(context);
final textScale = MediaQuery.textScalerOf(context);
```

**⚠️** កុំប្រើ `MediaQuery.of(context).size` ទៀត — វា rebuild widget របស់អ្នកពេល**អ្វីក៏ដោយ**ក្នុង MediaQuery ប្តូរ (រួមទាំងក្តារចុច)។ ប្រើ `MediaQuery.sizeOf(context)` វិញ។

---
# ជំពូក ៩ — Cupertino (iOS style)

**📖 ពេលណាគួរប្រើ?** ពេលអ្នកចង់ឲ្យ app មើលទៅដូច native iOS ១០០%។ ក្នុងការអនុវត្តជាក់ស្តែង ក្រុមហ៊ុនភាគច្រើនប្រើ Material ទាំង iOS និង Android ដើម្បីកុំឲ្យត្រូវថែទាំ UI ពីរឈុត។

```dart
import 'package:flutter/cupertino.dart';
```

### ឫសគល់ និងរចនាសម្ព័ន្ធ

```dart
CupertinoApp(
  theme: const CupertinoThemeData(
    brightness: Brightness.light,
    primaryColor: CupertinoColors.systemBlue,
  ),
  home: CupertinoPageScaffold(
    navigationBar: const CupertinoNavigationBar(middle: Text('ទំព័រដើម')),
    child: const Center(child: Text('មាតិកា')),
  ),
)

// មាន tab bar
CupertinoTabScaffold(
  tabBar: CupertinoTabBar(items: const [
    BottomNavigationBarItem(icon: Icon(CupertinoIcons.home), label: 'ដើម'),
    BottomNavigationBarItem(icon: Icon(CupertinoIcons.settings), label: 'កំណត់'),
  ]),
  tabBuilder: (context, index) => CupertinoTabView(
    builder: (c) => index == 0 ? const HomeTab() : const SettingsTab(),
  ),
)
```

### តារាង Widget ស្មើគ្នា

| Material | Cupertino |
|---|---|
| `MaterialApp` | `CupertinoApp` |
| `Scaffold` | `CupertinoPageScaffold` |
| `AppBar` | `CupertinoNavigationBar` / `CupertinoSliverNavigationBar` |
| `ElevatedButton` | `CupertinoButton.filled` |
| `TextButton` | `CupertinoButton` |
| `TextField` | `CupertinoTextField` |
| `Switch` | `CupertinoSwitch` |
| `Slider` | `CupertinoSlider` |
| `CircularProgressIndicator` | `CupertinoActivityIndicator` |
| `AlertDialog` | `CupertinoAlertDialog` |
| `showModalBottomSheet` | `showCupertinoModalPopup` + `CupertinoActionSheet` |
| `SegmentedButton` | `CupertinoSegmentedControl` / `CupertinoSlidingSegmentedControl` |
| `showDatePicker` | `CupertinoDatePicker` |
| `Dismissible` | `CupertinoContextMenu` (បែបផ្សេង) |
| `ListTile` | `CupertinoListTile` |
| `Card` | `CupertinoListSection.insetGrouped` |
| `RefreshIndicator` | `CupertinoSliverRefreshControl` |
| `Divider` | `Divider` (ប្រើរួម) |

### ឧទាហរណ៍សំខាន់ៗ

```dart
// ប្រអប់សន្ទនា
showCupertinoDialog(context: context, builder: (c) => CupertinoAlertDialog(
  title: const Text('លុប?'),
  content: const Text('មិនអាចត្រឡប់វិញបានទេ។', style: TextStyle(height: 1.7)),
  actions: [
    CupertinoDialogAction(onPressed: () => Navigator.pop(c), child: const Text('បោះបង់')),
    CupertinoDialogAction(isDestructiveAction: true, onPressed: () {}, child: const Text('លុប')),
  ],
));

// Action sheet
showCupertinoModalPopup(context: context, builder: (c) => CupertinoActionSheet(
  title: const Text('ជ្រើសសកម្មភាព'),
  actions: [
    CupertinoActionSheetAction(onPressed: () {}, child: const Text('ថតរូប')),
    CupertinoActionSheetAction(onPressed: () {}, child: const Text('ជ្រើសពីវិចិត្រសាល')),
  ],
  cancelButton: CupertinoActionSheetAction(
      onPressed: () => Navigator.pop(c), child: const Text('បោះបង់')),
));

// ជម្រើសកាលបរិច្ឆេទ
SizedBox(height: 200, child: CupertinoDatePicker(
  mode: CupertinoDatePickerMode.date,
  initialDateTime: DateTime.now(),
  onDateTimeChanged: (d) => setState(() => _date = d),
))

// បញ្ជីជាផ្នែក
CupertinoListSection.insetGrouped(
  header: const Text('គណនី'),
  children: const [
    CupertinoListTile(title: Text('ប្រវត្តិរូប'), trailing: CupertinoListTileChevron()),
    CupertinoListTile(title: Text('សុវត្ថិភាព'), trailing: CupertinoListTileChevron()),
  ],
)
```

### `Adaptive` constructors

Flutter ផ្តល់ constructor ដែល**ជ្រើសរើសដោយស្វ័យប្រវត្តិ**តាមវេទិកា:

```dart
Switch.adaptive(value: v, onChanged: (_) {})
Slider.adaptive(value: v, onChanged: (_) {})
CircularProgressIndicator.adaptive()
Checkbox.adaptive(value: v, onChanged: (_) {})
showAdaptiveDialog(context: context, builder: (c) => AlertDialog.adaptive(/* ... */));
Theme.of(context).platform  // អានវេទិកាបច្ចុប្បន្ន
```

---

# ជំពូក ១០ — Animation និង Motion

Flutter បែងចែក animation ជា **២ ត្រកូល**:

| ត្រកូល | ឈ្មោះចាប់ផ្តើមដោយ | ត្រូវការ controller? | ពេលណាប្រើ |
|---|---|---|---|
| **Implicit** | `Animated*` | ❌ ទេ | ការផ្លាស់ប្តូរសាមញ្ញពីតម្លៃ A ទៅ B |
| **Explicit** | `*Transition` | ✅ បាទ | ត្រូវការគ្រប់គ្រង (ចាប់ផ្តើម ឈប់ ធ្វើម្តងទៀត) |

---

## ១០.១ Implicit Animations — គ្មាន controller

### `AnimatedContainer`

```dart
AnimatedContainer(
  duration: const Duration(milliseconds: 300),
  curve: Curves.easeInOutCubic,
  width: _expanded ? 300 : 150,
  height: _expanded ? 200 : 100,
  decoration: BoxDecoration(
    color: _expanded ? Colors.indigo : Colors.grey,
    borderRadius: BorderRadius.circular(_expanded ? 24 : 8),
  ),
  onEnd: () => debugPrint('ចប់'),
  child: const Center(child: Text('ចុច')),
)
```

### បញ្ជី implicit widgets ពេញលេញ

| Widget | ធ្វើ animation លើ |
|---|---|
| `AnimatedContainer` | ទំហំ ពណ៌ padding decoration transform |
| `AnimatedOpacity` | ភាពថ្លា |
| `AnimatedPadding` | padding |
| `AnimatedAlign` | ទីតាំងតម្រឹម |
| `AnimatedPositioned` | ទីតាំងក្នុង `Stack` |
| `AnimatedPositionedDirectional` | ដូចខាងលើ តែគោរព RTL |
| `AnimatedSize` | ទំហំតាមកូន |
| `AnimatedDefaultTextStyle` | ស្ទីលអក្សរ |
| `AnimatedPhysicalModel` | elevation និងរូបរាង |
| `AnimatedTheme` | theme ទាំងមូល |
| `AnimatedCrossFade` | ប្តូររវាង widget ២ |
| `AnimatedSwitcher` | ប្តូររវាង widget ណាមួយ |
| `AnimatedList` / `AnimatedGrid` | បន្ថែម/លុបធាតុ |
| `AnimatedIcon` | រូបតំណាងបំលែងរូបរាង |
| `AnimatedRotation` | បង្វិល |
| `AnimatedScale` | ពង្រីក |
| `AnimatedSlide` | រំកិល |
| `AnimatedFractionallySizedBox` | ទំហំជាភាគរយ |
| `TweenAnimationBuilder` | តម្លៃណាមួយដែលអ្នកកំណត់ |

### `AnimatedSwitcher`

```dart
AnimatedSwitcher(
  duration: const Duration(milliseconds: 400),
  transitionBuilder: (child, animation) => FadeTransition(
    opacity: animation,
    child: ScaleTransition(scale: animation, child: child),
  ),
  child: _loading
      ? const CircularProgressIndicator(key: ValueKey('loading'))
      : Text(_data, key: ValueKey(_data)),  // ★ key ចាំបាច់!
)
```

**⚠️** បើគ្មាន `Key` ខុសគ្នា `AnimatedSwitcher` នឹងគិតថាជា widget ដដែល ហើយមិនធ្វើ animation ទេ។

### `TweenAnimationBuilder`

```dart
TweenAnimationBuilder<double>(
  tween: Tween(begin: 0, end: _progress),
  duration: const Duration(milliseconds: 600),
  curve: Curves.easeOut,
  builder: (context, value, child) => Column(children: [
    LinearProgressIndicator(value: value),
    Text('${(value * 100).round()}%'),
  ]),
)
```

---

## ១០.២ Explicit Animations — ត្រូវការ controller

### គំរូមូលដ្ឋាន

```dart
class _MyWidgetState extends State<MyWidget> with SingleTickerProviderStateMixin {
  late final AnimationController _controller = AnimationController(
    duration: const Duration(milliseconds: 800),
    vsync: this,
  );

  late final Animation<double> _fade = CurvedAnimation(
    parent: _controller,
    curve: Curves.easeIn,
  );

  late final Animation<Offset> _slide = Tween<Offset>(
    begin: const Offset(0, 0.3),
    end: Offset.zero,
  ).animate(CurvedAnimation(parent: _controller, curve: Curves.easeOutCubic));

  @override
  void initState() {
    super.initState();
    _controller.forward();
  }

  @override
  void dispose() {
    _controller.dispose();   // ★ ចាំបាច់ បើមិនដូច្នេះ leak
    super.dispose();
  }

  @override
  Widget build(BuildContext context) => FadeTransition(
        opacity: _fade,
        child: SlideTransition(position: _slide, child: widget.child),
      );
}
```

**Mixin ២ យ៉ាង:** `SingleTickerProviderStateMixin` (controller តែ ១) · `TickerProviderStateMixin` (controller ច្រើន)។

### បញ្ជី `*Transition` widgets

| Widget | ធ្វើអ្វី |
|---|---|
| `FadeTransition` | ភាពថ្លា |
| `SlideTransition` | រំកិល (ជា fraction នៃទំហំខ្លួន) |
| `ScaleTransition` | ពង្រីក/បង្រួម |
| `RotationTransition` | បង្វិល |
| `SizeTransition` | បើក/បិទតាមទំហំ |
| `PositionedTransition` / `RelativePositionedTransition` | ទីតាំងក្នុង `Stack` |
| `DecoratedBoxTransition` | ការតុបតែង |
| `AlignTransition` | ការតម្រឹម |
| `DefaultTextStyleTransition` | ស្ទីលអក្សរ |
| `AnimatedBuilder` | អ្វីក៏ដោយ — អ្នកសរសេរ builder ខ្លួនឯង |
| `AnimatedWidget` | class មេសម្រាប់សរសេរ transition ផ្ទាល់ខ្លួន |
| `MatrixTransition` | matrix transform ផ្ទាល់ |

### `AnimatedBuilder` — ការបង្កើនប្រសិទ្ធភាព

```dart
AnimatedBuilder(
  animation: _controller,
  child: const ExpensiveWidget(),   // ★ build តែម្តង មិន rebuild
  builder: (context, child) => Transform.rotate(
    angle: _controller.value * 2 * math.pi,
    child: child,                    // យក child ដដែលមកប្រើ
  ),
)
```

### `Curves` ដែលប្រើញឹកញាប់

```dart
Curves.linear          // ល្បឿនថេរ — កម្រប្រើ
Curves.easeIn          // ចាប់ផ្តើមយឺត
Curves.easeOut         // បញ្ចប់យឺត — ★ ធម្មជាតិបំផុតសម្រាប់ UI
Curves.easeInOut       // យឺតទាំងសងខាង
Curves.easeOutCubic    // ★ ជម្រើសទំនើប
Curves.elasticOut      // លោត
Curves.bounceOut       // ដក់ដូចបាល់
Curves.fastOutSlowIn   // ស្តង់ដារ Material
Interval(0.0, 0.5, curve: Curves.easeIn)  // ដំណើរការតែពាក់កណ្តាលដំបូង
```

---

## ១០.៣ `Hero` — animation ឆ្លងអេក្រង់

```dart
// អេក្រង់ A
Hero(tag: 'product-${product.id}', child: Image.network(product.image))

// អេក្រង់ B — tag ដូចគ្នា
Hero(tag: 'product-${product.id}', child: Image.network(product.image))
```

**⚠️** `tag` ត្រូវតែ**មានតែមួយ**ក្នុងអេក្រង់នីមួយៗ។ បើ `ListView` របស់អ្នកមាន `Hero` ដែល tag ដូចគ្នាពីរ → crash។

---

## ១០.៤ `AnimatedList` និង `AnimatedGrid`

```dart
final _listKey = GlobalKey<AnimatedListState>();

AnimatedList(
  key: _listKey,
  initialItemCount: items.length,
  itemBuilder: (context, index, animation) => SizeTransition(
    sizeFactor: animation,
    child: ListTile(title: Text(items[index])),
  ),
)

void _addItem(String v) {
  items.insert(0, v);
  _listKey.currentState!.insertItem(0, duration: const Duration(milliseconds: 300));
}

void _removeItem(int index) {
  final removed = items.removeAt(index);
  _listKey.currentState!.removeItem(
    index,
    (context, animation) => SizeTransition(
      sizeFactor: animation,
      child: ListTile(title: Text(removed)),
    ),
  );
}
```

---
# ជំពូក ១១ — Interaction: Gestures និង Navigation

---

### `GestureDetector`

**📖 វាជាអ្វី** ចាប់យកកាយវិការគ្រប់ប្រភេទ។ វា**មិនគូរអ្វីទេ** — គ្មាន ripple គ្មានស្រមោល។

```dart
GestureDetector(
  behavior: HitTestBehavior.opaque,  // ★ ចាប់ការចុចលើតំបន់ថ្លាដែរ
  onTap: () {},
  onDoubleTap: () {},
  onLongPress: () {},
  onTapDown: (details) => debugPrint('${details.localPosition}'),
  onPanUpdate: (d) => setState(() => _offset += d.delta),
  onScaleUpdate: (d) => setState(() => _scale = d.scale),
  onHorizontalDragEnd: (d) {
    if (d.primaryVelocity! > 0) _goBack(); else _goNext();
  },
  child: const SizedBox(width: 200, height: 200),
)
```

**កាយវិការទាំងអស់:** `Tap`, `DoubleTap`, `LongPress`, `VerticalDrag`, `HorizontalDrag`, `Pan`, `Scale`, `ForcePress`, `SecondaryTap` (ចុចស្តាំ), `TertiaryTap` (ចុចកណ្តាល)។

**⚠️** មិនអាចប្រើ `onPan*` និង `onScale*` ព្រមគ្នាទេ — `Scale` គ្របដណ្តប់ `Pan` រួចហើយ។

---

### `InkWell` vs `GestureDetector` — ជ្រើសយ៉ាងណា?

| ស្ថានភាព | ប្រើ |
|---|---|
| ធាតុ UI ដែលអ្នកប្រើចុច (ប៊ូតុង card) | `InkWell` — មាន ripple feedback |
| តំបន់ធំដែលចាប់ការចុច ឬអូស | `GestureDetector` |
| ត្រូវការកាយវិការស្មុគស្មាញ (scale, drag) | `GestureDetector` |
| បិទក្តារចុចពេលចុចផ្ទៃទទេ | `GestureDetector` |

---

### `Dismissible`

**📖 វាជាអ្វី** អូសដើម្បីលុប — ដូច Gmail។

```dart
Dismissible(
  key: ValueKey(item.id),      // ★ ចាំបាច់
  direction: DismissDirection.endToStart,
  background: Container(
    color: Colors.red,
    alignment: Alignment.centerRight,
    padding: const EdgeInsets.only(right: 20),
    child: const Icon(Icons.delete, color: Colors.white),
  ),
  confirmDismiss: (dir) async => await _askConfirm(),
  onDismissed: (dir) {
    setState(() => items.remove(item));
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(content: const Text('បានលុប'),
        action: SnackBarAction(label: 'មិនធ្វើវិញ', onPressed: _undo)),
    );
  },
  child: ListTile(title: Text(item.title)),
)
```

**⚠️** អ្នកត្រូវ**លុបធាតុចេញពីទិន្នន័យ**ក្នុង `onDismissed` បើមិនដូច្នេះនឹង crash។

---

### `Draggable` និង `DragTarget`

```dart
Draggable<Task>(
  data: task,
  feedback: Material(
    elevation: 8,
    child: SizedBox(width: 200, child: TaskCard(task)),
  ),
  childWhenDragging: Opacity(opacity: 0.3, child: TaskCard(task)),
  child: TaskCard(task),
)

DragTarget<Task>(
  onWillAcceptWithDetails: (details) => details.data.status != 'done',
  onAcceptWithDetails: (details) => setState(() => _moveTo(details.data, 'done')),
  builder: (context, candidates, rejected) => Container(
    color: candidates.isNotEmpty ? Colors.green.shade50 : Colors.grey.shade100,
    child: const Center(child: Text('ទម្លាក់ទីនេះ', style: TextStyle(height: 1.7))),
  ),
)
```

`LongPressDraggable` — ដូចគ្នាតែត្រូវចុចជាប់មុនអូស។

---

### `AbsorbPointer` និង `IgnorePointer`

```dart
// AbsorbPointer — ស្រូបយកការចុច កូនមិនទទួល តែខ្លួនវាទទួល
AbsorbPointer(absorbing: _isLoading, child: const MyForm())

// IgnorePointer — ការចុចឆ្លងកាត់ទាំងស្រុង ទៅ widget ខាងក្រោម
IgnorePointer(ignoring: true, child: const DecorativeOverlay())
```

---

### `MouseRegion`

**📖 វាជាអ្វី** ចាប់ការចលនា mouse — សម្រាប់ web និង desktop។

```dart
MouseRegion(
  cursor: SystemMouseCursors.click,
  onEnter: (_) => setState(() => _hovering = true),
  onExit: (_) => setState(() => _hovering = false),
  child: AnimatedContainer(
    duration: const Duration(milliseconds: 150),
    transform: Matrix4.diagonal3Values(_hovering ? 1.03 : 1, _hovering ? 1.03 : 1, 1),
    child: const ProductCard(),
  ),
)
```

---

### `Listener`

**📖 វាជាអ្វី** កម្រិតទាបជាង `GestureDetector` — ចាប់ pointer event ឆៅ ដោយមិនចូលរួម gesture arena។

```dart
Listener(
  onPointerDown: (e) => debugPrint('ចុះ ${e.position}'),
  onPointerMove: (e) => debugPrint('ផ្លាស់ ${e.delta}'),
  onPointerSignal: (e) {
    if (e is PointerScrollEvent) debugPrint('រមូរ ${e.scrollDelta}');
  },
  child: const SizedBox.expand(),
)
```

---

### `Navigator` និង Routing

```dart
// រុញអេក្រង់ថ្មី
Navigator.push(context, MaterialPageRoute(builder: (c) => const DetailPage()));

// រុញ ហើយរង់ចាំលទ្ធផល
final result = await Navigator.push<bool>(context,
    MaterialPageRoute(builder: (c) => const EditPage()));

// ថយក្រោយ ព្រមទាំងបញ្ជូនលទ្ធផល
Navigator.pop(context, true);

// ជំនួសអេក្រង់បច្ចុប្បន្ន
Navigator.pushReplacement(context, MaterialPageRoute(builder: (c) => const Home()));

// លុបទាំងអស់ រួចរុញថ្មី (ឧ. ក្រោយចេញពីគណនី)
Navigator.pushAndRemoveUntil(
  context, MaterialPageRoute(builder: (c) => const LoginPage()), (route) => false);

// route ដែលមានឈ្មោះ
Navigator.pushNamed(context, '/settings', arguments: {'tab': 2});
```

### `PopScope` — គ្រប់គ្រងការថយក្រោយ

```dart
PopScope(
  canPop: !_hasUnsavedChanges,
  onPopInvokedWithResult: (didPop, result) async {
    if (didPop) return;
    final leave = await _confirmLeave();
    if (leave && context.mounted) Navigator.pop(context);
  },
  child: const EditForm(),
)
```

**⚠️** `WillPopScope` ត្រូវបានលុបចោលហើយ។ ប្រើ `PopScope` វិញ។

### `PageRouteBuilder` — animation ផ្លាស់ប្តូរអេក្រង់ផ្ទាល់ខ្លួន

```dart
Navigator.push(context, PageRouteBuilder(
  transitionDuration: const Duration(milliseconds: 350),
  pageBuilder: (c, animation, secondary) => const DetailPage(),
  transitionsBuilder: (c, animation, secondary, child) => SlideTransition(
    position: Tween(begin: const Offset(1, 0), end: Offset.zero)
        .animate(CurvedAnimation(parent: animation, curve: Curves.easeOutCubic)),
    child: child,
  ),
));
```

---

# ជំពូក ១២ — Painting និង Visual Effects

---

### គ្រួសារ `Clip*`

```dart
ClipRRect(borderRadius: BorderRadius.circular(16), child: Image.network(url))
const ClipOval(child: FlutterLogo())
ClipRect(child: Align(alignment: Alignment.topCenter, heightFactor: 0.5, child: img))
ClipPath(clipper: _WaveClipper(), child: Container(color: Colors.blue))
```

**⚠️ ការបង្កើនប្រសិទ្ធភាព:** `Clip.antiAlias` ស្អាតជាង តែយឺតជាង `Clip.hardEdge`។ បើអ្នកចង់បានជ្រុងមូលលើពណ៌ធម្មតា ប្រើ `DecoratedBox(borderRadius:)` វិញ — លឿនជាង clip ច្រើន។

---

### `CustomPaint` និង `CustomPainter`

**📖 វាជាអ្វី** គូរអ្វីក៏ដោយដោយផ្ទាល់លើ canvas។

```dart
class RingPainter extends CustomPainter {
  final double progress;
  RingPainter(this.progress);

  @override
  void paint(Canvas canvas, Size size) {
    final center = size.center(Offset.zero);
    final radius = size.width / 2 - 8;

    final track = Paint()
      ..style = PaintingStyle.stroke
      ..strokeWidth = 12
      ..color = Colors.grey.shade200;

    final bar = Paint()
      ..style = PaintingStyle.stroke
      ..strokeWidth = 12
      ..strokeCap = StrokeCap.round
      ..shader = const SweepGradient(colors: [Colors.blue, Colors.purple])
          .createShader(Rect.fromCircle(center: center, radius: radius));

    canvas.drawCircle(center, radius, track);
    canvas.drawArc(
      Rect.fromCircle(center: center, radius: radius),
      -math.pi / 2, 2 * math.pi * progress, false, bar,
    );
  }

  @override
  bool shouldRepaint(RingPainter old) => old.progress != progress;
}

CustomPaint(
  size: const Size(160, 160),
  painter: RingPainter(0.72),
  child: const Center(child: Text('72%')),
)
```

**⚠️** `shouldRepaint` ត្រឡប់ `true` ជានិច្ច = គូរឡើងវិញរាល់ frame = យឺត។ ប្រៀបធៀបតម្លៃពិត។

---

### `Opacity` និង `AnimatedOpacity`

```dart
Opacity(opacity: 0.5, child: const MyWidget())
```

**⚠️ ថ្លៃ!** `Opacity` បង្កើត offscreen layer។ ជម្រើសល្អជាង:
- សម្រាប់ពណ៌: `Colors.black.withValues(alpha: 0.5)`
- សម្រាប់ animation: `FadeTransition` (លឿនជាង `AnimatedOpacity`)
- សម្រាប់លាក់ទាំងស្រុង: `Visibility` ឬ `if (cond)`

---

### `RepaintBoundary`

**📖 វាជាអ្វី** ញែក subtree ចេញជា layer ដាច់ដោយឡែក ដូច្នេះការគូរឡើងវិញរបស់វាមិនប៉ះពាល់អ្នកជិតខាង។

```dart
RepaintBoundary(child: const ComplexAnimation())

// ថតរូប widget
final boundary = _key.currentContext!.findRenderObject() as RenderRepaintBoundary;
final image = await boundary.toImage(pixelRatio: 3.0);
final bytes = await image.toByteData(format: ui.ImageByteFormat.png);
```

---

### `ShaderMask` និង `ColorFiltered`

```dart
// អក្សរមាន gradient
ShaderMask(
  shaderCallback: (bounds) => const LinearGradient(
    colors: [Colors.orange, Colors.pink],
  ).createShader(bounds),
  blendMode: BlendMode.srcIn,
  child: const Text('ជម្រាល', style: TextStyle(fontSize: 40, color: Colors.white, height: 1.7)),
)

// រូបស-ខ្មៅ
ColorFiltered(
  colorFilter: const ColorFilter.matrix([
    0.2126, 0.7152, 0.0722, 0, 0,
    0.2126, 0.7152, 0.0722, 0, 0,
    0.2126, 0.7152, 0.0722, 0, 0,
    0,      0,      0,      1, 0,
  ]),
  child: Image.network(url),
)
```

---

### `PhysicalModel` និង `PhysicalShape`

```dart
PhysicalModel(
  color: Colors.white,
  elevation: 8,
  borderRadius: BorderRadius.circular(16),
  shadowColor: Colors.black26,
  child: const SizedBox(width: 200, height: 100),
)
```

---

### `Banner` និង `CustomSingleChildLayout`

```dart
Banner(
  message: 'សាកល្បង',
  location: BannerLocation.topEnd,
  color: Colors.red,
  child: const MyApp(),
)
```

---
# ជំពូក ១៣ — Async Widgets

---

### `FutureBuilder`

```dart
FutureBuilder<List<Product>>(
  future: _future,          // ★ រក្សាក្នុង State មិនមែនហៅក្នុង build()
  builder: (context, snapshot) {
    return switch (snapshot.connectionState) {
      ConnectionState.waiting => const Center(child: CircularProgressIndicator()),
      _ when snapshot.hasError => Center(
          child: Text('មានបញ្ហា: ${snapshot.error}', style: const TextStyle(height: 1.7))),
      _ when !snapshot.hasData || snapshot.data!.isEmpty =>
          const Center(child: Text('គ្មានទិន្នន័យ', style: TextStyle(height: 1.7))),
      _ => ListView.builder(
          itemCount: snapshot.data!.length,
          itemBuilder: (c, i) => ProductTile(snapshot.data![i]),
        ),
    };
  },
)
```

**⚠️ កំហុសធំបំផុត:**

```dart
// ❌ ខុស — ហៅ API ថ្មីរាល់ពេល build()
FutureBuilder(future: api.fetchProducts(), builder: ...)

// ✅ ត្រូវ — បង្កើតម្តងក្នុង initState
late final Future<List<Product>> _future = api.fetchProducts();
```

---

### `StreamBuilder`

```dart
StreamBuilder<int>(
  stream: _counterStream,
  initialData: 0,
  builder: (context, snapshot) {
    if (snapshot.hasError) return Text('បញ្ហា: ${snapshot.error}');
    return Text('រាប់: ${snapshot.data}', style: const TextStyle(height: 1.7));
  },
)
```

**`ConnectionState` ៤ ស្ថានភាព:** `none` · `waiting` · `active` · `done`

---

### `ValueListenableBuilder`

**📖 វាជាអ្វី** ស្តាប់ `ValueNotifier` ហើយ rebuild តែផ្នែកតូចមួយ។ វិធីស្រាលបំផុតសម្រាប់ state មូលដ្ឋាន។

```dart
final _count = ValueNotifier<int>(0);

ValueListenableBuilder<int>(
  valueListenable: _count,
  child: const Icon(Icons.star),      // build តែម្តង
  builder: (context, value, child) => Row(children: [
    child!,
    Text('$value', style: const TextStyle(height: 1.7)),
  ]),
)

// កែតម្លៃ — គ្មាន setState
_count.value++;

@override
void dispose() { _count.dispose(); super.dispose(); }
```

---

### `ListenableBuilder` និង `AnimatedBuilder`

```dart
// ListenableBuilder — ស្តាប់ Listenable ណាមួយ (ChangeNotifier, ScrollController...)
ListenableBuilder(
  listenable: _scrollController,
  builder: (context, child) => Opacity(
    opacity: (_scrollController.offset / 200).clamp(0, 1),
    child: const AppBarTitle(),
  ),
)
```

`AnimatedBuilder` និង `ListenableBuilder` គឺ**ដូចគ្នាបេះបិទ**។ ប្រើឈ្មោះដែលឆ្លុះបញ្ចាំងចេតនា។

---

### `InheritedWidget` និង `InheritedNotifier`

**📖 វាជាអ្វី** មូលដ្ឋានគ្រឹះនៃ state management ទាំងអស់ក្នុង Flutter (`Provider`, `Theme`, `MediaQuery` សុទ្ធតែជាវា)។

```dart
class AppConfig extends InheritedWidget {
  final String apiUrl;
  final bool isDark;

  const AppConfig({
    super.key,
    required this.apiUrl,
    required this.isDark,
    required super.child,
  });

  static AppConfig of(BuildContext context) {
    final result = context.dependOnInheritedWidgetOfExactType<AppConfig>();
    assert(result != null, 'រកមិនឃើញ AppConfig ក្នុងមែកធាង');
    return result!;
  }

  @override
  bool updateShouldNotify(AppConfig old) =>
      apiUrl != old.apiUrl || isDark != old.isDark;
}

// ប្រើ — O(1) lookup មិនមែនដើរឡើងតាមមែកធាងទេ
final url = AppConfig.of(context).apiUrl;
```

**🔬 ភស្តុតាង:** `dependOnInheritedWidgetOfExactType` លឿនព្រោះ `Element` នីមួយៗរក្សា `Map<Type, InheritedElement>` ដែលទទួលមរតកពី parent។ វា**មិន**ដើរឡើងលើមែកធាងទេ។

---

### `Builder`

**📖 វាជាអ្វី** បង្កើត `BuildContext` ថ្មីមួយកម្រិតចុះក្រោម។ ដោះស្រាយកំហុស "Scaffold.of() called with a context that does not contain a Scaffold"។

```dart
Scaffold(
  body: Builder(
    builder: (context) => ElevatedButton(
      // context នេះនៅ *ក្រោម* Scaffold ហើយ
      onPressed: () => Scaffold.of(context).openDrawer(),
      child: const Text('បើកម៉ឺនុយ'),
    ),
  ),
)
```

---

### `StatefulBuilder`

**📖 វាជាអ្វី** មាន `setState` មូលដ្ឋានដោយមិនចាំបាច់បង្កើត `StatefulWidget` ថ្មី។ ល្អសម្រាប់ dialog។

```dart
showDialog(context: context, builder: (context) => StatefulBuilder(
  builder: (context, setLocalState) => AlertDialog(
    content: Switch(
      value: _temp,
      onChanged: (v) => setLocalState(() => _temp = v),
    ),
  ),
));
```

---

# ជំពូក ១៤ — Accessibility និង Semantics

---

### `Semantics`

**📖 វាជាអ្វី** ផ្តល់ព័ត៌មានឲ្យ screen reader (TalkBack, VoiceOver)។

```dart
Semantics(
  label: 'ប៊ូតុងលុបរូបភាព',
  hint: 'ចុចពីរដងដើម្បីលុប',
  button: true,
  enabled: true,
  onTap: _delete,
  child: const Icon(Icons.delete),
)

// លាក់ធាតុតុបតែងពី screen reader
const ExcludeSemantics(child: DecorativeBackground())

// បញ្ចូលកូនៗទាំងអស់ជាសំឡេងតែមួយ
const MergeSemantics(
  child: Row(children: [Icon(Icons.star), Text('៤.៥ ផ្កាយ')]),
)

// ប្រកាសផ្លាស់ប្តូរដល់អ្នកប្រើ
SemanticsService.announce('បានផ្ទុករួច', TextDirection.ltr);
```

### បញ្ជីត្រួតពិនិត្យ Accessibility

| ចំណុច | របៀបធ្វើ |
|---|---|
| គោលដៅចុចធំល្មម | យ៉ាងតិច 48×48 dp — ប្រើ `SizedBox` ឬ `IconButton` |
| កម្រិតកម្រិតពណ៌ | អត្រា 4.5:1 សម្រាប់អក្សរធម្មតា |
| គាំទ្រពង្រីកអក្សរ | កុំដាក់កម្ពស់ថេរលើ widget ដែលមានអក្សរ |
| ស្លាកសម្រាប់រូបតំណាង | `semanticLabel` លើ `Icon` និង `Image` |
| ចលនាបន្ថយ | ពិនិត្យ `MediaQuery.disableAnimationsOf(context)` |

---

### `Directionality`

```dart
const Directionality(
  textDirection: TextDirection.rtl,
  child: MyArabicSection(),
)
```

---

# ជំពូក ១៥ — Widgets កម្រប្រើ តែសង្គ្រោះជីវិត

| Widget | ពេលណាវាសង្គ្រោះអ្នក |
|---|---|
| `ColoredBox` | ជំនួស `Container(color:)` ដើម្បីបាន `const` |
| `SizedBox.shrink()` | ត្រឡប់ widget "គ្មានអ្វី" ដោយស្អាត |
| `Builder` | ដោះស្រាយ `context` ខុសកម្រិត |
| `LayoutBuilder` | responsive design ត្រឹមត្រូវ (មិនមែន `MediaQuery`) |
| `IntrinsicHeight` | ធ្វើឲ្យ column ក្នុង row កម្ពស់ស្មើគ្នា |
| `Flow` | animation ទីតាំងដោយគ្មាន relayout |
| `RepaintBoundary` | ញែក animation ធ្ងន់ចេញ + ថតរូប widget |
| `Overlay` / `OverlayEntry` | tooltip, dropdown ផ្ទាល់ខ្លួន, coach mark |
| `CompositedTransformTarget` / `Follower` | ភ្ជាប់ overlay ទៅ widget ដែលរំកិល |
| `Table` | តម្រឹមជួរឈរឲ្យស្របគ្នាពិតប្រាកដ |
| `ClipPath` | រូបរាងផ្ទាល់ខ្លួន (រលក ត្រីកោណ) |
| `WidgetSpan` | បញ្ចូល widget ក្នុងខ្សែអក្សរ |
| `AutomaticKeepAlive` | រក្សា state របស់ធាតុក្នុង `ListView` |
| `PrimaryScrollController` | ធ្វើឲ្យ "ចុចលើ status bar ដើម្បីឡើងលើ" ដំណើរការ |
| `Actions` / `Shortcuts` | keyboard shortcut បែបស្ថាបត្យកម្ម |
| `WidgetsBindingObserver` | ចាប់ app lifecycle (paused, resumed) |

### `Overlay` និង `CompositedTransformFollower`

```dart
final _link = LayerLink();
OverlayEntry? _entry;

void _showDropdown(BuildContext context) {
  _entry = OverlayEntry(builder: (context) => Positioned(
    width: 200,
    child: CompositedTransformFollower(
      link: _link,
      offset: const Offset(0, 48),
      child: Material(elevation: 4, child: _MenuItems()),
    ),
  ));
  Overlay.of(context).insert(_entry!);
}

// widget គោល
CompositedTransformTarget(link: _link, child: const MyButton())
```

### `AutomaticKeepAliveClientMixin`

```dart
class _TabState extends State<Tab> with AutomaticKeepAliveClientMixin {
  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);   // ★ ត្រូវតែហៅ
    return const MyContent();
  }
}
```

---
# ឧបសម្ព័ន្ធ — តារាងជ្រើសរើសរហ័ស

## 🎯 "ខ្ញុំចង់..." → ប្រើ widget ណា?

| ខ្ញុំចង់... | ប្រើ |
|---|---|
| ដាក់គម្លាតជុំវិញ | `Padding` |
| ដាក់ចន្លោះរវាងធាតុ | `SizedBox` ឬ `spacing:` របស់ `Row`/`Column` |
| ដាក់ពណ៌ផ្ទៃខាងក្រោយ | `ColoredBox` (មិនមែន `Container`) |
| ជ្រុងមូល + ស្រមោល | `DecoratedBox` ឬ `Card` |
| រៀបផ្ដេក | `Row` |
| រៀបបញ្ឈរ | `Column` |
| រៀបហើយចុះបន្ទាត់ថ្មីពេលពេញ | `Wrap` |
| ត្រួតលើគ្នា | `Stack` + `Positioned` |
| ចែកទំហំតាមសមាមាត្រ | `Expanded(flex:)` |
| រុញទៅចុងសងខាង | `Spacer` ឬ `MainAxisAlignment.spaceBetween` |
| ការពារ text overflow | `Expanded` + `overflow: ellipsis` |
| បញ្ជីវែង | `ListView.builder` |
| បញ្ជីជាក្រឡា | `GridView.builder` |
| មាតិកាតិចដែល scroll បាន | `SingleChildScrollView` |
| header បង្រួមពេល scroll | `CustomScrollView` + `SliverAppBar` |
| ទាញចុះដើម្បីផ្ទុកឡើងវិញ | `RefreshIndicator` |
| ផ្ទុកបន្ថែមពេល scroll ដល់ចុង | `NotificationListener<ScrollNotification>` |
| responsive តាមទំហំ | `LayoutBuilder` |
| ដឹងកម្ពស់ក្តារចុច | `MediaQuery.viewInsetsOf(context).bottom` |
| ជៀស notch | `SafeArea` |
| animation សាមញ្ញ A→B | `AnimatedContainer` ឬ `Animated*` ផ្សេងទៀត |
| animation ដែលគ្រប់គ្រងបាន | `AnimationController` + `*Transition` |
| ប្តូររវាង widget ២ ដោយរលូន | `AnimatedSwitcher` |
| animation ឆ្លងអេក្រង់ | `Hero` |
| ចាប់ការចុច (មាន ripple) | `InkWell` ក្នុង `Material` |
| ចាប់ការចុច (គ្មាន ripple) | `GestureDetector` |
| អូសដើម្បីលុប | `Dismissible` |
| អូស និងទម្លាក់ | `Draggable` + `DragTarget` |
| pinch zoom | `InteractiveViewer` |
| បង្ហាញទិន្នន័យពី API | `FutureBuilder` |
| បង្ហាញទិន្នន័យបន្តផ្ទាល់ | `StreamBuilder` |
| rebuild តែផ្នែកតូច | `ValueListenableBuilder` |
| បញ្ជូនទិន្នន័យចុះក្រោម | `InheritedWidget` |
| ដោះស្រាយ `context` ខុសកម្រិត | `Builder` |
| dropdown ផ្ទាល់ខ្លួន | `Overlay` + `CompositedTransformFollower` |
| គូររូបរាងផ្ទាល់ខ្លួន | `CustomPaint` + `CustomPainter` |
| កាត់រូបជារង្វង់ | `ClipOval` ឬ `CircleAvatar` |
| កញ្ចក់ព្រិល | `ClipRRect` + `BackdropFilter` |
| អក្សរមាន gradient | `ShaderMask` |
| លាក់តែរក្សា state | `Offstage` ឬ `Visibility(maintainState: true)` |
| ធ្វើឲ្យអ្នកខ្វាក់ប្រើបាន | `Semantics` |

---

## ⚡ តារាងប្រសិទ្ធភាព — ជៀស ↔ ប្រើ

| ❌ ជៀស | ✅ ប្រើវិញ | ហេតុអ្វី |
|---|---|---|
| `Container(color:)` | `ColoredBox` | `const` បាន · widget តិចជាង |
| `Container(padding:)` | `Padding` | ដូចខាងលើ |
| `Opacity` | `withValues(alpha:)` ឬ `FadeTransition` | ជៀស offscreen layer |
| `MediaQuery.of(context).size` | `MediaQuery.sizeOf(context)` | rebuild តិចជាង |
| `ListView(children: [...])` | `ListView.builder` | lazy loading |
| `shrinkWrap: true` | `SliverList` ក្នុង `CustomScrollView` | រក្សា lazy loading |
| `IntrinsicHeight` ក្នុងបញ្ជី | កម្ពស់ថេរ ឬ `itemExtent` | O(N²) → O(N) |
| `setState` លើ widget ធំ | `ValueListenableBuilder` | rebuild តូចជាង |
| `Image.network` (production) | `cached_network_image` | មាន disk cache |
| `AnimatedOpacity` | `FadeTransition` | គ្រប់គ្រងបានច្បាស់ជាង |
| `shouldRepaint => true` | ប្រៀបធៀបតម្លៃពិត | ជៀសការគូរឡើងវិញ |
| `Column` + `SingleChildScrollView` (ធាតុច្រើន) | `ListView` | lazy loading |

---

## 🔑 គោលការណ៍ `Key` — ពេលណាចាំបាច់

| ស្ថានភាព | ត្រូវការ Key? |
|---|---|
| បញ្ជីស្ថិតិ មិនប្តូរលំដាប់ | ❌ ទេ |
| `ListView` ដែលអាចលុប/បន្ថែម/រៀបលំដាប់ | ✅ `ValueKey(item.id)` |
| `Dismissible` | ✅ ចាំបាច់ |
| `ReorderableListView` | ✅ ចាំបាច់ |
| `AnimatedSwitcher` | ✅ ចាំបាច់ ដើម្បីស្គាល់ការប្តូរ |
| ចូលទៅ `State` ពីខាងក្រៅ (ឧ. `FormState`) | ✅ `GlobalKey` |
| រក្សា state ពេលប្តូរទីតាំងក្នុងមែកធាង | ✅ `ValueKey` ឬ `ObjectKey` |

**⚠️** កុំប្រើ `GlobalKey` ដោយឥតប្រយោជន៍ — វាថ្លៃ និងតែងតែជាសញ្ញាថាមានវិធីល្អជាង។

---

## 📐 មេរៀនចុងក្រោយ: ដោះស្រាយបញ្ហា Layout

ពេលអ្នកជួប layout error សួរខ្លួនឯង ៣ សំណួរនេះតាមលំដាប់:

**១. តើ parent ផ្តល់ constraint អ្វី?**
```dart
// ដាក់នេះបណ្តោះអាសន្នដើម្បីមើល
LayoutBuilder(builder: (context, constraints) {
  debugPrint('constraints: $constraints');
  return myWidget;
})
```

**២. តើ widget នេះចង់បានទំហំប៉ុន្មាន?**
- `Text`, `Icon` → តូចប៉ុនមាតិកា
- `ListView`, `Center`, `Container` (គ្មានកូន) → ធំបំផុតតាមដែលអាច
- `Row`, `Column` → តាម `mainAxisSize`

**៣. តើមាន "unbounded" នៅត្រង់ណា?**
- `Column` → កម្ពស់កូនគ្មានព្រំដែន
- `Row` → ទទឹងកូនគ្មានព្រំដែន
- `SingleChildScrollView` → ទិសដៅ scroll គ្មានព្រំដែន

**ដំណោះស្រាយទូទៅ:** រុំដោយ `Expanded` (ក្នុង Flex) ឬ `SizedBox(height:)` (ក្នុង scroll view)។

---

## 🛠 ឧបករណ៍បំបាត់កំហុស

```dart
// បង្ហាញព្រំដែន widget ទាំងអស់
import 'package:flutter/rendering.dart';
void main() {
  debugPaintSizeEnabled = true;      // ស៊ុមខៀវ + ព្រួញ
  debugPaintPointersEnabled = true;  // បង្ហាញការចុច
  debugRepaintRainbowEnabled = true; // ពណ៌ប្តូរពេលគូរឡើងវិញ
  runApp(const MyApp());
}

// បោះពុម្ពមែកធាង widget
debugDumpApp();
debugDumpRenderTree();
```

ក្នុង **Flutter DevTools**: `Widget Inspector` សម្រាប់មើលមែកធាង · `Performance` សម្រាប់រក jank · `Memory` សម្រាប់រក leak។

---

## 📖 ឯកសារយោង

- Widget catalog ផ្លូវការ: `docs.flutter.dev/ui/widgets`
- API reference: `api.flutter.dev`
- Source code: `github.com/flutter/flutter/tree/master/packages/flutter/lib/src`
- វីដេអូ "Widget of the Week": ឆាន់ណែល YouTube ផ្លូវការរបស់ Flutter

> **ដំបូន្មានចុងក្រោយ:** កុំព្យាយាមចាំ widget ទាំងអស់។ ចាំតែ **គោលការណ៍ constraint** និង **តារាងជ្រើសរើសរហ័ស**ខាងលើ។ ពេលអ្នកត្រូវការអ្វីជាក់លាក់ បើកសៀវភៅនេះឡើងវិញ។ ក្រោយសរសេរកូដ ៦ ខែ អ្នកនឹងចាំវាដោយស្វ័យប្រវត្តិ។

---

*ចងក្រងសម្រាប់ Flutter 3.47.1 · Dart 3.13.1 · កញ្ញា ២០២៦*
