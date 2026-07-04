# var, dynamic, const, readonly در C#

این مقاله به بررسی تفاوت‌های `var`, `dynamic`, `const` و `readonly` در C# می‌پردازد - مفاهیمی که در مصاحبه‌ها بسیار پرسیده می‌شوند.

---

## فهرست مطالب

1. [const (ثابت Compile-Time)](#const-ثابت-compile-time)
2. [readonly (ثابت Run-Time)](#readonly-ثابت-run-time)
3. [مقایسه const vs readonly](#مقایسه-const-vs-readonly)
4. [var (Type Inference)](#var-type-inference)
5. [dynamic (Dynamic Typing)](#dynamic-dynamic-typing)
6. [مقایسه var vs dynamic](#مقایسه-var-vs-dynamic)
7. [نکات مصاحبه](#نکات-مصاحبه)

---

## const (ثابت Compile-Time)

### تعریف

`const` یک **ثابت Compile-Time** است که:
- **باید** در زمان تعریف مقداردهی شود
- مقدار آن در **Compile-Time** مشخص می‌شود
- نمی‌توان بعد از تعریف تغییر داد
- باید یک **مقدار Literal** باشد

### مثال

```csharp
// ✅ صحیح
const int MaxUsers = 100;
const string AppName = "MyApp";
const double Pi = 3.14159;

// ❌ خطا - باید در زمان تعریف مقداردهی شود
// const int Value; // Compile Error

// ❌ خطا - نمی‌توان از متغیر استفاده کرد
int x = 10;
// const int Value = x; // Compile Error - باید Literal باشد

// ❌ خطا - نمی‌توان تغییر داد
// MaxUsers = 200; // Compile Error
```

### ویژگی‌ها

1. **Compile-Time Constant**: مقدار در Compile-Time مشخص می‌شود
2. **Implicitly Static**: به صورت خودکار Static است
3. **Value Type Only**: فقط برای Value Types (و string) قابل استفاده است
4. **Inlined**: در Compile-Time جایگزین می‌شود

### کاربردها

```csharp
public class Calculator
{
    private const double Pi = 3.14159;
    private const int MaxRetries = 3;
    
    public double CalculateArea(double radius)
    {
        return Pi * radius * radius; // Pi در Compile-Time جایگزین می‌شود
    }
}
```

---

## readonly (ثابت Run-Time)

### تعریف

`readonly` یک **ثابت Run-Time** است که:
- می‌تواند در زمان تعریف مقداردهی شود
- می‌تواند در **Constructor** مقداردهی شود
- حتی می‌تواند مقدار نداشته باشد (اگر در Constructor مقداردهی شود)
- مقدار آن در **Run-Time** مشخص می‌شود

### مثال

```csharp
public class MyClass
{
    // مقداردهی در زمان تعریف
    private readonly int Value1 = 10;
    
    // مقداردهی در Constructor
    private readonly int Value2;
    
    // مقداردهی نشده (اگر در Constructor مقداردهی شود، OK است)
    private readonly string Name;
    
    public MyClass(int value2, string name)
    {
        Value2 = value2; // ✅ می‌توان در Constructor مقداردهی کرد
        Name = name;
    }
    
    // ❌ خطا - نمی‌توان بعد از Constructor تغییر داد
    public void ChangeValue()
    {
        // Value2 = 20; // Compile Error
    }
}
```

### ویژگی‌ها

1. **Run-Time Constant**: مقدار در Run-Time مشخص می‌شود
2. **Instance or Static**: می‌تواند Instance یا Static باشد
3. **Reference Types**: می‌تواند Reference Type باشد
4. **Constructor Assignment**: می‌توان در Constructor مقداردهی کرد

### کاربردها

```csharp
public class Configuration
{
    private readonly string _connectionString;
    private readonly int _timeout;
    
    public Configuration(string connectionString, int timeout)
    {
        _connectionString = connectionString; // از Configuration File می‌آید
        _timeout = timeout; // از Environment Variable می‌آید
    }
    
    // بعد از Constructor نمی‌توان تغییر داد
}
```

---

## مقایسه const vs readonly

### جدول مقایسه

| ویژگی | const | readonly |
|---|---|---|
| **زمان مقداردهی** | فقط در زمان تعریف | در تعریف یا Constructor |
| **زمان تعیین مقدار** | Compile-Time | Run-Time |
| **Static** | بله (Implicitly) | می‌تواند Instance یا Static باشد |
| **Reference Types** | فقط string | همه نوع‌ها |
| **Performance** | بهتر (Inlined) | کمی کندتر |
| **مقدار از متغیر** | خیر (فقط Literal) | بله |

### مثال: تفاوت در استفاده

```csharp
public class Constants
{
    // const - Compile-Time
    private const int MaxUsers = 100; // Literal
    private const string AppName = "MyApp"; // Literal
    
    // readonly - Run-Time
    private readonly string _connectionString;
    private readonly int _timeout;
    
    public Constants(string connectionString, int timeout)
    {
        // const نمی‌تواند از متغیر استفاده کند
        // const int value = timeout; // ❌ Error
        
        // readonly می‌تواند از متغیر استفاده کند
        _connectionString = connectionString; // ✅ OK
        _timeout = timeout; // ✅ OK
    }
}
```

### چه زمانی از کدام استفاده کنیم؟

**از `const` استفاده کنیم وقتی:**
- مقدار در Compile-Time مشخص است
- مقدار Literal است
- نیاز به Performance بالا داریم
- مثال: `const double Pi = 3.14159;`

**از `readonly` استفاده کنیم وقتی:**
- مقدار در Run-Time مشخص می‌شود
- مقدار از Configuration یا Constructor می‌آید
- نیاز به Reference Types داریم
- مثال: `readonly string _connectionString;`

---

## var (Type Inference)

### تعریف

`var` یک **Type Inference** است که:
- نوع متغیر در **Compile-Time** استنتاج می‌شود
- به آن **Anonymous Variable** هم می‌گویند
- باید در زمان تعریف مقداردهی شود
- نوع آن **قابل تغییر نیست** بعد از تعریف

### مثال

```csharp
// ✅ صحیح - نوع از مقدار استنتاج می‌شود
var number = 10; // int
var text = "Hello"; // string
var list = new List<int>(); // List<int>

// ❌ خطا - باید در زمان تعریف مقداردهی شود
// var value; // Compile Error

// ❌ خطا - نمی‌توان نوع را تغییر داد
// var number = 10;
// number = "Hello"; // Compile Error - number از نوع int است
```

### ویژگی‌ها

1. **Compile-Time Type Inference**: نوع در Compile-Time مشخص می‌شود
2. **Strongly Typed**: همچنان Strongly Typed است
3. **Must Initialize**: باید در زمان تعریف مقداردهی شود
4. **Cannot be Nullable**: نمی‌توان به صورت Nullable تعریف کرد

### کاربردها

```csharp
// ✅ خوب - برای Long Type Names
var dictionary = new Dictionary<string, List<int>>();
// بهتر از:
Dictionary<string, List<int>> dictionary = new Dictionary<string, List<int>>();

// ✅ خوب - برای LINQ
var result = from x in numbers
             where x > 10
             select x * 2;

// ⚠️ استفاده نکنید - وقتی نوع واضح است
var number = 10; // بهتر است: int number = 10;
var name = "Ali"; // بهتر است: string name = "Ali";
```

### نکات مهم

```csharp
// ❌ نمی‌توان Nullable باشد
// var? nullable = null; // Compile Error

// ✅ اما می‌توان Nullable Type را استنتاج کرد
var nullableInt = (int?)null; // int?
```

---

## dynamic (Dynamic Typing)

### تعریف

`dynamic` یک **Dynamic Typing** است که:
- نوع متغیر در **Run-Time** مشخص می‌شود
- نوع و مقدار آن **آخرین Type و مقداری** است که می‌گیرد
- Type Checking در **Run-Time** انجام می‌شود
- می‌تواند هر نوعی باشد

### مثال

```csharp
// ✅ صحیح - نوع در Run-Time مشخص می‌شود
dynamic value = 10; // در Run-Time: int
Console.WriteLine(value.GetType()); // System.Int32

value = "Hello"; // در Run-Time: string
Console.WriteLine(value.GetType()); // System.String

value = new List<int>(); // در Run-Time: List<int>
Console.WriteLine(value.GetType()); // System.Collections.Generic.List`1[System.Int32]

// ⚠️ خطا در Run-Time - Type Checking در Run-Time است
dynamic number = 10;
// number.NonExistentMethod(); // Runtime Error - MethodNotFoundException
```

### ویژگی‌ها

1. **Run-Time Type**: نوع در Run-Time مشخص می‌شود
2. **Type Can Change**: می‌تواند نوع را تغییر دهد
3. **No Compile-Time Checking**: Type Checking در Compile-Time انجام نمی‌شود
4. **Performance Overhead**: Overhead دارد (Reflection)

### کاربردها

```csharp
// ✅ کار با COM Objects
dynamic excelApp = Activator.CreateInstance(Type.GetTypeFromProgID("Excel.Application"));

// ✅ کار با JSON (مثل Newtonsoft.Json)
dynamic json = JsonConvert.DeserializeObject(jsonString);
string name = json.Name; // Type Checking در Run-Time

// ✅ کار با DLR (Dynamic Language Runtime)
// برای کار با Python، Ruby و ...
```

### نکات مهم

```csharp
// ⚠️ خطا در Run-Time
dynamic value = 10;
value.SomeMethod(); // اگر Method وجود نداشته باشد، Runtime Error

// ✅ استفاده از Try-Catch
try
{
    dynamic obj = GetObject();
    obj.SomeMethod();
}
catch (RuntimeBinderException ex)
{
    // Handle error
}
```

---

## مقایسه var vs dynamic

### جدول مقایسه

| ویژگی | var | dynamic |
|---|---|---|
| **زمان Type Checking** | Compile-Time | Run-Time |
| **Type Inference** | بله (Compile-Time) | خیر (Run-Time) |
| **Strongly Typed** | بله | خیر |
| **Performance** | بدون Overhead | با Overhead (Reflection) |
| **IntelliSense** | بله | خیر |
| **تغییر نوع** | خیر | بله |

### مثال: تفاوت در استفاده

```csharp
// var - Compile-Time
var number = 10; // Compile-Time: int
// number = "Hello"; // ❌ Compile Error

// dynamic - Run-Time
dynamic value = 10; // Run-Time: int
value = "Hello"; // ✅ OK - در Run-Time: string
value = new List<int>(); // ✅ OK - در Run-Time: List<int>
```

### مثال: تفاوت در Type Checking

```csharp
// var - Compile-Time Error
var list = new List<int>();
// list.Add("Hello"); // ❌ Compile Error - IntelliSense می‌گوید

// dynamic - Run-Time Error
dynamic list2 = new List<int>();
list2.Add("Hello"); // ⚠️ Compile OK اما Runtime Error
```

### چه زمانی از کدام استفاده کنیم؟

**از `var` استفاده کنیم وقتی:**
- نوع از Context واضح است
- Type Name طولانی است
- با LINQ کار می‌کنیم
- **همیشه**: وقتی نوع واضح است

**از `dynamic` استفاده کنیم وقتی:**
- با COM Objects کار می‌کنیم
- با JSON/Dynamic Objects کار می‌کنیم
- با DLR کار می‌کنیم
- **به ندرت**: فقط در موارد خاص

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت const و readonly چیست؟

**پاسخ**:
- `const`: Compile-Time Constant، باید Literal باشد، فقط در تعریف
- `readonly`: Run-Time Constant، می‌تواند از متغیر باشد، در Constructor
- `const` بهتر برای Performance، `readonly` انعطاف‌پذیرتر

#### 2. تفاوت var و dynamic چیست؟

**پاسخ**:
- `var`: Compile-Time Type Inference، Strongly Typed، بدون Overhead
- `dynamic`: Run-Time Type، Weakly Typed، با Overhead
- `var` برای Readability، `dynamic` برای Dynamic Scenarios

#### 3. چه زمانی از var استفاده می‌کنیم؟

**پاسخ**:
- وقتی Type Name طولانی است
- با LINQ
- وقتی نوع از Context واضح است
- **نکته**: همیشه وقتی نوع واضح است

#### 4. چه زمانی از dynamic استفاده می‌کنیم؟

**پاسخ**:
- COM Objects
- JSON/Dynamic Objects
- DLR
- **نکته**: به ندرت و فقط در موارد خاص

#### 5. آیا var می‌تواند Nullable باشد؟

**پاسخ**:
- خیر، `var?` نمی‌تواند باشد
- اما می‌تواند Nullable Type را استنتاج کند: `var nullable = (int?)null;`

### نکات مهم برای مصاحبه

1. **همیشه مثال بزنید**: با مثال کد توضیح دهید
2. **Compile-Time vs Run-Time**: تفاوت را بدانید
3. **Performance**: Trade-off را توضیح دهید
4. **Best Practices**: چه زمانی از کدام استفاده کنیم
5. **Type Safety**: تفاوت Strongly Typed و Weakly Typed

---

## خلاصه

### const
- Compile-Time Constant
- فقط Literal
- Performance بهتر
- برای مقادیر ثابت

### readonly
- Run-Time Constant
- می‌تواند از متغیر باشد
- در Constructor مقداردهی می‌شود
- برای Configuration

### var
- Compile-Time Type Inference
- Strongly Typed
- برای Readability
- بدون Overhead

### dynamic
- Run-Time Type
- Weakly Typed
- برای Dynamic Scenarios
- با Overhead

**موفق باشید!** 🚀