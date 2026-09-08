# 📘 សៀវភៅលំហាត់ Flutter (Flutter Exercise Book)

> សម្រាប់អ្នកដែលបានរៀនចប់ Flutter Tutorial ជាភាសាខ្មែរ — លំហាត់ចំនួន **45** ដែលរៀបតាមលំដាប់ពី Dart internals ដល់ production app
>
> **Flutter stable 3.47 · Dart 3.12+**

---

## 🧭 របៀបប្រើសៀវភៅនេះ

### ច្បាប់ ៥ យ៉ាង

1. **កុំមើលចម្លើយមុន។** បើជាប់ ១៥ នាទី សូមមើលត្រឹម 💡 គន្លឹះ មុនសិន។
2. **សរសេរកូដឲ្យរត់បាន។** កុំគ្រាន់តែអានក្នុងក្បាល — បើកគម្រោងពិត ហើយ `flutter run`។
3. **ទាយចម្លើយមុនរត់កូដ។** ជាពិសេសលំហាត់ដែលសួរ "output ជាអ្វី?" — សរសេរចម្លើយចុះក្រដាសមុន រួចទើបរត់។ គម្លាតរវាងអ្វីដែលអ្នកទាយ និងអ្វីដែលកើតឡើងពិត គឺជាកន្លែងដែលអ្នករៀនបានច្រើនបំផុត។
4. **លំហាត់នីមួយៗមាន ✅ លក្ខខណ្ឌជោគជ័យ។** បើមិនទាន់គ្រប់លក្ខខណ្ឌ កុំទាន់ទៅមុខ។
5. **ធ្វើតាមលំដាប់។** ផ្នែកក្រោយពឹងលើផ្នែកមុន។

### ការរៀបចំគម្រោង

```bash
flutter create --org kh.study exercise_lab
cd exercise_lab
flutter --version   # confirm 3.47.x
```

បង្កើត folder មួយសម្រាប់លំហាត់នីមួយៗ៖

```
lib/
  ex01_null_safety/
  ex02_const/
  ex03_records/
  ...
  main.dart          # menu ដែលនាំទៅរាល់លំហាត់
```

### កម្រិតលំបាក

| សញ្ញា | មានន័យថា |
|---|---|
| ⭐ | មូលដ្ឋាន — គួរធ្វើបានក្នុង ១០–១៥ នាទី |
| ⭐⭐ | មធ្យម — ត្រូវគិត ត្រូវសាកល្បង |
| ⭐⭐⭐ | ពិបាក — ជិតនឹងបញ្ហាការងារពិត |
| ⭐⭐⭐⭐ | ជម្រៅ — អ្នកនឹងត្រូវអាន framework source |

---

# ផ្នែកទី ១ — Dart Internals (លំហាត់ ១.១ – ១.៩)

> គោលដៅផ្នែកនេះ៖ អ្នកត្រូវយល់ថា Dart *ដំណើរការយ៉ាងដូចម្ដេច* មិនត្រឹមតែ *សរសេរយ៉ាងដូចម្ដេច* ទេ។ បើផ្នែកនេះមិនរឹងមាំ រាល់ bug នៅ Flutter នឹងមើលទៅដូចជាមន្តអាគម។

---

### លំហាត់ ១.១ — Null Safety និង Flow Analysis
**កម្រិត:** ⭐ · **ពេលវេលា:** ~15 នាទី

**🎯 គោលដៅ:** យល់ថាហេតុអ្វី compiler ព្រមឬមិនព្រម promote `String?` ទៅជា `String`។

**📋 ការងារ:** សម្រាប់កូដខាងក្រោម សូម **ទាយមុន** ថាបន្ទាត់ណា compile មិនចេញ រួចទើបសាកល្បង។ បន្ទាប់មកកែឲ្យត្រូវទាំងអស់ ដោយ **មិនប្រើ `!`** សូម្បីតែមួយកន្លែង។

```dart
class Profile {
  String? nickname;
  final String? email;
  Profile(this.email);

  int nicknameLength() {
    if (nickname != null) {
      return nickname.length;        // (A)
    }
    return 0;
  }

  int emailLength() {
    if (email != null) {
      return email.length;           // (B)
    }
    return 0;
  }
}

void main() {
  String? name;
  final list = <String?>['a', null, 'c'];

  for (final item in list) {
    if (item != null) {
      print(item.toUpperCase());     // (C)
    }
  }

  name = 'Heak';
  print(name.length);                // (D)

  late final int cached;
  print(cached);                     // (E)
  cached = 10;
}
```

**✅ លក្ខខណ្ឌជោគជ័យ:**
- អ្នកពន្យល់បានថាហេតុអ្វី (A) ខុស តែ (B) ត្រូវ
- កូដ compile ស្អាតដោយគ្មាន `!` និងគ្មាន warning
- អ្នកដឹងថា (E) បរាជ័យនៅពេលណា — compile-time ឬ runtime

**💡 គន្លឹះ:** សួរខ្លួនឯង — "តើមានអ្នកណាផ្សេងអាចប្ដូរតម្លៃនេះនៅចន្លោះ `if` និងការប្រើប្រាស់ដែរឬទេ?"

---

### លំហាត់ ១.២ — const Canonicalization
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~20 នាទី

**🎯 គោលដៅ:** បញ្ជាក់ដោយភស្តុតាងថា `const` មិនត្រឹមតែ "លឿនជាង" ទេ — វាធ្វើឲ្យ object **តែមួយ** ត្រូវបានប្រើឡើងវិញ។

**📋 ការងារ:** ទាយ output នៃ `identical()` ទាំង ៦ ខាងក្រោម រួចរត់ដើម្បីផ្ទៀងផ្ទាត់។

```dart
class Point {
  final int x, y;
  const Point(this.x, this.y);
}

void main() {
  const a = Point(1, 2);
  const b = Point(1, 2);
  final c = Point(1, 2);
  final d = const Point(1, 2);
  const list1 = [1, 2, 3];
  const list2 = [1, 2, 3];

  print(identical(a, b));        // 1
  print(identical(a, c));        // 2
  print(identical(a, d));        // 3
  print(identical(list1, list2));// 4
  print(a == c);                 // 5
  print(identical([1,2,3], [1,2,3])); // 6
}
```

បន្ទាប់មក៖ បន្ថែម field `final DateTime? created;` ចូល `Point` ។ តើមានអ្វីកើតឡើង? ហេតុអ្វី?

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកពន្យល់បានថាហេតុអ្វី const widget នៅក្នុង Flutter មិន rebuild — ដោយភ្ជាប់ទៅនឹងលទ្ធផលនៃលំហាត់នេះ។

---

### លំហាត់ ១.៣ — Records ជំនួស Map
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~25 នាទី

**🎯 គោលដៅ:** ប្រើ records សម្រាប់ត្រឡប់តម្លៃច្រើន ដោយរក្សា type safety។

**📋 ការងារ:** Refactor កូដអាក្រក់នេះ៖

```dart
// ❌ មុន — គ្មាន type safety, key ខុសក៏មិនដឹង
Map<String, dynamic> parseKhmerPhone(String raw) {
  final cleaned = raw.replaceAll(RegExp(r'[\s-]'), '');
  if (!cleaned.startsWith('0') && !cleaned.startsWith('+855')) {
    return {'valid': false, 'operator': null, 'national': null};
  }
  final national = cleaned.startsWith('+855') ? '0${cleaned.substring(4)}' : cleaned;
  final prefix = national.substring(0, 3);
  final op = switch (prefix) {
    '010' || '015' || '016' || '069' || '070' || '081' || '086' || '087' => 'Smart',
    '011' || '012' || '017' || '061' || '076' || '077' || '085' || '089' => 'Cellcard',
    '031' || '060' || '066' || '067' || '068' || '071' || '088' => 'Metfone',
    _ => 'Unknown',
  };
  return {'valid': true, 'operator': op, 'national': national};
}
```

ឲ្យក្លាយជា៖
1. Function ដែលត្រឡប់ **named record** `({bool valid, String? operator, String? national})`
2. ប្រើ **pattern destructuring** នៅកន្លែងហៅ
3. សរសេរ `switch` ដែល destructure record ដោយផ្ទាល់ ដើម្បីបោះពុម្ពសារខុសៗគ្នា

**✅ លក្ខខណ្ឌជោគជ័យ:** បើអ្នកសរសេរ `result.oprator` (អក្ខរាវិរុទ្ធខុស) — compiler ត្រូវតែស្រែក។ នេះជាចំណុចសំខាន់ទាំងមូល។

---

### លំហាត់ ១.៤ — Sealed Class និង Exhaustiveness
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~30 នាទី

**🎯 គោលដៅ:** ឲ្យ compiler ក្លាយជាអ្នកត្រួតពិនិត្យ logic របស់អ្នក។

**📋 ការងារ:**
1. បង្កើត `sealed class UiState<T>` មាន ៤ variant៖ `Idle`, `Loading`, `Success<T>` (មាន `data`), `Failure` (មាន `message` និង `Object? cause`)
2. សរសេរ function `String describe<T>(UiState<T> s)` ដោយប្រើ `switch` expression **ដោយគ្មាន `default` ឬ `_`**
3. ឥឡូវបន្ថែម variant ទី ៥៖ `Empty`។ **កុំកែ `describe`។** រត់ `dart analyze`។ អានសារ error ឲ្យបានល្អិតល្អន់។
4. កែ `describe` ឲ្យត្រូវវិញ។

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកអាចពន្យល់បានថាហេតុអ្វី `sealed` + គ្មាន `default` = សុវត្ថិភាព ចំណែក `abstract` + `default` = គ្រោះថ្នាក់នៅពេល refactor។

**💡 គន្លឹះ:** `sealed` បញ្ជាក់ថាគ្រប់ subclass ស្ថិតក្នុង library តែមួយ ដូច្នេះ compiler ដឹងបញ្ជីពេញ។

---

### លំហាត់ ១.៥ — Event Loop: ទាយលំដាប់
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~30 នាទី

**🎯 គោលដៅ:** យល់ microtask queue ធៀបនឹង event queue។

**📋 ការងារ:** សរសេរលំដាប់ output នៅលើក្រដាស **មុននឹងរត់**។

```dart
import 'dart:async';

void main() {
  print('1');

  Future(() => print('2'));

  Future.microtask(() => print('3'));

  Future.value(4).then((v) => print(v));

  scheduleMicrotask(() => print('5'));

  Timer.run(() => print('6'));

  Future.delayed(Duration.zero, () => print('7'));

  Future(() => print('8')).then((_) => print('9'));

  () async {
    print('10');
    await null;
    print('11');
  }();

  print('12');
}
```

បន្ទាប់មក៖ ប្ដូរ `await null;` ទៅជា `await Future.delayed(Duration.zero);` ។ តើ `11` ផ្លាស់ទីទៅណា? ហេតុអ្វី?

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកទាយបានត្រូវយ៉ាងតិច ១០/១២ ហើយអាចពន្យល់ថាហេតុអ្វី `await null` ខុសពី `await Future.delayed(Duration.zero)`។

---

### លំហាត់ ១.៦ — សរសេរ Debounce StreamTransformer
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~40 នាទី

**🎯 គោលដៅ:** គ្រប់គ្រង Stream ក្នុងកម្រិតទាប ដោយមិនប្រើ package `rxdart`។

**📋 ការងារ:** សរសេរ `StreamTransformer<T, T> debounce<T>(Duration d)` ដោយប្រើ `StreamTransformer.fromBind` ឬ `StreamController` ដោយផ្ទាល់។

លក្ខខណ្ឌ៖
- បញ្ចេញតែ event ចុងក្រោយ បន្ទាប់ពីស្ងាត់រយៈពេល `d`
- ពេល source `done` ត្រូវបញ្ចេញ event ដែលកំពុងរង់ចាំ (pending) ជាមុនសិន រួចទើប close
- ត្រូវ cancel timer នៅពេល subscription ត្រូវបាន cancel (**មិនត្រូវមាន memory leak**)
- ត្រូវបញ្ជូន error ឆ្លងកាត់ (forward) ដោយមិនបាត់

សាកល្បងជាមួយ៖

```dart
Stream<int> source() async* {
  yield 1; await Future.delayed(const Duration(milliseconds: 50));
  yield 2; await Future.delayed(const Duration(milliseconds: 50));
  yield 3; await Future.delayed(const Duration(milliseconds: 400));
  yield 4;
}
// expected with debounce(300ms): 3, then 4
```

**✅ លក្ខខណ្ឌជោគជ័យ:** សរសេរ unit test ដោយប្រើ `fakeAsync` ឬ `Stream.timeout` ដែលបញ្ជាក់លទ្ធផល `[3, 4]`។

---

### លំហាត់ ១.៧ — Broadcast ធៀបនឹង Single-subscription
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~20 នាទី

**📋 ការងារ:** កូដនេះមាន bug ២។ រកឲ្យឃើញ ហើយកែ។

```dart
class TickerService {
  final _controller = StreamController<int>();
  Stream<int> get ticks => _controller.stream;

  TickerService() {
    Timer.periodic(const Duration(seconds: 1), (t) {
      _controller.add(t.tick);
    });
  }
}

void main() {
  final s = TickerService();
  s.ticks.listen((v) => print('A: $v'));
  s.ticks.listen((v) => print('B: $v'));   // 💥
}
```

**✅ លក្ខខណ្ឌជោគជ័យ:** ទាំង A និង B ទទួលបានតម្លៃ · មាន `dispose()` ដែល cancel timer និង close controller · គ្មាន "Bad state: Stream has already been listened to"។

---

### លំហាត់ ១.៨ — Isolate សម្រាប់ការងារធ្ងន់
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~35 នាទី

**🎯 គោលដៅ:** មើលឃើញ jank ដោយភ្នែក រួចលុបវាចោល។

**📋 ការងារ:**
1. បង្កើតទំព័រមាន `CircularProgressIndicator` កំពុងវិល និងប៊ូតុងមួយ
2. ប៊ូតុងត្រូវ parse JSON ធំ (~5MB — បង្កើតដោយ loop) ហើយរាប់ចំនួន item
3. ដំណាក់កាល A៖ ធ្វើនៅលើ main isolate → **កត់ត្រាថា indicator ឈប់វិល**
4. ដំណាក់កាល B៖ ប្ដូរទៅ `Isolate.run(...)` → indicator ត្រូវវិលរលូន
5. ដំណាក់កាល C៖ សាកល្បងបញ្ជូន object ដែល send មិនបាន (ឧ. object មាន closure) ចូល `Isolate.run` — អានសារ error

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកអាចនិយាយបានថាតើ data ប្រភេទណាខ្លះដែលអាចឆ្លងកាត់ isolate boundary បាន និងហេតុអ្វី `Isolate.run` ងាយស្រួលជាង `Isolate.spawn` ។

---

### លំហាត់ ១.៩ — Extension Type សម្រាប់ ID Safety
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~20 នាទី

**📋 ការងារ:** ក្នុងកូដពិត `String userId` និង `String bookId` ត្រូវបានច្រឡំគ្នាញឹកញាប់។ ប្រើ `extension type` (zero-cost wrapper) ដើម្បីឲ្យ compiler ចាប់កំហុសនេះ។

```dart
extension type UserId(String value) {}
extension type BookId(String value) {}

void borrow(UserId user, BookId book) { /* ... */ }
```

សរសេរកូដដែលបញ្ជាក់ថា៖
- `borrow(bookId, userId)` — compile មិនចេញ
- តែនៅ runtime វានៅតែជា `String` ធម្មតា (គ្មាន allocation បន្ថែម) — បញ្ជាក់ដោយ `identical()`

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកយល់ថាហេតុអ្វី `extension type` ខុសពី `class Wrapper { final String value; }` ក្នុងន័យ performance។

---

## ✅ ចម្លើយ — ផ្នែកទី ១

<a id="ans1"></a>

### ១.១ Null Safety

- **(A) ខុស។** `nickname` គឺជា **non-final instance field**។ Dart មិន promote វាបានទេ ព្រោះរវាង `if` និង `.length` អាចមាន getter override ឬ code ផ្សេងប្ដូរតម្លៃ។ Flow analysis ធានាបានតែលើអថេរ **local** និង **final field** ប៉ុណ្ណោះ។
- **(B) ត្រូវ។** `email` ជា `final` → មិនអាចប្ដូរបាន → promote បាន។
- **(C) ត្រូវ។** `item` ជា local variable នៃ for-in (implicitly final ក្នុងន័យ flow analysis)។
- **(D) ត្រូវ។** បន្ទាប់ពី assignment `name` មាន type `String` តាម flow analysis។
- **(E) បរាជ័យនៅ runtime** ជាមួយ `LateInitializationError` — `late` ផ្លាស់ការត្រួតពិនិត្យពី compile-time ទៅ runtime។

