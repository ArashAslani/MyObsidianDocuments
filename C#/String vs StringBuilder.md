# String vs StringBuilder در C#

دو مکانیسم برای ذخیره‌سازی متن‌ها در C# وجود دارند: `string` و `StringBuilder`. درک تفاوت بین این دو و دانستن چه زمانی از کدام استفاده کنیم، یکی از **مهم‌ترین مفاهیم** در C# است که در مصاحبه‌ها بسیار پرسیده می‌شود.

---

## فهرست مطالب

1. [String (Immutable)](#string-immutable)
2. [StringBuilder (Mutable)](#stringbuilder-mutable)
3. [مقایسه String vs StringBuilder](#مقایسه-string-vs-stringbuilder)
4. [چه زمانی از کدام استفاده کنیم؟](#چه-زمانی-از-کدام-استفاده-کنیم)
5. [Performance Comparison](#performance-comparison)
6. [نکات مصاحبه](#نکات-مصاحبه)

---

## String (Immutable)

### تعریف

**`string` ها Immutable هستند** - یعنی قابل تغییر نیستند.

### Immutability چیست؟

**Immutable به معنی این است که:**
- String‌ها قابل تغییر نیستند
- به ازای هر تغییر، نمونه قبلی پاک می‌شود
- یک نمونه جدید از آن در حافظه ایجاد می‌شود
- از نظر سرعت و حافظه بهینه نیستند (برای عملیات زیاد)

### مثال: Immutability

```csharp
string text = "Hello";
text += " World"; // یک String جدید ایجاد می‌شود
text += "!"; // یک String جدید دیگر ایجاد می‌شود

// در واقع:
// 1. "Hello" در حافظه
// 2. "Hello World" در حافظه (String جدید)
// 3. "Hello World!" در حافظه (String جدید دیگر)
// String قبلی‌ها در انتظار GC هستند
```

### مثال: Performance Issue

```csharp
// ❌ بد - برای عملیات زیاد
string result = "";
for (int i = 0; i < 10000; i++)
{
    result += i.ToString(); // هر بار یک String جدید ایجاد می‌شود!
}
// این کد کند است و حافظه زیادی مصرف می‌کند
```

### ویژگی‌های String

1. **Immutable**: قابل تغییر نیست
2. **Thread-Safe**: به دلیل Immutability، Thread-Safe است
3. **Value Equality**: می‌توان با `==` مقایسه کرد
4. **String Interning**: String‌های یکسان ممکن است به یک Reference اشاره کنند

---

## StringBuilder (Mutable)

### تعریف

**`StringBuilder` ها Mutable هستند** - یعنی می‌توانند تغییر کنند.

**`StringBuilder` در Namespace `System.Text` است** و مقداری که دارد را می‌تواند در همان حافظه قبلی تغییر دهد.

### Mutability چیست؟

**Mutable به معنی این است که:**
- StringBuilder‌ها قابل تغییر هستند
- تغییرات در همان حافظه قبلی انجام می‌شود
- نمونه جدید ایجاد نمی‌شود
- از نظر سرعت و حافظه بهینه هستند (برای عملیات زیاد)

### مثال: Mutability

```csharp
StringBuilder sb = new StringBuilder();
sb.Append("Hello"); // در همان حافظه اضافه می‌شود
sb.Append(" World"); // در همان حافظه اضافه می‌شود
sb.Append("!"); // در همان حافظه اضافه می‌شود

string result = sb.ToString(); // فقط یک بار String ایجاد می‌شود
```

### مثال: Performance

```csharp
// ✅ خوب - برای عملیات زیاد
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++)
{
    sb.Append(i.ToString()); // در همان حافظه اضافه می‌شود
}
string result = sb.ToString(); // فقط یک بار String ایجاد می‌شود
// این کد سریع است و حافظه کمتری مصرف می‌کند
```

### ویژگی‌های StringBuilder

1. **Mutable**: قابل تغییر است
2. **Performance**: برای عملیات زیاد سریع‌تر است
3. **Memory Efficient**: حافظه کمتری مصرف می‌کند
4. **Capacity**: می‌توان Capacity اولیه را تنظیم کرد

### متدهای مهم StringBuilder

```csharp
StringBuilder sb = new StringBuilder();

// Append: اضافه کردن به انتها
sb.Append("Hello");
sb.AppendLine("World"); // با New Line

// Insert: درج در موقعیت خاص
sb.Insert(5, " ");

// Remove: حذف
sb.Remove(0, 5);

// Replace: جایگزینی
sb.Replace("Hello", "Hi");

// Clear: پاک کردن
sb.Clear();

// ToString: تبدیل به String
string result = sb.ToString();
```

### تنظیم Capacity

```csharp
// ✅ تنظیم Capacity اولیه برای Performance بهتر
StringBuilder sb = new StringBuilder(1000); // Capacity اولیه 1000

// یا
StringBuilder sb2 = new StringBuilder("Initial", 1000); // با مقدار اولیه
```

---

## مقایسه String vs StringBuilder

### جدول مقایسه

| ویژگی | String | StringBuilder |
|---|---|---|
| **Mutability** | Immutable | Mutable |
| **Performance** | کند (برای عملیات زیاد) | سریع (برای عملیات زیاد) |
| **Memory** | بیشتر (String جدید برای هر تغییر) | کمتر (همان حافظه) |
| **Thread-Safe** | بله | خیر (نیاز به Synchronization) |
| **Syntax** | ساده‌تر | پیچیده‌تر |
| **Use Case** | عملیات کم | عملیات زیاد |

### مثال: تفاوت در Performance

```csharp
// ❌ String - کند
Stopwatch sw = Stopwatch.StartNew();
string result = "";
for (int i = 0; i < 100000; i++)
{
    result += i.ToString();
}
sw.Stop();
Console.WriteLine($"String: {sw.ElapsedMilliseconds}ms"); // ~5000ms

// ✅ StringBuilder - سریع
sw.Restart();
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 100000; i++)
{
    sb.Append(i.ToString());
}
string result2 = sb.ToString();
sw.Stop();
Console.WriteLine($"StringBuilder: {sw.ElapsedMilliseconds}ms"); // ~50ms
```

### مثال: تفاوت در Memory

```csharp
// String - حافظه بیشتر
string text = "Hello";
text += " World"; // String جدید: "Hello World"
text += "!"; // String جدید: "Hello World!"
// در حافظه: "Hello", "Hello World", "Hello World!" (3 String)

// StringBuilder - حافظه کمتر
StringBuilder sb = new StringBuilder();
sb.Append("Hello"); // همان حافظه
sb.Append(" World"); // همان حافظه
sb.Append("!"); // همان حافظه
string result = sb.ToString(); // فقط یک String
// در حافظه: StringBuilder buffer + یک String
```

---

## چه زمانی از کدام استفاده کنیم؟

### از String استفاده کنیم وقتی:

1. **عملیات کم**: تعداد عملیات String کم است
2. **Read-Only**: String فقط خوانده می‌شود
3. **Thread-Safe**: نیاز به Thread-Safety داریم
4. **سادگی**: کد ساده‌تر می‌خواهیم

```csharp
// ✅ مناسب برای String
string firstName = "Ali";
string lastName = "Bayat";
string fullName = firstName + " " + lastName; // عملیات کم
```

### از StringBuilder استفاده کنیم وقتی:

1. **عملیات زیاد**: تعداد عملیات String زیاد است (مثلاً در Loop)
2. **Concatenation مکرر**: Concatenation مکرر داریم
3. **Performance Critical**: Performance مهم است
4. **Memory Efficiency**: نیاز به Memory Efficiency داریم

```csharp
// ✅ مناسب برای StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++)
{
    sb.Append(i.ToString()); // عملیات زیاد
}
string result = sb.ToString();
```

### Rule of Thumb

**اگر بیش از 3-4 بار Concatenation دارید، از StringBuilder استفاده کنید.**

---

## Performance Comparison

### Benchmark Example

```csharp
// Test 1: String Concatenation
public string StringConcatenation(int iterations)
{
    string result = "";
    for (int i = 0; i < iterations; i++)
    {
        result += i.ToString();
    }
    return result;
}

// Test 2: StringBuilder
public string StringBuilderConcatenation(int iterations)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < iterations; i++)
    {
        sb.Append(i.ToString());
    }
    return sb.ToString();
}

// Results (approximate):
// 100 iterations: String ~1ms, StringBuilder ~0.1ms
// 1,000 iterations: String ~10ms, StringBuilder ~1ms
// 10,000 iterations: String ~500ms, StringBuilder ~10ms
// 100,000 iterations: String ~5000ms, StringBuilder ~50ms
```

### نکات Performance

1. **String Interning**: String‌های Literal ممکن است Intern شوند
2. **Capacity**: تنظیم Capacity اولیه StringBuilder می‌تواند Performance را بهبود دهد
3. **ToString()**: فقط یک بار در انتها ToString() را صدا بزنید

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت String و StringBuilder چیست؟

**پاسخ**:
- **String**: Immutable، برای عملیات کم، Thread-Safe
- **StringBuilder**: Mutable، برای عملیات زیاد، سریع‌تر
- **تفاوت اصلی**: Immutability و Performance

#### 2. چرا String Immutable است؟

**پاسخ**:
- **Thread-Safety**: Thread-Safe است بدون نیاز به Lock
- **Security**: امنیت بیشتر (مثلاً در Connection Strings)
- **String Interning**: امکان Interning
- **Predictable Behavior**: رفتار قابل پیش‌بینی

#### 3. چه زمانی از StringBuilder استفاده می‌کنیم؟

**پاسخ**:
- وقتی عملیات String زیاد است (مثلاً در Loop)
- وقتی Concatenation مکرر داریم
- وقتی Performance مهم است
- **Rule of Thumb**: بیش از 3-4 بار Concatenation

#### 4. آیا StringBuilder Thread-Safe است؟

**پاسخ**:
- خیر، StringBuilder Thread-Safe نیست
- اگر نیاز به Thread-Safety داریم، باید از Synchronization استفاده کنیم
- یا از String استفاده کنیم (که Immutable و Thread-Safe است)

#### 5. Performance تفاوت چقدر است؟

**پاسخ**:
- برای عملیات کم: تفاوت ناچیز
- برای عملیات زیاد (1000+): StringBuilder 10-100 برابر سریع‌تر
- **مثال**: 100,000 iteration: String ~5000ms, StringBuilder ~50ms

#### 6. آیا می‌توان Capacity StringBuilder را تنظیم کرد؟

**پاسخ**:
- بله، می‌توان Capacity اولیه را تنظیم کرد
- این می‌تواند Performance را بهبود دهد
- **مثال**: `new StringBuilder(1000)`

### نکات مهم برای مصاحبه

1. **Immutability**: String Immutable است، StringBuilder Mutable
2. **Performance**: برای عملیات زیاد، StringBuilder بهتر است
3. **Memory**: StringBuilder حافظه کمتری مصرف می‌کند
4. **Thread-Safety**: String Thread-Safe است، StringBuilder نیست
5. **Rule of Thumb**: بیش از 3-4 بار Concatenation → StringBuilder

---

## خلاصه

### String
- **Immutable**: قابل تغییر نیست
- **Thread-Safe**: Thread-Safe است
- **برای عملیات کم**: مناسب است
- **ساده‌تر**: Syntax ساده‌تر

### StringBuilder
- **Mutable**: قابل تغییر است
- **Performance**: برای عملیات زیاد سریع‌تر
- **Memory Efficient**: حافظه کمتری مصرف می‌کند
- **برای عملیات زیاد**: مناسب است

### Best Practice

- **عملیات کم (< 5)**: از String استفاده کنید
- **عملیات زیاد (> 5)**: از StringBuilder استفاده کنید
- **در Loop**: همیشه از StringBuilder استفاده کنید

**موفق باشید!** 🚀