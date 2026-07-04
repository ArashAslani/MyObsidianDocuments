# SOLID Principles در C#

SOLID Principles پنج اصل **بنیادی** در طراحی نرم‌افزار هستند که توسط Robert C. Martin (Uncle Bob) معرفی شدند. درک این اصول برای هر توسعه‌دهنده Senior **ضروری** است و در مصاحبه‌ها بسیار پرسیده می‌شوند.

---

## فهرست مطالب

1. [مقدمه: SOLID چیست؟](#مقدمه-solid-چیست)
2. [S - Single Responsibility Principle (SRP)](#s---single-responsibility-principle-srp)
3. [O - Open-Closed Principle (OCP)](#o---open-closed-principle-ocp)
4. [L - Liskov Substitution Principle (LSP)](#l---liskov-substitution-principle-lsp)
5. [I - Interface Segregation Principle (ISP)](#i---interface-segregation-principle-isp)
6. [D - Dependency Inversion Principle (DIP)](#d---dependency-inversion-principle-dip)
7. [Low Coupling and High Cohesion](#low-coupling-and-high-cohesion)
8. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: SOLID چیست؟

### تعریف

**SOLID** مخفف پنج اصل طراحی نرم‌افزار است:

- **S** - Single Responsibility Principle
- **O** - Open-Closed Principle
- **L** - Liskov Substitution Principle
- **I** - Interface Segregation Principle
- **D** - Dependency Inversion Principle

### معرفی

**Uncle Bob (Robert C. Martin)**: معرف SOLID و Clean Code و Agile Approach

### هدف SOLID

- **Maintainability**: کد قابل نگهداری
- **Scalability**: قابلیت مقیاس‌پذیری
- **Testability**: قابلیت تست
- **Flexibility**: انعطاف‌پذیری

---

## S - Single Responsibility Principle (SRP)

### تعریف

**هر کلاس و قسمت از نرم‌افزار باید یک وظیفه و یک دلیل برای تغییر داشته باشد.**

### برداشت اشتباه

**برداشت اشتباه**: هر کلاس باید یک کار انجام دهد - اگر یک کلاس چند تا متد داشت، باید چند تا کلاس داشته باشیم.

**این درست نیست!** کلاس و متدهای کلاس کوچک‌ترین Level در این مفهوم هستند.

### درک صحیح SRP

**SRP درباره "دلیل تغییر" است، نه "تعداد کارها":**

```csharp
// ❌ نقض SRP - چند دلیل برای تغییر
public class Order
{
    public void CalculateTotal() { } // دلیل تغییر: تغییرات Business Logic
    public void SaveToDatabase() { } // دلیل تغییر: تغییرات Database Schema
    public void SendEmail() { } // دلیل تغییر: تغییرات Email Service
    public void GenerateInvoice() { } // دلیل تغییر: تغییرات Invoice Format
}

// ✅ رعایت SRP - یک دلیل برای تغییر
public class Order
{
    public decimal CalculateTotal() { } // فقط Business Logic
}

public class OrderRepository
{
    public void Save(Order order) { } // فقط Database Operations
}

public class EmailService
{
    public void Send(Order order) { } // فقط Email Operations
}

public class InvoiceGenerator
{
    public void Generate(Order order) { } // فقط Invoice Generation
}
```

### ارتباط با Low Coupling and High Cohesion

**یک مفهوم و شعار مهم در برنامه‌نویسی وجود دارد به اسم Low Coupling and High Cohesion:**

- **Low Coupling**: در هم‌تنیدگی مفاهیم کم شود
- **High Cohesion**: پیوستگی یک کد زیاد شود

**SRP دقیقاً آمده تا در هم‌تنیدگی یا Coupling را کاهش دهد.**

### نکته مهم

**این Principle مربوط به افراد است:**

- اگر یک کلاس توسط چند تیم یا چند نقش مدیریت می‌شود، احتمالاً SRP را نقض کرده‌اید
- هر کلاس باید یک "Owner" داشته باشد

### مزایا

**این قانون پیاده‌سازی و خوانایی کد را بهتر می‌کند:**

- تغییرات کد به راحتی اعمال می‌شود
- Testing آسان‌تر می‌شود
- Reusability افزایش می‌یابد

### مثال عملی

```csharp
// ❌ نقض SRP
public class User
{
    public string Name { get; set; }
    public string Email { get; set; }
    
    public void Save() // Database Concern
    {
        // Save to database
    }
    
    public void SendEmail() // Email Concern
    {
        // Send email
    }
    
    public void Validate() // Validation Concern
    {
        // Validate user
    }
}

// ✅ رعایت SRP
public class User
{
    public string Name { get; set; }
    public string Email { get; set; }
}

public class UserRepository
{
    public void Save(User user) { }
}

public class EmailService
{
    public void SendEmail(User user) { }
}

public class UserValidator
{
    public bool Validate(User user) { }
}
```

### لینک ویدیو

برای دیدن مثال: https://youtu.be/421IhpFo2zA?si=TfFfX2rlYqYI1AeH

---

## O - Open-Closed Principle (OCP)

### تعریف

**امکان توسعه وجود داشته باشد بدون اینکه کد تغییر کند.**

**نسبت به تغییرات بسته و نسبت به عملیات جدید باز باشد.**

### درک OCP

**در یک محصول نهایی، امکان اضافه کردن ویژگی و رفتار وجود داشته باشد:**

- **Open for Extension**: باز برای توسعه (اضافه کردن Feature جدید)
- **Closed for Modification**: بسته برای تغییر (تغییر کد موجود)

### روش‌های پیاده‌سازی

**این امر توسط Design Pattern‌ها قابل پیاده‌سازی است:**

- **Plugin Architecture**: معماری Plugin
- **Decorator Pattern**: الگوی Decorator
- **Strategy Pattern**: الگوی Strategy
- **Visitor Pattern**: الگوی Visitor
- **Rule Engine**: موتور قوانین

### مثال: VS Code و مرورگرها

**برای مثال برنامه‌هایی مانند VS Code یا مرورگرها:**

- دارای یک Core اصلی هستند
- رفتارهای جدید به صورت Extension یا Plugin روی آن نصب می‌شوند
- Core تغییر نمی‌کند، فقط Extension اضافه می‌شود

### مثال کد

```csharp
// ❌ نقض OCP - باید کد را تغییر دهیم
public class OrderProcessor
{
    public void Process(Order order)
    {
        if (order.Type == "Standard")
        {
            // Process standard order
        }
        else if (order.Type == "Premium")
        {
            // Process premium order
        }
        // برای اضافه کردن نوع جدید، باید این کلاس را تغییر دهیم
    }
}

// ✅ رعایت OCP - بدون تغییر کد موجود
public interface IOrderProcessor
{
    void Process(Order order);
}

public class StandardOrderProcessor : IOrderProcessor
{
    public void Process(Order order) { }
}

public class PremiumOrderProcessor : IOrderProcessor
{
    public void Process(Order order) { }
}

// می‌توانیم بدون تغییر کد موجود، Processor جدید اضافه کنیم
public class ExpressOrderProcessor : IOrderProcessor
{
    public void Process(Order order) { }
}
```

### مثال: Strategy Pattern

```csharp
// ✅ استفاده از Strategy Pattern
public interface IDiscountStrategy
{
    decimal CalculateDiscount(decimal amount);
}

public class StandardDiscount : IDiscountStrategy
{
    public decimal CalculateDiscount(decimal amount) => amount * 0.1m;
}

public class PremiumDiscount : IDiscountStrategy
{
    public decimal CalculateDiscount(decimal amount) => amount * 0.2m;
}

public class OrderCalculator
{
    private readonly IDiscountStrategy _discountStrategy;
    
    public OrderCalculator(IDiscountStrategy discountStrategy)
    {
        _discountStrategy = discountStrategy;
    }
    
    public decimal CalculateTotal(Order order)
    {
        decimal subtotal = order.Subtotal;
        decimal discount = _discountStrategy.CalculateDiscount(subtotal);
        return subtotal - discount;
    }
}

// می‌توانیم Strategy جدید اضافه کنیم بدون تغییر OrderCalculator
public class VIPDiscount : IDiscountStrategy
{
    public decimal CalculateDiscount(decimal amount) => amount * 0.3m;
}
```

---

## L - Liskov Substitution Principle (LSP)

### تعریف

**به این مفهوم است که آیا لزوماً دو ماهیت به اسم مشابه می‌توانند پایه یکسانی داشته باشند.**

**یعنی ویژگی‌های یک کلاس والد به درد یک فرزند می‌خورد یا نه.**

### تعریف دقیق‌تر

**به تعبیری دیگر، شما باید از هر کلاس مشتق شده از والد بتوانید به جای نمونه‌ای از کلاس والد استفاده کنید و همان رفتار را بدون تغییرات داشته باشید:**

- باید اطمینان حاصل کنید که کلاس مشتق شده تأثیری در عملکرد کلاس والد ندارد
- بتوانید کلاس‌های مشتق شده از والد را در جای والد تعویض کنید

### مثال: نقض LSP

```csharp
// ❌ نقض LSP
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    
    public int Area() => Width * Height;
}

public class Square : Rectangle
{
    public override int Width
    {
        get => base.Width;
        set
        {
            base.Width = value;
            base.Height = value; // مشکل: Square رفتار متفاوتی دارد
        }
    }
    
    public override int Height
    {
        get => base.Height;
        set
        {
            base.Width = value;
            base.Height = value; // مشکل: Square رفتار متفاوتی دارد
        }
    }
}

// استفاده
Rectangle rect = new Square();
rect.Width = 5;
rect.Height = 10;
int area = rect.Area(); // 100? یا 50? - رفتار غیرمنتظره!
```

### مثال: رعایت LSP

```csharp
// ✅ رعایت LSP
public abstract class Shape
{
    public abstract int Area();
}

public class Rectangle : Shape
{
    public int Width { get; set; }
    public int Height { get; set; }
    
    public override int Area() => Width * Height;
}

public class Square : Shape
{
    public int Side { get; set; }
    
    public override int Area() => Side * Side;
}

// استفاده
Shape shape = new Rectangle { Width = 5, Height = 10 };
int area = shape.Area(); // 50 - رفتار قابل پیش‌بینی

shape = new Square { Side = 5 };
area = shape.Area(); // 25 - رفتار قابل پیش‌بینی
```

### قوانین LSP

1. **Preconditions**: کلاس فرزند نباید Precondition قوی‌تری داشته باشد
2. **Postconditions**: کلاس فرزند نباید Postcondition ضعیف‌تری داشته باشد
3. **Invariants**: Invariant‌های کلاس والد باید حفظ شوند
4. **Exception**: کلاس فرزند نباید Exception جدیدی throw کند

### مثال: Exception Rule

```csharp
// ❌ نقض LSP - Exception جدید
public class BaseClass
{
    public virtual void Process()
    {
        // No exception
    }
}

public class DerivedClass : BaseClass
{
    public override void Process()
    {
        throw new NotImplementedException(); // Exception جدید - نقض LSP
    }
}

// ✅ رعایت LSP
public class DerivedClass : BaseClass
{
    public override void Process()
    {
        // Implementation without new exceptions
    }
}
```

---

## I - Interface Segregation Principle (ISP)

### تعریف

**هرچه که لازم داری انجام بده و اضافه کاری نکن.**

**شکستن Interface‌ها به بخش‌هایی کوچک طبق نیاز.**

### درک ISP

**به تعبیری دیگر:**

- اینترفیس‌های کوچک را برای نیاز خود بسازید
- از ساخت اینترفیس‌هایی با قابلیت‌های زیاد و استفاده در هر جایی اجتناب کنید
- نباید کلاس‌های مشتق شده را مجبور به پیاده‌سازی کنید که به آن نیاز ندارند
- **به جای یک کلاس بزرگ، چند کلاس کوچک بسازید**

### Over-Engineering

**در ابتدا نباید Over-Engineering انجام شود:**

- فکر به اینکه یک Interface می‌تواند به چه بخش‌هایی شکسته شود
- در روند توسعه پروژه، اگر یک Interface اضافاتی نسبت به مدل جدید و مشابه ما داشت، باید Interface شکسته شود
- **حتماً باید شکسته شود**

### مثال: نقض ISP

```csharp
// ❌ نقض ISP - Interface بزرگ
public interface IWorker
{
    void Work();
    void Eat();
    void Sleep();
    void Code();
    void Design();
    void Test();
}

public class Developer : IWorker
{
    public void Work() { }
    public void Eat() { }
    public void Sleep() { }
    public void Code() { } // فقط این را نیاز دارد
    public void Design() { } // مجبور به پیاده‌سازی
    public void Test() { } // مجبور به پیاده‌سازی
}

public class Designer : IWorker
{
    public void Work() { }
    public void Eat() { }
    public void Sleep() { }
    public void Code() { } // مجبور به پیاده‌سازی
    public void Design() { } // فقط این را نیاز دارد
    public void Test() { } // مجبور به پیاده‌سازی
}
```

### مثال: رعایت ISP

```csharp
// ✅ رعایت ISP - Interface‌های کوچک
public interface IWorkable
{
    void Work();
}

public interface IEatable
{
    void Eat();
}

public interface ISleepable
{
    void Sleep();
}

public interface ICodeable
{
    void Code();
}

public interface IDesignable
{
    void Design();
}

public interface ITestable
{
    void Test();
}

// Developer فقط Interface‌های مورد نیاز را Implement می‌کند
public class Developer : IWorkable, IEatable, ISleepable, ICodeable, ITestable
{
    public void Work() { }
    public void Eat() { }
    public void Sleep() { }
    public void Code() { }
    public void Test() { }
    // Design را Implement نمی‌کند چون نیاز ندارد
}

// Designer فقط Interface‌های مورد نیاز را Implement می‌کند
public class Designer : IWorkable, IEatable, ISleepable, IDesignable
{
    public void Work() { }
    public void Eat() { }
    public void Sleep() { }
    public void Design() { }
    // Code و Test را Implement نمی‌کند چون نیاز ندارد
}
```

### Best Practice

**Interface‌ها را بر اساس نیاز Client‌ها طراحی کنید، نه بر اساس Implementation:**

```csharp
// ✅ Interface بر اساس نیاز Client
public interface IOrderRepository
{
    Order GetById(int id);
    void Save(Order order);
}

// نه بر اساس Implementation
// public interface IRepository<T> // ممکن است Over-Engineering باشد
```

---

## D - Dependency Inversion Principle (DIP)

### تعریف

**ماژول‌های سطح بالا و ماژول‌های سطح پایین نباید مستقیماً به هم دسترسی داشته باشند.**

**باید این اتفاق توسط Abstraction‌ها صورت بگیرد.**

### درک DIP

**ماژول‌های سطح بالا:**

- Business و Flow یک پروژه
- به صورت Abstract قرار دارد

**ماژول سطح پایین:**

- پیاده‌سازی‌ها
- شرط‌ها و جزئیات

### نحوه فکر کردن

**دسترسی و وابستگی‌ها باید برعکس شود:**

- با اعمال Interface و Abstraction، آزادی عمل بیشتر در Switch کردن بین پیاده‌سازی‌های مختلف داریم
- بدون اینکه بخواهیم در ماژول سطح بالا تغییری ایجاد کنیم

### قوانین DIP

1. **ماژول‌های سطح بالا باید به Abstraction (Interface / کلاس انتزاعی) وابسته باشند، نه مستقیماً به کلاس‌های Concrete در سطح پایین**
2. **Abstractionها نباید به جزئیات (Concreteها) وابسته باشند؛ این جزئیات هستند که باید به Abstraction وابسته باشند**
3. **استفاده از کلاس‌های Concrete (مثل `new`) در لایه‌های زیرساخت/ترکیب اشکال ندارد؛ مشکل وقتی است که منطق سطح بالا به پیاده‌سازی خاص قفل شود**

### مثال: نقض DIP

```csharp
// ❌ نقض DIP - وابستگی به Concrete Class
public class OrderService // High-Level Module
{
    private readonly SqlServerOrderRepository _repository; // Concrete Class
    
    public OrderService()
    {
        _repository = new SqlServerOrderRepository(); // new در High-Level - نقض DIP
    }
    
    public void ProcessOrder(Order order)
    {
        _repository.Save(order);
    }
}

// اگر بخواهیم به MongoDB تغییر دهیم، باید OrderService را تغییر دهیم
```

### مثال: رعایت DIP

```csharp
// ✅ رعایت DIP - وابستگی به Abstraction
public interface IOrderRepository // Abstraction
{
    void Save(Order order);
    Order GetById(int id);
}

public class SqlServerOrderRepository : IOrderRepository // Low-Level Module
{
    public void Save(Order order) { }
    public Order GetById(int id) { }
}

public class MongoOrderRepository : IOrderRepository // Low-Level Module
{
    public void Save(Order order) { }
    public Order GetById(int id) { }
}

public class OrderService // High-Level Module
{
    private readonly IOrderRepository _repository; // Abstraction
    
    public OrderService(IOrderRepository repository) // Dependency Injection
    {
        _repository = repository;
    }
    
    public void ProcessOrder(Order order)
    {
        _repository.Save(order); // وابستگی به Abstraction
    }
}

// می‌توانیم بدون تغییر OrderService، Repository را تغییر دهیم
var service = new OrderService(new MongoOrderRepository());
```

### نکته مهم

**داشتن `new` در ماژول‌های سطح بالا برای کلاس‌های Logical در اصل نقض این قانون است:**

```csharp
// ❌ نقض DIP
public class OrderService
{
    public void ProcessOrder(Order order)
    {
        var logger = new FileLogger(); // new در High-Level
        logger.Log("Processing order");
    }
}

// ✅ رعایت DIP
public class OrderService
{
    private readonly ILogger _logger;
    
    public OrderService(ILogger logger) // Dependency Injection
    {
        _logger = logger;
    }
    
    public void ProcessOrder(Order order)
    {
        _logger.Log("Processing order");
    }
}
```

---

## Low Coupling and High Cohesion

### تعریف

**یک مفهوم و شعار مهم در برنامه‌نویسی وجود دارد به اسم Low Coupling and High Cohesion:**

- **Low Coupling**: در هم‌تنیدگی مفاهیم کم شود
- **High Cohesion**: پیوستگی یک کد زیاد شود

### ارتباط با SOLID

**SOLID Principles به ما کمک می‌کنند تا:**

- **Coupling را کاهش دهیم**: با SRP، DIP
- **Cohesion را افزایش دهیم**: با SRP، ISP

### مثال

```csharp
// ❌ High Coupling, Low Cohesion
public class OrderProcessor
{
    private SqlServerDatabase _db; // Tight Coupling
    private FileLogger _logger; // Tight Coupling
    private EmailService _email; // Tight Coupling
    
    public void Process(Order order)
    {
        // همه چیز در یک جا - Low Cohesion
        _db.Save(order);
        _logger.Log("Order saved");
        _email.Send(order);
    }
}

// ✅ Low Coupling, High Cohesion
public class OrderProcessor
{
    private readonly IOrderRepository _repository; // Loose Coupling
    private readonly ILogger _logger; // Loose Coupling
    private readonly INotificationService _notification; // Loose Coupling
    
    public OrderProcessor(
        IOrderRepository repository,
        ILogger logger,
        INotificationService notification)
    {
        _repository = repository;
        _logger = logger;
        _notification = notification;
    }
    
    public void Process(Order order) // High Cohesion - فقط Business Logic
    {
        _repository.Save(order);
        _logger.Log("Order saved");
        _notification.Notify(order);
    }
}
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. SOLID Principles چیست و چرا مهم است؟

**پاسخ**:
- پنج اصل طراحی نرم‌افزار برای Maintainability و Scalability
- **S**: Single Responsibility - یک دلیل برای تغییر
- **O**: Open-Closed - باز برای Extension، بسته برای Modification
- **L**: Liskov Substitution - کلاس فرزند باید جایگزین والد شود
- **I**: Interface Segregation - Interface‌های کوچک
- **D**: Dependency Inversion - وابستگی به Abstraction

#### 2. تفاوت SRP و SOC چیست؟

**پاسخ**:
- **SRP**: در سطح پایین (کلاس) - یک دلیل برای تغییر
- **SOC**: در سطح بالا (ماژول) - جدا کردن Concerns
- هر دو درباره Abstraction صحبت می‌کنند

#### 3. OCP چگونه پیاده‌سازی می‌شود؟

**پاسخ**:
- با Design Pattern‌ها: Strategy، Decorator، Plugin Architecture
- با Interface و Abstraction
- **مثال**: VS Code Extensions

#### 4. LSP چیست و چرا مهم است؟

**پاسخ**:
- کلاس فرزند باید بتواند جایگزین والد شود
- رفتار باید قابل پیش‌بینی باشد
- **مثال**: Rectangle و Square مشکل

#### 5. ISP چیست و چرا Interface‌های بزرگ بد هستند؟

**پاسخ**:
- Interface‌ها باید کوچک و متمرکز باشند
- Client نباید مجبور به Implement کردن متدهای غیرضروری باشد
- **مثال**: IWorker بزرگ vs Interface‌های کوچک

#### 6. DIP چیست و چرا `new` در High-Level بد است؟

**پاسخ**:
- High-Level نباید به Low-Level وابسته باشد
- باید به Abstraction وابسته باشد
- `new` در High-Level = Tight Coupling
- **راه‌حل**: Dependency Injection

#### 7. Low Coupling و High Cohesion چیست؟

**پاسخ**:
- **Low Coupling**: وابستگی کم بین ماژول‌ها
- **High Cohesion**: پیوستگی بالا در یک ماژول
- SOLID به دستیابی به این دو کمک می‌کند

### نکات مهم برای مصاحبه

1. **SRP**: درباره "دلیل تغییر" است، نه "تعداد کارها"
2. **OCP**: با Design Pattern‌ها پیاده‌سازی می‌شود
3. **LSP**: کلاس فرزند باید جایگزین والد شود
4. **ISP**: Interface‌های کوچک بهتر از بزرگ
5. **DIP**: وابستگی به Abstraction، نه Concrete Class
6. **Trade-off**: همیشه Trade-off بین Principles وجود دارد

---

## خلاصه

### SOLID Principles

- **S - SRP**: یک دلیل برای تغییر
- **O - OCP**: باز برای Extension، بسته برای Modification
- **L - LSP**: کلاس فرزند جایگزین والد
- **I - ISP**: Interface‌های کوچک
- **D - DIP**: وابستگی به Abstraction

### مزایا

- Maintainability بهتر
- Testability بیشتر
- Flexibility بیشتر
- Scalability بهتر

### Best Practices

- از Over-Engineering پرهیز کنید
- Trade-off را در نظر بگیرید
- به صورت تدریجی اعمال کنیدش
- با Test Coverage انجام دهید

---

## منابع

- Robert C. Martin (Uncle Bob) – *Agile Software Development, Principles, Patterns, and Practices* (فصل اصول SOLID)
- Robert C. Martin (Uncle Bob) – *Clean Code*
- ویدیو مثال SRP: https://youtu.be/421IhpFo2zA?si=TfFfX2rlYqYI1AeH

**موفق باشید.**