ដំណោះស្រាយសម្រាប់ (A) ដោយគ្មាន `!`៖

```dart
int nicknameLength() {
  final n = nickname;          // copy ទៅ local → promote បាន
  return n == null ? 0 : n.length;
}
```

> 🔑 **គោលការណ៍:** ពេលជាប់ null promotion — ចម្លងទៅ local variable។ នេះជាចម្លើយត្រឹមត្រូវ ៩០% នៃករណី ហើយវាល្អជាង `!` ជានិច្ច។

---

### ១.២ const Canonicalization

| # | លទ្ធផល | មូលហេតុ |
|---|---|---|
| 1 | `true` | `const` ដូចគ្នា → canonicalized ជា instance តែមួយ |
| 2 | `false` | `c` បង្កើតថ្មីនៅ runtime |
| 3 | `true` | `const Point(1,2)` នៅ `d` ចង្អុលទៅ instance ដដែល |
| 4 | `true` | const list ក៏ canonicalized ដែរ |
| 5 | `true` | `==` លំនាំដើមជា identity → តែ... សូមកត់សម្គាល់ថា `a` និង `c` **មិន** identical ដូច្នេះ `a == c` ពិតជា **`false`** បើគ្មាន `operator ==` |
| 6 | `false` | list ធម្មតាពីរ គឺជា object ពីរ |

> ⚠️ ចំណុច #5 គឺជាអន្ទាក់។ បើអ្នកទាយ `true` — អ្នកភ្លេចថា Dart **មិន** បង្កើត `==` ដោយស្វ័យប្រវត្តិសម្រាប់ class ធម្មតាទេ។ ត្រូវសរសេរ `operator ==` និង `hashCode` ដោយខ្លួនឯង (ឬប្រើ `freezed`)។

**បន្ថែម `DateTime? created`:** `const Point(1, 2)` នៅតែ compile បាន (field ជា `null`)។ ប៉ុន្តែ `const Point(1, 2, created: DateTime.now())` **compile មិនចេញ** ព្រោះ `DateTime.now()` មិនមែនជា compile-time constant។

**ភ្ជាប់ទៅ Flutter:** ពេលអ្នកសរសេរ `const Text('សួស្ដី')` នៅក្នុង `build()` — គ្រប់ការហៅ `build()` ទាំងអស់ត្រឡប់ **object ដដែល**។ Flutter ប្រៀបធៀបដោយ `identical()` នៅក្នុង `Element.updateChild()`។ ដូចគ្នា → រំលង rebuild ទាំងស្រុង។ នេះជាមូលហេតុដែល `const` គឺជាការ optimize ដ៏ថោកបំផុតដែលអ្នកអាចធ្វើបាន។

---

### ១.៣ Records

```dart
typedef PhoneInfo = ({bool valid, String? operator, String? national});

PhoneInfo parseKhmerPhone(String raw) {
  final cleaned = raw.replaceAll(RegExp(r'[\s-]'), '');
  if (!cleaned.startsWith('0') && !cleaned.startsWith('+855')) {
    return (valid: false, operator: null, national: null);
  }
  final national =
      cleaned.startsWith('+855') ? '0${cleaned.substring(4)}' : cleaned;
  if (national.length < 3) {
    return (valid: false, operator: null, national: null);
  }
  final op = switch (national.substring(0, 3)) {
    '010' || '015' || '016' || '069' || '070' || '081' || '086' || '087' => 'Smart',
    '011' || '012' || '017' || '061' || '076' || '077' || '085' || '089' => 'Cellcard',
    '031' || '060' || '066' || '067' || '068' || '071' || '088' => 'Metfone',
    _ => 'Unknown',
  };
  return (valid: true, operator: op, national: national);
}

void main() {
  // destructuring
  final (valid: ok, operator: op, national: num_) = parseKhmerPhone('+855 12 345 678');
  print('$ok $op $num_');

  // switch លើ record ដោយផ្ទាល់
  final msg = switch (parseKhmerPhone('012345678')) {
    (valid: false, operator: _, national: _) => 'លេខមិនត្រឹមត្រូវ',
    (valid: true, operator: 'Unknown', national: final n) => 'មិនស្គាល់ប្រតិបត្តិករ: $n',
    (valid: true, operator: final o, national: final n) => '$o — $n',
  };
  print(msg);
}
```

> 🔑 Record ត្រូវប្រើសម្រាប់ **តម្លៃត្រឡប់បណ្ដោះអាសន្ន** ។ បើ structure នោះឆ្លងកាត់ layer ច្រើន (repository → viewmodel → UI) សូមប្រើ `class` ឬ `freezed` វិញ ព្រោះវាមានឈ្មោះច្បាស់ និងអាចដាក់ method បាន។

---

### ១.៤ Sealed Class

```dart
sealed class UiState<T> {
  const UiState();
}

class Idle<T> extends UiState<T> { const Idle(); }
class Loading<T> extends UiState<T> { const Loading(); }
class Success<T> extends UiState<T> {
  final T data;
  const Success(this.data);
}
class Failure<T> extends UiState<T> {
  final String message;
  final Object? cause;
  const Failure(this.message, [this.cause]);
}

String describe<T>(UiState<T> s) => switch (s) {
      Idle() => 'មិនទាន់ចាប់ផ្ដើម',
      Loading() => 'កំពុងផ្ទុក...',
      Success(data: final d) => 'ជោគជ័យ: $d',
      Failure(message: final m) => 'បរាជ័យ: $m',
    };
```

បន្ទាប់ពីបន្ថែម `class Empty<T> extends UiState<T> {}` ដោយមិនកែ `describe`, analyzer នឹងបញ្ចេញ៖

```
error • The type 'UiState<T>' is not exhaustively matched by the switch cases
        since it doesn't match 'Empty<Object?>()'
```

នេះជា **compile error មិនមែន warning** ។ គម្រោងអ្នកនឹង build មិនចេញ — មានន័យថាអ្នក **មិនអាចភ្លេច** ដោះស្រាយ state ថ្មីនៅកន្លែងណាមួយក្នុងកម្មវិធីទាំងមូលបានទេ។

> ⚠️ ពេលណាដែលអ្នកដាក់ `_ =>` ឬ `default:` ចូល switch លើ sealed type — អ្នកបានបោះបង់អត្ថប្រយោជន៍ទាំងស្រុងនេះចោល។ ចូរទប់ចិត្ត។

---

### ១.៥ Event Loop

**លទ្ធផលត្រឹមត្រូវ:**

```
1
10      ← async closure រត់ synchronously រហូតដល់ await ដំបូង
12      ← main ចប់ synchronous phase
3       ← microtask
4       ← Future.value(...).then → microtask
5       ← scheduleMicrotask
11      ← await null → microtask
2       ← event queue (Future(...) = Timer.run ខាងក្នុង)
6       ← Timer.run
7       ← Future.delayed(Duration.zero)
8
9       ← .then លើ 8 → microtask បន្ទាប់ពី 8 រត់ចប់
```

**គោលការណ៍ ៣ យ៉ាង៖**
1. កូដ synchronous រត់រហូតចប់ **មុនគេ** (`1, 10, 12`)។
2. **Microtask queue ត្រូវរត់ឲ្យអស់** មុននឹង event queue មួយណាក៏ដោយត្រូវបាន pick។
3. Microtask ត្រូវបានរត់តាមលំដាប់ដែលគេ schedule (FIFO)។

**`await null` ធៀបនឹង `await Future.delayed(Duration.zero)`:**
`await null` → Dart wrap ជា `Future.value(null)` → បន្តនៅ **microtask** → `11` ចេញមុន `2`។
`await Future.delayed(Duration.zero)` → ត្រូវការ **Timer** → ចូល **event queue** → `11` ធ្លាក់ទៅក្រោយ `7` (ជាទូទៅចេញរវាង `7` និង `8`)។

> 🔑 នេះជាមូលហេតុដែល `await Future.delayed(Duration.zero)` ត្រូវបានប្រើដើម្បី "ដកដង្ហើម" ឲ្យ UI render មួយ frame ចំណែក `await null` មិនជួយអ្វីទេ។

---

### ១.៦ Debounce

```dart
StreamTransformer<T, T> debounce<T>(Duration duration) {
  return StreamTransformer<T, T>((input, cancelOnError) {
    Timer? timer;
    T? pending;
    bool hasPending = false;
    late StreamController<T> controller;
    late StreamSubscription<T> sub;

    void flush() {
      if (hasPending) {
        controller.add(pending as T);
        hasPending = false;
        pending = null;
      }
    }

    controller = StreamController<T>(
      onListen: () {
        sub = input.listen(
          (data) {
            pending = data;
            hasPending = true;
            timer?.cancel();
            timer = Timer(duration, () {
              timer = null;
              flush();
            });
          },
          onError: controller.addError,   // error ឆ្លងកាត់ភ្លាមៗ
          onDone: () {
            timer?.cancel();
            flush();                       // បញ្ចេញ pending មុន close
            controller.close();
          },
          cancelOnError: cancelOnError,
        );
      },
      onPause: () => sub.pause(),
      onResume: () => sub.resume(),
      onCancel: () {
        timer?.cancel();                   // 🔑 គ្មាន leak
        return sub.cancel();
      },
      sync: true,
    );
    return controller.stream.listen(null);
  });
}
```

**ចំណុចដែលមនុស្សភ្លេចញឹកញាប់បំផុត:** `onCancel` ដែល cancel timer។ បើភ្លេច — Timer នៅតែរស់បន្ទាប់ពី widget ត្រូវ dispose ហើយអ្នកនឹងទទួល `setState() called after dispose()`។

---

### ១.៧ Broadcast

**Bug ១:** `StreamController()` លំនាំដើមជា **single-subscription** → listener ទី ២ throw។
**Bug ២:** `Timer.periodic` គ្មានអ្នក cancel → leak រហូត ហើយ `_controller.add()` នឹងបន្ត add ទៅ controller ដែលបាន close។

```dart
class TickerService {
  final _controller = StreamController<int>.broadcast();   // fix 1
  Timer? _timer;

  Stream<int> get ticks => _controller.stream;

  TickerService() {
    _timer = Timer.periodic(const Duration(seconds: 1), (t) {
      if (!_controller.isClosed) _controller.add(t.tick);
    });
  }

  void dispose() {                                          // fix 2
    _timer?.cancel();
    _timer = null;
    _controller.close();
  }
}
```

> ⚠️ ចំណាំពី broadcast៖ អ្នក subscribe យឺត **មិនទទួល** event ចាស់ទេ។ បើអ្នកត្រូវការតម្លៃចុងក្រោយភ្លាមៗ សូមរក្សា `_last` ហើយ emit វានៅ `onListen` ឬប្រើ `ValueNotifier` វិញ។

---

### ១.៨ Isolate

```dart
// ធ្ងន់ — ត្រូវជា top-level ឬ static function
int countItems(String jsonStr) {
  final decoded = jsonDecode(jsonStr) as List;
  return decoded.length;
}

// ដំណាក់កាល A (jank)
final n = countItems(bigJson);

// ដំណាក់កាល B (រលូន)
final n = await Isolate.run(() => countItems(bigJson));
```

**អ្វីដែលឆ្លងកាត់ isolate boundary បាន:** primitives, `String`, `List`/`Map` នៃតម្លៃ send បាន, `TransferableTypedData`, `SendPort`។
**អ្វីដែលមិនបាន:** closure ដែលចាប់យក state មិន send បាន, `ReceivePort`, native handles, object ភ្ជាប់នឹង platform (`BuildContext`, `File` handle ដែលបើករួច)។

`Isolate.run` ខុសពី `Isolate.spawn` ត្រង់ថាវាបង្កើត isolate → រត់ → បញ្ជូនលទ្ធផលត្រឡប់ → បិទដោយស្វ័យប្រវត្តិ។ អ្នកមិនចាំបាច់គ្រប់គ្រង `ReceivePort` ដោយខ្លួនឯងទេ។ ប្រើ `Isolate.spawn` តែពេលអ្នកត្រូវការ **long-lived worker** ដែលទទួលការងារច្រើនដង។

> 💡 សម្រាប់ការងារខ្លីៗដដែលៗ `compute()` នៅតែជាជម្រើសល្អ ព្រោះលើ web វា fallback ទៅ main thread ដោយស្វ័យប្រវត្តិ។

---

### ១.៩ Extension Type

```dart
extension type const UserId(String value) {}
extension type const BookId(String value) {}

void borrow(UserId user, BookId book) => print('${user.value} → ${book.value}');

void main() {
  const u = UserId('u_1');
  const b = BookId('b_9');

  borrow(u, b);       // ✅
  // borrow(b, u);    // ❌ compile error — នេះជាចំណុចសំខាន់

  // zero-cost: នៅ runtime វាជា String ដដែល
  const raw = 'u_1';
  print(identical(u.value, raw));   // true
  print(u is String);               // true (representation type)
}
```

ខុសពី `class Wrapper { final String value; }` ត្រង់ថា class ពិត **បង្កើត object ថ្មីនៅ heap** រាល់ដង។ បើអ្នកមាន ID ១ម៉ឺននៅក្នុង list នោះជា ១ម៉ឺន allocation។ `extension type` ត្រូវបាន **erase ចោលនៅពេល compile** — សល់តែ `String` ដើម។ Type safety ដោយឥតបង់ថ្លៃ។

---

# ផ្នែកទី ២ — Flutter Core Mechanics (លំហាត់ ២.១ – ២.៨)

> គោលដៅផ្នែកនេះ៖ ឈប់មើល Flutter ជា "widget ជាច្រើនដាក់ត្រួតគ្នា" ហើយចាប់ផ្ដើមមើលវាជា **ដើមឈើបី** ដែលធ្វើការជាមួយគ្នា។

---

### លំហាត់ ២.១ — បញ្ជាក់ Widget/Element/RenderObject
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~35 នាទី

**🎯 គោលដៅ:** មើលឃើញដោយភ្នែកថា Widget ត្រូវបានបោះចោលរាល់ frame តែ Element និង State នៅដដែល។

**📋 ការងារ:** បង្កើត `CounterPage` ដែលមាន `setState` រៀងរាល់ ១ វិនាទី។ ខាងក្នុងដាក់៖

```dart
class ProbeWidget extends StatefulWidget {
  final String label;
  const ProbeWidget(this.label, {super.key});
  @override State<ProbeWidget> createState() => _ProbeWidgetState();
}

class _ProbeWidgetState extends State<ProbeWidget> {
  @override
  void initState() {
    super.initState();
    print('initState  ${widget.label}  state=${identityHashCode(this)}');
  }
  @override
  Widget build(BuildContext context) {
    print('build      ${widget.label}  '
          'widget=${identityHashCode(widget)}  '
          'element=${identityHashCode(context)}');
    return Text(widget.label);
  }
}
```

រួចឆ្លើយសំណួរដោយផ្អែកលើ log៖
1. តើ `identityHashCode(widget)` ប្ដូរគ្រប់ frame ឬទេ?
2. តើ `identityHashCode(context)` (= Element) ប្ដូរឬទេ?
3. តើ `initState` រត់ប៉ុន្មានដង?
4. ឥឡូវ wrap `ProbeWidget` ដោយ `const` — តើ hash ណាមួយឈប់ប្ដូរ?
5. ដាក់ `ProbeWidget` ចូល `Column` ហើយប្ដូរលំដាប់ children។ តើមានអ្វីកើតឡើងចំពោះ State?

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកសរសេរបានប្រយោគមួយពន្យល់ថា៖ *Widget = configuration (immutable, បោះចោល), Element = instance (រស់នៅ), RenderObject = layout/paint។*

**💡 គន្លឹះ:** បើក **Flutter DevTools → Widget Inspector → Details Tree** ដើម្បីមើលដើមឈើទាំងបី។

---

### លំហាត់ ២.២ — Lifecycle: initState ធៀបនឹង didUpdateWidget
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~25 នាទី

**📋 ការងារ:** កូដនេះមាន bug — ពេល parent ប្ដូរ `initialText` នោះ TextField មិនប្ដូរតាម។ រកឲ្យឃើញ ហើយកែ។

```dart
class EditorField extends StatefulWidget {
  final String initialText;
  const EditorField({super.key, required this.initialText});
  @override State<EditorField> createState() => _EditorFieldState();
}

class _EditorFieldState extends State<EditorField> {
  late TextEditingController _c;

  @override
  void initState() {
    super.initState();
    _c = TextEditingController(text: widget.initialText);
  }

  @override
  Widget build(BuildContext context) => TextField(controller: _c);
}
```

តម្រូវការបន្ថែម៖
- ត្រូវមាន `dispose()` ត្រឹមត្រូវ
- ពេល `initialText` ប្ដូរ ត្រូវ update តែពេលអ្នកប្រើមិនទាន់កែ (កុំលុបអ្វីដែលគាត់វាយ)
- ត្រូវ **មិន** បង្កើត controller ថ្មីរាល់ពេល update (ព្រោះនឹងបាត់ cursor position)

