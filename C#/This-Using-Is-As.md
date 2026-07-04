# this, using, is, as در C#

این مقاله به بررسی چهار مفهوم مهم در C# می‌پردازد: `this`, `using`, `is` و `as` - مفاهیمی که در مصاحبه‌ها بسیار پرسیده می‌شوند.

---

## فهرست مطالب

1. [this Keyword](#this-keyword)
2. [using (دو مفهوم)](#using-دو-مفهوم)
3. [is Operator](#is-operator)
4. [as Operator](#as-operator)
5. [مقایسه is vs as](#مقایسه-is-vs-as)
6. [نکات مصاحبه](#نکات-مصاحبه)

---

## this Keyword

### تعریف

**`this` اشاره مستقیمی به محتویات نمونه (Instance) که از شیء ساخته شده دارد.**

`this` یک Reference به Instance فعلی کلاس است.

### کاربردها

#### 1. دسترسی به Members

```csharp
public class Person
{
    private string _name;
    
    public void SetName(string name)
    {
        this._name = name; // this برای تمایز بین Parameter و Field
    }
}
```

#### 2. تمایز بین Parameter و Field

```csharp
public class Person
{
    private string name;
    
    public Person(string name)
    {
        this.name = name; // this.name = Field, name = Parameter
    }
}
```

#### 3. Pass Instance به Method دیگر

```csharp
public class Calculator
{
    public void Calculate(Calculator other)
    {
        // Pass this instance
        Process(this);
    }
}
```

#### 4. Constructor Chaining

```csharp
public class Person
{
    private string _name;
    private int _age;
    
    public Person(string name) : this(name, 0) // Call other constructor
    {
    }
    
    public Person(string name, int age)
    {
        this._name = name;
        this._age = age;
    }
}
```

#### 5. Extension Methods

```csharp
public static class StringExtensions
{
    public static bool IsEmpty(this string str) // this برای Extension Method
    {
        return string.IsNullOrEmpty(str);
    }
}

// استفاده
string text = "Hello";
bool isEmpty = text.IsEmpty(); // Extension Method
```

### نکات مهم

- `this` فقط در Instance Methods قابل استفاده است
- در Static Methods نمی‌توان از `this` استفاده کرد
- `this` اختیاری است (مگر در موارد خاص مثل Constructor Chaining)

---

## using (دو مفهوم)

### 1. using Directive (Namespace)

**`using` برای اضافه کردن Namespace‌ها استفاده می‌شود:**

```csharp
// using Directive
using System.IO;
using System.Text;
using System.Collections.Generic;

// حالا می‌توانیم بدون Namespace کامل استفاده کنیم
var file = new FileStream("data.txt", FileMode.Open);
var builder = new StringBuilder();
var list = new List<int>();
```

**بدون using:**
```csharp
// بدون using - باید Namespace کامل را بنویسیم
var file = new System.IO.FileStream("data.txt", System.IO.FileMode.Open);
var builder = new System.Text.StringBuilder();
```

### 2. using Statement (Resource Management)

**`using` برای Dispose کردن ارتباط و فضای RAM استفاده می‌شود:**

```csharp
// using Statement - برای IDisposable
using (var file = new FileStream("data.txt", FileMode.Open))
{
    // استفاده از file
} // به صورت خودکار Dispose می‌شود

// یا با using Declaration (C# 8.0+)
using var file = new FileStream("data.txt", FileMode.Open);
// استفاده از file
// در انتهای Scope به صورت خودکار Dispose می‌شود
```

### مثال: Database Connection

```csharp
// ✅ با using Statement
using (var connection = new SqlConnection(connectionString))
{
    connection.Open();
    // استفاده از connection
} // Connection به صورت خودکار Close و Dispose می‌شود

// ✅ با using Declaration (C# 8.0+)
using var connection = new SqlConnection(connectionString);
connection.Open();
// استفاده از connection
// در انتهای Scope Dispose می‌شود
```

### مزایای using Statement

1. **Automatic Dispose**: به صورت خودکار Dispose می‌شود
2. **Exception Safe**: حتی در صورت Exception، Dispose می‌شود
3. **Cleaner Code**: کد تمیزتر و کوتاه‌تر
4. **Less Error-Prone**: احتمال فراموش کردن Dispose کمتر است

### تفاوت using Directive vs Statement

| ویژگی | using Directive | using Statement |
|---|---|---|
| **هدف** | اضافه کردن Namespace | مدیریت منابع |
| **Syntax** | `using System.IO;` | `using (var obj = ...)` |
| **Scope** | فایل | Block |
| **Dispose** | ندارد | دارد |

---

## is Operator

### تعریف

**`is` برای مقایسه Type‌ها استفاده می‌شود:**

`is` بررسی می‌کند که آیا یک Object از نوع خاصی است یا نه.

### Syntax

```csharp
if (obj is Type)
{
    // obj از نوع Type است
}
```

### مثال‌ها

```csharp
object obj = "Hello";

// ✅ بررسی نوع
if (obj is string)
{
    Console.WriteLine("obj is a string");
}

// ✅ Pattern Matching (C# 7.0+)
if (obj is string str)
{
    Console.WriteLine($"String value: {str}"); // str به صورت خودکار cast می‌شود
}

// ✅ با null check
if (obj is string str2 && str2.Length > 0)
{
    Console.WriteLine($"Non-empty string: {str2}");
}
```

### کاربردها

#### 1. Type Checking

```csharp
public void Process(object obj)
{
    if (obj is int number)
    {
        Console.WriteLine($"Integer: {number}");
    }
    else if (obj is string text)
    {
        Console.WriteLine($"String: {text}");
    }
    else if (obj is Person person)
    {
        Console.WriteLine($"Person: {person.Name}");
    }
}
```

#### 2. Pattern Matching

```csharp
// ✅ Pattern Matching با is
if (shape is Circle circle)
{
    double area = Math.PI * circle.Radius * circle.Radius;
}
else if (shape is Rectangle rect)
{
    double area = rect.Width * rect.Height;
}
```

#### 3. Null Checking

```csharp
// ✅ بررسی null و type
if (obj is string str && str.Length > 0)
{
    // obj null نیست، string است و طول آن بیشتر از 0 است
}
```

### ویژگی‌ها

- **Type-Safe**: Type-Safe است
- **Returns bool**: true یا false برمی‌گرداند
- **Pattern Matching**: از C# 7.0+ Pattern Matching پشتیبانی می‌کند

---

## as Operator

### تعریف

**`as` برای Cast کردن استفاده می‌شود:**

`as` سعی می‌کند Object را به نوع خاصی Cast کند. اگر موفق نشود، `null` برمی‌گرداند.

### Syntax

```csharp
Type result = obj as Type;
```

### مثال‌ها

```csharp
object obj = "Hello";

// ✅ Cast با as
string str = obj as string;
if (str != null)
{
    Console.WriteLine($"String: {str}");
}

// ✅ اگر Cast موفق نشود، null برمی‌گرداند
object obj2 = 10;
string str2 = obj2 as string; // null - چون 10 string نیست
if (str2 == null)
{
    Console.WriteLine("Cast failed");
}
```

### کاربردها

#### 1. Safe Casting

```csharp
public void Process(object obj)
{
    // ✅ Safe Casting با as
    Person person = obj as Person;
    if (person != null)
    {
        Console.WriteLine($"Person: {person.Name}");
    }
    else
    {
        Console.WriteLine("Object is not a Person");
    }
}
```

#### 2. با Null-Coalescing Operator

```csharp
// ✅ با ?? operator
Person person = obj as Person ?? new Person("Default");
```

#### 3. با Null-Conditional Operator

```csharp
// ✅ با ?. operator
Person person = obj as Person;
string name = person?.Name ?? "Unknown";
```

### ویژگی‌ها

- **Safe Casting**: Exception نمی‌دهد
- **Returns null**: اگر Cast موفق نشود، null برمی‌گرداند
- **Reference Types Only**: فقط برای Reference Types کار می‌کند
- **Nullable Types**: برای Nullable Types هم کار می‌کند

### محدودیت‌ها

```csharp
// ❌ برای Value Types کار نمی‌کند
int number = 10;
// long result = number as long; // Compile Error

// ✅ باید از Explicit Cast استفاده کنیم
long result = (long)number;
```

---

## مقایسه is vs as

### جدول مقایسه

| ویژگی | is | as |
|---|---|---|
| **نوع بازگشتی** | bool | Type یا null |
| **Exception** | نمی‌دهد | نمی‌دهد |
| **Pattern Matching** | بله (C# 7.0+) | خیر |
| **Value Types** | بله | خیر |
| **کاربرد** | Type Checking | Safe Casting |

### مثال: تفاوت در استفاده

```csharp
object obj = "Hello";

// ✅ با is - Type Checking
if (obj is string)
{
    string str = (string)obj; // باید Explicit Cast کنیم
    Console.WriteLine(str);
}

// ✅ با is + Pattern Matching (بهتر)
if (obj is string str2)
{
    Console.WriteLine(str2); // به صورت خودکار cast شده
}

// ✅ با as - Safe Casting
string str3 = obj as string;
if (str3 != null)
{
    Console.WriteLine(str3);
}
```

### چه زمانی از کدام استفاده کنیم؟

**از `is` استفاده کنیم وقتی:**
- فقط می‌خواهیم Type را بررسی کنیم
- Pattern Matching می‌خواهیم
- با Value Types کار می‌کنیم

**از `as` استفاده کنیم وقتی:**
- می‌خواهیم Safe Cast کنیم
- می‌خواهیم null check کنیم
- فقط با Reference Types کار می‌کنیم

### Best Practice

```csharp
// ✅ بهتر - Pattern Matching با is
if (obj is Person person)
{
    Console.WriteLine(person.Name);
}

// ⚠️ قابل قبول - اما Pattern Matching بهتر است
Person person = obj as Person;
if (person != null)
{
    Console.WriteLine(person.Name);
}
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. this چیست و چه کاربردهایی دارد؟

**پاسخ**:
- `this` Reference به Instance فعلی کلاس است
- برای تمایز بین Parameter و Field
- برای Constructor Chaining
- برای Extension Methods

#### 2. تفاوت using Directive و using Statement چیست؟

**پاسخ**:
- **using Directive**: برای اضافه کردن Namespace (`using System.IO;`)
- **using Statement**: برای مدیریت منابع (`using (var file = ...)`)
- **using Declaration**: برای مدیریت منابع (C# 8.0+)

#### 3. تفاوت is و as چیست؟

**پاسخ**:
- `is`: Type Checking، bool برمی‌گرداند، Pattern Matching دارد
- `as`: Safe Casting، Type یا null برمی‌گرداند، Pattern Matching ندارد
- `is` برای Type Checking، `as` برای Safe Casting

#### 4. چه زمانی از is استفاده می‌کنیم؟

**پاسخ**:
- وقتی فقط می‌خواهیم Type را بررسی کنیم
- وقتی Pattern Matching می‌خواهیم
- وقتی با Value Types کار می‌کنیم

#### 5. چه زمانی از as استفاده می‌کنیم؟

**پاسخ**:
- وقتی می‌خواهیم Safe Cast کنیم
- وقتی می‌خواهیم null check کنیم
- فقط با Reference Types

#### 6. آیا as برای Value Types کار می‌کند؟

**پاسخ**:
- خیر، `as` فقط برای Reference Types و Nullable Types کار می‌کند
- برای Value Types باید از Explicit Cast استفاده کنیم

### نکات مهم برای مصاحبه

1. **this**: Reference به Instance فعلی
2. **using**: دو مفهوم (Directive و Statement)
3. **is**: Type Checking با Pattern Matching
4. **as**: Safe Casting
5. **Best Practice**: Pattern Matching با `is` بهتر از `as` است

---

## خلاصه

### this
- Reference به Instance فعلی
- برای تمایز Parameter و Field
- برای Constructor Chaining
- برای Extension Methods

### using
- **Directive**: اضافه کردن Namespace
- **Statement**: مدیریت منابع (Dispose)
- **Declaration**: مدیریت منابع (C# 8.0+)

### is
- Type Checking
- Pattern Matching (C# 7.0+)
- Returns bool
- برای Value Types هم کار می‌کند

### as
- Safe Casting
- Returns Type یا null
- فقط برای Reference Types
- Exception نمی‌دهد

**موفق باشید!** 🚀