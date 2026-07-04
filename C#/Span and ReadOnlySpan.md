# Span و ReadOnlySpan در C#

`Span<T>` و `ReadOnlySpan<T>` یکی از **مهم‌ترین مفاهیم پیشرفته** در C# هستند که برای Performance Optimization و کاهش Memory Allocation استفاده می‌شوند. درک این مفاهیم در مصاحبه‌های Senior بسیار مهم است.

---

## فهرست مطالب

1. [مقدمه: Span چیست؟](#مقدمه-span-چیست)
2. [Span<T>](#spant)
3. [ReadOnlySpan<T>](#readonlyspant)
4. [View روی Memory](#view-روی-memory)
5. [کاربردها و مزایا](#کاربردها-و-مزایا)
6. [محدودیت‌ها](#محدودیتها)
7. [Performance Comparison](#performance-comparison)
8. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: Span چیست؟

### تعریف

**Span یک View (نمای) روی Sequence از Data است:**

- **Struct**: `Span<T>` یک Struct است و در Stack ذخیره می‌شود
- **Reference**: می‌تواند Reference به Sequence از Data داشته باشد
- **Memory Types**: می‌تواند Reference به Managed یا Unmanaged Memory داشته باشد
- **View**: یک View است، نه کپی - داده واقعی برش نمی‌خورد

### ویژگی‌های کلیدی

1. **Stack Allocation**: Struct است و در Stack ذخیره می‌شود
2. **Zero Allocation**: Allocation جدید ایجاد نمی‌کند
3. **Memory Safe**: Memory-Safe است
4. **Contiguous**: داده‌ها باید پشت سر هم باشند (Contiguous)

---

## Span<T>

### تعریف

**`Span<T>` یک Struct است که به یک Reference اشاره می‌کند و طول مورد نظر ما را از آن Reference می‌گیرد.**

### ساختار داخلی

```csharp
// Span داخل خودش یک Struct است که:
// - یک Reference به Memory دارد
// - طول (Length) مورد نظر را نگه می‌دارد
// - در Stack ذخیره می‌شود
```

### مثال: استفاده از Span

```csharp
// ✅ ایجاد Span از Array
int[] array = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
Span<int> span = array; // View روی Array

// ✅ Slice - بدون Allocation جدید
Span<int> firstHalf = span.Slice(0, 5); // [1, 2, 3, 4, 5]
Span<int> secondHalf = span.Slice(5); // [6, 7, 8, 9, 10]

// تغییر در Span روی Array اصلی تأثیر می‌گذارد
firstHalf[0] = 100;
Console.WriteLine(array[0]); // 100 - تغییر کرده!
```

### مثال: Split بدون Allocation

**فرض کنیم یک Array داریم که می‌خواهیم آن را Split کنیم:**

```csharp
// ❌ روش قدیمی - Allocation جدید
int[] array = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
int[] firstHalf = new int[5]; // Allocation جدید در Heap
Array.Copy(array, 0, firstHalf, 0, 5);

// ✅ روش جدید - با Span (بدون Allocation)
int[] array = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
Span<int> span = array;
Span<int> firstHalf = span.Slice(0, 5); // View - بدون Allocation
```

**در اصل داده به صورت واقعی برش نمی‌خورد - فقط یک View ایجاد می‌شود.**

---

## ReadOnlySpan<T>

### تعریف

**`ReadOnlySpan<T>` نسخه Read-Only از `Span<T>` است:**

- **Read-Only**: نمی‌تواند داده را تغییر دهد
- **Performance**: همان Performance `Span<T>` را دارد
- **Safety**: برای داده‌های Immutable مناسب است

### مثال

```csharp
int[] array = { 1, 2, 3, 4, 5 };
ReadOnlySpan<int> readOnlySpan = array;

// ✅ می‌تواند بخواند
int first = readOnlySpan[0]; // 1

// ❌ نمی‌تواند تغییر دهد
// readOnlySpan[0] = 100; // Compile Error
```

### کاربرد

```csharp
// ✅ برای String Operations
string text = "Hello World";
ReadOnlySpan<char> span = text.AsSpan();
ReadOnlySpan<char> hello = span.Slice(0, 5); // "Hello" - بدون Allocation
```

---

## View روی Memory

### Overlap و تغییرات

**در یک سناریو اگر View‌های ما با هم روی Array مورد نظر Overlap داشته باشند، تغییرات به آخرین Update که انجام شده می‌مانند:**

```csharp
int[] array = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
Span<int> span = array;

// Overlapping Views
Span<int> view1 = span.Slice(0, 5); // [1, 2, 3, 4, 5]
Span<int> view2 = span.Slice(3, 5); // [4, 5, 6, 7, 8] - Overlap با view1

// تغییر در view1
view1[3] = 100; // array[3] = 100
Console.WriteLine(array[3]); // 100
Console.WriteLine(view2[0]); // 100 - تغییر کرده چون Overlap دارد

// تغییر در view2
view2[0] = 200; // array[3] = 200
Console.WriteLine(array[3]); // 200
Console.WriteLine(view1[3]); // 200 - تغییر کرده چون Overlap دارد
```

**نکته مهم**: چون View‌ها به همان Memory اشاره می‌کنند، تغییرات در همه View‌ها منعکس می‌شود.

### Contiguous Memory

**داده‌ای که Span به آن اشاره می‌کند باید پشت سر هم باشند (Contiguous):**

```csharp
// ✅ Contiguous - Array
int[] array = { 1, 2, 3, 4, 5 };
Span<int> span = array; // OK

// ✅ Contiguous - Stackalloc
Span<int> stackSpan = stackalloc int[10]; // OK

// ❌ Non-Contiguous - List
List<int> list = new List<int> { 1, 2, 3 };
// Span<int> span = list; // Compile Error - List Contiguous نیست
```

---

## کاربردها و مزایا

### 1. کاهش Memory Allocation

**با استفاده از Span می‌توانیم یک View از مکانی که به Array اشاره می‌کند در Stack ایجاد کنیم:**

```csharp
// ❌ بدون Span - Allocation جدید
string text = "Hello,World,Test";
string[] parts = text.Split(','); // Allocation جدید برای هر String

// ✅ با Span - بدون Allocation
string text = "Hello,World,Test";
ReadOnlySpan<char> span = text.AsSpan();
// می‌توانیم بدون Split کار کنیم
```

### 2. String Operations

```csharp
// ✅ Substring با Span (بدون Allocation)
string text = "Hello World";
ReadOnlySpan<char> span = text.AsSpan();
ReadOnlySpan<char> hello = span.Slice(0, 5); // "Hello" - View

// ❌ Substring قدیمی (با Allocation)
string hello = text.Substring(0, 5); // Allocation جدید
```

### 3. Array Operations

```csharp
// ✅ Slice Array بدون Allocation
int[] array = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
Span<int> span = array;
Span<int> first = span.Slice(0, 5); // View - بدون Allocation
Span<int> second = span.Slice(5); // View - بدون Allocation
```

### 4. Performance Critical Code

```csharp
// ✅ برای Performance Critical Code
public int Sum(Span<int> numbers)
{
    int sum = 0;
    foreach (int num in numbers)
    {
        sum += num;
    }
    return sum;
}

// استفاده
int[] array = { 1, 2, 3, 4, 5 };
int result = Sum(array); // بدون Boxing
```

---

## محدودیت‌ها

### 1. Stack-Only

**Span نمی‌تواند در Heap ذخیره شود:**

```csharp
// ❌ نمی‌تواند Field باشد
public class MyClass
{
    // Span<int> _span; // Compile Error - نمی‌تواند Field باشد
}

// ✅ می‌تواند Local Variable باشد
public void Method()
{
    Span<int> span = stackalloc int[10]; // OK
}
```

### 2. Contiguous Memory

**فقط برای Contiguous Memory کار می‌کند:**

```csharp
// ✅ Array - Contiguous
int[] array = { 1, 2, 3 };
Span<int> span = array; // OK

// ❌ List - Non-Contiguous
List<int> list = new List<int> { 1, 2, 3 };
// Span<int> span = list; // Compile Error
```

### 3. Async/Await

**Span نمی‌تواند در Async Methods استفاده شود:**

```csharp
// ❌ نمی‌تواند در Async باشد
public async Task<int> ProcessAsync()
{
    // Span<int> span = stackalloc int[10]; // Compile Error
}
```

---

## Performance Comparison

### Benchmark: Split vs Span

**Benchmark تبدیل یک String به وسیله Split، Substring، Slice in Span:**

```csharp
// ❌ Split - با Allocation
string text = "Hello,World,Test";
string[] parts = text.Split(','); // Allocation برای هر String

// ❌ Substring - با Allocation
string hello = text.Substring(0, 5); // Allocation جدید

// ✅ Span - بدون Allocation
ReadOnlySpan<char> span = text.AsSpan();
ReadOnlySpan<char> hello = span.Slice(0, 5); // View - بدون Allocation
```

### نتایج تقریبی

- **Split**: ~1000ns (با Allocation)
- **Substring**: ~500ns (با Allocation)
- **Span.Slice**: ~10ns (بدون Allocation)

**Span 50-100 برابر سریع‌تر است!**

### مثال: Performance Test

```csharp
// Test 1: Substring (با Allocation)
Stopwatch sw = Stopwatch.StartNew();
for (int i = 0; i < 1000000; i++)
{
    string result = text.Substring(0, 5);
}
sw.Stop();
Console.WriteLine($"Substring: {sw.ElapsedMilliseconds}ms"); // ~500ms

// Test 2: Span (بدون Allocation)
sw.Restart();
for (int i = 0; i < 1000000; i++)
{
    ReadOnlySpan<char> result = text.AsSpan().Slice(0, 5);
}
sw.Stop();
Console.WriteLine($"Span: {sw.ElapsedMilliseconds}ms"); // ~10ms
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. Span چیست و چه تفاوتی با Array دارد؟

**پاسخ**:
- Span یک View روی Memory است، نه کپی
- Struct است و در Stack ذخیره می‌شود
- Allocation جدید ایجاد نمی‌کند
- Array یک Object است و در Heap ذخیره می‌شود

#### 2. چرا از Span استفاده می‌کنیم؟

**پاسخ**:
- **Performance**: بدون Allocation، سریع‌تر
- **Memory Efficiency**: حافظه کمتری مصرف می‌کند
- **Zero-Copy**: کپی نمی‌کند، فقط View ایجاد می‌کند

#### 3. تفاوت Span و ReadOnlySpan چیست؟

**پاسخ**:
- `Span<T>`: می‌تواند تغییر دهد
- `ReadOnlySpan<T>`: فقط Read-Only است
- هر دو Performance یکسانی دارند

#### 4. محدودیت‌های Span چیست؟

**پاسخ**:
- نمی‌تواند Field باشد (Stack-Only)
- فقط برای Contiguous Memory کار می‌کند
- نمی‌تواند در Async Methods استفاده شود
- نمی‌تواند در Heap ذخیره شود

#### 5. Span Memory-Safe است؟

**پاسخ**:
- بله، Span Memory-Safe است
- توسط Runtime بررسی می‌شود
- نمی‌تواند به Memory غیرمجاز دسترسی داشته باشد

#### 6. چه زمانی از Span استفاده می‌کنیم؟

**پاسخ**:
- Performance Critical Code
- String Operations (بدون Allocation)
- Array Slicing
- وقتی نیاز به Zero-Copy داریم

### نکات مهم برای مصاحبه

1. **View نه کپی**: Span یک View است، نه کپی
2. **Stack-Only**: نمی‌تواند در Heap باشد
3. **Zero Allocation**: Allocation جدید ایجاد نمی‌کند
4. **Performance**: 50-100 برابر سریع‌تر از Substring/Split
5. **Contiguous**: فقط برای Contiguous Memory

---

## خلاصه

### Span<T>
- **View**: یک View روی Memory است
- **Struct**: در Stack ذخیره می‌شود
- **Zero Allocation**: Allocation جدید ایجاد نمی‌کند
- **Performance**: بسیار سریع‌تر از روش‌های قدیمی

### ReadOnlySpan<T>
- **Read-Only**: نمی‌تواند تغییر دهد
- **Performance**: همان Performance Span را دارد
- **Safety**: برای داده‌های Immutable

### مزایا
- کاهش Memory Allocation
- Performance بهتر
- Zero-Copy Operations
- Memory-Safe

### محدودیت‌ها
- Stack-Only
- فقط Contiguous Memory
- نمی‌تواند در Async باشد

**موفق باشید!** 🚀