**✅ លក្ខខណ្ឌជោគជ័យ:** សរសេរ widget test ដែលបញ្ជាក់ថា ពេល parent rebuild ជាមួយ `initialText` ថ្មី នោះ field ប្ដូរតាម ហើយ `_c` គឺជា instance ដដែល។

---

### លំហាត់ ២.៣ — Keys: Bug បុរាណ
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~30 នាទី

**📋 ការងារ:** កូដនេះមានប៊ូតុង shuffle។ ពេលចុច — **ពណ៌ស្ថិតនៅដដែល តែតួលេខផ្លាស់ប្ដូរ** (ឬផ្ទុយ)។ ស្វែងយល់ថាហេតុអ្វី។

```dart
class ShuffleDemo extends StatefulWidget {
  const ShuffleDemo({super.key});
  @override State<ShuffleDemo> createState() => _ShuffleDemoState();
}

class _ShuffleDemoState extends State<ShuffleDemo> {
  List<int> ids = [1, 2, 3];

  @override
  Widget build(BuildContext context) => Column(children: [
        for (final id in ids) ColorBox(id: id),   // ← គ្មាន key
        ElevatedButton(
          onPressed: () => setState(() => ids.shuffle()),
          child: const Text('Shuffle'),
        ),
      ]);
}

class ColorBox extends StatefulWidget {
  final int id;
  const ColorBox({super.key, required this.id});
  @override State<ColorBox> createState() => _ColorBoxState();
}

class _ColorBoxState extends State<ColorBox> {
  // ពណ៌ចៃដន្យ រក្សាទុកក្នុង State
  final Color color = Colors.primaries[Random().nextInt(Colors.primaries.length)];
  @override
  Widget build(BuildContext context) =>
      Container(height: 60, color: color, child: Text('${widget.id}'));
}
```

**ជំហាន:**
1. ពន្យល់ថាហេតុអ្វី State មិនតាមទៅជាមួយ widget (គិតពី `Element.updateChild` និង `Widget.canUpdate`)
2. កែដោយប្រើ `ValueKey(id)`
3. សាកល្បងប្ដូរទៅ `ObjectKey`, `UniqueKey` — ពន្យល់ថាហេតុអ្វី `UniqueKey()` នៅក្នុង `build()` **ជាការសម្រេចចិត្តអាក្រក់**
4. សាកល្បង `GlobalKey` — តើវាដោះស្រាយបានដែរឬទេ? តម្លៃដែលត្រូវបង់គឺអ្វី?

**✅ លក្ខខណ្ឌជោគជ័យ:** ពណ៌ដើរតាមលេខ · អ្នកពន្យល់បាននូវ `Widget.canUpdate(old, new) => old.runtimeType == new.runtimeType && old.key == new.key`។

---

### លំហាត់ ២.៤ — Constraints Puzzles
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~40 នាទី

**🎯 គោលដៅ:** ចាំច្បាប់មាសឲ្យជាប់៖ **Constraints go down. Sizes go up. Parent sets position.**

**📋 ការងារ:** សម្រាប់ករណីនីមួយៗ សូម **គូររូបលទ្ធផលនៅលើក្រដាសមុន** រួចរត់ ហើយផ្ទៀងផ្ទាត់ដោយ `debugPaintSizeEnabled = true`។

```dart
// A
Container(color: Colors.red)

// B
Center(child: Container(color: Colors.red))

// C
Center(child: Container(width: 100, height: 100, color: Colors.red))

// D
Row(children: [
  Container(width: 100, color: Colors.red),
  Container(width: 100, color: Colors.blue),
])

// E
Column(children: [
  Expanded(flex: 2, child: Container(color: Colors.red)),
  Expanded(flex: 1, child: Container(color: Colors.blue)),
])

// F
UnconstrainedBox(child: Container(width: 5000, height: 50, color: Colors.red))

// G
SizedBox(
  height: 100,
  child: ListView(children: [
    Container(height: 300, color: Colors.red),
  ]),
)

// H — ហេតុអ្វី crash?
Column(children: [
  ListView(children: const [Text('a'), Text('b')]),
])

// I — កែ H ដោយវិធី ៣ យ៉ាងផ្សេងគ្នា
```

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកកែ (H) បានយ៉ាងតិច ៣ វិធី ហើយអាចនិយាយបានថាវិធីណាសមស្របនៅពេលណា។

---

### លំហាត់ ២.៥ — LayoutBuilder និង Intrinsic
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~35 នាទី

**📋 ការងារ:** បង្កើត `ResponsiveShell` widget៖
- `< 600px` → `BottomNavigationBar`
- `600–1024px` → `NavigationRail`
- `> 1024px` → `NavigationRail` (extended) + panel ខាងស្ដាំ

លក្ខខណ្ឌ៖
- ត្រូវប្រើ `LayoutBuilder` **មិនមែន** `MediaQuery.of(context).size`
- ត្រូវពន្យល់ក្នុង comment ថាហេតុអ្វី `LayoutBuilder` ត្រឹមត្រូវជាង
- state របស់ page ដែលកំពុងបើក **មិនត្រូវបាត់** ពេលប្ដូរទំហំ window

បន្ថែម (⭐⭐⭐⭐): ធ្វើ `Row` ដែល children ទាំងអស់មានកម្ពស់ស្មើនឹង children ខ្ពស់បំផុត ដោយប្រើ `IntrinsicHeight`។ បន្ទាប់មកវាស់ថាតើវាថ្លៃប៉ុណ្ណា ដោយប្រើ DevTools timeline។

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកអាចពន្យល់បានថាហេតុអ្វី `IntrinsicHeight` មាន complexity `O(N²)` ក្នុងករណីអាក្រក់បំផុត។

---

### លំហាត់ ២.៦ — Slivers
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~50 នាទី

**📋 ការងារ:** បង្កើតទំព័រ profile មួយដោយប្រើ `CustomScrollView` តែមួយ (មិនត្រូវមាន nested scroll view)៖

1. `SliverAppBar` — រូបភាព collapse បាន, `pinned: true`, `stretch: true`
2. `SliverToBoxAdapter` — កាតព័ត៌មាន
3. `SliverPersistentHeader` **ផ្ទាល់ខ្លួន** — tab bar ដែល `pinned` នៅក្រោម app bar ហើយ **តូចទៅតាមការ scroll**
4. `SliverGrid` — រូបភាព ៣ ជួរឈរ
5. `SliverList` — comment ចំនួន ១០០

**✅ លក្ខខណ្ឌជោគជ័យ:**
- `shouldRebuild` នៃ delegate ត្រឡប់ `false` ពេលគ្មានអ្វីប្ដូរ
- Scroll រលូន ៦០fps លើ profile mode (`flutter run --profile`)
- គ្មាន `Vertical viewport was given unbounded height`

**💡 គន្លឹះ:** `SliverPersistentHeaderDelegate` តម្រូវឲ្យ `maxExtent >= minExtent` ជានិច្ច។ ប្រើ `shrinkOffset` ដើម្បីគណនា opacity ឬ scale។

---

### លំហាត់ ២.៧ — សរសេរ InheritedWidget ដោយខ្លួនឯង
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~40 នាទី

**🎯 គោលដៅ:** យល់ថា `Theme.of(context)` និង `Provider.of(context)` ធ្វើអ្វីខាងក្នុង។

**📋 ការងារ:** សរសេរ `AppConfig` ដោយគ្មាន package ណាមួយ៖

```dart
class AppConfig extends InheritedWidget {
  final String apiBaseUrl;
  final bool isDarkMode;
  const AppConfig({
    super.key,
    required this.apiBaseUrl,
    required this.isDarkMode,
    required super.child,
  });

  static AppConfig of(BuildContext context) { /* TODO */ }
  static AppConfig? maybeOf(BuildContext context) { /* TODO */ }

  @override
  bool updateShouldNotify(AppConfig old) { /* TODO */ }
}
```

រួច៖
1. បង្កើត widget ២ ដែលអាន `AppConfig` — មួយអាន `apiBaseUrl` មួយអាន `isDarkMode`
2. ដាក់ `print` ក្នុង `build` ទាំងពីរ
3. ប្ដូរតែ `isDarkMode` → **តើទាំងពីរ rebuild ឬតែមួយ?**
4. ដោះស្រាយបញ្ហានេះដោយប្រើ `InheritedModel` ជំនួស
5. ពន្យល់ភាពខុសគ្នារវាង `dependOnInheritedWidgetOfExactType` និង `getInheritedWidgetOfExactType`

**✅ លក្ខខណ្ឌជោគជ័យ:** បន្ទាប់ពីប្រើ `InheritedModel` នោះ widget ដែលអាន `apiBaseUrl` **មិន** rebuild ពេល `isDarkMode` ប្ដូរ។

---

### លំហាត់ ២.៨ — BuildContext ខុសទីតាំង
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~20 នាទី

**📋 ការងារ:** កូដនេះ throw `No Scaffold widget found`។ រកមូលហេតុ ហើយកែដោយ **វិធី ៣ យ៉ាង**។

```dart
class BadPage extends StatelessWidget {
  const BadPage({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            ScaffoldMessenger.of(context).showSnackBar(
              const SnackBar(content: Text('សួស្ដី')),
            );
          },
          child: const Text('បង្ហាញ'),
        ),
      ),
    );
  }
}
```

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកពន្យល់បានថា `context` គឺជា `Element` ដែលមានទីតាំងច្បាស់លាស់ក្នុងដើមឈើ ហើយ `.of()` ស្វែងរក **ឡើងលើ** មិនមែនចុះក្រោមទេ។

---

## ✅ ចម្លើយ — ផ្នែកទី ២

### ២.១ ដើមឈើបី

1. **`identityHashCode(widget)` ប្ដូរគ្រប់ frame** — Widget ជា object ថ្មីរាល់ `build()`។
2. **`identityHashCode(context)` មិនប្ដូរ** — Element ដដែលត្រូវបាន reuse។
3. **`initState` រត់តែ ១ ដង** — State ភ្ជាប់នឹង Element មិនមែន Widget។
4. ជាមួយ `const` នោះ **widget hash ក៏ឈប់ប្ដូរដែរ** ព្រោះ canonicalized (ត្រឡប់ទៅលំហាត់ ១.២)។ ហើយ `build` នៃ child នឹង **មិនត្រូវហៅឡើងវិញ** ទាល់តែសោះ។
5. ប្ដូរលំដាប់ children ដោយគ្មាន key → State **មិន** ដើរតាម ព្រោះ Flutter ផ្គូផ្គងតាម *ទីតាំង* + *type* (សូមមើលលំហាត់ ២.៣)។

**សេចក្ដីសង្ខេប:**

| ដើមឈើ | អាយុកាល | តួនាទី |
|---|---|---|
| Widget | មួយ frame | configuration — immutable, ថោក |
| Element | យូរ | ភ្ជាប់ Widget ↔ RenderObject, កាន់ State, គ្រប់គ្រង rebuild |
| RenderObject | យូរ | layout, paint, hit testing |

---

### ២.២ didUpdateWidget

```dart
class _EditorFieldState extends State<EditorField> {
  late final TextEditingController _c;

  @override
  void initState() {
    super.initState();
    _c = TextEditingController(text: widget.initialText);
  }

  @override
  void didUpdateWidget(covariant EditorField old) {
    super.didUpdateWidget(old);
    // ប្ដូរតែពេល prop ពិតជាប្ដូរ ហើយអ្នកប្រើមិនទាន់កែ
    if (old.initialText != widget.initialText && _c.text == old.initialText) {
      _c.value = TextEditingValue(
        text: widget.initialText,
        selection: TextSelection.collapsed(offset: widget.initialText.length),
      );
    }
  }

  @override
  void dispose() {
    _c.dispose();          // 🔑 មិនត្រូវភ្លេច
    super.dispose();
  }

  @override
  Widget build(BuildContext context) => TextField(controller: _c);
}
```

**ច្បាប់មេ:**
- `initState` — បង្កើតធនធាន **តែម្ដង**
- `didUpdateWidget` — ប្រតិកម្មទៅនឹង **prop ថ្មី** (តែងតែប្រៀបធៀបនឹង `old` មុន!)
- `didChangeDependencies` — ប្រតិកម្មទៅនឹង **InheritedWidget** ដែលប្ដូរ
- `dispose` — បញ្ចេញធនធាន (controller, subscription, timer, focus node)

> ⚠️ ការសរសេរ `_c = TextEditingController(...)` ឡើងវិញនៅក្នុង `didUpdateWidget` គឺជាកំហុសធម្មតា — cursor លោត ហើយ controller ចាស់ leak។

---

### ២.៣ Keys

**មូលហេតុ:** ពេល `setState` រត់ Flutter ហៅ `Element.updateChild(oldChild, newWidget)` ដែលពឹងលើ៖

```dart
static bool canUpdate(Widget oldWidget, Widget newWidget) {
  return oldWidget.runtimeType == newWidget.runtimeType
      && oldWidget.key == newWidget.key;
}
```

ដោយ `key` ទាំងអស់ជា `null` ហើយ type ដូចគ្នាទាំងអស់ (`ColorBox`) — Flutter មើលឃើញថា "ទីតាំងទី ០ គឺ ColorBox ដដែល" → **reuse Element និង State ចាស់** ហើយគ្រាន់តែបញ្ចូល `widget.id` ថ្មី។ ដូច្នេះ `id` ប្ដូរ តែ `color` (ដែលរស់នៅក្នុង State) នៅដដែល។

**ការកែ:**

```dart
for (final id in ids) ColorBox(key: ValueKey(id), id: id),
```

ឥឡូវ `key` ខុសគ្នា → `canUpdate` ត្រឡប់ `false` → Flutter រុករក children ទាំងអស់ ហើយ **ផ្លាស់ទី Element** ទៅតាម key។

**ប្រភេទ Key:**

| Key | ប្រើពេលណា |
|---|---|
| `ValueKey(id)` | មាន ID ឬតម្លៃពិសេសមួយ — ជម្រើសលំនាំដើម |
| `ObjectKey(obj)` | អត្តសញ្ញាណគឺជា object ខ្លួនឯង (គ្មាន ID) |
| `UniqueKey()` | ចង់ **បង្ខំ** ឲ្យ State ត្រូវបំផ្លាញ — ⚠️ បើដាក់ក្នុង `build()` នោះ State នឹងត្រូវបំផ្លាញ **រាល់ frame** → animation ដាច់, controller leak, performance អាក្រក់ |
| `GlobalKey` | ត្រូវការចូលដំណើរការ State ពីខាងក្រៅ ឬផ្លាស់ទី subtree ឆ្លង parent — ថ្លៃ (global registry + deactivate/reactivate) ដូច្នេះប្រើតែពេលចាំបាច់ |

`GlobalKey` ដោះស្រាយបាន **តែ** តម្លៃដែលត្រូវបង់គឺ Flutter ត្រូវ deactivate/reactivate element នៅក្នុង `_InactiveElements` ។ សម្រាប់ list — `ValueKey` ជាចម្លើយត្រឹមត្រូវជានិច្ច។

---

### ២.៤ Constraints

| # | លទ្ធផល | មូលហេតុ |
|---|---|---|
| A | ក្រហមពេញអេក្រង់ | `Container` គ្មានទំហំ + constraints ធំ (tight) → យកអស់ |
| B | **មើលមិនឃើញ** (0×0) | `Center` ផ្ដល់ constraints រលុង (loose) → `Container` គ្មានទំហំ → តូចបំផុត |
| C | ការ៉េ 100×100 កណ្ដាល | Container មានទំហំច្បាស់ |
| D | ខ្សែ ២ ជាប់គ្នាឆ្វេង កម្ពស់ ០ | `Row` ផ្ដល់កម្ពស់រលុង → Container គ្មានកម្ពស់ → ០ (ត្រូវប្រើ `height` ឬ `CrossAxisAlignment.stretch`) |
| E | ក្រហម ⅔ លើ ខៀវ ⅓ ក្រោម | `Expanded` បង្ខំ tight constraint តាម flex |
| F | ក្រហមទទឹង 5000 ហូរចេញ + សញ្ញា overflow ពណ៌លឿង | `UnconstrainedBox` ដក constraints ចេញ |
| G | ListView កម្ពស់ 100 scroll ចេញ | `SizedBox` ផ្ដល់កម្ពស់ → viewport មានព្រំដែន |
| H | **crash** | `Column` ផ្ដល់កម្ពស់ **unbounded** ចុះទៅ ហើយ `ListView` ក៏ចង់បាន unbounded ដែរ → `Vertical viewport was given unbounded height` |

**កែ (H) ៣ វិធី:**

