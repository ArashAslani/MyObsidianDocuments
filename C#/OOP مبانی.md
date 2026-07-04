# مبانی برنامه‌نویسی شی‌گرا (OOP) در C#

برنامه‌نویسی شی‌گرا (Object-Oriented Programming) یکی از مهم‌ترین پارادایم‌های برنامه‌نویسی است که در اکثر زبان‌های مدرن از جمله C# به صورت بومی پشتیبانی می‌شود. درک عمیق چهار اصل بنیادی OOP (Inheritance، Encapsulation، Abstraction، Polymorphism) و همچنین درک trade-off‌ها و محدودیت‌های هر کدام، برای هر توسعه‌دهنده Backend ضروری است. این مفاهیم در مصاحبه‌های فنی به صورت مستقیم و غیرمستقیم (از طریق سوالات طراحی و معماری) بسیار پرسیده می‌شوند.

---

## فهرست مطالب

1. [مقدمه: OOP چیست؟](#مقدمه-oop-چیست)
2. [مفاهیم پایه: Class و Object](#مفاهیم-پایه-class-و-object)
3. [چهار اصل بنیادی OOP](#چهار-اصل-بنیادی-oop)
4. [1. Inheritance (ارث‌بری)](#1-inheritance-ارثبری)
   - [Composition vs Inheritance](#composition-vs-inheritance)
5. [2. Encapsulation (کپسوله‌سازی)](#2-encapsulation-کپسولهسازی)
   - [Access Modifiers](#access-modifiers-در-c)
6. [3. Abstraction (انتزاع)](#3-abstraction-انتزاع)
   - [Interface vs Abstract Class](#interface-vs-abstract-class-چه-زمانی-کدام-را-استفاده-کنیم)
7. [4. Polymorphism (چندریختی)](#4-polymorphism-چندریختی)
   - [Virtual vs Abstract Methods](#virtual-vs-abstract-methods)
   - [Liskov Substitution Principle](#liskov-substitution-principle-lsp)
8. [مزایا و معایب OOP](#مزایا-و-معایب-oop)
9. [Dependency Injection با Polymorphism](#dependency-injection-با-polymorphism)
10. [Partial Classes](#partial-classes)
11. [Anti-Patterns و نکات مهم](#anti-patterns-و-نکات-مهم)
12. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: OOP چیست؟

### تعریف

OOP (Object-Oriented Programming) یک پارادایم برنامه‌نویسی است که کد را حول مفهوم "شیء" (Object) سازماندهی می‌کند. هر شیء ترکیبی از داده‌ها (Data/State) و رفتارها (Methods/Behavior) است که با هم یک موجودیت منطقی را تشکیل می‌دهند.

**تفاوت کلیدی با Procedural Programming**: در برنامه‌نویسی رویه‌ای، داده و رفتار جدا از هم هستند. در OOP، داده و رفتار در کنار هم در یک کلاس قرار می‌گیرند و این باعث می‌شود کد به مدل‌های دنیای واقعی نزدیک‌تر شود.

### رویکرد

**برنامه‌نویسی شی‌گرا یکسری تفکر و رویکرد برای توسعه کد است:**

- **Data-Centric**: برنامه‌نویسی شی‌گرا با محوریت داده انجام می‌شود
- **Real-World Modeling**: مدل‌سازی دنیای واقعی
- **Reusability**: قابلیت استفاده مجدد

### منابع

**پلی‌لیست مبانی شی‌گرایی از Ardiland:**
https://www.youtube.com/watch?v=RVSuXUodOyw&list=PLx2zI6hCyJvzpjb5pLDL0VefNNO7IJK0D&index=1

---

## مفاهیم پایه: Class و Object

### Class (کلاس)

**یک کلاس می‌تواند داخل خودش Attribute و Method‌هایی داشته باشد که به طور کلی ویژگی کلاس را مشخص می‌کنند:**

```csharp
public class Car
{
    // Attributes (Properties)
    public string Brand { get; set; }
    public string Model { get; set; }
    public int Speed { get; private set; }
    
    // Methods
    public void Start()
    {
        // Logic
    }
    
    public void Accelerate()
    {
        Speed += 10;
    }
}
```

### Object (شیء)

**زمانی که از Class نمونه‌هایی (Instance/Object) ساخته می‌شود، در اصل Object‌هایی از آن کلاس شکل می‌گیرند که در Scope پیاده‌سازی (Initialize) شده، دارای یک وضعیت یا State و Data در لحظه هستند:**

```csharp
// ایجاد Object
Car myCar = new Car();
myCar.Brand = "Toyota";
myCar.Model = "Camry";

// Object دارای State است
// State فعلی: Brand = "Toyota", Model = "Camry", Speed = 0
```

### State (وضعیت)

**برای مثال ما در زمان Initialize یک Object در یک State هستیم:**

- **Initial State**: Object در حالت اولیه
- **State Change**: زمانی که یک متد از آن صدا زده می‌شود، آن Object در State بعدی قرار می‌گیرد و تغییر می‌کند

**مثال: ایجاد Object از کلاس Car و صدا زدن متد Start که تغییر State را به ارمغان می‌آورد:**

```csharp
Car car = new Car();
// State: Engine = Off, Speed = 0

car.Start();
// State: Engine = On, Speed = 0

car.Accelerate();
// State: Engine = On, Speed = 10
```

---

## چهار اصل بنیادی OOP

### خلاصه

1. **Inheritance (ارث‌بری)**: استفاده مجدد از کد
2. **Encapsulation (کپسوله‌سازی)**: محافظت از داده
3. **Abstraction (انتزاع)**: مخفی کردن پیچیدگی
4. **Polymorphism (چندریختی)**: انعطاف‌پذیری

---

## 1. Inheritance (ارث‌بری)

### تعریف

**در پیاده‌سازی موجودیت‌ها می‌توانیم با موجودیت‌های دیگر اشتراک داشته باشیم که همه از یک رفتار مشترک پیروی می‌کنند:**

- در این شرایط معمولاً یک پایه (Base Class) وجود دارد که این ویژگی‌های مشترک را در خود دارد
- Base Class می‌تواند Interface یا Abstract یا Class باشد

### هدف

Inheritance دو هدف اصلی دارد:

1. **Code Reusability**: جلوگیری از تکرار کد با قرار دادن ویژگی‌های مشترک در Base Class
2. **Polymorphism**: امکان استفاده از Derived Classes به جای Base Class (با رعایت Liskov Substitution Principle)

**نکته مهم**: Inheritance یک رابطه "is-a" ایجاد می‌کند. اگر می‌گوییم `Employee : Person`، یعنی Employee یک نوع Person است. این رابطه باید از نظر منطقی و معنایی درست باشد.

### مثال

**مانند موجودیت Person به عنوان کلاس پایه و ارث‌بری کلاس‌های Employee یا Teacher یا Student از آن:**

```csharp
// Base Class
public class Person
{
    public string Name { get; set; }
    public DateTime BirthDate { get; set; }
    
    // محاسبه صحیح سن - در نظر گرفتن اینکه آیا تولد امسال رخ داده یا نه
    public int Age
    {
        get
        {
            var today = DateTime.Today;
            var age = today.Year - BirthDate.Year;
            if (BirthDate.Date > today.AddYears(-age)) age--;
            return age;
        }
    }
    
    public void Walk()
    {
        Console.WriteLine($"{Name} is walking");
    }
}

// Derived Classes
public class Employee : Person
{
    public string EmployeeId { get; set; }
    public decimal Salary { get; set; }
}

public class Teacher : Person
{
    public string Subject { get; set; }
    public void Teach()
    {
        Console.WriteLine($"{Name} is teaching {Subject}");
    }
}

public class Student : Person
{
    public string StudentId { get; set; }
    public void Study()
    {
        Console.WriteLine($"{Name} is studying");
    }
}
```

### محدودیت‌های Inheritance در C#

**در C# یک کلاس می‌تواند فقط از یک کلاس دیگر ارث‌بری کند:**

```csharp
// ✅ Single Inheritance
public class Student : Person { }

// ❌ Multiple Inheritance (مستقیم) - Compile Error
// public class Student : Person, Teacher { }

// ✅ Multiple Inheritance با Interface
public interface IWorker { }
public interface IStudent { }
public class Person : IWorker, IStudent { } // OK
```

**برای Multiple Inheritance، بقیه کلاس‌ها می‌بایست Interface باشند.**

### انواع Inheritance

#### 1. Multilevel Inheritance

**کلاس‌ها به صورت زنجیروار از هم ارث‌بری می‌کنند:**

```csharp
// A از B و C از A
public class Animal { }
public class Mammal : Animal { }
public class Dog : Mammal { }
```

#### 2. Hierarchical Inheritance

**چند کلاس از یک Base Class ارث‌بری می‌کنند:**

```csharp
// چند کلاس از Person ارث‌بری می‌کنند
public class Person { }
public class Employee : Person { }
public class Teacher : Person { }
public class Student : Person { }
```

### Sealed Classes

**اگر بخواهیم از کلاسی ارث‌بری نشود، می‌توان از `sealed` استفاده کرد:**

```csharp
public sealed class String { } // نمی‌توان از String ارث‌بری کرد
```

**کاربردهای `sealed`**:
- **امنیت**: جلوگیری از تغییر رفتار کلاس‌های حیاتی (مثل `String`)
- **Performance**: کامپایلر می‌تواند بهینه‌سازی‌های بیشتری انجام دهد (مثل درlining متدهای virtual)
- **طراحی**: نشان دادن اینکه این کلاس برای ارث‌بری طراحی نشده است

### Composition vs Inheritance

**سوال کلیدی**: چه زمانی از Inheritance استفاده کنیم و چه زمانی از Composition؟

**قاعده کلی**: "Favor Composition over Inheritance" - در صورت امکان، Composition را ترجیح دهید.

#### Inheritance (is-a relationship)

**استفاده کنید وقتی**:
- رابطه واقعاً "is-a" است (مثل `Dog : Animal`)
- نیاز به Polymorphism دارید
- رفتار مشترک واقعاً مشترک است و تغییر نمی‌کند
- Base Class برای ارث‌بری طراحی شده است

**استفاده نکنید وقتی**:
- فقط می‌خواهید کد را reuse کنید (این دلیل کافی نیست)
- رابطه "has-a" است نه "is-a"
- ممکن است در آینده نیاز به تغییر رفتار Base Class داشته باشید

#### Composition (has-a relationship)

**مثال**:

```csharp
// ❌ Bad: استفاده نادرست از Inheritance
public class Car : Engine { } // Car یک Engine نیست!

// ✅ Good: استفاده از Composition
public class Car
{
    private readonly Engine _engine; // Car یک Engine دارد
    
    public Car(Engine engine)
    {
        _engine = engine;
    }
    
    public void Start()
    {
        _engine.Start();
    }
}
```

**مزایای Composition**:
- **انعطاف‌پذیری**: می‌توانید Engine را در runtime تغییر دهید
- **Loose Coupling**: Car به Engine وابسته است اما به کلاس خاصی از Engine وابسته نیست
- **Testability**: می‌توانید Mock Engine را inject کنید
- **Single Responsibility**: هر کلاس مسئولیت خودش را دارد

**مثال واقعی در Production**:

```csharp
// ❌ Anti-Pattern: Stack<T> : List<T>
// Stack یک List نیست! این Inheritance نادرست است.

// ✅ Good: استفاده از Composition
public class Stack<T>
{
    private readonly List<T> _items = new List<T>();
    
    public void Push(T item) => _items.Add(item);
    public T Pop()
    {
        var item = _items[_items.Count - 1];
        _items.RemoveAt(_items.Count - 1);
        return item;
    }
}
```

---

## 2. Encapsulation (کپسوله‌سازی)

### تعریف

Encapsulation به معنی جمع‌آوری داده و رفتارهای مرتبط در یک کلاس و کنترل دسترسی به آنها از طریق Access Modifiers است. این اصل دو جنبه اصلی دارد:

1. **Bundling**: جمع‌آوری داده و رفتار در یک واحد (کلاس)
2. **Information Hiding**: مخفی‌سازی جزئیات پیاده‌سازی و کنترل دسترسی

### دو جنبه اصلی Encapsulation

#### 1. Bundling (جمع‌آوری در یک واحد)

**تمام رفتارها و ویژگی‌های مرتبط با یک موجودیت باید در یک کلاس جمع‌آوری شوند:**

```csharp
public class Car
{
    // تمام ویژگی‌های Car در یک کلاس
    public string Brand { get; set; }
    public int Speed { get; private set; }
    
    public void Start() { }
    public void Stop() { }
    public void Accelerate() { }
}
```

**چرا Bundling مهم است؟**

- **Cohesion**: کدهای مرتبط در کنار هم هستند
- **Maintainability**: تغییرات در یک موجودیت در یک مکان انجام می‌شود
- **Understanding**: درک کد آسان‌تر می‌شود

#### 2. Information Hiding (مخفی‌سازی اطلاعات)

**Information Hiding به معنی کنترل دسترسی به داده و رفتارهای داخلی کلاس است:**

- داده‌ها و متدهای داخلی که جزئیات پیاده‌سازی هستند باید مخفی بمانند
- فقط API عمومی (Public Interface) باید در دسترس باشد
- با Access Modifiers (`private`, `protected`, `internal`, `public`) دسترسی را کنترل می‌کنیم

**مثال: عملکرد افزایش سرعت در ماشین باید با انجام پروسه فشار دادن پدال انجام شود و نباید مستقیماً به فیلد سرعت دسترسی داشت:**

```csharp
public class Car
{
    private int _speed = 0;
    
    // ❌ نباید مستقیماً Speed را تغییر داد
    // public int Speed { get; set; }
    
    // ✅ باید از متد استفاده کرد
    public void PressPedal()
    {
        _speed += 10; // کنترل شده
    }
    
    public int GetSpeed() => _speed;
}
```

#### 2. Information Hiding (مخفی‌سازی اطلاعات)

**که به وسیله همان Access Modifiers کنترل می‌شوند:**

**برای مثال در یک موجودیت دارای یک Attribute `BirthDay` هستیم و در همان موجودیت دارای Attribute `Age` هم هستیم:**

- زمانی که تاریخ تولد کاربر را می‌گیریم، به صورت کنترل شده سن کاربر را محاسبه می‌کنیم و قرار می‌دهیم
- لازم نیست و نباید کاربر بتواند سن را تغییر دهد
- پس Attribute آن را `Private Set` قرار می‌دهیم

```csharp
public class Person
{
    public DateTime BirthDate { get; set; }
    
    // Age فقط Read-Only است
    public int Age
    {
        get
        {
            return DateTime.Now.Year - BirthDate.Year;
        }
        // Private Set - نمی‌توان مستقیماً Age را تغییر داد
        private set { }
    }
    
    // یا بهتر:
    public int Age => DateTime.Now.Year - BirthDate.Year; // Read-Only Property
}
```

### Access Modifiers در C#

Access Modifiers سطح دسترسی به اعضای کلاس را تعیین می‌کنند:

```csharp
public class Example
{
    // Public: دسترسی از هر جایی
    public int PublicProperty { get; set; }
    
    // Private: فقط داخل همین کلاس
    private int _privateField;
    
    // Protected: کلاس خودش و کلاس‌های فرزند (در همان Assembly یا Assembly دیگر)
    protected int ProtectedProperty { get; set; }
    
    // Internal: فقط در همان Assembly
    internal int InternalProperty { get; set; }
    
    // Protected Internal: Protected OR Internal (Union)
    protected internal int ProtectedInternalProperty { get; set; }
    
    // Private Protected: Protected AND Internal (Intersection) - فقط در همان Assembly و کلاس‌های فرزند
    private protected int PrivateProtectedProperty { get; set; }
}
```

**Best Practice در Production**:

1. **Principle of Least Privilege**: کمترین سطح دسترسی لازم را بدهید
2. **Fields باید private باشند**: از Properties برای دسترسی استفاده کنید
3. **Public API را محدود نگه دارید**: هر Public Member یک تعهد است که باید حفظ شود

**مثال واقعی**:

```csharp
// ❌ Bad: Field عمومی
public class BankAccount
{
    public decimal Balance; // هر کسی می‌تواند مستقیماً تغییر دهد!
}

// ✅ Good: Encapsulation صحیح
public class BankAccount
{
    private decimal _balance;
    
    public decimal Balance => _balance; // Read-only از بیرون
    
    public void Deposit(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Amount must be positive");
        _balance += amount;
    }
    
    public bool Withdraw(decimal amount)
    {
        if (amount <= 0 || amount > _balance)
            return false;
        _balance -= amount;
        return true;
    }
}
```

---

## 3. Abstraction (انتزاع)

### تعریف

**تمام پیچیدگی‌ها از نظر کاربر مخفی می‌شوند.**

### تفاوت با Encapsulation

**از نظر مفهومی، تفاوت آن با Encapsulation در این است که:**

- **Abstraction**: بر روی مخفی کردن پیچیدگی پیاده‌سازی (Implementation Hiding) تمرکز دارد
- **Encapsulation**: بر روی محافظت از داده (Data Protection) تمرکز دارد

**Abstraction با ایجاد انتزاعی از کلاس پیاده‌سازی این کار را می‌کند.**

### هدف

Abstraction برای ساده‌سازی ارتباط بین موجودیت‌ها و کاهش وابستگی‌ها (Coupling) استفاده می‌شود. با مخفی کردن جزئیات پیاده‌سازی، کلاینت‌ها فقط با Contract (قرارداد) کار می‌کنند و به پیاده‌سازی خاص وابسته نمی‌شوند.

**تفاوت با Encapsulation**: 
- **Encapsulation**: محافظت از داده و کنترل دسترسی (Data Protection)
- **Abstraction**: مخفی کردن پیچیدگی پیاده‌سازی (Implementation Hiding)

**نکته مهم**: Abstraction مستقیماً برای امنیت استفاده نمی‌شود، اما با کاهش وابستگی‌ها، امنیت را به صورت غیرمستقیم بهبود می‌بخشد.

### روش‌های پیاده‌سازی

**که به وسیله Abstract یا Interface پیاده‌سازی می‌شوند:**

#### Interface

**انتزاع به وسیله Interface به معنی این است که با قرارداد مد نظر من می‌توانی از امکانات این سرویس استفاده کنی:**

```csharp
public interface ILogger
{
    void Log(string message);
}

public class FileLogger : ILogger
{
    public void Log(string message)
    {
        // پیاده‌سازی - پیچیدگی مخفی است
        File.WriteAllText("log.txt", message);
    }
}

public class DatabaseLogger : ILogger
{
    public void Log(string message)
    {
        // پیاده‌سازی متفاوت - پیچیدگی مخفی است
        // Database operations
    }
}

// استفاده - فقط Interface را می‌بینیم
ILogger logger = new FileLogger();
logger.Log("Hello"); // پیچیدگی مخفی است
```

#### Abstract Class

**Abstract یک پله جلوتر می‌رود و امکان پیاده‌سازی یک روند Method را به کاربر می‌دهد:**

```csharp
public abstract class Animal
{
    // Abstract Method - باید پیاده‌سازی شود
    public abstract void MakeSound();
    
    // Concrete Method - پیاده‌سازی شده
    public void Sleep()
    {
        Console.WriteLine("Sleeping...");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

public class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow!");
    }
}
```

### Interface vs Abstract Class: چه زمانی کدام را استفاده کنیم؟

این یکی از سوالات رایج در مصاحبه‌ها است. انتخاب بین Interface و Abstract Class به نیازهای طراحی بستگی دارد:

#### Interface

**استفاده کنید وقتی**:
- فقط Contract (قرارداد) می‌خواهید بدون پیاده‌سازی
- چندین کلاس غیرمرتبط باید یک رفتار مشترک داشته باشند
- نیاز به Multiple Inheritance دارید (C# فقط Multiple Interface Inheritance را پشتیبانی می‌کند)
- می‌خواهید Contract را از پیاده‌سازی جدا کنید (مثل Dependency Injection)

**مثال**:

```csharp
// چندین کلاس غیرمرتبط که همه می‌توانند Log کنند
public interface ILogger
{
    void Log(string message);
}

public class FileLogger : ILogger { }
public class DatabaseLogger : ILogger { }
public class EmailService : ILogger { } // حتی EmailService هم می‌تواند Log کند
```

#### Abstract Class

**استفاده کنید وقتی**:
- کلاس‌های مرتبط (با رابطه is-a) دارید که رفتار مشترک دارند
- نیاز به پیاده‌سازی پیش‌فرض برای برخی متدها دارید
- می‌خواهید از تکرار کد جلوگیری کنید
- نیاز به Constructor، Fields، یا Properties مشترک دارید

**مثال**:

```csharp
// همه حیوانات رفتار مشترک دارند (Sleep) اما MakeSound متفاوت است
public abstract class Animal
{
    protected string Name { get; set; } // Field مشترک
    
    public Animal(string name) // Constructor
    {
        Name = name;
    }
    
    public void Sleep() // پیاده‌سازی مشترک
    {
        Console.WriteLine($"{Name} is sleeping");
    }
    
    public abstract void MakeSound(); // باید پیاده‌سازی شود
}
```

#### مقایسه در Production

| ویژگی | Interface | Abstract Class |
|-------|-----------|----------------|
| پیاده‌سازی متد | ❌ (قبل از C# 8.0) | ✅ (Concrete Methods) |
| Default Implementation | ✅ (C# 8.0+) | ✅ |
| Fields/Properties | ❌ | ✅ |
| Constructor | ❌ | ✅ |
| Multiple Inheritance | ✅ | ❌ |
| Access Modifiers | ❌ (همه public) | ✅ |
| Performance | بهتر (کمتر Overhead) | کمی کندتر |

**Best Practice**: در اکثر موارد، Interface را ترجیح دهید مگر اینکه واقعاً نیاز به Abstract Class داشته باشید. این رویکرد به "Interface Segregation Principle" و "Dependency Inversion Principle" کمک می‌کند.

### فلسفه

Abstraction و Encapsulation هر دو برای ساده‌سازی و کاهش پیچیدگی استفاده می‌شوند، اما از زوایای مختلف:

- **Abstraction**: "چه کاری انجام می‌دهد؟" (What) - Contract را تعریف می‌کند
- **Encapsulation**: "چگونه انجام می‌دهد؟" (How) - پیاده‌سازی را مخفی می‌کند

هر دو اصل با هم باعث می‌شوند کد قابل نگهداری‌تر، قابل تست‌تر، و قابل توسعه‌تر شود.

---

## 4. Polymorphism (چندریختی)

### تعریف

Polymorphism به معنی توانایی استفاده از یک Interface یا Base Class برای ارجاع به انواع مختلف (Derived Classes) است. در زمان اجرا، متد مناسب بر اساس نوع واقعی Object فراخوانی می‌شود.

**دو نوع اصلی Polymorphism**:
1. **Compile-Time Polymorphism (Static)**: Method Overloading - کامپایلر در زمان کامپایل تصمیم می‌گیرد
2. **Run-Time Polymorphism (Dynamic)**: Method Overriding - در زمان اجرا تصمیم گرفته می‌شود

**مثال ساده**:

```csharp
// Base Class یا Interface
IAnimal animal = GetAnimal(); // می‌تواند Dog، Cat، یا هر نوع دیگری باشد

// در زمان اجرا، متد مناسب فراخوانی می‌شود
animal.MakeSound(); // اگر Dog باشد "Woof"، اگر Cat باشد "Meow"
```

**چرا Polymorphism مهم است؟**
- **Loose Coupling**: کد به نوع خاصی وابسته نیست
- **Extensibility**: می‌توانید انواع جدید اضافه کنید بدون تغییر کد موجود
- **Testability**: می‌توانید Mock Objects را استفاده کنید

### انواع Polymorphism

#### 1. Compile-Time Polymorphism: Method Overloading

**به پیاده‌سازی یک رفتار با ورودی‌های مختلف در تعداد و ترتیب و نوع، عمل Overloading صورت می‌گیرد:**

```csharp
public class Calculator
{
    // Overloading - تعداد پارامترها متفاوت
    public int Add(int a, int b)
    {
        return a + b;
    }
    
    public int Add(int a, int b, int c)
    {
        return a + b + c;
    }
    
    // Overloading - نوع پارامترها متفاوت
    public double Add(double a, double b)
    {
        return a + b;
    }
    
    // Overloading - ترتیب پارامترها متفاوت
    public void Print(string name, int age)
    {
        Console.WriteLine($"{name}, {age}");
    }
    
    public void Print(int age, string name)
    {
        Console.WriteLine($"{age}, {name}");
    }
}
```

#### 2. Run-Time Polymorphism: Method Overriding

**به پیاده‌سازی مجدد یک رفتار در کلاس فرزند (Derived Class) که آن رفتار یا متد در کلاس والد به صورت `Virtual` هست، عمل Overriding با همین کلیدواژه (`Override`) صورت می‌گیرد:**

```csharp
public class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Animal makes sound");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}

public class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Meow!");
    }
}

// استفاده
Animal animal = new Dog();
animal.MakeSound(); // "Woof!" - Run-Time Polymorphism
```

### Virtual vs Abstract Methods

**تفاوت کلیدی بین `virtual` و `abstract`:**

| ویژگی | Virtual Method | Abstract Method |
|-------|---------------|----------------|
| پیاده‌سازی در Base Class | ✅ دارد | ❌ ندارد |
| باید در Derived Class Override شود | ❌ اختیاری | ✅ اجباری |
| می‌تواند در کلاس معمولی باشد | ✅ | ❌ فقط در Abstract Class |
| می‌تواند در Abstract Class باشد | ✅ | ✅ |

**مثال**:

```csharp
public abstract class Animal
{
    // Abstract Method - باید پیاده‌سازی شود
    public abstract void MakeSound();
    
    // Virtual Method - می‌تواند Override شود (اختیاری)
    public virtual void Sleep()
    {
        Console.WriteLine("Sleeping...");
    }
    
    // Concrete Method - نمی‌تواند Override شود
    public void Breathe()
    {
        Console.WriteLine("Breathing...");
    }
}

public class Dog : Animal
{
    // باید پیاده‌سازی شود (Abstract)
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
    
    // می‌تواند Override شود (Virtual - اختیاری)
    public override void Sleep()
    {
        Console.WriteLine("Dog is sleeping...");
    }
    
    // Breathe نمی‌تواند Override شود (مگر با new که Bad Practice است)
}
```

**نکته مهم برای مصاحبه**: 
- از `virtual` استفاده کنید وقتی می‌خواهید پیاده‌سازی پیش‌فرض داشته باشید اما امکان Override را هم بدهید
- از `abstract` استفاده کنید وقتی می‌خواهید Derived Class حتماً پیاده‌سازی کند

### Liskov Substitution Principle (LSP)

**این یکی از اصول SOLID است که در مصاحبه‌های Senior بسیار پرسیده می‌شود.**

**تعریف**: اشیاء از یک Base Class باید بتوانند بدون تغییر در صحت برنامه، با اشیاء Derived Classes جایگزین شوند.

**به زبان ساده**: اگر `Dog : Animal` است، هر جا که از `Animal` استفاده می‌کنید، باید بتوانید `Dog` را جایگزین کنید و برنامه همچنان درست کار کند.

**مثال نقض LSP**:

```csharp
// ❌ Bad: نقض LSP
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    
    public int Area => Width * Height;
}

public class Square : Rectangle
{
    public override int Width
    {
        get => base.Width;
        set
        {
            base.Width = value;
            base.Height = value; // Square همیشه Width == Height
        }
    }
    
    public override int Height
    {
        get => base.Height;
        set
        {
            base.Height = value;
            base.Width = value; // Square همیشه Width == Height
        }
    }
}

// مشکل: این کد انتظار دارد Rectangle مستقل Width و Height داشته باشد
public void TestRectangle(Rectangle rect)
{
    rect.Width = 5;
    rect.Height = 4;
    Console.WriteLine(rect.Area); // انتظار: 20
}

// اما اگر Square را پاس دهیم:
TestRectangle(new Square()); // Area = 16 نه 20! رفتار غیرمنتظره
```

**راه‌حل**: از Interface یا Abstract Class استفاده کنید:

```csharp
// ✅ Good: استفاده از Interface
public interface IShape
{
    int Area { get; }
}

public class Rectangle : IShape
{
    public int Width { get; set; }
    public int Height { get; set; }
    public int Area => Width * Height;
}

public class Square : IShape
{
    public int Side { get; set; }
    public int Area => Side * Side;
}
```

**نکته مهم برای مصاحبه**: اگر در Polymorphism با مشکل مواجه شدید، احتمالاً LSP را نقض کرده‌اید. همیشه از خود بپرسید: "آیا Derived Class می‌تواند در همه جا جایگزین Base Class شود بدون تغییر رفتار برنامه؟"

### Method Hiding

**یک مفهوم دیگر به نام Method Hiding داریم که از کلمه `new` استفاده می‌کند:**

```csharp
public class BaseClass
{
    public void Method()
    {
        Console.WriteLine("Base Method");
    }
}

public class DerivedClass : BaseClass
{
    public new void Method() // Method Hiding
    {
        Console.WriteLine("Derived Method");
    }
}

// استفاده
BaseClass obj = new DerivedClass();
obj.Method(); // "Base Method" - ویژگی‌های پدر نمایش داده می‌شود
```

### Bad Practice: Overriding Concrete Methods

**در صورتی که متد در کلاس والد پیاده‌سازی دارد، پیاده‌سازی و تغییر عملکرد متد در کلاس فرزند یک Bad Practice است که نباید انجام شود:**

- عملاً مفهوم انتزاع و اشتراک را بین موجودیت‌های شما زیر سؤال می‌برد
- در این شرایط، شکستن کلاس والد به لایه‌های انتزاعی بیشتر حل کرد که لزوماً راه‌حل خوبی نیست

**راه‌حل‌های دیگر:**

1. **استفاده از Interface**
2. **Extension Method**
3. **Composition over Inheritance**

```csharp
// ❌ Bad Practice
public class BaseClass
{
    public void Method() // Concrete Method
    {
        // Implementation
    }
}

public class DerivedClass : BaseClass
{
    public new void Method() // Hiding - Bad Practice
    {
        // Different Implementation
    }
}

// ✅ Good Practice - استفاده از Interface
public interface IAction
{
    void Method();
}

public class BaseClass : IAction
{
    public void Method() { }
}

public class DerivedClass : IAction
{
    public void Method() { } // OK - از Interface
}
```

---

## مزایا و معایب OOP

### مزایا

1. **استفاده مجدد از کدها (INHERITANCE)**: با Inheritance می‌توانیم کد را Reuse کنیم و از تکرار جلوگیری کنیم
2. **انعطاف‌پذیری (POLYMORPHISM)**: با Polymorphism می‌توانیم انعطاف‌پذیری داشته باشیم و کد را بدون تغییر گسترش دهیم
3. **امنیت برنامه و داده (ENCAPSULATION)**: با Encapsulation داده‌ها محافظت می‌شوند و دسترسی کنترل شده است
4. **توسعه آسان**: کد سازمان‌یافته و قابل نگهداری است
5. **عیب‌یابی آسان**: با Modularity، پیدا کردن Bug آسان‌تر است
6. **قابلیت تست**: با Polymorphism و Dependency Injection، Unit Testing آسان‌تر می‌شود
7. **Team Collaboration**: با Separation of Concerns، تیم‌ها می‌توانند روی بخش‌های مختلف کار کنند

### معایب

1. **برای برنامه‌های بسیار کوچک اصلاً مناسب نیست**: Overhead زیاد برای برنامه‌های ساده - برای Script های کوچک، OOP می‌تواند Over-Engineering باشد
2. **پیچیدگی**: ممکن است برای برنامه‌های ساده پیچیده باشد - نیاز به درک مفاهیم بیشتری دارد
3. **Performance**: ممکن است کمی کندتر از Procedural Programming باشد - Virtual Method Calls و Object Creation Overhead دارد
4. **Over-Abstraction**: ممکن است بیش از حد انتزاعی شود و کد را پیچیده کند
5. **Inheritance Hierarchy**: سلسله مراتب پیچیده می‌تواند مشکل‌ساز شود (Diamond Problem در زبان‌هایی که Multiple Inheritance دارند)
6. **Memory Overhead**: هر Object دارای Overhead است (Object Header در .NET)

### Trade-offs در Production

**چه زمانی OOP مناسب است؟**
- برنامه‌های متوسط تا بزرگ
- نیاز به Maintainability و Extensibility
- کار تیمی
- نیاز به Testability

**چه زمانی OOP مناسب نیست؟**
- Script های کوچک (مثل Utility Scripts)
- Performance-Critical Code (مثل Game Engines - اما حتی در اینجا هم از OOP استفاده می‌شود)
- Functional Programming بهتر است (مثل Data Processing Pipelines)

---

## Dependency Injection با Polymorphism

### مثال عملی

**Dependency Injection using Constructor با استفاده از Polymorphism:**

این مثال نشان می‌دهد چگونه Polymorphism و Dependency Injection با هم کار می‌کنند تا کد انعطاف‌پذیر و قابل تست شود:

```csharp
public interface IAction
{
    void Action();
}

public class Sweep : IAction
{
    public void Sweeper()
    {
        Console.WriteLine("sweep");
    }
    
    public void Action()
    {
        this.Sweeper();
    }
}

public class Exhibit : IAction
{
    public void ExhibitMethod()
    {
        Console.WriteLine("exhibit");
    }
    
    public void Action()
    {
        this.ExhibitMethod();
    }
}

public class Carpet
{
    private readonly IAction[] _actions;
    
    public Carpet(params IAction[] actions)
    {
        _actions = actions;
    }
    
    public void Process()
    {
        foreach (var action in _actions)
        {
            action.Action(); // Polymorphism - نوع واقعی در Runtime مشخص می‌شود
        }
    }
}

// استفاده
class Program
{
    static void Main()
    {
        var sweep = new Sweep();
        var exhibit = new Exhibit();

        var carpet = new Carpet(sweep, exhibit); // Dependency Injection
        carpet.Process();
    }
}
```

### مزایای این رویکرد

1. **Loose Coupling**: `Carpet` به کلاس خاصی وابسته نیست، فقط به `IAction` Interface
2. **Testability**: می‌توانید Mock `IAction` را برای تست inject کنید
3. **Extensibility**: می‌توانید `IAction` جدید اضافه کنید بدون تغییر `Carpet`
4. **Polymorphism**: در Runtime، متد مناسب هر نوع فراخوانی می‌شود

### مثال واقعی در Production

```csharp
// مثال واقعی: Logging Service
public interface ILogger
{
    void Log(string message);
}

public class FileLogger : ILogger
{
    public void Log(string message) => File.AppendAllText("log.txt", message);
}

public class DatabaseLogger : ILogger
{
    public void Log(string message) => /* Database operation */;
}

public class EmailService
{
    private readonly ILogger _logger;
    
    public EmailService(ILogger logger) // Dependency Injection
    {
        _logger = logger;
    }
    
    public void SendEmail(string to, string subject)
    {
        _logger.Log($"Sending email to {to}"); // Polymorphism
        // Send email logic
    }
}

// استفاده - می‌توانید Logger را تغییر دهید بدون تغییر EmailService
var fileLogger = new FileLogger();
var emailService = new EmailService(fileLogger);
```

---

## Partial Classes

### تعریف

**کلاس‌های Partial کلاسی است که در یک Solution از آن چند تا داریم:**

- یک کلاس می‌تواند در چند فایل مختلف تعریف شود
- همه بخش‌ها باید با کلمه کلیدی `partial` مشخص شوند
- در زمان Compile، همه بخش‌ها به یک کلاس تبدیل می‌شوند
- نمی‌توانید عضو تکراری (متد، Property، Field) با همان signature داشته باشید

### مثال

```csharp
// File 1: Car.cs
public partial class Car
{
    public string Brand { get; set; }
    public void Start() { }
}

// File 2: Car.Engine.cs
public partial class Car
{
    public void Accelerate() { }
    public void Stop() { }
}

// در زمان Compile، این دو Partial Class به یک کلاس تبدیل می‌شوند
// می‌توانید از همه اعضا در هر دو فایل استفاده کنید
```

### کاربرد

- **Generated Code**: برای Code Generator (مثل Entity Framework) - کد تولید شده در یک فایل، کد دستی در فایل دیگر
- **Large Classes**: برای تقسیم کلاس‌های بزرگ به بخش‌های منطقی
- **Team Development**: برای کار تیمی روی یک کلاس (اما باید مراقب conflict باشید)

### مثال واقعی: Entity Framework

```csharp
// File 1: User.cs (Generated by EF)
public partial class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    // ... other properties from database
}

// File 2: User.Extensions.cs (Manual code)
public partial class User
{
    public string FullName => $"{FirstName} {LastName}";
    
    public bool IsActive()
    {
        return Status == "Active";
    }
}
```

**نکته مهم**: Partial Classes نباید برای پنهان کردن پیچیدگی استفاده شوند. اگر کلاس شما خیلی بزرگ است، بهتر است آن را به کلاس‌های کوچک‌تر تقسیم کنید (Single Responsibility Principle).

---

## Anti-Patterns و نکات مهم

### Switch Case و Type-Based Separation

**نکته: روتین کلاس‌هایی که نیاز به OOP دارند، استفاده از Switch Case و روی Property با ماهیت جدا‌سازی بر اساس Type می‌تواند باشد:**

```csharp
// ⚠️ Anti-Pattern - Switch Case بر اساس Type
public void Process(object entity)
{
    switch (entity)
    {
        case Employee emp:
            // Process employee
            break;
        case Teacher teacher:
            // Process teacher
            break;
        case Student student:
            // Process student
            break;
    }
}
```

**مشکلات این رویکرد:**

1. **Null Data**: در اینجا احتمال زیاد با داده‌های Null و گاهی اشتباه می‌توان رو به رو شد
2. **Database Normalization**: با این شرایط Database Normalize نمی‌تواند باشد
3. **Maintainability**: هر بار باید Switch Case را تغییر دهیم
4. **Open/Closed Principle**: نقض می‌شود - برای اضافه کردن نوع جدید باید کد موجود را تغییر دهیم
5. **Type Safety**: استفاده از `object` باعث از دست رفتن Type Safety می‌شود

**راه‌حل بهتر: استفاده از Polymorphism**

```csharp
// ✅ Good Practice - Polymorphism
public abstract class Person
{
    public abstract void Process();
}

public class Employee : Person
{
    public override void Process()
    {
        // Process employee
    }
}

public class Teacher : Person
{
    public override void Process()
    {
        // Process teacher
    }
}

// استفاده
Person person = GetPerson();
person.Process(); // Polymorphism - بدون Switch Case
```

**مزایای رویکرد Polymorphism:**

- **Open/Closed Principle**: می‌توانید نوع جدید اضافه کنید بدون تغییر کد موجود
- **Type Safety**: کامپایلر Type Checking انجام می‌دهد
- **Maintainability**: هر کلاس مسئولیت خودش را دارد
- **Testability**: می‌توانید هر کلاس را جداگانه تست کنید

### God Object Anti-Pattern

**کلاسی که مسئولیت‌های زیادی دارد و همه کارها را انجام می‌دهد:**

```csharp
// ❌ Bad: God Object
public class User
{
    public void Login() { }
    public void Logout() { }
    public void SendEmail() { }
    public void ProcessPayment() { }
    public void GenerateReport() { }
    public void UpdateProfile() { }
    // ... 50 متد دیگر
}
```

**مشکلات:**
- نقض Single Responsibility Principle
- Testing دشوار است
- Maintainability پایین
- High Coupling

**راه‌حل: تقسیم به کلاس‌های کوچک‌تر**

```csharp
// ✅ Good: Separation of Concerns
public class User { }
public class AuthenticationService { }
public class EmailService { }
public class PaymentService { }
public class ReportGenerator { }
```

### Anemic Domain Model

**کلاس‌هایی که فقط داده دارند و رفتار ندارند:**

```csharp
// ❌ Bad: Anemic Domain Model
public class BankAccount
{
    public decimal Balance { get; set; }
    // فقط داده، هیچ رفتار نیست
}

// منطق در کلاس دیگری
public class BankAccountService
{
    public void Withdraw(BankAccount account, decimal amount)
    {
        account.Balance -= amount; // منطق خارج از Domain Model
    }
}
```

**مشکلات:**
- Encapsulation نقض می‌شود
- منطق در جاهای مختلف پراکنده است
- Domain Model بی‌معنی می‌شود

**راه‌حل: Rich Domain Model**

```csharp
// ✅ Good: Rich Domain Model
public class BankAccount
{
    private decimal _balance;
    
    public decimal Balance => _balance;
    
    public void Withdraw(decimal amount)
    {
        if (amount <= 0 || amount > _balance)
            throw new InvalidOperationException();
        _balance -= amount; // منطق در Domain Model
    }
}
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. چهار اصل بنیادی OOP چیست؟

**پاسخ**:
- **Inheritance**: استفاده مجدد از کد
- **Encapsulation**: محافظت از داده
- **Abstraction**: مخفی کردن پیچیدگی
- **Polymorphism**: انعطاف‌پذیری

#### 2. تفاوت Abstraction و Encapsulation چیست؟

**پاسخ**:
- **Abstraction**: مخفی کردن پیچیدگی پیاده‌سازی (Implementation Hiding)
- **Encapsulation**: محافظت از داده (Data Protection)
- **Abstraction**: با Interface/Abstract
- **Encapsulation**: با Access Modifiers

#### 3. تفاوت Method Overloading و Overriding چیست؟

**پاسخ**:
- **Overloading**: Compile-Time، همان نام با پارامترهای مختلف
- **Overriding**: Run-Time، پیاده‌سازی مجدد متد Virtual
- **Overloading**: در همان کلاس
- **Overriding**: در کلاس فرزند

#### 4. Multiple Inheritance در C# چگونه است؟

**پاسخ**:
- یک کلاس فقط از یک کلاس می‌تواند ارث‌بری کند
- اما می‌تواند چند Interface را Implement کند
- **مثال**: `public class A : B, I1, I2 { }`

#### 5. Method Hiding چیست؟

**پاسخ**:
- استفاده از `new` برای مخفی کردن متد والد
- **تفاوت با Override**: در Override، متد والد Virtual است
- **Best Practice**: بهتر است از Interface استفاده شود

#### 6. مزایا و معایب OOP چیست؟

**پاسخ**:
- **مزایا**: Reusability، Flexibility، Security، Maintainability
- **معایب**: برای برنامه‌های کوچک مناسب نیست، پیچیدگی

### نکات مهم برای مصاحبه

1. **چهار اصل**: Inheritance، Encapsulation، Abstraction، Polymorphism
2. **تفاوت‌ها**: Abstraction vs Encapsulation، Overloading vs Overriding
3. **محدودیت‌ها**: Single Inheritance در C#
4. **Best Practices**: از Method Hiding پرهیز کنید
5. **مثال‌ها**: همیشه با مثال توضیح دهید

### سوالات پیشرفته (Senior Level)

#### 7. Composition vs Inheritance - چه زمانی کدام را استفاده کنیم؟

**پاسخ Senior-Level**:
- **Inheritance**: وقتی رابطه واقعاً "is-a" است و رفتار مشترک ثابت است
- **Composition**: وقتی رابطه "has-a" است یا نیاز به انعطاف‌پذیری بیشتر داریم
- **قاعده**: "Favor Composition over Inheritance"
- **مثال**: `Stack<T>` نباید از `List<T>` ارث‌بری کند، بلکه باید از آن استفاده کند (Composition)

#### 8. Liskov Substitution Principle چیست و چرا مهم است؟

**پاسخ**:
- Derived Class باید بتواند در همه جا جایگزین Base Class شود بدون تغییر رفتار
- **مثال نقض**: `Square : Rectangle` - Square نمی‌تواند مستقل Width و Height داشته باشد
- **راه‌حل**: استفاده از Interface یا Abstract Class مناسب

#### 9. Interface vs Abstract Class - چه زمانی کدام را استفاده کنیم؟

**پاسخ**:
- **Interface**: وقتی فقط Contract می‌خواهیم، Multiple Inheritance نیاز داریم، یا کلاس‌های غیرمرتبط
- **Abstract Class**: وقتی رفتار مشترک داریم، نیاز به Constructor/Fields داریم، یا کلاس‌های مرتبط هستند
- **Best Practice**: در اکثر موارد Interface را ترجیح دهید

#### 10. Virtual vs Abstract Method - تفاوت چیست؟

**پاسخ**:
- **Virtual**: متد در Base Class پیاده‌سازی دارد اما می‌تواند Override شود
- **Abstract**: متد در Base Class پیاده‌سازی ندارد و باید در Derived Class پیاده‌سازی شود
- **Abstract Method**: فقط در Abstract Class می‌تواند باشد
- **Virtual Method**: می‌تواند در کلاس معمولی هم باشد

---

## منابع

- **پلی‌لیست مبانی شی‌گرایی از Ardiland**: https://www.youtube.com/watch?v=RVSuXUodOyw&list=PLx2zI6hCyJvzpjb5pLDL0VefNNO7IJK0D&index=1
- **مثال عملی املاک**: https://youtu.be/n-Zqpes7QGw?si=TPYiVD5x93qak6Xa

---

## خلاصه

### چهار اصل بنیادی OOP

1. **Inheritance**: استفاده مجدد از کد
2. **Encapsulation**: محافظت از داده
3. **Abstraction**: مخفی کردن پیچیدگی
4. **Polymorphism**: انعطاف‌پذیری

### مفاهیم کلیدی

- **Class**: الگو برای ساخت Object
- **Object**: نمونه از Class با State
- **State**: وضعیت Object در لحظه

### Best Practices

- از Method Hiding پرهیز کنید
- از Interface برای Abstraction استفاده کنید
- از Dependency Injection استفاده کنید
- Single Responsibility را رعایت کنید

**موفق باشید!** 🚀
