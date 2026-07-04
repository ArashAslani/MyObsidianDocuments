
# مبانی برنامه‌نویسی شی‌گرا (OOP) در C#

برنامه‌نویسی شی‌گرا (Object-Oriented Programming) یکی از **مهم‌ترین پارادایم‌های** برنامه‌نویسی است. درک کامل چهار اصل بنیادی OOP (Inheritance، Encapsulation، Abstraction، Polymorphism) برای هر توسعه‌دهنده **ضروری** است و در مصاحبه‌ها بسیار پرسیده می‌شود.

---

## فهرست مطالب

1. [مقدمه: OOP چیست؟](#مقدمه-oop-چیست)
2. [مفاهیم پایه: Class و Object](#مفاهیم-پایه-class-و-object)
3. [چهار اصل بنیادی OOP](#چهار-اصل-بنیادی-oop)
4. [1. Inheritance (ارث‌بری)](#1-inheritance-ارثبری)
5. [2. Encapsulation (کپسوله‌سازی)](#2-encapsulation-کپسولهسازی)
6. [3. Abstraction (انتزاع)](#3-abstraction-انتزاع)
7. [4. Polymorphism (چندریختی)](#4-polymorphism-چندریختی)
8. [مزایا و معایب OOP](#مزایا-و-معایب-oop)
9. [Dependency Injection با Polymorphism](#dependency-injection-با-polymorphism)
10. [Partial Classes](#partial-classes)
11. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: OOP چیست؟

### تعریف

**OOP به معنی طراحی شی‌گرا است که تمرکز آن روی مدل یا اشیاء است.**

**اشیاء شبیه‌سازی چیزهایی در دنیای واقعی است.**

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

**این ویژگی باعث عدم تکرار فیلدها و ویژگی‌های رفتاری مشترک در Class پایه می‌شود.**

**بحث و فلسفه ارث‌بری روی Reusability است.**

### مثال

**مانند موجودیت Person به عنوان کلاس پایه و ارث‌بری کلاس‌های Employee یا Teacher یا Student از آن:**

```csharp
// Base Class
public class Person
{
    public string Name { get; set; }
    public DateTime BirthDate { get; set; }
    public int Age => DateTime.Now.Year - BirthDate.Year;
    
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

---

## 2. Encapsulation (کپسوله‌سازی)

### تعریف

**به معنی جمع‌آوری ویژگی‌های مشترک در یک ظرف (کلاس یا متد) و دادن دسترسی کنترل شده به بیرون:**

### سه ویژگی Encapsulation

#### 1. Capsulation (کپسوله‌سازی)

**تمام رفتارها و ویژگی‌های مشترک یک موجودیت باید در یک ظرف جمع‌آوری شوند:**

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

**Encapsulation باعث ایجاد امنیت در کلاس ما می‌شود:**

- برای مثال یک سری متد و ویژگی وجود دارد که برای مصرف داخلی خود کلاس است
- با Access Modifiers‌های `Private` و `Protected` دسترسی به این ویژگی‌ها را کنترل می‌کنیم

**مثال: عملکرد افزایش سرعت در ماشین باید با انجام پروسه فشار دادن پدال انجام شود و نباید مستقیماً به متد افزایش سرعت دسترسی داشت:**

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

### Access Modifiers

```csharp
public class Example
{
    public int PublicProperty { get; set; } // دسترسی عمومی
    private int _privateField; // فقط داخل کلاس
    protected int ProtectedProperty { get; set; } // کلاس و فرزندان
    internal int InternalProperty { get; set; } // فقط در همان Assembly
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

**Abstraction برای ساده‌سازی ارتباط بین موجودیت‌ها و کم کردن وابستگی‌ها کمک می‌کند و نه امنیت پروژه.**

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

### فلسفه

**بحث و فلسفه کپسوله‌سازی و انتزاع روی دید انتزاعی و ایجاد محدودیت و داشتن سادگی و امنیت است.**

---

## 4. Polymorphism (چندریختی)

### تعریف

**یک قابلیت به متد می‌دهند که بتوانند به چند ریخت استفاده شوند.**

**ما یک Abstract یا Interface یا ویژگی‌های توافق شده و مشترک داریم که می‌تواند به شکل‌های متفاوتی طبق نیاز ما در موقعیت‌های متفاوت پیاده‌سازی شوند.**

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

1. **استفاده مجدد از کدها (INHERITANCE)**: با Inheritance می‌توانیم کد را Reuse کنیم
2. **انعطاف‌پذیری (POLYMORPHISM)**: با Polymorphism می‌توانیم انعطاف‌پذیری داشته باشیم
3. **امنیت برنامه و داده (ENCAPSULATION)**: با Encapsulation داده‌ها محافظت می‌شوند
4. **توسعه آسان**: کد سازمان‌یافته و قابل نگهداری
5. **عیب‌یابی آسان**: با Modularity، پیدا کردن Bug آسان‌تر است

### معایب

1. **برای برنامه‌های بسیار کوچک اصلاً مناسب نیست**: Overhead زیاد برای برنامه‌های ساده
2. **پیچیدگی**: ممکن است برای برنامه‌های ساده پیچیده باشد
3. **Performance**: ممکن است کمی کندتر از Procedural Programming باشد

---

## Dependency Injection با Polymorphism

### مثال عملی

**Dependency Injection using Constructor با استفاده از Polymorphism:**

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
            action.Action(); // Polymorphism
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

---

## Partial Classes

### تعریف

**کلاس‌های Partial کلاسی است که در یک Solution از آن چند تا داریم:**

- مانند هر کلاس واحدی نمی‌توانیم داخل چند متد و متغیر مشابه داشته باشیم
- در زمان Compile، دو کلاس تبدیل به یک کلاس می‌شوند

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
```

### کاربرد

- **Generated Code**: برای Code Generator (مثل Entity Framework)
- **Large Classes**: برای تقسیم کلاس‌های بزرگ
- **Team Development**: برای کار تیمی روی یک کلاس

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