```dart
// 1) Expanded — ListView យកកន្លែងនៅសល់ (ល្អបំផុតជាទូទៅ)
Column(children: [Expanded(child: ListView(children: items))]);

// 2) SizedBox / ConstrainedBox — កម្ពស់ថេរ
Column(children: [SizedBox(height: 200, child: ListView(children: items))]);

// 3) shrinkWrap — ListView វាស់ children ទាំងអស់ ហើយកន្ត្រាក់តាម
//    ⚠️ បាត់ lazy rendering → កុំប្រើជាមួយ list វែង
Column(children: [
  ListView(shrinkWrap: true, physics: const NeverScrollableScrollPhysics(), children: items),
]);
```

> 🔑 ជម្រើសទី ១ សម្រាប់ list ធំ។ ជម្រើសទី ៣ សម្រាប់ list ខ្លី (< ~20 item) ដែលនៅក្នុងទំព័រ scroll រួម។ បើ list វែងហើយអ្នកចង់លាយជាមួយ content ផ្សេង — ប្រើ `CustomScrollView` + slivers (លំហាត់ ២.៦) វិញ។

---

### ២.៥ LayoutBuilder

```dart
class ResponsiveShell extends StatefulWidget {
  const ResponsiveShell({super.key});
  @override State<ResponsiveShell> createState() => _ResponsiveShellState();
}

class _ResponsiveShellState extends State<ResponsiveShell> {
  int _index = 0;   // 🔑 state នៅ State មិននៅក្នុង build → មិនបាត់ពេល resize

  @override
  Widget build(BuildContext context) {
    // LayoutBuilder ផ្ដល់ constraints នៃ *parent ជិតបំផុត*
    // ចំណែក MediaQuery ផ្ដល់ទំហំ *អេក្រង់ទាំងមូល* —
    // ខុសភ្លាមៗនៅពេល widget នេះស្ថិតក្នុង dialog, split view, ឬ side panel។
    return LayoutBuilder(builder: (context, c) {
      final w = c.maxWidth;
      final body = _pages[_index];

      if (w < 600) {
        return Scaffold(
          body: body,
          bottomNavigationBar: NavigationBar(
            selectedIndex: _index,
            onDestinationSelected: (i) => setState(() => _index = i),
            destinations: _destinations,
          ),
        );
      }

      return Scaffold(
        body: Row(children: [
          NavigationRail(
            extended: w > 1024,
            selectedIndex: _index,
            onDestinationSelected: (i) => setState(() => _index = i),
            destinations: _railDestinations,
          ),
          const VerticalDivider(width: 1),
          Expanded(child: body),
          if (w > 1024) const SizedBox(width: 320, child: DetailPanel()),
        ]),
      );
    });
  }
}
```

**ហេតុអ្វី `IntrinsicHeight` ថ្លៃ:** វាតម្រូវឲ្យ layout ធ្វើ **ការហៅបន្ថែមមួយជុំ** (`computeMaxIntrinsicHeight`) លើ children ទាំងអស់ មុននឹង layout ពិត។ បើ child នោះក៏មាន intrinsic ខាងក្នុងទៀត — ការហៅនេះកើនជាពហុគុណ។ Flutter ចងក្រងឯកសារថាវាអាច `O(N²)`។ ប្រើតែពេលពិតជាចាំបាច់ ហើយកុំដាក់វានៅក្នុង list ដែល scroll។

---

### ២.៦ Slivers — SliverPersistentHeaderDelegate

```dart
class _TabBarDelegate extends SliverPersistentHeaderDelegate {
  final TabController controller;
  const _TabBarDelegate(this.controller);

  @override double get minExtent => 48;
  @override double get maxExtent => 72;

  @override
  Widget build(BuildContext context, double shrinkOffset, bool overlapsContent) {
    final t = (shrinkOffset / (maxExtent - minExtent)).clamp(0.0, 1.0);
    return Material(
      elevation: overlapsContent ? 4 : 0,
      color: Theme.of(context).colorScheme.surface,
      child: Padding(
        padding: EdgeInsets.symmetric(vertical: 12 * (1 - t)),
        child: TabBar(controller: controller, tabs: const [...]),
      ),
    );
  }

  @override
  bool shouldRebuild(covariant _TabBarDelegate old) =>
      old.controller != controller;      // 🔑 កុំត្រឡប់ true ជានិច្ច
}
```

ដាក់បញ្ចូល៖

```dart
CustomScrollView(slivers: [
  SliverAppBar(pinned: true, stretch: true, expandedHeight: 240, flexibleSpace: ...),
  const SliverToBoxAdapter(child: ProfileCard()),
  SliverPersistentHeader(pinned: true, delegate: _TabBarDelegate(_tabs)),
  SliverGrid.builder(
    gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 3),
    itemBuilder: (_, i) => Image.network(urls[i]),
    itemCount: urls.length,
  ),
  SliverList.builder(itemCount: 100, itemBuilder: (_, i) => CommentTile(i)),
]);
```

> 🔑 ប្រើ `SliverList.builder` / `SliverGrid.builder` (មាន `.builder`) ដើម្បីរក្សា lazy rendering។ `SliverList(delegate: SliverChildListDelegate([...]))` បង្កើត widget ទាំងអស់ភ្លាមៗ។

---

### ២.៧ InheritedWidget

```dart
class AppConfig extends InheritedWidget {
  final String apiBaseUrl;
  final bool isDarkMode;
  const AppConfig({
    super.key,
    required this.apiBaseUrl,
    required this.isDarkMode,
    required super.child,
  });

  static AppConfig of(BuildContext context) {
    final r = maybeOf(context);
    assert(r != null, 'AppConfig មិនត្រូវបានរកឃើញនៅខាងលើ context នេះទេ');
    return r!;
  }

  static AppConfig? maybeOf(BuildContext context) =>
      context.dependOnInheritedWidgetOfExactType<AppConfig>();

  @override
  bool updateShouldNotify(AppConfig old) =>
      old.apiBaseUrl != apiBaseUrl || old.isDarkMode != isDarkMode;
}
```

**លទ្ធផលនៃជំហានទី ៣:** widget **ទាំងពីរ** rebuild។ ព្រោះ `dependOnInheritedWidgetOfExactType` ចុះឈ្មោះ dependency លើ **widget ទាំងមូល** មិនមែនលើ field ណាមួយទេ។

**ដំណោះស្រាយ — `InheritedModel`:**

```dart
enum ConfigAspect { url, theme }

class AppConfig extends InheritedModel<ConfigAspect> {
  final String apiBaseUrl;
  final bool isDarkMode;
  const AppConfig({super.key, required this.apiBaseUrl,
                   required this.isDarkMode, required super.child});

  static AppConfig of(BuildContext context, ConfigAspect aspect) =>
      InheritedModel.inheritFrom<AppConfig>(context, aspect: aspect)!;

  @override
  bool updateShouldNotify(AppConfig old) =>
      old.apiBaseUrl != apiBaseUrl || old.isDarkMode != isDarkMode;

  @override
  bool updateShouldNotifyDependent(AppConfig old, Set<ConfigAspect> deps) {
    if (deps.contains(ConfigAspect.url) && old.apiBaseUrl != apiBaseUrl) return true;
    if (deps.contains(ConfigAspect.theme) && old.isDarkMode != isDarkMode) return true;
    return false;
  }
}
```

**`dependOnInheritedWidgetOfExactType` ធៀបនឹង `getInheritedWidgetOfExactType`:**
- `dependOn...` → **ចុះឈ្មោះ** ជា dependent → rebuild ពេលតម្លៃប្ដូរ។ ប្រើក្នុង `build()` និង `didChangeDependencies()`។
- `get...` → អានតែម្ដង **ដោយមិនចុះឈ្មោះ** → មិន rebuild។ ប្រើក្នុង `initState()` ឬក្នុង callback (ឧ. `onPressed`) ដែលអ្នកគ្រាន់តែចង់អានតម្លៃបច្ចុប្បន្ន។

---

### ២.៨ BuildContext

**មូលហេតុ:** `context` នៅក្នុង `build()` នៃ `BadPage` គឺជា Element នៃ `BadPage` **ខ្លួនឯង** — ដែលនៅ **ខាងលើ** `Scaffold`។ `ScaffoldMessenger.of(context)` រុករក **ឡើងលើ** ដូច្នេះវាមិនឃើញ `Scaffold` ដែលនៅខាងក្រោមទេ។

**វិធីកែ ៣:**

```dart
// 1) Builder — បង្កើត context ថ្មីនៅខាងក្នុង Scaffold
Scaffold(body: Builder(builder: (context) => ElevatedButton(
  onPressed: () => ScaffoldMessenger.of(context).showSnackBar(...),
  child: const Text('បង្ហាញ'),
)));

// 2) បំបែកជា widget ដាច់ដោយឡែក (ល្អបំផុត — ក៏ជួយ performance ផង)
class _ShowButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) => ElevatedButton(
    onPressed: () => ScaffoldMessenger.of(context).showSnackBar(...),
    child: const Text('បង្ហាញ'),
  );
}

// 3) GlobalKey<ScaffoldMessengerState> ដាក់នៅ MaterialApp
final messengerKey = GlobalKey<ScaffoldMessengerState>();
MaterialApp(scaffoldMessengerKey: messengerKey, ...);
messengerKey.currentState?.showSnackBar(...);   // ហៅពីកន្លែងណាក៏បាន
```

> 💡 វិធីទី ៣ មានប្រយោជន៍ខ្លាំងសម្រាប់បង្ហាញ error ពី layer ដែលគ្មាន `BuildContext` (ឧ. Dio interceptor)។ យើងនឹងប្រើវានៅលំហាត់ ៣.៥។

---

# ផ្នែកទី ៣ — ស្ថាបត្យកម្មកម្មវិធីពិត (លំហាត់ ៣.១ – ៣.៩)

> ចាប់ពីទីនេះ លំហាត់នីមួយៗគឺជា **អង្គភាពមួយនៃកម្មវិធីពិត**។ ធ្វើឲ្យអស់ទាំង ៩ នោះអ្នកនឹងមានគ្រឿងបន្លាស់គ្រប់គ្រាន់សម្រាប់គម្រោងចុងក្រោយ។

---

### លំហាត់ ៣.១ — go_router ជាមួយ Auth Guard
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~60 នាទី

**📋 ការងារ:** សាងសង់ router មួយដែលមាន៖

```
/splash                       ← ពិនិត្យ token
/login
/                             ← ShellRoute (bottom nav)
  /home
  /search
  /profile
/book/:id                     ← ទំព័រពេញ (លើ shell)
/book/:id/review/new          ← nested, ត្រូវការ auth
```

លក្ខខណ្ឌ៖
1. **`redirect`** — អ្នកមិនទាន់ login → បញ្ជូនទៅ `/login` ព្រមទាំងរក្សា `?from=` ដើម្បីត្រឡប់មកវិញ
2. **`refreshListenable`** — ពេល auth state ប្ដូរ router ត្រូវវាយតម្លៃឡើងវិញដោយស្វ័យប្រវត្តិ
3. **`ShellRoute`** — bottom nav **មិនត្រូវលោត** ពេលប្ដូរ tab
4. **Deep link** — `myapp://book/42` បើកទំព័រត្រឹមត្រូវ (សាកល្បងដោយ `adb shell am start`)
5. **`errorBuilder`** — ទំព័រ 404 ស្អាត
6. ប្រើ **typed routes** (`go_router_builder`) សម្រាប់យ៉ាងតិចមួយ route

**✅ លក្ខខណ្ឌជោគជ័យ:**
- បើកកម្មវិធីពេលមិន login ដោយ deep link `myapp://book/42/review/new` → ចូល login → បន្ទាប់ពី login ជោគជ័យ **ត្រូវទៅដល់ទំព័រ review** ដោយស្វ័យប្រវត្តិ
- ប៊ូតុងថយក្រោយរបស់ Android ដំណើរការត្រឹមត្រូវគ្រប់ករណី

**💡 គន្លឹះ:** `redirect` ត្រូវតែជា **pure function** — កុំហៅ API នៅក្នុងវា។ Guard ត្រូវអានតែ state ដែលមានស្រាប់។

---

### លំហាត់ ៣.២ — State Management ៥ បែប លើ feature តែមួយ
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~90 នាទី

**🎯 គោលដៅ:** ជ្រើសរើសដោយផ្អែកលើបទពិសោធន៍ មិនមែនផ្អែកលើ Twitter។

**📋 ការងារ:** បង្កើត feature **តែមួយ** — "ស្វែងរកសៀវភៅ" ដែលមាន៖
- ប្រអប់ស្វែងរក (debounced 300ms — ប្រើ transformer ពីលំហាត់ ១.៦!)
- Loading / Success / Empty / Error states (ប្រើ `UiState` ពីលំហាត់ ១.៤!)
- Pull to refresh
- Retry ពេលបរាជ័យ

រួចសរសេរវា **៥ ដង** ក្នុង folder ដាច់ដោយឡែក៖

| # | វិធីសាស្ត្រ |
|---|---|
| 1 | `setState` សុទ្ធ |
| 2 | `ValueNotifier` + `ValueListenableBuilder` |
| 3 | `ChangeNotifier` + `provider` |
| 4 | `riverpod` (`AsyncNotifier`) |
| 5 | `bloc` / `cubit` |

**បំពេញតារាងនេះដោយខ្លួនឯងបន្ទាប់ពីធ្វើចប់៖**

| លក្ខណៈ | setState | ValueNotifier | Provider | Riverpod | Bloc |
|---|---|---|---|---|---|
| បន្ទាត់កូដសរុប | | | | | |
| ងាយសរសេរ test | | | | | |
| ចែករំលែក state ឆ្លងទំព័រ | | | | | |
| ភាពងាយស្រួលរបស់អ្នកចាប់ផ្ដើម | | | | | |
| Boilerplate | | | | | |

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកអាចនិយាយបានក្នុងប្រយោគមួយថា **ក្នុងគម្រោងណាអ្នកនឹងជ្រើសរើសមួយណា និងហេតុអ្វី**។ (ចម្លើយ "Riverpod ព្រោះគេថាល្អ" មិនរាប់ទេ។)

---

### លំហាត់ ៣.៣ — MVVM ជាមួយ Command Pattern
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~50 នាទី

**📋 ការងារ:** អនុវត្តតាមស្ថាបត្យកម្មផ្លូវការរបស់ Flutter។ សរសេរ `Command` class ដែលរុំគ្រប់ action ដែលមាន side effect៖

```dart
class Command<T> extends ChangeNotifier {
  Command(this._action);
  final Future<Result<T>> Function() _action;

  bool _running = false;
  Result<T>? _result;

  bool get running => _running;
  Result<T>? get result => _result;
  bool get error => _result is Error;
  bool get completed => _result is Ok;

  Future<void> execute() async { /* TODO */ }
  void clearResult() { /* TODO */ }
}
```

លក្ខខណ្ឌ៖
- មិនអនុញ្ញាតឲ្យ execute ស្របគ្នា (បើ `running` → ត្រឡប់ភ្លាម)
- `notifyListeners()` នៅចាប់ផ្ដើម និងបញ្ចប់
- ViewModel មាន `Command0 load` និង `Command1<void, String> search`
- View ស្ដាប់ `command.running` ដើម្បីបង្ហាញ spinner ហើយស្ដាប់ `command.error` ដើម្បីបង្ហាញ SnackBar

**✅ លក្ខខណ្ឌជោគជ័យ:** View **គ្មាន `try/catch`** និង **គ្មាន `bool _isLoading`** សូម្បីតែមួយ។ ចុចប៊ូតុងលឿនៗ ១០ ដង → API ត្រូវហៅតែម្ដង។

---

### លំហាត់ ៣.៤ — Result Type
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~30 នាទី

**📋 ការងារ:** បង្កើត `sealed class Result<T>` (`Ok<T>` / `Error<T>`) និង៖
- `Result.guard(() => ...)` — ចាប់ exception ដោយស្វ័យប្រវត្តិ
- `map<R>()`, `flatMap<R>()`, `getOrElse()`
- Extension លើ `Future<Result<T>>` ដើម្បី chain បាន

រួច refactor repository មួយឲ្យ **គ្មាន `throw` ចេញក្រៅ layer** ទាល់តែសោះ។

**✅ លក្ខខណ្ឌជោគជ័យ:** ViewModel មិនអាចភ្លេចដោះស្រាយ error បានទេ ព្រោះ compiler បង្ខំដោយ exhaustive switch។

---

### លំហាត់ ៣.៥ — Dio Interceptor + Token Refresh
**កម្រិត:** ⭐⭐⭐⭐ · **ពេលវេលា:** ~75 នាទី

**🎯 គោលដៅ:** នេះជា feature ដែលបេក្ខជនភាគច្រើនធ្លាក់នៅ interview។

