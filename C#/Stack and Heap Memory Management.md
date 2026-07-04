# Stack و Heap در .NET

سیستم‌عامل و .NET CLR حافظه را به دو بخش **Stack** و **Heap** تقسیم‌بندی می‌کنند. درک تفاوت بین این دو و نحوه کار آن‌ها یکی از **مهم‌ترین مفاهیم** در C# است که در مصاحبه‌ها بسیار پرسیده می‌شود.

---

## فهرست مطالب

1. [Stack (پشته)](#stack-پشته)
2. [Heap (هیپ)](#heap-هیپ)
3. [مقایسه Stack vs Heap](#مقایسه-stack-vs-heap)
4. [Value Types و Reference Types در Stack و Heap](#value-types-و-reference-types-در-stack-و-heap)
5. [StackOverflowException](#stackoverflowexception)
6. [Memory Leak](#memory-leak)
7. [نکات مصاحبه](#نکات-مصاحبه)

---

## Stack (پشته)

### تعریف

**Stack یا پشته همان Static Memory است که در بخش User-Space قرار دارد و به صورت خودکار توسط CPU مدیریت می‌شود.**

### ویژگی‌های Stack

#### 1. Static Memory
- Stack یک Static Memory است
- در بخش User-Space قرار دارد
- به صورت خودکار توسط CPU مدیریت می‌شود

#### 2. Performance
- **CPU Cache**: Stack از بخش CPU Cache بهره می‌برد
- **سریع**: واکشی اطلاعات به دلیل تخصیص فقط یک اشاره‌گر بسیار سریع و آسان است
- **محدود**: فضای محدودی دارد (چند مگابایت)

#### 3. LIFO (Last In First Out)
**Stack دارای یک الگوی ثابت LIFO برای ذخیره اطلاعات است:**

```csharp
public void Method1()
{
    int a = 10; // Push to Stack
    Method2();
    // a popped from Stack when Method1 ends
}

public void Method2()
{
    int b = 20; // Push to Stack
    // b popped from Stack when Method2 ends
}
```

#### 4. Lifetime
**طول عمر حافظه تخصیص داده شده به متغیرها در حد یک Block در برنامه‌نویسی است:**

- وقتی متد صدا زده می‌شود، متغیرها Push می‌شوند
- وقتی متد تمام می‌شود، متغیرها Pop می‌شوند
- به همین دلیل **Fragmentation (قطعه‌قطعه شدن) در این حافظه اتفاق نمی‌افتد**

#### 5. Initialization
**Stack چون دارای یک ساختار مشخص است، تمامی متغیرها باید در ابتدا مقداردهی شوند:**

```csharp
// ✅ صحیح
int number = 10;

// ❌ خطا - باید مقداردهی شود
// int number; // Compile Error (برای Local Variables)

// ✅ Nullable Type
int? nullableNumber = null; // با Nullable Type می‌توان null تخصیص داد
```

### چه چیزهایی در Stack قرار می‌گیرند؟

**Thread‌ها یا Value Type یا توابع و مقدار Return آن‌ها در Stack قرار می‌گیرند:**

1. **Value Types**: `int`, `bool`, `struct`, etc.
2. **Local Variables**: متغیرهای محلی
3. **Parameters**: پارامترهای ورودی متد
4. **Return Values**: مقدار بازگشتی یک متد
5. **References**: Reference به Object‌های Heap

### مثال

```csharp
public int Calculate(int a, int b) // a, b در Stack
{
    int result = a + b; // result در Stack
    return result; // return value در Stack
}
```

![[Pasted image 20230918200421.png]]

این ساختمان داده در Memory نگهداری میشود.

Stack چون دارای یک ساختار مشخص هست، تمامی متغیر ها باید در ابتدا مقدار دهی شوند (نوع داده باید مشخص شود) و در غیر این صورت خطای کامپایلر رخ میدهد.

البته در C# با قابلیت Nullable type امکان تخصیص مقدار null در متغیر های محلی مهیا شده.

thread ها یا Value type یا توابع و مقدار Return آنها در stack قرار می گیرند. و تغیرهای محلی (Local Variable ها)، پارامترهای ورودی متد و مقدار بازگشتی یک متد، جز مواردی هستند که مقادیرشان داخل Stack قرار میگیرد.

### StackOverflowException

**از خطاهایی که ممکن است در اثر استفاده نادرست از Stack صورت بگیرد، خطای Stack Overflow است:**

#### علل StackOverflowException

1. **سرریز اطلاعات**: به دلیل استفاده از متغیرهای محلی حجیم
2. **کاهش فضای آزاد**: باعث کاهش فضای آزاد و تخریب (Corrupt) شدن بخشی از Memory می‌شود
3. **Recursion بی‌نهایت**: اگر یک متد به طور دائم خودش را صدا بزند و هیچ‌گاه از آن خارج نشود

#### محدودیت Stack

**فضای Stack محدود است:**

- **32-bit Systems**: 1 مگابایت
- **64-bit Systems**: 4 مگابایت

([Reference](https://stackoverflow.com/a/28658130/6661314))

#### مثال: Recursion بی‌نهایت

```csharp
// ❌ StackOverflowException
public void InfiniteRecursion()
{
    InfiniteRecursion(); // خودش را صدا می‌زند
    // هیچ Base Case ندارد
    // Stack پر می‌شود و StackOverflowException رخ می‌دهد
}

// ✅ صحیح - با Base Case
public int Factorial(int n)
{
    if (n <= 1) // Base Case
        return 1;
    
    return n * Factorial(n - 1); // Recursive Call
}
```

#### مثال: متغیرهای حجیم

```csharp
// ❌ مشکل - Array بزرگ در Stack
public void LargeArray()
{
    int[] largeArray = new int[1000000]; // اگر در Stack باشد، مشکل ایجاد می‌کند
    // اما در C#، Array در Heap است، نه Stack
}

// ✅ Value Type بزرگ در Stack
public struct LargeStruct
{
    public byte Data1;
    public byte Data2;
    // ... 1000 فیلد
}

public void UseLargeStruct()
{
    LargeStruct large = new LargeStruct(); // در Stack - ممکن است مشکل ایجاد کند
}
```


## Heap (هیپ)

### تعریف

**Heap یا همان Dynamic Memory است که در بخش User-Space حافظه مجازی قرار دارد و باید به صورت دستی توسط برنامه‌نویس مدیریت شود.**

**در C# و Java، این مدیریت به ترتیب با CLR و JVM انجام می‌شود (Garbage Collector).**

### ویژگی‌های Heap

#### 1. Dynamic Memory
- Heap یک Dynamic Memory است
- در بخش User-Space حافظه مجازی قرار دارد
- در C# توسط Garbage Collector مدیریت می‌شود

#### 2. ساختار
**Heap برعکس Stack قاعده خاصی برای ذخیره اطلاعات ندارد:**

- **بدون ساختار مشخص**: ساختار، ترتیب و Layout خاصی ندارد
- **فضای بیشتر**: دارای فضای بیشتری برای ذخیره اطلاعات است
- **کندتر**: به دلیل مراحل تخصیص مقدار در حافظه کندتر عمل می‌کند

#### 3. مراحل تخصیص حافظه

**تخصیص حافظه در Heap شامل مراحل زیر است:**

1. **جستجوی مکان**: جستجوی Block با اندازه مناسب Object برای ذخیره
2. **رزرو فضا**: رزرو فضا برای Object
3. **Allocating**: تخصیص داده به حافظه
4. **Assign آدرس**: Assign آدرس به Stack

### چه چیزهایی در Heap قرار می‌گیرند؟

**وقتی یک متغیر از نوع String را ایجاد می‌کنیم:**

- **مقدار**: مقدار آن داخل Heap
- **Reference**: Memory-Address آن متغیر روی Heap، در Stack نگه‌داری می‌شود

```csharp
string text = "Hello";
// "Hello" در Heap ذخیره می‌شود
// Reference به "Hello" در Stack ذخیره می‌شود
```

**وقتی یک Reference Type را `new` می‌کنیم:**

```csharp
MyClass obj = new MyClass();
// Object در Heap ذخیره می‌شود
// Reference (آدرس) در Stack ذخیره می‌شود
```

### مدیریت Heap

**تخصیص حافظه در Heap توسط سیستم‌عامل و Heap Manager صورت می‌گیرد:**

- **In Use**: به Block‌های در حال استفاده In Use می‌گویند
- **Free**: به فضاهای خالی Free می‌گویند
- **Overlap**: Block‌ها نباید با یکدیگر تداخل یا Overlap داشته باشند

### Lifetime

**Heap مربوط به زمان اجرا (Runtime) است:**

- **طول عمر بیشتر**: دارای طول عمری بیشتری هستند
- **با اتمام کار آزاد نمی‌شوند**: با اتمام کار آزاد نمی‌شوند
- **GC**: زمانی که کار ما با آن تمام می‌شود، توسط Garbage Collector فضا آزاد (Clean) و Object‌ها حذف (Deallocation) می‌شوند

### مثال

```csharp
public void CreateObject()
{
    // Object در Heap
    MyClass obj = new MyClass(); // Reference در Stack
    
    // استفاده از obj
    obj.DoSomething();
    
    // وقتی متد تمام می‌شود:
    // - Reference از Stack Pop می‌شود
    // - Object در Heap باقی می‌ماند (تا زمان GC)
}
```
![[Pasted image 20230918200621.png]]

---

## مقایسه Stack vs Heap

### جدول مقایسه

| ویژگی | Stack | Heap |
|---|---|---|
| **نوع** | Static Memory | Dynamic Memory |
| **مدیریت** | خودکار (CPU) | دستی (GC در C#) |
| **ساختار** | LIFO (مشخص) | بدون ساختار مشخص |
| **سرعت** | سریع (CPU Cache) | کندتر |
| **فضا** | محدود (1-4 MB) | بیشتر |
| **Lifetime** | تا پایان Block | تا زمان GC |
| **Fragmentation** | ندارد | دارد |
| **Thread-Safe** | هر Thread Stack خودش را دارد | مشترک (نیاز به Synchronization) |

### مثال: تفاوت در استفاده

```csharp
public void Example()
{
    // Stack: Value Types
    int number = 10; // در Stack
    bool flag = true; // در Stack
    
    // Heap: Reference Types
    string text = "Hello"; // "Hello" در Heap، Reference در Stack
    MyClass obj = new MyClass(); // Object در Heap، Reference در Stack
    
    // Array: در Heap (حتی Value Types)
    int[] numbers = new int[10]; // Array در Heap
    // اما هر Element (int) در Array در Heap است
}
```

---

## Value Types و Reference Types در Stack و Heap

### Value Types

**معمولاً در Stack قرار می‌گیرند:**

```csharp
public void ValueTypeExample()
{
    int number = 10; // در Stack
    bool flag = true; // در Stack
    Point point = new Point(10, 20); // Struct در Stack
}
```

**استثنا: Value Types در Heap**

```csharp
// Value Type در Heap (زمانی که در Reference Type باشد)
public class Container
{
    public int Number; // در Heap (چون بخشی از Object است)
}

Container container = new Container();
container.Number = 10; // Number در Heap است
```

### Reference Types

**همیشه در Heap قرار می‌گیرند:**

```csharp
public void ReferenceTypeExample()
{
    // Object در Heap
    MyClass obj = new MyClass();
    // Reference (آدرس) در Stack
}
```

### مثال کامل

```csharp
public class Person
{
    public string Name; // Reference Type - در Heap
    public int Age; // Value Type - اما در Heap (چون بخشی از Object است)
}

public void Example()
{
    // Stack: person (Reference)
    // Heap: Person Object با Name و Age
    Person person = new Person { Name = "Ali", Age = 30 };
    
    // Stack: age (Value Type)
    // Heap: هیچ چیز
    int age = person.Age;
}
```

---

## Memory Leak

### تعریف

**در صورتی که در آزادسازی Heap دقت صورت نگیرد، هرچه بیشتر از برنامه استفاده شود، Object‌های بیشتری ایجاد می‌شوند و در نتیجه مشکلی به نام نشت حافظه یا Memory Leak صورت می‌گیرد.**

### Memory Leak در C++

**این مشکل در برنامه‌نویسی با زبان C++ بسیار شایع است** چون باید دستی Memory را مدیریت کنیم:

```cpp
// C++ - Manual Memory Management
int* ptr = new int(10); // تخصیص حافظه
// استفاده از ptr
// اگر delete فراموش شود = Memory Leak
delete ptr; // باید دستی آزاد کنیم
```

### Memory Leak در C#

**در C#، Garbage Collector Memory Leak را کاهش می‌دهد، اما هنوز ممکن است رخ دهد:**

#### علل Memory Leak در C#

1. **Event Handlers**: Unsubscribe نکردن Event‌ها
2. **Static References**: Static Reference به Object‌ها
3. **Dispose نکردن**: Dispose نکردن منابع Unmanaged
4. **Circular References**: Reference‌های دایره‌ای

#### مثال: Event Handler Leak

```csharp
// ❌ Memory Leak
public class Publisher
{
    public event EventHandler SomethingHappened;
}

public class Subscriber
{
    private Publisher _publisher;
    
    public Subscriber(Publisher publisher)
    {
        _publisher = publisher;
        _publisher.SomethingHappened += OnSomethingHappened; // Subscribe
        // اگر Unsubscribe نشود = Memory Leak
    }
    
    // ✅ باید Unsubscribe کنیم
    public void Dispose()
    {
        _publisher.SomethingHappened -= OnSomethingHappened; // Unsubscribe
    }
}
```

#### مثال: Static Reference Leak

```csharp
// ❌ Memory Leak
public static class Cache
{
    private static List<LargeObject> _cache = new List<LargeObject>();
    
    public static void Add(LargeObject obj)
    {
        _cache.Add(obj); // Static Reference - GC نمی‌تواند Collect کند
    }
}

// ✅ راه‌حل: Weak Reference
public static class Cache
{
    private static List<WeakReference<LargeObject>> _cache = new List<WeakReference<LargeObject>>();
    
    public static void Add(LargeObject obj)
    {
        _cache.Add(new WeakReference<LargeObject>(obj)); // GC می‌تواند Collect کند
    }
}
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت Stack و Heap چیست؟

**پاسخ**:
- **Stack**: Static Memory، LIFO، سریع، محدود، خودکار
- **Heap**: Dynamic Memory، بدون ساختار، کندتر، بیشتر، توسط GC
- **Lifetime**: Stack تا پایان Block، Heap تا زمان GC

#### 2. چه چیزهایی در Stack قرار می‌گیرند؟

**پاسخ**:
- Value Types (معمولاً)
- Local Variables
- Parameters
- Return Values
- References به Object‌های Heap

#### 3. چه چیزهایی در Heap قرار می‌گیرند؟

**پاسخ**:
- Reference Types (همیشه)
- Value Types که بخشی از Reference Type هستند
- Arrays

#### 4. StackOverflowException چیست و چرا رخ می‌دهد؟

**پاسخ**:
- وقتی Stack پر می‌شود
- Recursion بی‌نهایت
- متغیرهای محلی حجیم
- **محدودیت**: 1 MB (32-bit) یا 4 MB (64-bit)

#### 5. Memory Leak چیست و چگونه رخ می‌دهد؟

**پاسخ**:
- وقتی Object‌ها آزاد نمی‌شوند
- Event Handlers Unsubscribe نشده
- Static References
- Dispose نکردن منابع

#### 6. آیا Value Types همیشه در Stack هستند؟

**پاسخ**:
- خیر، Value Types معمولاً در Stack هستند
- اما اگر بخشی از Reference Type باشند، در Heap هستند
- **مثال**: `int` در `class` در Heap است

### نکات مهم برای مصاحبه

1. **Stack**: LIFO، سریع، محدود، خودکار
2. **Heap**: Dynamic، کندتر، بیشتر، توسط GC
3. **Value Types**: معمولاً Stack (مگر در Reference Type)
4. **Reference Types**: همیشه Heap
5. **Memory Leak**: در C# هم ممکن است رخ دهد

---

## خلاصه

### Stack
- **Static Memory**: LIFO، سریع، محدود
- **خودکار**: توسط CPU مدیریت می‌شود
- **Value Types**: معمولاً در Stack
- **Lifetime**: تا پایان Block

### Heap
- **Dynamic Memory**: بدون ساختار، کندتر، بیشتر
- **GC**: توسط Garbage Collector مدیریت می‌شود
- **Reference Types**: همیشه در Heap
- **Lifetime**: تا زمان GC

### Best Practices
- از Recursion بی‌نهایت پرهیز کنید
- Event Handlers را Unsubscribe کنید
- از Static References با احتیاط استفاده کنید
- منابع را Dispose کنید

**موفق باشید!** 🚀


