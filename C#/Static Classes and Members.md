# Static Classes و Static Members در C#

Static یکی از **مهم‌ترین مفاهیم** در C# است که در مصاحبه‌ها بسیار پرسیده می‌شود. درک صحیح Static Classes، Static Members، و تفاوت آن‌ها با Instance Members برای نوشتن کد بهینه و قابل اعتماد ضروری است.

---

## فهرست مطالب

1. [Static Class](#static-class)
2. [Static Members](#static-members)
3. [Instance Method vs Static Method](#instance-method-vs-static-method)
4. [Memory Management](#memory-management)
5. [Thread-Safety](#thread-safety)
6. [کاربردها](#کاربردها)
7. [نکات مصاحبه](#نکات-مصاحبه)

---

## Static Class

### تعریف

**زمانی نوع کلاس را `static` می‌گذاریم که تمام موارد داخل کلاس Static باشد.**

### ویژگی‌های Static Class

1. **نمونه‌سازی نمی‌شود**: این کلاس نمی‌تواند نمونه‌سازی شود
2. **بدون نمونه‌سازی**: می‌توان بدون نمونه‌سازی از متدهای آن استفاده کرد
3. **بسته‌بندی**: تنها وظیفه آن بسته‌بندی متدها و متغیرهای مشابه است

### مثال

```csharp
// ✅ Static Class
public static class MathHelper
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
    
    public static int Multiply(int a, int b)
    {
        return a * b;
    }
}

// استفاده - بدون نمونه‌سازی
int result = MathHelper.Add(5, 3); // ✅
int product = MathHelper.Multiply(4, 2); // ✅

// ❌ نمی‌توان نمونه‌سازی کرد
// MathHelper helper = new MathHelper(); // Compile Error
```

### محدودیت‌های Static Class

```csharp
public static class MyStaticClass
{
    // ✅ می‌تواند Static Members داشته باشد
    public static int Value { get; set; }
    public static void Method() { }
    
    // ❌ نمی‌تواند Instance Members داشته باشد
    // public int InstanceValue { get; set; } // Compile Error
    // public void InstanceMethod() { } // Compile Error
    
    // ❌ نمی‌تواند Constructor داشته باشد (مگر Static Constructor)
    // public MyStaticClass() { } // Compile Error
}
```

### کاربرد Static Class

- **Utility Classes**: کلاس‌های کمکی (مثل `Math`, `Console`)
- **Extension Methods**: برای Extension Methods
- **Constants**: برای Constants
- **Helper Methods**: متدهای Helper که به Instance نیاز ندارند

![[Pasted image 20240117200155.png]]

## Static Members

### تعریف

**Static Members** به Members گفته می‌شود که به Instance کلاس وابسته نیستند و می‌توان بدون نمونه‌سازی از آن‌ها استفاده کرد.

### انواع Static Members

```csharp
public class MyClass
{
    // Static Field
    public static int StaticField = 10;
    
    // Static Property
    public static string StaticProperty { get; set; }
    
    // Static Method
    public static void StaticMethod()
    {
        Console.WriteLine("Static Method");
    }
    
    // Static Constructor
    static MyClass()
    {
        StaticField = 20;
    }
    
    // Instance Members
    public int InstanceField = 5;
    public void InstanceMethod() { }
}

// استفاده
MyClass.StaticMethod(); // ✅ بدون نمونه‌سازی
int value = MyClass.StaticField; // ✅

MyClass obj = new MyClass();
obj.InstanceMethod(); // ✅ نیاز به نمونه‌سازی
```

---

## Instance Method vs Static Method

### تعریف

**متدهایی داخل یک کلاس، زمان صدا زدن به اصطلاح Instance Method هستند و متدهای Static، Class Method هستند.**

### تفاوت‌ها

| ویژگی | Instance Method | Static Method |
|---|---|---|
| **وابستگی** | به Instance وابسته است | به Instance وابسته نیست |
| **دسترسی** | نیاز به نمونه‌سازی | بدون نمونه‌سازی |
| **دسترسی به Instance Members** | دارد | ندارد |
| **دسترسی به Static Members** | دارد | دارد |
| **Memory** | برای هر Instance | یک بار در Memory |

### مثال

```csharp
public class Calculator
{
    // Instance Method
    public int Add(int a, int b)
    {
        return a + b;
    }
    
    // Static Method
    public static int Multiply(int a, int b)
    {
        return a * b;
    }
}

// استفاده
Calculator calc = new Calculator();
int sum = calc.Add(5, 3); // Instance Method

int product = Calculator.Multiply(4, 2); // Static Method
```

---

## Memory Management

### نحوه ذخیره Static‌ها در Memory

**Class Method‌ها (Static Methods) در حافظه Heap ذخیره می‌شوند که حافظه مشترکی در برنامه است.**

**اما Instance Method‌ها زمانی که یک نمونه از کلاس ساخته می‌شود، یک متد هم در Heap ساخته می‌شوند که ماهیت متفاوتی دارند.**

### توضیح دقیق‌تر

```csharp
public class MyClass
{
    // Static Method - یک بار در Memory (High Frequency Heap)
    public static void StaticMethod()
    {
        // این متد یک بار در Memory ذخیره می‌شود
        // و بین تمام Instance‌ها مشترک است
    }
    
    // Instance Method - برای هر Instance
    public void InstanceMethod()
    {
        // این متد برای هر Instance در Memory ذخیره می‌شود
    }
}

// Memory Layout:
// StaticMethod: یک بار در High Frequency Heap (مشترک)
// InstanceMethod: برای هر Instance در Heap
```

### High Frequency Heap (HFH)

**Static Members در High Frequency Heap ذخیره می‌شوند:**

- **مشترک**: بین تمام Instance‌ها مشترک است
- **یک بار**: فقط یک بار در Memory ذخیره می‌شود
- **زندگی طولانی**: تا زمان Unload شدن Application در Memory می‌ماند
- **GC**: توسط Garbage Collector مدیریت نمی‌شود (مگر در .NET Core با Unloading)

### مثال: Memory Usage

```csharp
public class Counter
{
    // Static - یک بار در Memory
    public static int StaticCounter = 0;
    
    // Instance - برای هر Instance
    public int InstanceCounter = 0;
}

// ایجاد 1000 Instance
for (int i = 0; i < 1000; i++)
{
    var counter = new Counter();
    counter.InstanceCounter++;
    Counter.StaticCounter++;
}

// Memory:
// StaticCounter: 1 بار در Memory (مشترک)
// InstanceCounter: 1000 بار در Memory (برای هر Instance)
```

---

## Thread-Safety

### نکته مهم

**Class Method‌ها (Static Methods) Thread-Safe نیستند.**

### مشکل Thread-Safety

```csharp
// ❌ مشکل - Thread-Safe نیست
public static class Counter
{
    private static int _count = 0;
    
    public static void Increment()
    {
        _count++; // Race Condition!
    }
    
    public static int GetCount()
    {
        return _count;
    }
}

// استفاده در Multi-Thread
Task.Run(() => Counter.Increment());
Task.Run(() => Counter.Increment());
// ممکن است _count به درستی Increment نشود
```

### راه‌حل: Thread-Safe Static Methods

```csharp
// ✅ Thread-Safe
public static class Counter
{
    private static int _count = 0;
    private static readonly object _lock = new object();
    
    public static void Increment()
    {
        lock (_lock)
        {
            _count++; // Thread-Safe
        }
    }
    
    public static int GetCount()
    {
        lock (_lock)
        {
            return _count;
        }
    }
}

// یا با Interlocked
public static class Counter
{
    private static int _count = 0;
    
    public static void Increment()
    {
        Interlocked.Increment(ref _count); // Thread-Safe
    }
}
```

### Best Practice

**اگر Static Method از Shared State استفاده می‌کند، باید Thread-Safe باشد.**

---

## کاربردها

### چه زمانی از Static استفاده کنیم؟

**زمانی که می‌خواهیم بین تمام منابع یک متد را فراخوانی کنیم، از Static استفاده می‌کنیم.**

### مثال‌های کاربرد

#### 1. Utility Classes

```csharp
public static class StringHelper
{
    public static string Reverse(string input)
    {
        return new string(input.Reverse().ToArray());
    }
    
    public static bool IsNullOrEmpty(string input)
    {
        return string.IsNullOrEmpty(input);
    }
}
```

#### 2. Constants

```csharp
public static class Constants
{
    public const int MaxUsers = 100;
    public const string AppName = "MyApp";
    public static readonly DateTime StartDate = new DateTime(2024, 1, 1);
}
```

#### 3. Factory Methods

```csharp
public static class LoggerFactory
{
    public static ILogger CreateLogger(string name)
    {
        return new FileLogger(name);
    }
}
```

#### 4. Extension Methods

```csharp
public static class StringExtensions
{
    public static bool IsEmail(this string input)
    {
        return input.Contains("@");
    }
}
```

### چه زمانی از Static استفاده نکنیم؟

1. **Stateful Operations**: وقتی نیاز به State داریم
2. **Polymorphism**: وقتی نیاز به Polymorphism داریم
3. **Dependency Injection**: وقتی نیاز به DI داریم
4. **Testing**: وقتی نیاز به Mock داریم

---

## نکات مصاحبه

### سوالات رایج

#### 1. Static Class چیست و چه ویژگی‌هایی دارد؟

**پاسخ**:
- کلاسی که تمام Members آن Static است
- نمی‌توان نمونه‌سازی کرد
- می‌توان بدون نمونه‌سازی استفاده کرد
- برای Utility Classes مناسب است

#### 2. تفاوت Instance Method و Static Method چیست؟

**پاسخ**:
- **Instance Method**: به Instance وابسته است، نیاز به نمونه‌سازی
- **Static Method**: به Instance وابسته نیست، بدون نمونه‌سازی
- **Memory**: Instance Method برای هر Instance، Static Method یک بار

#### 3. Static Methods در کجا ذخیره می‌شوند؟

**پاسخ**:
- در High Frequency Heap (HFH)
- حافظه مشترک در برنامه
- یک بار در Memory ذخیره می‌شوند
- تا زمان Unload شدن Application در Memory می‌مانند

#### 4. آیا Static Methods Thread-Safe هستند؟

**پاسخ**:
- خیر، Static Methods به خودی خود Thread-Safe نیستند
- اگر از Shared State استفاده می‌کنند، باید Thread-Safe شوند
- از `lock` یا `Interlocked` استفاده کنیم

#### 5. چه زمانی از Static استفاده می‌کنیم؟

**پاسخ**:
- وقتی می‌خواهیم بین تمام منابع یک متد را فراخوانی کنیم
- برای Utility Classes
- برای Constants
- برای Factory Methods
- **نکته**: وقتی نیاز به State یا Polymorphism نداریم

#### 6. محدودیت‌های Static Class چیست؟

**پاسخ**:
- نمی‌تواند Instance Members داشته باشد
- نمی‌تواند نمونه‌سازی شود
- نمی‌تواند Interface را Implement کند (مگر برای Extension Methods)
- نمی‌تواند از Generic Constraints استفاده کند

### نکات مهم برای مصاحبه

1. **Memory**: Static Members در HFH ذخیره می‌شوند
2. **Thread-Safety**: Static Methods Thread-Safe نیستند
3. **Use Case**: برای Utility Classes و Constants
4. **Limitations**: نمی‌توان Instance Members داشت
5. **Best Practice**: فقط وقتی استفاده کنید که واقعاً نیاز دارید

---

## خلاصه

### Static Class
- تمام Members Static هستند
- نمی‌توان نمونه‌سازی کرد
- برای Utility Classes مناسب است

### Static Members
- به Instance وابسته نیستند
- بدون نمونه‌سازی قابل استفاده
- در HFH ذخیره می‌شوند

### Memory
- Static Methods: یک بار در HFH (مشترک)
- Instance Methods: برای هر Instance در Heap

### Thread-Safety
- Static Methods Thread-Safe نیستند
- باید از Synchronization استفاده کنیم

### Best Practice
- فقط وقتی استفاده کنید که واقعاً نیاز دارید
- برای Utility Classes و Constants
- از Stateful Static Methods پرهیز کنید

**موفق باشید!** 🚀

![[Pasted image 20240222172431.png]]


![[Pasted image 20240222172509.png]]