**📋 ការងារ:** សរសេរ interceptor ដែល៖
1. បញ្ចូល `Authorization: Bearer <token>` គ្រប់ request
2. ពេលទទួល `401` → ហៅ `/auth/refresh` → រត់ request ដើមឡើងវិញ
3. **បើមាន request ៥ ទទួល `401` ព្រមគ្នា → ត្រូវហៅ `/auth/refresh` តែម្ដង** ហើយ request ទាំង ៥ រង់ចាំលទ្ធផលដដែល
4. បើ refresh បរាជ័យ → logout + navigate ទៅ `/login` (ប្រើ `scaffoldMessengerKey` ពីលំហាត់ ២.៨)
5. Log request/response នៅតែ debug mode
6. Retry ស្វ័យប្រវត្តិលើ network error ជាមួយ **exponential backoff** (200ms, 400ms, 800ms — អតិបរមា ៣ ដង)

**✅ លក្ខខណ្ឌជោគជ័យ:** សរសេរ test ដោយប្រើ `DioAdapter` (`http_mock_adapter`) ដែលបញ្ជាក់ថា ក្នុងករណី ៥ request ស្របគ្នា នោះ `/auth/refresh` ត្រូវបានហៅ **ពិតប្រាកដ ១ ដង**។

**💡 គន្លឹះ:** អ្នកត្រូវការ `Completer` និង flag `_isRefreshing` នៅកម្រិត interceptor។

---

### លំហាត់ ៣.៦ — freezed + JSON ក្នុងករណីពិត
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~45 នាទី

**📋 ការងារ:** Model សម្រាប់ API response នេះ — ដែលមាន **អន្ទាក់ ៥**៖

```json
{
  "id": "b_001",
  "title": "សៀវភៅរៀន Flutter",
  "author": { "id": "a_9", "name": "Heak" },
  "published_at": "2026-03-01T08:00:00Z",
  "tags": ["flutter", "dart"],
  "status": "AVAILABLE",
  "rating": null,
  "price": "12.50",
  "meta": { "views": 100, "extra": { "anything": true } }
}
```

អន្ទាក់៖
1. `snake_case` → `camelCase` (`published_at`)
2. `status` ជា enum តែ server អាចផ្ញើតម្លៃថ្មីមកថ្ងៃណាមួយ → ត្រូវមាន `@JsonValue` + `unknown` fallback (`@JsonKey(unknownEnumValue:)`)
3. `rating` អាច `null` → `double?`
4. `price` មកជា `String` តែយើងចង់បាន `double` → custom converter
5. `meta.extra` ជា free-form → `Map<String, dynamic>`

លក្ខខណ្ឌ៖ ប្រើ `freezed` + `json_serializable`, បង្កើត `copyWith`, `==`, `toJson`, និង **unit test** ដែល round-trip (`fromJson(toJson(x)) == x`)។

**✅ លក្ខខណ្ឌជោគជ័យ:** ពេល server ផ្ញើ `"status": "SOMETHING_NEW"` — កម្មវិធី **មិន crash** ទេ។

---

### លំហាត់ ៣.៧ — Secure Storage + Repository Cache
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~50 នាទី

**📋 ការងារ:** បង្កើត `AuthRepository` ដែល៖
- រក្សា access/refresh token ក្នុង `flutter_secure_storage`
- Expose `Stream<AuthState>` (broadcast — ត្រឡប់ទៅលំហាត់ ១.៧!) ដែល router ស្ដាប់
- មាន in-memory cache ដើម្បីជៀសវាងអាន keystore រាល់ request (keystore យឺត!)
- លុប token ទាំងអស់ពេល logout

រួច `BookRepository` ដែល៖
- Cache-first៖ ត្រឡប់ data ចាស់ភ្លាមៗ រួច fetch ថ្មីនៅផ្ទៃខាងក្រោយ (stale-while-revalidate)
- ត្រឡប់ `Stream<Result<List<Book>>>` ដែល emit ២ ដង (cache, រួច network)

**✅ លក្ខខណ្ឌជោគជ័យ:** បិទ internet → កម្មវិធីនៅតែបង្ហាញ data ចាស់ ព្រមទាំង banner "offline"។

---

### លំហាត់ ៣.៨ — Form Validation ត្រឹមត្រូវ
**កម្រិត:** ⭐⭐ · **ពេលវេលា:** ~40 នាទី

**📋 ការងារ:** ទម្រង់ចុះឈ្មោះមាន៖ ឈ្មោះ, អ៊ីមែល, លេខទូរស័ព្ទខ្មែរ (ប្រើ parser ពីលំហាត់ ១.៣!), ពាក្យសម្ងាត់, បញ្ជាក់ពាក្យសម្ងាត់។

លក្ខខណ្ឌ៖
- Validate **នៅពេលបាត់ focus** មិនមែនរាល់ការវាយអក្សរ
- បង្ហាញ error ជាភាសាខ្មែរ (ត្រៀមសម្រាប់ i18n នៅលំហាត់ ៤.៨)
- ប៊ូតុង submit disabled ពេលទម្រង់មិនត្រឹមត្រូវ
- ពេល server ត្រឡប់ `422` ជាមួយ field errors → map ទៅ field ត្រឹមត្រូវ
- `FocusNode` ទាំងអស់ត្រូវ dispose

**✅ លក្ខខណ្ឌជោគជ័យ:** ចុច "next" លើ keyboard → ទៅ field បន្ទាប់តាមលំដាប់ត្រឹមត្រូវ ហើយ field ចុងក្រោយ submit។

---

### លំហាត់ ៣.៩ — ភ្ជាប់ទៅ Backend ពិត
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~60 នាទី

**📋 ការងារ:** ភ្ជាប់កម្មវិធីទៅ backend ពិតមួយ (Spring Boot បើអ្នកមានស្រាប់ ឬ `json-server` សម្រាប់សាកល្បង)។

លក្ខខណ្ឌ៖
- `--dart-define` សម្រាប់ `API_BASE_URL` (កុំ hardcode!)
- Android emulator ត្រូវប្រើ `10.0.2.2` មិនមែន `localhost`
- ដោះស្រាយ CORS សម្រាប់ Flutter web
- ដោះស្រាយ self-signed cert នៅ dev (និង **កុំ** ដោះស្រាយវានៅ production)
- Timeout ត្រឹមត្រូវ (connect 10s, receive 15s)

**✅ លក្ខខណ្ឌជោគជ័យ:** កម្មវិធីតែមួយអាចចង្អុលទៅ dev/staging/prod ដោយប្ដូរតែ command line។

---

## ✅ ចម្លើយ — ផ្នែកទី ៣

### ៣.១ go_router

```dart
final _rootKey = GlobalKey<NavigatorState>();
final _shellKey = GlobalKey<NavigatorState>();

GoRouter createRouter(AuthRepository auth) => GoRouter(
      navigatorKey: _rootKey,
      initialLocation: '/splash',
      refreshListenable: GoRouterRefreshStream(auth.changes),  // 🔑
      redirect: (context, state) {
        final status = auth.status;                 // sync read — pure!
        final loc = state.matchedLocation;

        if (status == AuthStatus.unknown) {
          return loc == '/splash' ? null : '/splash';
        }
        final loggedIn = status == AuthStatus.authenticated;
        final atAuthPage = loc == '/login' || loc == '/splash';

        if (!loggedIn && !atAuthPage) {
          return '/login?from=${Uri.encodeComponent(state.uri.toString())}';
        }
        if (loggedIn && atAuthPage) {
          final from = state.uri.queryParameters['from'];
          return from != null ? Uri.decodeComponent(from) : '/home';
        }
        return null;
      },
      errorBuilder: (_, state) => NotFoundPage(uri: state.uri),
      routes: [
        GoRoute(path: '/splash', builder: (_, __) => const SplashPage()),
        GoRoute(path: '/login', builder: (_, __) => const LoginPage()),
        ShellRoute(
          navigatorKey: _shellKey,
          builder: (_, __, child) => AppShell(child: child),
          routes: [
            GoRoute(path: '/home', pageBuilder: (_, s) =>
                NoTransitionPage(key: s.pageKey, child: const HomePage())),
            GoRoute(path: '/search', pageBuilder: (_, s) =>
                NoTransitionPage(key: s.pageKey, child: const SearchPage())),
            GoRoute(path: '/profile', pageBuilder: (_, s) =>
                NoTransitionPage(key: s.pageKey, child: const ProfilePage())),
          ],
        ),
        GoRoute(
          path: '/book/:id',
          parentNavigatorKey: _rootKey,        // 🔑 លើ shell មិននៅក្នុង
          builder: (_, s) => BookPage(id: s.pathParameters['id']!),
          routes: [
            GoRoute(
              path: 'review/new',
              parentNavigatorKey: _rootKey,
              builder: (_, s) => NewReviewPage(bookId: s.pathParameters['id']!),
            ),
          ],
        ),
      ],
    );

/// បំប្លែង Stream ទៅ Listenable សម្រាប់ refreshListenable
class GoRouterRefreshStream extends ChangeNotifier {
  late final StreamSubscription<dynamic> _sub;
  GoRouterRefreshStream(Stream<dynamic> stream) {
    notifyListeners();
    _sub = stream.asBroadcastStream().listen((_) => notifyListeners());
  }
  @override
  void dispose() { _sub.cancel(); super.dispose(); }
}
```

**ចំណុចសំខាន់ ៣:**
1. `redirect` ត្រូវអាន state **synchronously**។ បើអ្នកត្រូវការការងារ async (អាន token) — ធ្វើវានៅ splash ហើយ push លទ្ធផលទៅ `auth.status`។
2. `NoTransitionPage` + `state.pageKey` ធ្វើឲ្យការប្ដូរ tab មិនមាន animation លោត។
3. `parentNavigatorKey: _rootKey` ធ្វើឲ្យទំព័រ push លើ shell (bottom nav បាត់) ជំនួសឲ្យខាងក្នុង។

សម្រាប់ Android deep link — `AndroidManifest.xml`៖

```xml
<intent-filter android:autoVerify="true">
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
  <data android:scheme="myapp" />
</intent-filter>
```

```bash
adb shell am start -a android.intent.action.VIEW -d "myapp://book/42/review/new"
```

---

### ៣.២ ការប្រៀបធៀប State Management

នេះជាចម្លើយសង្ខេប — **តែតារាងរបស់អ្នកមានតម្លៃជាង** ព្រោះវាមកពីដៃអ្នកផ្ទាល់។

| លក្ខណៈ | setState | ValueNotifier | Provider | Riverpod | Bloc |
|---|---|---|---|---|---|
| បន្ទាត់កូដ (ប្រហែល) | 70 | 90 | 110 | 100 | 160 |
| Test ដោយគ្មាន widget | ❌ | ✅ | ✅ | ✅✅ | ✅✅ |
| ចែក state ឆ្លងទំព័រ | ❌ | ពិបាក | ✅ | ✅✅ | ✅ |
| Compile-time safety | — | — | ❌ (runtime `ProviderNotFoundException`) | ✅ | ✅ |
| Auto dispose | ✅ | ដោយដៃ | ដោយដៃ | ✅ | ដោយដៃ (ឬ BlocProvider) |
| ខ្សែសង្វាក់រៀន | ទាបបំផុត | ទាប | មធ្យម | ខ្ពស់ | ខ្ពស់បំផុត |

**ការណែនាំជាក់ស្ដែង:**
- **State ក្នុង widget តែមួយ** (animation, form field, បើក/បិទ) → `setState` ជានិច្ច។ កុំដាក់វាចូល Riverpod។
- **គម្រោងតូច / freelance ១–២ សប្ដាហ៍** → `ValueNotifier` + `Provider`។ លឿន ហើយអ្នកផ្សេងអានចេញ។
- **គម្រោងមធ្យមឡើងទៅ, ក្រុមតូច** → **Riverpod**។ `AsyncNotifier` ដោះស្រាយ loading/error ជំនួសអ្នក ហើយ compile-time safety ជួយពិត។
- **ក្រុមធំ, business logic ស្មុគស្មាញ, ត្រូវការ audit trail នៃ event** → **Bloc**។ Boilerplate ច្រើន តែរាល់ការប្ដូរ state មាន event ដែលអាច log បាន។

> 🔑 កំហុសធំបំផុតរបស់អ្នកចាប់ផ្ដើម៖ ដាក់ **គ្រប់យ៉ាង** ចូល global state management។ ចំនួន state ដែលពិតជាត្រូវការ global គឺតិចជាងអ្វីដែលអ្នកគិត។

---

### ៣.៣ Command Pattern

```dart
abstract class Command<T> extends ChangeNotifier {
  bool _running = false;
  Result<T>? _result;

  bool get running => _running;
  Result<T>? get result => _result;
  bool get error => _result is Error<T>;
  bool get completed => _result is Ok<T>;

  Future<void> _execute(Future<Result<T>> Function() action) async {
    if (_running) return;                 // 🔑 ការពារ double-tap
    _running = true;
    _result = null;
    notifyListeners();
    try {
      _result = await action();
    } finally {
      _running = false;
      notifyListeners();
    }
  }

  void clearResult() {
    _result = null;
    notifyListeners();
  }
}

class Command0<T> extends Command<T> {
  Command0(this._action);
  final Future<Result<T>> Function() _action;
  Future<void> execute() => _execute(_action);
}

class Command1<T, A> extends Command<T> {
  Command1(this._action);
  final Future<Result<T>> Function(A) _action;
  Future<void> execute(A arg) => _execute(() => _action(arg));
}
```

ViewModel៖

```dart
class SearchViewModel extends ChangeNotifier {
  SearchViewModel(this._repo) {
    load = Command0(_load)..execute();
    search = Command1(_search);
  }
  final BookRepository _repo;

  late final Command0<List<Book>> load;
  late final Command1<List<Book>, String> search;

  List<Book> books = [];

  Future<Result<List<Book>>> _load() async {
    final r = await _repo.recent();
    if (r is Ok<List<Book>>) books = r.value;
    return r;
  }

  Future<Result<List<Book>>> _search(String q) async {
    final r = await _repo.search(q);
    if (r is Ok<List<Book>>) books = r.value;
    return r;
  }
}
```

View — សូមកត់សម្គាល់ថា **គ្មាន try/catch គ្មាន `_isLoading`**៖

```dart
ListenableBuilder(
  listenable: vm.search,
  builder: (context, _) {
    if (vm.search.running) return const CircularProgressIndicator();
    if (vm.search.error) {
      WidgetsBinding.instance.addPostFrameCallback((_) {
        ScaffoldMessenger.of(context).showSnackBar(
          const SnackBar(content: Text('មានបញ្ហា — សូមព្យាយាមម្ដងទៀត')),
        );
        vm.search.clearResult();
      });
    }
    return BookList(books: vm.books);
  },
)
```

> ⚠️ `addPostFrameCallback` ចាំបាច់ ព្រោះអ្នកមិនអាចបង្ហាញ SnackBar នៅពេលកំពុង build។

---

### ៣.៤ Result

```dart
sealed class Result<T> {
  const Result();

  static Result<T> guard<T>(T Function() body) {
    try { return Ok(body()); } catch (e, st) { return Error(e, st); }
  }

  static Future<Result<T>> guardAsync<T>(Future<T> Function() body) async {
    try { return Ok(await body()); } catch (e, st) { return Error(e, st); }
  }

  Result<R> map<R>(R Function(T) f) => switch (this) {
        Ok(value: final v) => Result.guard(() => f(v)),
        Error(:final error, :final stackTrace) => Error(error, stackTrace),
      };

  Result<R> flatMap<R>(Result<R> Function(T) f) => switch (this) {
        Ok(value: final v) => f(v),
        Error(:final error, :final stackTrace) => Error(error, stackTrace),
      };

  T getOrElse(T Function(Object) fallback) => switch (this) {
        Ok(value: final v) => v,
        Error(:final error) => fallback(error),
      };
}

final class Ok<T> extends Result<T> {
  final T value;
  const Ok(this.value);
}

final class Error<T> extends Result<T> {
  final Object error;
  final StackTrace? stackTrace;
  const Error(this.error, [this.stackTrace]);
}

extension FutureResultX<T> on Future<Result<T>> {
  Future<Result<R>> mapAsync<R>(FutureOr<R> Function(T) f) async =>
      switch (await this) {
        Ok(value: final v) => await Result.guardAsync(() async => await f(v)),
        Error(:final error, :final stackTrace) => Error(error, stackTrace),
      };
}
```

Repository៖

```dart
class BookRepository {
  BookRepository(this._api);
  final Dio _api;

  Future<Result<List<Book>>> search(String q) => Result.guardAsync(() async {
        final res = await _api.get('/books', queryParameters: {'q': q});
        return (res.data['items'] as List)
            .map((e) => Book.fromJson(e as Map<String, dynamic>))
            .toList();
      });
}
```

