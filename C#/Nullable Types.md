# Nullable Types در C#

Nullable Types یکی از **مهم‌ترین مفاهیم** در C# است که به ما امکان می‌دهد Value Types را Nullable کنیم. درک این مفهوم برای نوشتن کد ایمن و جلوگیری از NullReferenceException ضروری است.

---

## فهرست مطالب

1. [مقدمه: Nullable چیست؟](#مقدمه-nullable-چیست)
2. [Nullable<T> Struct](#nullablet-struct)
3. [Syntax Sugar: ?](#syntax-sugar-)
4. [HasValue و Value](#hasvalue-و-value)
5. [Null Coalescing Operator (??)](#null-coalescing-operator-)
6. [Null-Conditional Operator (?.)](#null-conditional-operator-)
7. [Nullable Reference Types (C# 8.0+)](#nullable-reference-types-c-80)
8. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: Nullable چیست؟

### تعریف

**با استفاده از نوع Nullable می‌تواند هیچ مقداری نداشته باشد.**

### مشکل Value Types

**Value Types به صورت پیش‌فرض نمی‌توانند null باشند:**

```csharp
// ❌ خطا - Value Type نمی‌تواند null باشد
int number = null; // Compile Error

// ✅ با Nullable
int? number = null; // OK
```

### چرا Nullable؟

1. **Database**: فیلدهای Database ممکن است NULL باشند
2. **Optional Values**: مقادیر اختیاری
3. **API Responses**: ممکن است فیلدی وجود نداشته باشد
4. **Default Values**: تشخیص بین 0 و "مقدار تنظیم نشده"

---

## Nullable<T> Struct

### تعریف

**`Nullable<T>` به معنی این است که یک Primitive Type تبدیل به یک Struct ذخیره می‌شود.**

**که هنوز هم در Stack ذخیره می‌شود.**

### ساختار داخلی

```csharp
// Nullable<T> یک Struct است
public struct Nullable<T> where T : struct
{
    private bool hasValue;
    private T value;
    
    public bool HasValue => hasValue;
    public T Value => hasValue ? value : throw new InvalidOperationException();
    
    public T GetValueOrDefault() => hasValue ? value : default(T);
}
```

### نحوه کار

**این عکس نشان می‌دهد که این متغیر بعد Compile به Nullable Generic تبدیل می‌شود:**

```csharp
// Syntax Sugar
int? number = null;

// بعد از Compile تبدیل می‌شود به:
Nullable<int> number = null;
```

**و در حالت عادی به این شکل است:**

```csharp
// حالت عادی (Non-Nullable)
int number = 10;

// Nullable
int? nullableNumber = 10;
int? nullNumber = null;
```

### مثال

```csharp
// ✅ Nullable Types
int? age = null;
bool? isActive = null;
DateTime? birthDate = null;
decimal? price = null;

// همه این‌ها در Stack ذخیره می‌شوند (چون Struct هستند)
```

---

## Syntax Sugar: ?

### تعریف

**C# Syntax Sugar برای `Nullable<T>` استفاده از `?` است:**

```csharp
// این دو یکسان هستند:
int? number1 = null;
Nullable<int> number2 = null;
```

### انواع Nullable

```csharp
// Value Types می‌توانند Nullable باشند
int? number = null;
bool? flag = null;
DateTime? date = null;
decimal? price = null;
double? value = null;

// Reference Types به صورت پیش‌فرض Nullable هستند
string text = null; // OK - Reference Type
object obj = null; // OK - Reference Type
```

---

## HasValue و Value

### تعریف

**متغیرهای معمولی از ویژگی‌های Object بهره می‌برند. زمانی که یک متغیر Nullable می‌شود، از دو متد دیگر `HasValue` و `Value` هم بهره‌مند هستند.**

### HasValue

**`HasValue` بررسی می‌کند که آیا مقدار وجود دارد یا نه:**

```csharp
int? number = null;

if (number.HasValue)
{
    Console.WriteLine($"Number is: {number.Value}");
}
else
{
    Console.WriteLine("Number is null");
}
```

### Value

**`Value` مقدار را برمی‌گرداند (اگر null باشد، Exception می‌دهد):**

```csharp
int? number = 10;

// ✅ OK - HasValue = true
int value = number.Value; // 10

// ❌ Exception - HasValue = false
int? nullNumber = null;
int value2 = nullNumber.Value; // InvalidOperationException
```

### GetValueOrDefault

**`GetValueOrDefault` مقدار را برمی‌گرداند یا Default Value:**

```csharp
int? number = null;
int value = number.GetValueOrDefault(); // 0 (default int)

int? number2 = 10;
int value2 = number2.GetValueOrDefault(); // 10
```

### مثال عملی

```csharp
public int? GetAge(string userId)
{
    // ممکن است null برگرداند
    var user = GetUser(userId);
    return user?.Age; // Nullable int
}

// استفاده
int? age = GetAge("123");

if (age.HasValue)
{
    Console.WriteLine($"Age: {age.Value}");
}
else
{
    Console.WriteLine("Age not found");
}

// یا با GetValueOrDefault
int ageValue = age.GetValueOrDefault(0); // اگر null باشد، 0 برمی‌گرداند
```

---

## Null Coalescing Operator (??)

### تعریف

**Null Coalescing Operator (`??`) اگر مقدار null باشد، مقدار پیش‌فرض برمی‌گرداند:**

```csharp
int? number = null;
int value = number ?? 0; // اگر number null باشد، 0 برمی‌گرداند

int? number2 = 10;
int value2 = number2 ?? 0; // 10 برمی‌گرداند
```

### مثال‌های عملی

```csharp
// ✅ با Null Coalescing
int? age = GetAge(userId);
int ageValue = age ?? 0; // اگر null باشد، 0

// ✅ Chain کردن
int? value1 = null;
int? value2 = null;
int result = value1 ?? value2 ?? 10; // اولین مقدار non-null

// ✅ با Method
int? price = GetPrice();
decimal finalPrice = price ?? CalculateDefaultPrice();
```

---

## Null-Conditional Operator (?.)

### تعریف

**Null-Conditional Operator (`?.`) اگر Object null باشد، null برمی‌گرداند (بدون Exception):**

```csharp
// ❌ بدون Null-Conditional
string name = null;
int length = name.Length; // NullReferenceException

// ✅ با Null-Conditional
string name = null;
int? length = name?.Length; // null (بدون Exception)
```

### مثال‌های عملی

```csharp
// ✅ Chain کردن
Person person = GetPerson();
string city = person?.Address?.City; // اگر person یا Address null باشد، null

// ✅ با Method
string result = person?.GetName()?.ToUpper(); // اگر null باشد، null

// ✅ با Collection
List<int> numbers = null;
int? first = numbers?.FirstOrDefault(); // null (بدون Exception)

// ✅ با Index
int[] array = null;
int? value = array?[0]; // null (بدون Exception)
```

### ترکیب با Null Coalescing

```csharp
// ✅ ترکیب ?. و ??
Person person = GetPerson();
string city = person?.Address?.City ?? "Unknown"; // اگر null باشد، "Unknown"
```

---

## Nullable Reference Types (C# 8.0+)

### تعریف

**در C# 8.0+، می‌توانیم Reference Types را Non-Nullable کنیم:**

```csharp
// Enable Nullable Reference Types
#nullable enable

// Non-Nullable Reference Type
string name = null; // Warning - باید مقداردهی شود

// Nullable Reference Type
string? nullableName = null; // OK
```

### مثال

```csharp
#nullable enable

public class Person
{
    public string Name { get; set; } // Non-Nullable - Warning اگر null باشد
    public string? Email { get; set; } // Nullable - OK
}

// استفاده
Person person = new Person();
person.Name = null; // Warning
person.Email = null; // OK
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. Nullable Types چیست و چرا استفاده می‌کنیم؟

**پاسخ**:
- امکان null بودن برای Value Types
- برای Database Fields، Optional Values
- تشخیص بین 0 و "مقدار تنظیم نشده"
- **مثال**: `int? age = null;`

#### 2. Nullable<T> چگونه کار می‌کند؟

**پاسخ**:
- یک Struct است
- در Stack ذخیره می‌شود
- دو فیلد دارد: `hasValue` و `value`
- **Syntax**: `int?` = `Nullable<int>`

#### 3. تفاوت HasValue و Value چیست؟

**پاسخ**:
- **HasValue**: بررسی می‌کند آیا مقدار وجود دارد (bool)
- **Value**: مقدار را برمی‌گرداند (اگر null باشد، Exception)
- **Best Practice**: همیشه قبل از Value، HasValue را چک کنید

#### 4. Null Coalescing Operator (??) چیست؟

**پاسخ**:
- اگر مقدار null باشد، مقدار پیش‌فرض برمی‌گرداند
- **مثال**: `int value = number ?? 0;`
- می‌توان Chain کرد: `value1 ?? value2 ?? 0`

#### 5. Null-Conditional Operator (?.) چیست؟

**پاسخ**:
- اگر Object null باشد، null برمی‌گرداند (بدون Exception)
- **مثال**: `string city = person?.Address?.City;`
- می‌توان Chain کرد

#### 6. Nullable Reference Types چیست؟

**پاسخ**:
- در C# 8.0+ معرفی شد
- می‌توانیم Reference Types را Non-Nullable کنیم
- **Syntax**: `#nullable enable` و `string?`

### نکات مهم برای مصاحبه

1. **Nullable<T>**: یک Struct است، در Stack ذخیره می‌شود
2. **HasValue**: همیشه قبل از Value چک کنید
3. **??**: برای مقدار پیش‌فرض
4. **?.**: برای Safe Navigation
5. **Best Practice**: از Nullable برای Optional Values استفاده کنید

---

## خلاصه

### Nullable Types

- **تعریف**: امکان null بودن برای Value Types
- **Syntax**: `int?` = `Nullable<int>`
- **ذخیره**: در Stack (چون Struct است)

### HasValue و Value

- **HasValue**: بررسی وجود مقدار
- **Value**: دریافت مقدار (Exception اگر null)
- **GetValueOrDefault**: مقدار یا Default

### Operators

- **??**: Null Coalescing - مقدار پیش‌فرض
- **?.**: Null-Conditional - Safe Navigation

### Best Practices

- همیشه HasValue را چک کنید
- از ?? برای مقدار پیش‌فرض استفاده کنید
- از ?. برای Safe Navigation استفاده کنید

**موفق باشید!** 🚀
