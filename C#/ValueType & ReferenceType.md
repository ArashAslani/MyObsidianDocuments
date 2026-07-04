# Value Type و Reference Type در C#

نوع داده‌ها، اجزای اصلی سازنده یک زبان برنامه‌نویسی هستند و شبیه قواعد هر زبانی عمل می‌کنند. درک تفاوت بین Value Type و Reference Type یکی از **مهم‌ترین مفاهیم** در C# است که در مصاحبه‌ها بسیار پرسیده می‌شود.

---

## فهرست مطالب

1. [مقدمه: Unified Type System](#مقدمه-unified-type-system)
2. [Strong Typing در C#](#strong-typing-در-c)
3. [Value Type (انواع مقداری)](#value-type-انواع-مقداری)
4. [Reference Type (انواع ارجاعی)](#reference-type-انواع-ارجاعی)
5. [Stack vs Heap Memory](#stack-vs-heap-memory)
6. [Nullable Types](#nullable-types)
7. [مقایسه و تفاوت‌ها](#مقایسه-و-تفاوت‌ها)
8. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: Unified Type System

در .NET، **همه نوع داده‌ها** (Data Type) به صورت مستقیم یا غیرمستقیم از کلاس `System.Object` مشتق شده‌اند. این موضوع **Unified Type System** نام دارد.

```csharp
int number = 10;
string text = "Hello";

// همه از Object ارث‌بری می‌کنند
object obj1 = number; // Boxing
object obj2 = text;
```

**مزایای Unified Type System**:
- همه نوع‌ها می‌توانند به `object` تبدیل شوند
- Reflection روی همه نوع‌ها کار می‌کند
- Collections می‌توانند هر نوعی را نگه دارند

---

## Strong Typing در C#

C# یک زبان **Strongly Typed** است (البته با در نظر نگرفتن نوع `dynamic`). این یعنی:

1. **تعریف اجباری**: کلیه متغیرها قبل از استفاده باید تعریف و مقداردهی شوند
2. **عدم تغییر نوع**: بعد از تعریف متغیر، نمی‌توان نوع آن را تغییر داد
3. **رفتار مشخص**: رفتار یک متغیر بر اساس نوع انتخابی مشخص می‌شود

### مثال

```csharp
int number = 10; // ✅ تعریف و مقداردهی
// number = "Hello"; // ❌ Compile Error - Type Mismatch

// برای تغییر رفتار، باید تبدیل کنیم
string text = number.ToString(); // تبدیل int به string
```

**نکته**: با انتخاب نوع `int` تنها می‌توان اعداد صحیح را ذخیره کرد. برای تغییر رفتار متغیرها باید آن‌ها را تبدیل کنیم.

![[Pasted image 20230918200023.png]]

## Value Type (انواع مقداری)

در C# دو مدل نوع داده وجود دارد: **Value Type** و **Reference Type**.

### ویژگی‌های Value Type

1. **ذخیره مستقیم داده**: انواع مقداری مستقیماً حاوی داده‌ها هستند
2. **کپی مقدار**: اگر یک متغیر از نوع مقداری را به متغیر دیگر تخصیص دهید، **مقدار مستقیماً کپی می‌شود**
3. **ارث‌بری از ValueType**: کلیه نوع‌های مقداری از کلاس `ValueType` مشتق شده‌اند
4. **ذخیره در Stack**: در فضای **Stack** به آن‌ها حافظه تخصیص داده می‌شود
5. **عدم پذیرش null**: نمی‌توانند مقدار `null` بپذیرند (مگر با Nullable Type)
6. **سازنده پیش‌فرض**: همه نوع‌های مقداری یک سازنده پیش‌فرض دارند که به صورت ضمنی مقداردهی اولیه انجام می‌دهد

### مثال: کپی مقدار

```csharp
int a = 10;
int b = a; // مقدار a کپی می‌شود

b = 20; // تغییر b تأثیری روی a ندارد
Console.WriteLine(a); // 10
Console.WriteLine(b); // 20
```

**نکته مهم**: در Value Type، هر متغیر **کپی مستقل** از داده دارد.  
  
### دسته‌بندی Value Type

انواع مقداری به دو دسته اصلی تقسیم می‌شوند:

#### 1. Structs

**Numeric Types**:
- **Integral Types**: `sbyte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`
- **Floating-Point Types**: `float`, `double`
- **Decimal**: `decimal`

**Bool**: دو مقدار `true` و `false`

**User Defined Struct**: Struct‌های تعریف شده توسط کاربر

```csharp
// مثال User Defined Struct
public struct Point
{
    public int X { get; set; }
    public int Y { get; set; }
}

Point p1 = new Point { X = 10, Y = 20 };
Point p2 = p1; // کپی می‌شود
p2.X = 30; // p1.X همچنان 10 است
```

#### 2. Enumerations

```csharp
public enum Status
{
    Pending,
    Approved,
    Rejected
}

Status status = Status.Pending;
```

### مقادیر پیش‌فرض

همه نوع‌های مقداری یک مقدار پیش‌فرض دارند:

```csharp
int number; // 0
bool flag; // false
char ch; // '\0'
Point p; // تمام فیلدها مقدار پیش‌فرض دارند
```

برای مطالعه بیشتر درباره مقادیر پیش‌فرض به [مستندات Microsoft](https://msdn.microsoft.com/en-us/library/83fhsxwc.aspx) مراجعه کنید.
 
## Nullable Types

در ابتدای معرفی نوع داده‌های مقداری گفتیم که همیشه باید وضعیت متغیر مشخص و مقداردهی اولیه آن (به صورت ضمنی یا آشکار) انجام شود. هیچ یک از نوع داده‌های مقداری نمی‌توانند به صورت `null` تعریف شوند.

### تعریف Nullable Type

برای تبدیل یک نوع داده مقداری به صورتی که قابلیت ذخیره مقدار `null` را داشته باشد، بعد از نوشتن نوع داده، علامت سوال `?` قرار می‌دهیم:

```csharp
// Syntax
<data type>? <variable name> = null;

// مثال‌ها
int? nullableInt = null;
bool? nullableBool = null;
DateTime? nullableDate = null;

// یا به صورت کامل
Nullable<int> nullableInt2 = null;
```

### استفاده از Nullable Types

```csharp
int? number = null;

if (number.HasValue)
{
    Console.WriteLine($"Value: {number.Value}");
}
else
{
    Console.WriteLine("Number is null");
}

// یا با استفاده از Null Coalescing Operator
int result = number ?? 0; // اگر null باشد، 0 برگردان
```

### نکات مهم

- **`var` نمی‌تواند به صورت Nullable تعریف شود**:
  ```csharp
  // ❌ این کار نمی‌کند
  var? nullableVar = null;
  
  // ✅ باید نوع را مشخص کنیم
  int? nullableInt = null;
  ```

- Nullable Type در واقع یک `Nullable<T>` است که یک Struct است
- Nullable Types خودشان Value Type هستند اما می‌توانند `null` باشند

## Reference Type (انواع ارجاعی)

### ویژگی‌های Reference Type

1. **ارجاع به آدرس**: انواع ارجاعی مستقیماً حاوی اطلاعات نیستند و **ارجاعی** هستند به آدرسی از حافظه که حاوی اطلاعات واقعی است
2. **اشاره‌گر**: به بیانی دیگر، **اشاره‌گری** به آدرسی از حافظه هستند
3. **ذخیره غیرمستقیم**: انواع ارجاعی به صورت غیرمستقیم حاوی داده‌ها هستند
4. **ذخیره در Heap**: در بخشی از حافظه که به آن **Heap** می‌گوییم، به آن‌ها فضا اختصاص داده می‌شود
5. **قبول null**: می‌توانند به صورت `null` (بدون مقدار) باشند

### دسته‌بندی Reference Type

انواع ارجاعی به دو دسته کلی تقسیم می‌شوند:

#### 1. انواع از پیش تعریف شده

- **Object**: پایه همه نوع‌ها
- **String**: رشته‌های متنی (Immutable)
- **Dynamic**: نوع پویا (در Runtime بررسی می‌شود)

#### 2. انواع تعریف شده توسط کاربر

- **Class**: کلاس‌های تعریف شده توسط کاربر
- **Interface**: رابط‌ها
- **Delegate**: نمایندگان

### مثال: Reference Type

```csharp
// Reference در Stack، Object در Heap
MyClass obj; // allocating reference on stack
obj = new MyClass(55); // allocating object on heap
```

### نکته مهم: اشتراک Reference

**دو متغیر از نوع ارجاعی می‌توانند به یک آدرس از حافظه اشاره کنند:**

```csharp
MyClass obj1 = new MyClass { Value = 10 };
MyClass obj2 = obj1; // Reference کپی می‌شود، نه Object

obj2.Value = 20; // تغییر obj2 تأثیر روی obj1 دارد!
Console.WriteLine(obj1.Value); // 20
Console.WriteLine(obj2.Value); // 20
```

**تفاوت کلیدی**: در Reference Type، **Reference کپی می‌شود**، نه خود Object. بنابراین هر دو متغیر به همان Object اشاره می‌کنند.

## مقایسه و تفاوت‌ها

### جدول مقایسه

| ویژگی | Value Type | Reference Type |
|---|---|---|
| **ذخیره داده** | مستقیم | غیرمستقیم (Reference) |
| **مکان ذخیره** | Stack | Heap (Object) + Stack (Reference) |
| **کپی** | کپی مقدار | کپی Reference |
| **Null** | خیر (مگر Nullable) | بله |
| **Default Value** | دارد | null |
| **Performance** | سریع‌تر | کندتر (GC Overhead) |
| **Memory** | کم | بیشتر |

### مثال: تفاوت در کپی

```csharp
// Value Type - کپی مقدار
int a = 10;
int b = a;
b = 20;
Console.WriteLine(a); // 10 - تغییر نکرد

// Reference Type - کپی Reference
MyClass obj1 = new MyClass { Value = 10 };
MyClass obj2 = obj1;
obj2.Value = 20;
Console.WriteLine(obj1.Value); // 20 - تغییر کرد!
```

### Boxing و Unboxing

```csharp
// Boxing: Value Type به Reference Type
int number = 10;
object obj = number; // Boxing - در Heap کپی می‌شود

// Unboxing: Reference Type به Value Type
int unboxed = (int)obj; // Unboxing - از Heap کپی می‌شود
```

**نکته**: Boxing و Unboxing Performance Overhead دارند و باید از آن‌ها پرهیز کرد.

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت Value Type و Reference Type چیست؟

**پاسخ**:
- Value Type مستقیماً داده را ذخیره می‌کند و در Stack قرار دارد
- Reference Type Reference به داده را ذخیره می‌کند (Reference در Stack، Object در Heap)
- کپی Value Type، مقدار را کپی می‌کند
- کپی Reference Type، Reference را کپی می‌کند

#### 2. String یک Value Type است یا Reference Type؟

**پاسخ**: String یک **Reference Type** است اما رفتارش شبیه Value Type است چون **Immutable** است:

```csharp
string str1 = "Hello";
string str2 = str1;
str2 = "World"; // str1 تغییر نمی‌کند چون String Immutable است
Console.WriteLine(str1); // "Hello"
```

#### 3. Struct vs Class

**پاسخ**:
- Struct: Value Type، در Stack، سریع‌تر، برای داده‌های کوچک
- Class: Reference Type، در Heap، کندتر، برای داده‌های پیچیده

#### 4. چرا String Immutable است؟

**پاسخ**: 
- Thread Safety
- Security (مثلاً در Connection Strings)
- Performance (String Interning)
- Predictable Behavior

#### 5. Boxing و Unboxing چیست؟

**پاسخ**: 
- Boxing: تبدیل Value Type به Reference Type (Object)
- Unboxing: تبدیل Reference Type به Value Type
- Performance Overhead دارد

### نکات مهم برای مصاحبه

1. **همیشه مثال بزنید**: با مثال کد توضیح دهید
2. **Stack vs Heap**: تفاوت را بدانید
3. **Nullability**: Nullable Types را توضیح دهید
4. **Performance**: Trade-off بین Value و Reference Type را بدانید
5. **String**: String Immutable است اما Reference Type است

---

## خلاصه

- **Value Type**: داده مستقیم، Stack، کپی مقدار
- **Reference Type**: Reference به داده، Heap، کپی Reference
- **Stack**: سریع، خودکار، برای Value Types
- **Heap**: کندتر، توسط GC، برای Objects
- **Nullable**: امکان null برای Value Types
- **String**: Reference Type اما Immutable

**موفق باشید!** 🚀