> 🔑 អត្ថប្រយោជន៍ពិត៖ signature `Future<Result<List<Book>>>` **ប្រាប់អ្នកហៅថាវាអាចបរាជ័យ**។ ចំណែក `Future<List<Book>>` ដែល `throw` នៅខាងក្នុង — គ្មានអ្វីរំលឹកអ្នកទេ ហើយកម្មវិធីនឹង crash នៅថ្ងៃដែលអ្នកភ្លេច។

---

### ៣.៥ Token Refresh Interceptor

នេះជាចម្លើយពេញ — សូមអានផ្នែក `_isRefreshing` និង `_queue` ឲ្យបានយកចិត្តទុកដាក់។

```dart
class AuthInterceptor extends QueuedInterceptor {
  AuthInterceptor(this._store, this._refreshDio, this._onLogout);

  final TokenStore _store;
  final Dio _refreshDio;          // 🔑 Dio ដាច់ដោយឡែក — គ្មាន interceptor នេះ
  final VoidCallback _onLogout;

  bool _isRefreshing = false;
  Completer<String?>? _refreshCompleter;

  @override
  void onRequest(RequestOptions o, RequestInterceptorHandler h) {
    final t = _store.accessToken;
    if (t != null) o.headers['Authorization'] = 'Bearer $t';
    h.next(o);
  }

  @override
  Future<void> onError(DioException e, ErrorInterceptorHandler h) async {
    if (e.response?.statusCode != 401 || e.requestOptions.extra['retried'] == true) {
      return h.next(e);
    }

    final newToken = await _refreshOnce();
    if (newToken == null) {
      _onLogout();
      return h.next(e);
    }

    final o = e.requestOptions
      ..headers['Authorization'] = 'Bearer $newToken'
      ..extra['retried'] = true;          // 🔑 ការពារ loop មិនចេះចប់

    try {
      final r = await Dio(BaseOptions(baseUrl: o.baseUrl)).fetch(o);
      return h.resolve(r);
    } catch (_) {
      return h.next(e);
    }
  }

  /// ធានាថា refresh រត់តែម្ដង ទោះមាន caller ប៉ុន្មានក៏ដោយ
  Future<String?> _refreshOnce() {
    if (_isRefreshing) return _refreshCompleter!.future;   // 🔑 រង់ចាំដដែល

    _isRefreshing = true;
    _refreshCompleter = Completer<String?>();

    () async {
      try {
        final rt = _store.refreshToken;
        if (rt == null) return _refreshCompleter!.complete(null);
        final res = await _refreshDio.post('/auth/refresh', data: {'refresh_token': rt});
        final access = res.data['access_token'] as String;
        await _store.save(access, res.data['refresh_token'] as String);
        _refreshCompleter!.complete(access);
      } catch (_) {
        _refreshCompleter!.complete(null);
      } finally {
        _isRefreshing = false;
      }
    }();

    return _refreshCompleter!.future;
  }
}
```

Retry ជាមួយ backoff (interceptor ដាច់ដោយឡែក)៖

```dart
class RetryInterceptor extends Interceptor {
  static const _max = 3;

  @override
  Future<void> onError(DioException e, ErrorInterceptorHandler h) async {
    final retriable = e.type == DioExceptionType.connectionTimeout ||
        e.type == DioExceptionType.receiveTimeout ||
        e.type == DioExceptionType.connectionError;

    final count = (e.requestOptions.extra['retry_count'] as int?) ?? 0;
    if (!retriable || count >= _max) return h.next(e);

    await Future.delayed(Duration(milliseconds: 200 * (1 << count)));  // 200,400,800
    final o = e.requestOptions..extra['retry_count'] = count + 1;

    try {
      return h.resolve(await Dio(BaseOptions(baseUrl: o.baseUrl)).fetch(o));
    } catch (err) {
      return h.next(err is DioException ? err : e);
    }
  }
}
```

**លំដាប់ interceptor សំខាន់ណាស់:**

```dart
dio.interceptors.addAll([
  AuthInterceptor(store, refreshDio, onLogout),   // 1 — ដាក់ token
  RetryInterceptor(),                             // 2 — retry network
  if (kDebugMode) LogInterceptor(responseBody: true),  // 3 — log ចុងក្រោយ
]);
```

> 🔑 ចំណុចដែលមនុស្សភ្លេច៖ `_refreshDio` ត្រូវជា instance **ដាច់ដោយឡែក** ដែលគ្មាន `AuthInterceptor`។ បើមិនដូច្នេះទេ ការហៅ `/auth/refresh` ដែលបរាជ័យនឹងបង្កឲ្យ refresh ម្ដងទៀត → recursion មិនចេះចប់។

---

### ៣.៦ freezed

```dart
@freezed
class Book with _$Book {
  const factory Book({
    required String id,
    required String title,
    required Author author,
    @JsonKey(name: 'published_at') required DateTime publishedAt,
    @Default(<String>[]) List<String> tags,
    @JsonKey(unknownEnumValue: BookStatus.unknown)
    @Default(BookStatus.unknown) BookStatus status,
    double? rating,
    @StringDoubleConverter() required double price,
    @Default(<String, dynamic>{}) Map<String, dynamic> meta,
  }) = _Book;

  factory Book.fromJson(Map<String, dynamic> json) => _$BookFromJson(json);
}

enum BookStatus {
  @JsonValue('AVAILABLE') available,
  @JsonValue('BORROWED') borrowed,
  unknown,                                   // 🔑 fallback
}

class StringDoubleConverter implements JsonConverter<double, dynamic> {
  const StringDoubleConverter();
  @override
  double fromJson(dynamic json) => switch (json) {
        num n => n.toDouble(),
        String s => double.tryParse(s) ?? 0,
        _ => 0,
      };
  @override
  dynamic toJson(double v) => v.toStringAsFixed(2);
}
```

```bash
dart run build_runner build --delete-conflicting-outputs
```

Test round-trip៖

```dart
test('round-trip', () {
  final b = Book.fromJson(sampleJson);
  expect(Book.fromJson(b.toJson()), b);      // freezed ផ្ដល់ == ឲ្យ
});

test('enum ដែលមិនស្គាល់មិន crash', () {
  final b = Book.fromJson({...sampleJson, 'status': 'SOMETHING_NEW'});
  expect(b.status, BookStatus.unknown);
});
```

> 💡 សម្រាប់ `@Default` និង `unknownEnumValue` — គ្រប់ field ដែលមកពី server គួរមាន **យុទ្ធសាស្ត្រពេលបាត់ ឬពេលមិនស្គាល់**។ Backend នឹងប្ដូរនៅថ្ងៃណាមួយដោយមិនប្រាប់អ្នក។

---

### ៣.៧ Repository និង Cache

```dart
class AuthRepository {
  AuthRepository(this._storage);
  final FlutterSecureStorage _storage;

  final _controller = StreamController<AuthStatus>.broadcast();
  Stream<AuthStatus> get changes => _controller.stream;

  AuthStatus _status = AuthStatus.unknown;
  String? _accessCache;                     // 🔑 in-memory — keystore យឺត

  AuthStatus get status => _status;
  String? get accessToken => _accessCache;

  Future<void> restore() async {
    _accessCache = await _storage.read(key: 'access');
    _set(_accessCache == null
        ? AuthStatus.unauthenticated
        : AuthStatus.authenticated);
  }

  Future<void> logout() async {
    await _storage.deleteAll();
    _accessCache = null;
    _set(AuthStatus.unauthenticated);
  }

  void _set(AuthStatus s) {
    _status = s;
    _controller.add(s);
  }

  void dispose() => _controller.close();
}
```

Stale-while-revalidate៖

```dart
Stream<Result<List<Book>>> watchBooks() async* {
  final cached = await _local.readBooks();
  if (cached.isNotEmpty) yield Ok(cached);       // 1) ចាស់ភ្លាមៗ

  final fresh = await Result.guardAsync(() => _api.fetchBooks());
  if (fresh is Ok<List<Book>>) {
    await _local.writeBooks(fresh.value);
  }
  yield fresh;                                   // 2) ថ្មី (ឬ error)
}
```

> 🔑 ចំណាំ UX៖ បើ `fresh` ជា `Error` តែ `cached` មិនទទេ — កុំបង្ហាញទំព័រ error ពេញអេក្រង់។ បង្ហាញ data ចាស់ + banner តូច។ នេះជាភាពខុសគ្នារវាងកម្មវិធីល្អ និងកម្មវិធីធម្មតា។

---

### ៣.៨ Form

```dart
class _SignUpFormState extends State<SignUpForm> {
  final _formKey = GlobalKey<FormState>();
  final _name = TextEditingController();
  final _email = TextEditingController();
  final _phone = TextEditingController();
  final _pass = TextEditingController();
  final _confirm = TextEditingController();

  final _emailFocus = FocusNode();
  final _phoneFocus = FocusNode();
  // ...

  @override
  void dispose() {
    for (final c in [_name, _email, _phone, _pass, _confirm]) { c.dispose(); }
    for (final f in [_emailFocus, _phoneFocus]) { f.dispose(); }
    super.dispose();
  }

  @override
  Widget build(BuildContext context) => Form(
        key: _formKey,
        autovalidateMode: AutovalidateMode.onUserInteraction,  // 🔑
        child: Column(children: [
          TextFormField(
            controller: _name,
            textInputAction: TextInputAction.next,
            onFieldSubmitted: (_) => _emailFocus.requestFocus(),
            validator: (v) => (v == null || v.trim().length < 2)
                ? 'សូមបញ្ចូលឈ្មោះ' : null,
          ),
          TextFormField(
            controller: _phone,
            focusNode: _phoneFocus,
            keyboardType: TextInputType.phone,
            validator: (v) {
              final r = parseKhmerPhone(v ?? '');       // ពីលំហាត់ ១.៣
              return r.valid ? null : 'លេខទូរស័ព្ទមិនត្រឹមត្រូវ';
            },
          ),
          TextFormField(
            controller: _confirm,
            textInputAction: TextInputAction.done,
            onFieldSubmitted: (_) => _submit(),
            validator: (v) => v == _pass.text ? null : 'ពាក្យសម្ងាត់មិនដូចគ្នា',
          ),
        ]),
      );
}
```

**Map server error ទៅ field:**

```dart
final Map<String, String?> _serverErrors = {};

// ក្នុង validator៖
validator: (v) => _serverErrors['email'] ?? _localEmailRule(v),

// ពេលទទួល 422៖
setState(() {
  _serverErrors
    ..clear()
    ..addAll((e.response!.data['errors'] as Map).map(
        (k, v) => MapEntry(k as String, (v as List).first as String)));
});
_formKey.currentState!.validate();
```

> ⚠️ `AutovalidateMode.always` បង្ហាញ error ភ្លាមៗពេលបើកទំព័រ — UX អាក្រក់។ ប្រើ `onUserInteraction` ជានិច្ច។

---

### ៣.៩ Backend

```bash
flutter run \
  --dart-define=API_BASE_URL=http://10.0.2.2:8080/api \
  --dart-define=ENV=dev
```

```dart
class Env {
  static const apiBaseUrl = String.fromEnvironment(
    'API_BASE_URL',
    defaultValue: 'http://10.0.2.2:8080/api',
  );
  static const env = String.fromEnvironment('ENV', defaultValue: 'dev');
  static bool get isProd => env == 'prod';
}
```

| បញ្ហា | ដំណោះស្រាយ |
|---|---|
| Android emulator មិនឃើញ `localhost` | ប្រើ `10.0.2.2` (iOS simulator ប្រើ `localhost` បាន) |
| Android 9+ បិទ HTTP ធម្មតា | បន្ថែម `network_security_config.xml` អនុញ្ញាត cleartext **តែ debug** |
| Flutter web ជាប់ CORS | Spring Boot៖ `@CrossOrigin` ឬ `WebMvcConfigurer.addCorsMappings` |
| Self-signed cert នៅ dev | `HttpClient.badCertificateCallback` — រុំដោយ `if (!Env.isProd)` |

```dart
if (!Env.isProd) {
  (dio.httpClientAdapter as IOHttpClientAdapter).createHttpClient = () =>
      HttpClient()..badCertificateCallback = (_, __, ___) => true;
}
```

> ⚠️ បន្ទាត់ខាងលើនៅក្នុង production = គ្មានសុវត្ថិភាព TLS ទាល់តែសោះ។ ការរុំដោយ `if (!Env.isProd)` មិនមែនជាការណែនាំទេ — វាចាំបាច់។

---

# ផ្នែកទី ៤ — Advanced និង Production (លំហាត់ ៤.១ – ៤.៩)

---

### លំហាត់ ៤.១ — រក និងកែ Jank
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~50 នាទី

**📋 ការងារ:** បង្កើត list ៥០០ item ដែលមានបញ្ហា **ដោយចេតនា**៖
- រូបភាពពី network គ្មាន `cacheWidth`
- `BoxShadow` blur ធំលើគ្រប់ item
- `Opacity` widget wrap គ្រប់ item
- គណនាធ្ងន់ (`sqrt` ក្នុង loop) នៅក្នុង `build()`
- `setState` នៅ parent ដែលធ្វើឲ្យ list ទាំងមូល rebuild

រួច៖
1. រត់ `flutter run --profile` ហើយបើក **DevTools → Performance**
2. កត់ត្រា frame time មុនកែ (ថតរូបទុក)
3. កែម្ដងមួយបញ្ហា ហើយវាស់ម្ដងទៀត — **កត់ត្រាថាការកែណាមួយជួយបានច្រើនជាងគេ**
4. បើក **Rendering → Highlight repaints** ដើម្បីមើល repaint boundary

**✅ លក្ខខណ្ឌជោគជ័យ:** ចុះពី > 30ms/frame មកក្រោម 16ms។ អ្នកមានតារាងវាស់ពិត មិនមែនការទាយ។

---

### លំហាត់ ៤.២ — Animation ៣ កម្រិត
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~60 នាទី

**📋 ការងារ:** ធ្វើ animation ដដែល (កាតដែលបើកទៅជាទំព័រពេញ) ដោយ ៣ វិធី៖

1. **Implicit** — `AnimatedContainer`, `AnimatedOpacity`, `AnimatedSwitcher`
2. **Explicit** — `AnimationController` + `CurvedAnimation` + staggered `Interval` (រូបភាព → ចំណងជើង → អត្ថបទ → ប៊ូតុង)
3. **Hero + PageRouteBuilder** — transition រវាងទំព័រ ២ ជាមួយ custom curve

លក្ខខណ្ឌ៖
- `AnimationController` ត្រូវ `dispose()`
- ប្រើ `SingleTickerProviderStateMixin` (ហើយពន្យល់ថាហេតុអ្វីមិនប្រើ `TickerProviderStateMixin`)
- គោរព `MediaQuery.disableAnimations` (accessibility)
- ប្រើ `AnimatedBuilder` ជាមួយ `child:` ដើម្បីជៀសវាង rebuild subtree ដែលមិនប្ដូរ

**✅ លក្ខខណ្ឌជោគជ័យ:** ៦០fps លើ profile mode · បើកកម្មវិធីជាមួយ "Remove animations" បើកនៅ OS → animation បាត់ តែកម្មវិធីនៅដំណើរការត្រឹមត្រូវ។

---

### លំហាត់ ៤.៣ — CustomPainter
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~60 នាទី

**📋 ការងារ:** គូរ **line chart** ដោយគ្មាន package៖
- អ័ក្ស X/Y មានស្លាកជាលេខខ្មែរ (០១២៣...)
- ខ្សែកោង smooth (ប្រើ `Path.cubicTo` មិនមែន `lineTo`)
- តំបន់ gradient នៅក្រោមខ្សែ
- ចំណុចដែលចុចបាន → បង្ហាញ tooltip (ប្រើ `hitTest` ឬ `GestureDetector` + `localPosition`)
- Animation ពេលទិន្នន័យប្ដូរ (`Tween<List<double>>` ផ្ទាល់ខ្លួន)

លក្ខខណ្ឌ៖ `shouldRepaint` ត្រូវត្រឡប់ `false` ពេលទិន្នន័យមិនប្ដូរ។

**✅ លក្ខខណ្ឌជោគជ័យ:** បើក "Highlight repaints" → chart **មិន** ភ្លឺឡើងពេលផ្នែកផ្សេងនៃទំព័រ rebuild។

**💡 គន្លឹះ:** សម្រាប់អក្សរខ្មែរក្នុង canvas ត្រូវប្រើ `TextPainter` ជាមួយ `TextStyle(height: 1.7)` — បើមិនដូច្នេះទេ ជើងអក្សរ និងស្រៈនឹងត្រូវកាត់។

---

### លំហាត់ ៤.៤ — ពីរ៉ាមីតនៃការសាកល្បង
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~90 នាទី

**📋 ការងារ:** សរសេរ test គ្រប់កម្រិតសម្រាប់ feature ស្វែងរក (លំហាត់ ៣.២)៖

| ប្រភេទ | ចំនួន | អ្វីដែលត្រូវសាកល្បង |
|---|---|---|
| Unit | ~10 | `parseKhmerPhone`, `Result`, debounce, ViewModel logic |
| Widget | ~5 | loading state, empty state, error + retry, tap → navigate |
| Golden | ~3 | កាតសៀវភៅ (light/dark), ទំព័រទទេ |
| Integration | ~1 | flow ពេញ៖ login → search → បើកសៀវភៅ |

លក្ខខណ្ឌ៖
- Mock Dio ដោយ `http_mock_adapter` (មិនត្រូវហៅ network ពិត)
- ប្រើ `fakeAsync` ឬ `tester.pump(Duration(...))` សម្រាប់ debounce — **កុំប្រើ `pumpAndSettle` ជាមួយ animation មិនចេះចប់** (វានឹង timeout)
- Golden test ត្រូវរត់បានលើ CI (`flutter test --update-goldens` នៅ local តែប្រៀបធៀបនៅ CI)
- Coverage យ៉ាងតិច 70% លើ `lib/domain` និង `lib/data`

**✅ លក្ខខណ្ឌជោគជ័យ:** `flutter test` ចប់ក្នុងរយៈពេលក្រោម ៣០ វិនាទី ហើយ `flutter test --coverage` បង្ហាញលេខគោលដៅ។

**💡 អន្ទាក់សំខាន់:** golden test ផ្ដល់លទ្ធផលខុសគ្នារវាង macOS និង Linux (font rendering)។ ដំណោះស្រាយ៖ រត់ golden **តែនៅ CI** ដោយប្រើ Docker image ដដែល ឬប្រើ `flutter_test_config.dart` ដើម្បី load font ជាក់លាក់។

---

### លំហាត់ ៤.៥ — Custom RenderObject
**កម្រិត:** ⭐⭐⭐⭐ · **ពេលវេលា:** ~90 នាទី

**📋 ការងារ:** សរសេរ `StaggeredGrid` (Pinterest-style) ជា `RenderBox` ពិត — **មិនប្រើ package**។

អ្នកត្រូវសរសេរ៖
- `class StaggeredGrid extends MultiChildRenderObjectWidget`
- `class RenderStaggeredGrid extends RenderBox with ContainerRenderObjectMixin, RenderBoxContainerDefaultsMixin`
- `performLayout()` — ដាក់ child ចូលជួរឈរដែលខ្លីបំផុត
- `computeDryLayout()`
- `paint()` — ប្រើ `defaultPaint`
- `hitTestChildren()` — ប្រើ `defaultHitTestChildren`

**✅ លក្ខខណ្ឌជោគជ័យ:** ចុចលើ child ណាមួយក៏ដំណើរការត្រឹមត្រូវ · គ្មាន overflow · `debugPaintSizeEnabled` បង្ហាញព្រំដែនត្រឹមត្រូវ។

**💡 គន្លឹះ:** ចាប់ផ្ដើមដោយអាន source នៃ `RenderFlex` (`packages/flutter/lib/src/rendering/flex.dart`)។ វាជាឧទាហរណ៍ដ៏ល្អបំផុត។

---

### លំហាត់ ៤.៦ — Platform Channel ជាមួយ Pigeon
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~60 នាទី

**📋 ការងារ:** ទាញព័ត៌មានឧបករណ៍ពី native ដោយប្រើ **Pigeon** (type-safe មិនមែន `MethodChannel` ធម្មតា)៖

```dart
// pigeons/device_api.dart
@HostApi()
abstract class DeviceApi {
  int getBatteryLevel();
  @async String getDeviceModel();
}

@FlutterApi()
abstract class BatteryWatcher {
  void onBatteryChanged(int level);   // native → Dart
}
```

អនុវត្ត Kotlin (Android) និង Swift (iOS)។

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកពន្យល់បានថាហេតុអ្វី Pigeon ល្អជាង `MethodChannel` (ចម្លើយ៖ ឈ្មោះ method និង type ត្រូវបានពិនិត្យនៅពេល compile ទាំងសងខាង — សរសេរឈ្មោះខុសមិនអាច build បាន)។

**💡 គន្លឹះ:** បើអ្នកបានធ្វើ Kotlin មករួច ផ្នែក Android នឹងលឿន។ សម្រាប់ iOS ត្រូវការ macOS + Xcode។

---

### លំហាត់ ៤.៧ — Flavors និង CI/CD
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~75 នាទី

**📋 ការងារ:**

**ក. Flavors** — `dev`, `staging`, `prod` ដែលមាន៖
- `applicationId` ខុសគ្នា (`kh.study.app.dev`) → តម្លើងព្រមគ្នាបានលើទូរស័ព្ទតែមួយ
- ឈ្មោះ និង icon ខុសគ្នា
- `API_BASE_URL` ខុសគ្នា
- `main_dev.dart`, `main_staging.dart`, `main_prod.dart`

**ខ. GitHub Actions** — workflow ដែល៖
```yaml
on: [pull_request, push]
jobs:
  - flutter analyze          # ត្រូវគ្មាន warning
  - dart format --set-exit-if-changed
  - flutter test --coverage
  - flutter build apk --flavor staging (តែលើ main branch)
  - upload artifact
```

**✅ លក្ខខណ្ឌជោគជ័យ:** PR ដែលមាន warning ត្រូវបាន **block ដោយស្វ័យប្រវត្តិ**។

---

### លំហាត់ ៤.៨ — i18n និងអក្សរខ្មែរ
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~60 នាទី

**📋 ការងារ:** បំប្លែងកម្មវិធីទាំងមូលទៅជា `flutter_localizations` + ARB (ខ្មែរ + អង់គ្លេស)។

លក្ខខណ្ឌពិសេសសម្រាប់ភាសាខ្មែរ៖
- `TextStyle(height: 1.7)` នៅកម្រិត `ThemeData` — ដើម្បីកុំឲ្យជើងអក្សរ និងស្រៈត្រូវកាត់
- Font ខ្មែរដែលបញ្ចូលក្នុង asset (ឧ. Noto Sans Khmer) — កុំពឹងលើ font របស់ប្រព័ន្ធ
- លេខ៖ `NumberFormat` ជាមួយ locale `km` (០១២៣...)
- កាលបរិច្ឆេទ៖ `DateFormat.yMMMMd('km')`
- Plural និង gender តាមវេយ្យាករណ៍ខ្មែរ (ខ្មែរគ្មាន plural form ដូចអង់គ្លេស — សូមកត់ត្រាថាវាប៉ះពាល់ ARB យ៉ាងណា)
- ការកាត់បន្ទាត់ (line break) ខ្មែរខុសពីអង់គ្លេស — សាកល្បងអត្ថបទវែងក្នុងប្រអប់តូច

**បន្ថែម — Accessibility:**
- `Semantics` label សម្រាប់រូបភាព និងប៊ូតុងគ្រប់អាន
- Contrast ratio យ៉ាងតិច 4.5:1
- Tap target យ៉ាងតិច 48×48
- សាកល្បងជាមួយ TalkBack (Android) ឬ VoiceOver (iOS)

**✅ លក្ខខណ្ឌជោគជ័យ:** ប្ដូរភាសាក្នុងកម្មវិធី → អត្ថបទប្ដូរភ្លាមៗដោយមិនចាំបាច់បើកឡើងវិញ · គ្មានអក្សរខ្មែរណាមួយត្រូវកាត់ជើង។

---

### លំហាត់ ៤.៩ — សុវត្ថិភាព
**កម្រិត:** ⭐⭐⭐ · **ពេលវេលា:** ~45 នាទី

**📋 ការងារ:** ធ្វើ audit លើកម្មវិធីរបស់អ្នក៖

1. **Secret** — រកមើលថាមាន API key ណាមួយនៅក្នុងកូដទេ? (ចម្លើយ៖ គ្រប់ secret នៅក្នុង APK **អាចដកយកបាន** — `unzip app.apk && strings`)
2. **Storage** — token នៅក្នុង `SharedPreferences` (មិនអ៊ិនគ្រីប) ឬ `flutter_secure_storage` (Keystore/Keychain)?
3. **Certificate pinning** — អនុវត្តជាមួយ Dio
4. **Obfuscation** — `flutter build apk --obfuscate --split-debug-info=build/symbols`
5. **Root/jailbreak detection** — តើគួរធ្វើទេ? ពិភាក្សាគុណវិបត្តិ
6. **Screenshot ការពារ** — `FLAG_SECURE` លើទំព័រដែលមានទិន្នន័យរសើប
7. **Deep link validation** — កុំទុកចិត្ត parameter ពី deep link ដោយងងឹតងងុល

**✅ លក្ខខណ្ឌជោគជ័យ:** អ្នកសរសេរបញ្ជីរកឃើញ (findings) ដែលមានកម្រិតគ្រោះថ្នាក់ និងដំណោះស្រាយសម្រាប់នីមួយៗ — ដូចរបាយការណ៍ audit ពិត។

---

## ✅ ចម្លើយសង្ខេប — ផ្នែកទី ៤

### ៤.១ Jank — លំដាប់នៃផលប៉ះពាល់

តាមបទពិសោធន៍ ការកែដែលជួយបានច្រើនតាមលំដាប់៖

| # | បញ្ហា | ការកែ | ផលប៉ះពាល់ |
|---|---|---|---|
| 1 | គណនាធ្ងន់ក្នុង `build()` | ផ្លាស់ទៅ `initState` ឬ memoize | 🔥🔥🔥 |
| 2 | រូបភាពពេញទំហំដើម | `cacheWidth: (w * dpr).round()` ឬ `ResizeImage` | 🔥🔥🔥 |
| 3 | `Opacity` widget | `Color.withValues(alpha:)` ឬ `AnimatedOpacity` តែពេលចាំបាច់ | 🔥🔥 |
| 4 | Parent rebuild ទាំង list | បំបែក widget + `const` + `ListView.builder` | 🔥🔥 |
| 5 | `BoxShadow` blur ធំ | បន្ថយ `blurRadius` ឬប្រើ `RepaintBoundary` | 🔥 |

```dart
// រូបភាព
Image.network(url, cacheWidth: (120 * MediaQuery.devicePixelRatioOf(context)).round())

// Opacity — កុំប្រើ widget នៅពេលអាចប្រើ color
Container(color: Colors.black.withValues(alpha: 0.5))   // ថោក
Opacity(opacity: 0.5, child: ...)                        // ថ្លៃ — បង្កើត layer ថ្មី
```

> 🔑 `Opacity` ថ្លៃព្រោះវាបង្ខំ `saveLayer()` នៅ GPU។ ដូចគ្នាដែរជាមួយ `ClipRRect` លើ list ធំ — សូមប្រើ `BorderRadius` លើ `Container` decoration វិញ។

---

### ៤.២ Animation

```dart
class _CardExpandState extends State<CardExpand>
    with SingleTickerProviderStateMixin {          // 🔑 ticker តែ ១
  late final AnimationController _c = AnimationController(
    vsync: this, duration: const Duration(milliseconds: 600));

  late final _image = CurvedAnimation(
      parent: _c, curve: const Interval(0.0, 0.4, curve: Curves.easeOut));
  late final _title = CurvedAnimation(
      parent: _c, curve: const Interval(0.2, 0.6, curve: Curves.easeOut));
  late final _body = CurvedAnimation(
      parent: _c, curve: const Interval(0.4, 1.0, curve: Curves.easeOut));

  @override
  void dispose() { _c.dispose(); super.dispose(); }

  @override
  Widget build(BuildContext context) {
    // គោរព accessibility
    if (MediaQuery.disableAnimationsOf(context)) _c.value = 1.0;

    return AnimatedBuilder(
      animation: _c,
      child: const HeavySubtree(),        // 🔑 build តែម្ដង
      builder: (context, child) => Column(children: [
        FadeTransition(opacity: _image, child: const CardImage()),
        FadeTransition(opacity: _title, child: const CardTitle()),
        FadeTransition(opacity: _body, child: child),
      ]),
    );
  }
}
```

**`SingleTickerProviderStateMixin` ធៀបនឹង `TickerProviderStateMixin`:** ទី ១ ផ្ដល់ ticker តែមួយ ហើយមាន assertion ដែលចាប់កំហុសបើអ្នកបង្កើត controller ទី ២។ ទី ២ ផ្ដល់ច្រើន តែថ្លៃជាងបន្តិច។ ប្រើទី ១ ជាលំនាំដើម — assertion នោះជាមិត្តរបស់អ្នក។

---

### ៤.៣ CustomPainter — ចំណុចសំខាន់

```dart
class LineChartPainter extends CustomPainter {
  LineChartPainter(this.values, this.progress);
  final List<double> values;
  final double progress;

  @override
  void paint(Canvas canvas, Size size) {
    final path = Path();
    // ... cubicTo សម្រាប់ខ្សែកោង smooth
    canvas.drawPath(path, Paint()..style = PaintingStyle.stroke..strokeWidth = 2);

    // អក្សរខ្មែរក្នុង canvas
    final tp = TextPainter(
      text: const TextSpan(
        text: 'ខែមករា',
        style: TextStyle(fontSize: 12, height: 1.7),   // 🔑 height សម្រាប់ខ្មែរ
      ),
      textDirection: TextDirection.ltr,
    )..layout();
    tp.paint(canvas, Offset(0, size.height - tp.height));
  }

  @override
  bool shouldRepaint(covariant LineChartPainter old) =>
      old.progress != progress || !listEquals(old.values, values);   // 🔑
}
```

`shouldRepaint` ដែលត្រឡប់ `true` ជានិច្ច = repaint រាល់ frame = jank។ នេះជាកំហុសទី ១ របស់អ្នកប្រើ `CustomPainter`។

---

### ៤.៤ Testing — អន្ទាក់ដែលត្រូវដឹង

```dart
// ❌ timeout ព្រោះ CircularProgressIndicator មិនចេះឈប់
await tester.pumpAndSettle();

// ✅ pump តាមចំនួនកំណត់
await tester.pump();                                  // ចាប់ផ្ដើម
await tester.pump(const Duration(milliseconds: 350)); // ឆ្លងកាត់ debounce
```

```dart
// Mock Dio
final dio = Dio(BaseOptions(baseUrl: 'http://test'));
final adapter = DioAdapter(dio: dio);
adapter.onGet('/books', (s) => s.reply(200, {'items': []}),
    queryParameters: {'q': 'flutter'});

// Golden
testWidgets('book card golden', (t) async {
  await t.pumpWidget(wrap(const BookCard(book: sample)));
  await expectLater(find.byType(BookCard),
      matchesGoldenFile('goldens/book_card.png'));
});
```

**រចនាសម្ព័ន្ធពីរ៉ាមីត:** unit ច្រើន (លឿន, ថោក) → widget មធ្យម → integration តិច (យឺត, ផុយ)។ បើអ្នកមាន integration test ៥០ ហើយ unit test ៥ — អ្នកបានធ្វើផ្ទុយ។

---

### ៤.៥ RenderObject — គ្រោង

```dart
class RenderStaggeredGrid extends RenderBox
    with ContainerRenderObjectMixin<RenderBox, StaggeredParentData>,
         RenderBoxContainerDefaultsMixin<RenderBox, StaggeredParentData> {
  RenderStaggeredGrid({required int columns, required double gap})
      : _columns = columns, _gap = gap;

  int _columns; double _gap;

  @override
  void setupParentData(RenderBox child) {
    if (child.parentData is! StaggeredParentData) {
      child.parentData = StaggeredParentData();
    }
  }

  @override
  void performLayout() {
    final colWidth = (constraints.maxWidth - _gap * (_columns - 1)) / _columns;
    final heights = List<double>.filled(_columns, 0);

    var child = firstChild;
    while (child != null) {
      // constraints ចុះទៅ៖ ទទឹងតឹង កម្ពស់សេរី
      child.layout(BoxConstraints.tightFor(width: colWidth), parentUsesSize: true);

      final col = _shortestColumn(heights);
      final pd = child.parentData! as StaggeredParentData;
      pd.offset = Offset(col * (colWidth + _gap), heights[col]);   // parent កំណត់ទីតាំង

      heights[col] += child.size.height + _gap;                    // sizes ឡើងលើ
      child = pd.nextSibling;
    }

    size = constraints.constrain(
        Size(constraints.maxWidth, heights.reduce(math.max)));
  }

  int _shortestColumn(List<double> h) {
    var best = 0;
    for (var i = 1; i < h.length; i++) { if (h[i] < h[best]) best = i; }
    return best;
  }

  @override
  void paint(PaintingContext context, Offset offset) =>
      defaultPaint(context, offset);

  @override
  bool hitTestChildren(BoxHitTestResult result, {required Offset position}) =>
      defaultHitTestChildren(result, position: position);
}

class StaggeredParentData extends ContainerBoxParentData<RenderBox> {}
```

កូដនេះជាការអនុវត្តផ្ទាល់នៃច្បាប់មាសពីលំហាត់ ២.៤៖ **constraints ចុះទៅ → sizes ឡើងលើ → parent កំណត់ទីតាំង**។

---

### ៤.៦ Pigeon

```bash
dart run pigeon --input pigeons/device_api.dart \
  --dart_out lib/generated/device_api.g.dart \
  --kotlin_out android/app/src/main/kotlin/kh/study/app/DeviceApi.kt \
  --swift_out ios/Runner/DeviceApi.swift
```

Kotlin៖

```kotlin
class DeviceApiImpl(private val context: Context) : DeviceApi {
    override fun getBatteryLevel(): Long {
        val bm = context.getSystemService(Context.BATTERY_SERVICE) as BatteryManager
        return bm.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY).toLong()
    }
    override fun getDeviceModel(callback: (Result<String>) -> Unit) {
        callback(Result.success("${Build.MANUFACTURER} ${Build.MODEL}"))
    }
}
// នៅ MainActivity.configureFlutterEngine:
DeviceApi.setUp(flutterEngine.dartExecutor.binaryMessenger, DeviceApiImpl(this))
```

**Pigeon ធៀបនឹង MethodChannel:** ជាមួយ `MethodChannel` អ្នកសរសេរ `'getBatteryLevel'` ជា string ទាំងសងខាង។ សរសេរខុសមួយអក្សរ → បរាជ័យនៅ **runtime** ។ Pigeon បង្កើតកូដទាំងសងខាងពី schema តែមួយ → សរសេរខុស = **build មិនចេញ**។

---

### ៤.៧ Flavors និង CI

`android/app/build.gradle.kts`៖

```kotlin
flavorDimensions += "env"
productFlavors {
    create("dev") {
        dimension = "env"
        applicationIdSuffix = ".dev"
        resValue("string", "app_name", "MyApp Dev")
    }
    create("staging") {
        dimension = "env"
        applicationIdSuffix = ".stg"
        resValue("string", "app_name", "MyApp Staging")
    }
    create("prod") { dimension = "env"; resValue("string", "app_name", "MyApp") }
}
```

```yaml
name: CI
on: [pull_request, push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: subosito/flutter-action@v2
        with: { flutter-version: '3.47.x', channel: stable, cache: true }
      - run: flutter pub get
      - run: dart format --set-exit-if-changed .
      - run: flutter analyze --fatal-infos
      - run: flutter test --coverage
      - if: github.ref == 'refs/heads/main'
        run: flutter build apk --flavor staging -t lib/main_staging.dart
              --dart-define=API_BASE_URL=${{ secrets.STAGING_URL }}
      - if: github.ref == 'refs/heads/main'
        uses: actions/upload-artifact@v4
        with: { name: staging-apk, path: build/app/outputs/flutter-apk/*.apk }
```

> 🔑 `--fatal-infos` គឺជាបន្ទាត់ដែលធ្វើឲ្យ CI តឹងរឹងពិត។ បើគ្មានវា — warning កកកុញរហូតដល់គ្មាននរណាមើលទៀត។

---

### ៤.៨ i18n និងអក្សរខ្មែរ

```dart
MaterialApp(
  localizationsDelegates: AppLocalizations.localizationsDelegates,
  supportedLocales: AppLocalizations.supportedLocales,
  locale: settings.locale,                    // ប្ដូរបានក្នុងកម្មវិធី
  theme: ThemeData(
    fontFamily: 'NotoSansKhmer',
    textTheme: Typography.blackMountainView.apply(
      fontFamily: 'NotoSansKhmer',
      // 🔑 សំខាន់បំផុតសម្រាប់ខ្មែរ
    ).merge(const TextTheme(
      bodyMedium: TextStyle(height: 1.7),
      bodyLarge: TextStyle(height: 1.7),
      titleMedium: TextStyle(height: 1.7),
    )),
  ),
);
```

```json
// lib/l10n/app_km.arb
{
  "@@locale": "km",
  "bookCount": "{count} ក្បាល",
  "@bookCount": {
    "placeholders": { "count": { "type": "int", "format": "compact" } }
  }
}
```

**ចំណាំវេយ្យាករណ៍ខ្មែរ:** ខ្មែរគ្មានទម្រង់ពហុវចនៈផ្លាស់ប្ដូរដូចអង់គ្លេស (`1 book` / `2 books`)។ ដូច្នេះនៅក្នុង ARB អ្នក **មិនចាំបាច់** `plural` syntax សម្រាប់ `km` — គ្រាន់តែ `{count} ក្បាល` គ្រប់គ្រាន់។ តែ `app_en.arb` ត្រូវការ៖

```json
"bookCount": "{count, plural, =0{No books} =1{1 book} other{{count} books}}"
```

**លេខខ្មែរ:**
```dart
NumberFormat.decimalPattern('km').format(1234);       // ១,២៣៤
DateFormat.yMMMMd('km').format(DateTime.now());       // ០៨ កញ្ញា ២០២៦
```

Accessibility៖
```dart
Semantics(
  label: 'សៀវភៅ ${book.title} ដោយ ${book.author.name}',
  button: true,
  child: BookCard(book: book),
)
```

---

### ៤.៩ សុវត្ថិភាព — បញ្ជីរកឃើញគំរូ

| # | រកឃើញ | កម្រិត | ដំណោះស្រាយ |
|---|---|---|---|
| 1 | API key នៅក្នុងកូដ Dart | 🔴 ខ្ពស់ | ផ្លាស់ទៅ backend proxy។ **គ្មានវិធីលាក់ secret ក្នុង client** — `--dart-define` ក៏ដកចេញបានដែរ |
| 2 | Token ក្នុង `SharedPreferences` | 🔴 ខ្ពស់ | ប្ដូរទៅ `flutter_secure_storage` (Android Keystore / iOS Keychain) |
| 3 | គ្មាន certificate pinning | 🟡 មធ្យម | `badCertificateCallback` ពិនិត្យ SHA-256 fingerprint |
| 4 | គ្មាន obfuscation | 🟡 មធ្យម | `--obfuscate --split-debug-info=` (រក្សា symbol ទុកសម្រាប់ crash report) |
| 5 | Deep link parameter មិន validate | 🟡 មធ្យម | Validate `id` format មុនហៅ API |
| 6 | Screenshot នៅទំព័រ token | 🟢 ទាប | `FLAG_SECURE` លើ Android |

**Root detection:** ត្រូវដឹងថាវា **អាច bypass បាន** ជានិច្ច (Magisk Hide ជាដើម)។ វាបង្កើនតម្លៃឲ្យអ្នកវាយប្រហារ តែមិនបញ្ឈប់គេ។ ហើយវាធ្វើឲ្យអ្នកប្រើស្មោះត្រង់ដែល root ទូរស័ព្ទខ្លួន (developer, power user) ខឹង។ សម្រាប់ banking — សមហេតុផល។ សម្រាប់កម្មវិធីធម្មតា — ជាទូទៅមិនគួរទេ។

> 🔑 គោលការណ៍មេ៖ **កុំទុកចិត្ត client ទាល់តែសោះ។** រាល់ការត្រួតពិនិត្យសិទ្ធិត្រូវធ្វើនៅ server។ អ្វីដែលធ្វើនៅ client គឺជា UX មិនមែនសុវត្ថិភាព។

---

# 🏗️ ផ្នែកទី ៥ — គម្រោងចុងក្រោយ: សាងសង់កម្មវិធីពិត

> បន្ទាប់ពីលំហាត់ ៤៥ ខាងលើ អ្នកមានគ្រឿងបន្លាស់គ្រប់គ្រាន់ហើយ។ ផ្នែកនេះមិនមែនជាលំហាត់ទេ — វាជា **spec** ។ សរសេរវាដូចអ្នកសរសេរកម្មវិធីឲ្យអតិថិជន។

---

## 📱 គម្រោង: **BookNest** — កម្មវិធីខ្ចី/តាមដានសៀវភៅ

ខ្ញុំជ្រើសរើសគម្រោងនេះព្រោះវាមានគ្រប់ធាតុពិបាកទាំងអស់ (auth, list, search, offline, form, media, notification) ដោយមិនទាមទារ domain knowledge ស្មុគស្មាញ។ **បើអ្នកមានគំនិតផ្សេង — ប្រើគំនិតរបស់អ្នកទៅ។** អ្វីដែលសំខាន់គឺ *រចនាសម្ព័ន្ធនៃ milestone* មិនមែនប្រធានបទទេ។

### មុខងារ

| # | មុខងារ | ប្រើលំហាត់ណា |
|---|---|---|
| 1 | ចុះឈ្មោះ / ចូល / logout | ៣.១, ៣.៥, ៣.៧, ៣.៨ |
| 2 | បញ្ជីសៀវភៅ + ស្វែងរក (debounced) | ១.៦, ២.៦, ៣.២ |
| 3 | ទំព័រលម្អិត + Hero animation | ២.៣, ៤.២ |
| 4 | ខ្ចី / សង + ប្រវត្តិ | ៣.៣, ៣.៤ |
| 5 | ដំណើរការ offline (អានបាន) | ៣.៧ |
| 6 | ស្ថិតិផ្ទាល់ខ្លួន (chart) | ៤.៣ |
| 7 | ភាសាខ្មែរ/អង់គ្លេស + dark mode | ២.៧, ៤.៨ |
| 8 | ថតរូបសៀវភៅ (camera) | ថ្មី — `image_picker` |
| 9 | រំលឹកថ្ងៃត្រូវសង (notification) | ថ្មី — `flutter_local_notifications` |

---

## 🗺️ ផែនទីធ្វើការ ៦ សប្ដាហ៍

### សប្ដាហ៍ ១ — គ្រឹះ
- [ ] រៀបចំរចនាសម្ព័ន្ធ folder (`lib/data`, `lib/domain`, `lib/ui`, `lib/routing`)
- [ ] Flavors ៣ (លំហាត់ ៤.៧)
- [ ] Theme + font ខ្មែរ + `height: 1.7` (លំហាត់ ៤.៨)
- [ ] Router គ្រោង + ទំព័រទទេទាំងអស់ (លំហាត់ ៣.១)
- [ ] CI workflow ដំណើរការតាំងពីថ្ងៃដំបូង (លំហាត់ ៤.៧)

> 🔑 ដាក់ CI នៅសប្ដាហ៍ទី ១ មិនមែនសប្ដាហ៍ចុងក្រោយ។ នេះជាភាពខុសគ្នាធំបំផុតរវាងគម្រោងសិស្ស និងគម្រោងវិជ្ជាជីវៈ។

### សប្ដាហ៍ ២ — Data layer
- [ ] Model ទាំងអស់ជាមួយ freezed (លំហាត់ ៣.៦)
- [ ] `Result<T>` (លំហាត់ ៣.៤)
- [ ] Dio + interceptor ពេញលេញ (លំហាត់ ៣.៥)
- [ ] `AuthRepository` + secure storage (លំហាត់ ៣.៧)
- [ ] Local DB (`drift` ឬ `sqflite`) សម្រាប់ cache
- [ ] Unit test សម្រាប់ layer នេះ — **មុន** ចាប់ផ្ដើម UI

### សប្ដាហ៍ ៣ — Auth flow ពេញ
- [ ] ទំព័រ login/register (លំហាត់ ៣.៨)
- [ ] Splash + token restore
- [ ] Redirect guard + `from=` (លំហាត់ ៣.១)
- [ ] Token refresh ដំណើរការពិត — សាកល្បងដោយធ្វើឲ្យ token ផុតកំណត់ក្នុង ៦០ វិនាទី
- [ ] Widget test សម្រាប់ flow នេះ

### សប្ដាហ៍ ៤ — មុខងារស្នូល
- [ ] បញ្ជី + ស្វែងរក + pagination (infinite scroll)
- [ ] ទំព័រលម្អិត + Hero
- [ ] ខ្ចី/សង ជាមួយ Command pattern (លំហាត់ ៣.៣)
- [ ] Optimistic update៖ UI ប្ដូរភ្លាមៗ រួច rollback បើ API បរាជ័យ
- [ ] Offline banner + cache-first

### សប្ដាហ៍ ៥ — ការបញ្ចប់
- [ ] Chart ស្ថិតិ (លំហាត់ ៤.៣)
- [ ] កាមេរ៉ា + upload រូបភាព (multipart + progress)
- [ ] Notification រំលឹក
- [ ] Animation និង transition ទាំងអស់ (លំហាត់ ៤.២)
- [ ] Empty state, error state, loading skeleton សម្រាប់ **គ្រប់ទំព័រ**

### សប្ដាហ៍ ៦ — Production
- [ ] Performance profiling (លំហាត់ ៤.១) — គោលដៅ ៦០fps គ្រប់ទំព័រ
- [ ] Golden test + integration test (លំហាត់ ៤.៤)
- [ ] Security audit (លំហាត់ ៤.៩)
- [ ] Accessibility pass (លំហាត់ ៤.៨)
- [ ] Crash reporting (Sentry ឬ Firebase Crashlytics)
- [ ] Build release + `--obfuscate` + ចែកចាយ

---

## 🔌 Backend

មានជម្រើស ៣៖

| ជម្រើស | ពេលវេលា | សមរម្យពេលណា |
|---|---|---|
| `json-server` ឬ Mockoon | ២ ម៉ោង | ចង់ផ្ដោតលើ Flutter ១០០% |
| Supabase / Firebase | ១ ថ្ងៃ | ចង់បាន auth + DB + storage ភ្លាមៗ |
| **Spring Boot ដោយខ្លួនឯង** | ១ សប្ដាហ៍ | ចង់បានគម្រោង full-stack ក្នុង portfolio |

បើអ្នកជ្រើសរើស Spring Boot — API contract ដែលត្រូវការ៖

```
POST   /api/auth/register       → { access_token, refresh_token, user }
POST   /api/auth/login
POST   /api/auth/refresh
GET    /api/books?q=&page=&size=  → { items: [], total, page }
GET    /api/books/{id}
POST   /api/loans               → { book_id }
PATCH  /api/loans/{id}/return
GET    /api/loans/me?status=
POST   /api/books/{id}/cover    → multipart
GET    /api/stats/me            → { borrowed_per_month: [...] }
```

រៀបចំ `springdoc-openapi` នៅខាង Spring Boot រួច generate Dart client ដោយ `openapi_generator` — អ្នកនឹងសន្សំពេលបានច្រើន ហើយ model ទាំងសងខាងនឹងមិនខុសគ្នាឡើយ។

---

## ✅ លក្ខខណ្ឌ "រួចរាល់" (Definition of Done)

កម្មវិធីរបស់អ្នករួចរាល់នៅពេល៖

- [ ] `flutter analyze --fatal-infos` ស្អាត ១០០%
- [ ] Test coverage > 70% លើ `data` និង `domain`
- [ ] គ្រប់ទំព័រមាន loading / empty / error state
- [ ] ដំណើរការបានពេលគ្មាន internet (អានពី cache)
- [ ] ដំណើរការបានលើអេក្រង់ 320px ដល់ tablet
- [ ] គ្មានអក្សរខ្មែរណាមួយត្រូវកាត់ជើង
- [ ] Cold start < 2 វិនាទី
- [ ] គ្មាន `print()` នៅក្នុងកូដ production (ប្រើ `logger` វិញ)
- [ ] README មានរូបភាព, របៀប build, និងស្ថាបត្យកម្មពន្យល់

---

## 🎯 អនុសាសន៍ចុងក្រោយ

**កុំធ្វើលំហាត់ទាំង ៤៥ ឲ្យអស់មុនចាប់ផ្ដើមកម្មវិធី។** នេះជាអន្ទាក់ធម្មតា។

ផ្លូវដែលខ្ញុំណែនាំ៖

1. **ផ្នែកទី ១ + ២ ឲ្យអស់** (សប្ដាហ៍ ១–២) — នេះជាគ្រឹះដែលមិនអាចរំលងបាន
2. **ផ្នែកទី ៣ លំហាត់ ៣.១, ៣.៣, ៣.៤, ៣.៥, ៣.៦** (សប្ដាហ៍ ៣)
3. **ចាប់ផ្ដើមកម្មវិធីភ្លាម** — ត្រឡប់មករៀនលំហាត់ផ្នែកទី ៤ **នៅពេលអ្នកជួបបញ្ហានោះពិត**

ចំណេះដឹងដែលរៀនពេលអ្នកកំពុងជាប់បញ្ហាពិត នៅជាប់យូរជាងចំណេះដឹងដែលរៀនទុកមុន ១០ ដង។

---

*សរសេរសម្រាប់ Flutter 3.47 / Dart 3.12+ · កញ្ញា ២០២៦*
