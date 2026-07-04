## 1️⃣ Interview Relevance Summary

**چرا این موضوع در مصاحبه‌ها مهم است؟**

مقایسه‌ی **Abstract Class vs Interface** یکی از کلاسیک‌ترین و در عین حال _فریبنده‌ترین_ سوال‌های مصاحبه ASP.NET Core است.  
تقریباً همه‌ی Junior/Midها تفاوت‌های ظاهری را می‌دانند، اما:

- **Senior**‌ها دلیل طراحی، اثر روی تست‌پذیری، معماری، توسعه‌پذیری و backward compatibility را توضیح می‌دهند.
    
- این مبحث مستقیماً به:
    
    - Dependency Injection
        
    - Unit Testing / Mocking
        
    - Clean Architecture
        
    - Open/Closed Principle
        
    - Package versioning  
        گره خورده است.
        

اگر این فایل درست نوشته نشود، **اعتبار فنی نویسنده در سطح بین‌المللی زیر سؤال می‌رود**.

---

## 2️⃣ Structured Summary (خلاصه‌ی فنی و دقیق)

- **Abstract Class** زمانی استفاده می‌شود که:
    
    - رفتار مشترک واقعی داریم
        
    - بخشی از پیاده‌سازی ثابت و غیرقابل تغییر است
        
    - کنترل بیشتری روی وراثت می‌خواهیم
        
- **Interface** زمانی استفاده می‌شود که:
    
    - فقط قرارداد (Contract) مهم است
        
    - تست‌پذیری و انعطاف معماری اولویت دارد
        
    - وابستگی‌ها باید قابل جایگزینی باشند
        
    - طراحی برای آینده و تغییرات مداوم انجام می‌شود
        

---

## 3️⃣ Revised Content (Senior-Level)

### 🔹 Abstraction vs Interface — نگاه یک مهندس ارشد

در دنیای واقعی نرم‌افزار، **Abstract Class و Interface ابزار هستند، نه هدف**.  
انتخاب بین آن‌ها باید از روی _نیاز طراحی_ انجام شود، نه حفظ کردن تفاوت‌های سطحی.

---

### 🔹 Abstract Class چیست و چرا وجود دارد؟

کلاس **Abstract** می‌تواند هم **تعریف رفتار** داشته باشد و هم **پیاده‌سازی مشترک**.

```csharp
public abstract class PaymentProcessor
{
    protected decimal TaxRate = 0.09m;

    public abstract void Process(decimal amount);

    public virtual decimal CalculateTax(decimal amount)
        => amount * TaxRate;
}
```

**ویژگی‌های کلیدی:**

- می‌تواند:
    
    - متد abstract داشته باشد
        
    - متد پیاده‌سازی‌شده داشته باشد
        
    - فیلد، property و protected logic داشته باشد
        
- **Constructor دارد**
    
- از **multiple inheritance پشتیبانی نمی‌کند**
    
- برای زمانی مناسب است که:
    
    - بخشی از منطق _نباید_ در کلاس‌های فرزند تغییر کند
        
    - invariantهای مهمی داریم
        

📌 **دید مهندس ارشد:**  
Abstract یعنی _کنترل رفتار مشترک_، نه فقط اشتراک کد.

---

### 🔹 Interface چیست و چرا در ASP.NET Core حیاتی است؟

Interface فقط **قرارداد** است، نه پیاده‌سازی (با یک استثنای مهم در C# 8+).

```csharp
public interface IEmailSender
{
    Task SendAsync(string to, string message);
}
```

**ویژگی‌های کلیدی:**

- فقط تعریف رفتار
    
- **بدون constructor**
    
- پیاده‌سازی اجباری
    
- پشتیبانی از **multiple inheritance**
    
- پایه‌ی اصلی:
    
    - Dependency Injection
        
    - Mocking
        
    - Testability
        

📌 **در ASP.NET Core**:  
تقریباً تمام سرویس‌ها بر اساس Interface تزریق می‌شوند، نه Abstract Class.

---

### 🔹 Default Implementation در Interface (C# 8+)

از نسخه 8 به بعد، Interface می‌تواند **پیاده‌سازی پیش‌فرض** داشته باشد:

```csharp
public interface ICache
{
    void Set(string key, object value);

    object Get(string key)
        => null;
}
```

### ❗ چرا این قابلیت اضافه شد؟

برای **Package Versioning**.

سناریوی واقعی:

- یک پکیج public منتشر شده
    
- صدها consumer دارد
    
- می‌خواهیم متد جدید اضافه کنیم
    
- اگر متد abstract اضافه کنیم → breaking change
    

📌 راه‌حل:

- افزودن متد با default implementation
    
- استفاده از **upcasting**
    
- بدون شکستن backward compatibility
    

⚠️ **هشدار مهندس ارشد:**  
این قابلیت برای _framework و library design_ است، نه کدنویسی روزمره‌ی اپلیکیشن.

---

### 🔹 مقایسه‌ی دقیق (نه حفظی)

|ویژگی|Abstract Class|Interface|
|---|---|---|
|پیاده‌سازی متد|دارد|ندارد (جز default)|
|Constructor|دارد|ندارد|
|Multiple inheritance|❌|✅|
|State (field)|دارد|ندارد|
|Testability|متوسط|عالی|
|DI Friendly|متوسط|عالی|
|انعطاف معماری|کمتر|بیشتر|

---

### 🔹 تست‌نویسی (جایی که Interface می‌درخشد)

```csharp
public class OrderService
{
    private readonly IPaymentGateway _gateway;

    public OrderService(IPaymentGateway gateway)
    {
        _gateway = gateway;
    }
}
```

در تست:

```csharp
var mock = new Mock<IPaymentGateway>();
```

📌 **نکته مصاحبه‌ای مهم:**  
Mock کردن Abstract Class پیچیده‌تر، شکننده‌تر و وابسته به implementation است.

---

## 4️⃣ Key Interview Talking Points

- Interface = Contract, Abstract = Partial Behavior
    
- Interface پایه‌ی DI و Testability
    
- Abstract برای invariantها و shared logic
    
- Default Interface Method برای versioning است
    
- ASP.NET Core عمداً Interface-centric طراحی شده
    

---

## 5️⃣ Common Mistakes & Red Flags

❌ «Interface فقط برای چند وراثت است»  
❌ استفاده از Abstract به جای Interface در DI  
❌ استفاده‌ی بی‌رویه از default method  
❌ ناتوانی در توضیح اثر روی تست‌نویسی  
❌ پاسخ حفظی بدون سناریوی واقعی

---

## 6️⃣ When to Use / When NOT to Use

### ✅ Interface استفاده کن وقتی:

- تست‌پذیری مهم است
    
- معماری Clean یا Hexagonal داری
    
- تغییر پیاده‌سازی محتمل است
    
- کد public یا library می‌نویسی
    

### ⚠️ Abstract استفاده کن وقتی:

- منطق مشترک واقعی داری
    
- کنترل روی رفتار حیاتی است
    
- hierarchy مشخص و پایدار است
    
# 1️⃣ Interview Relevance Summary (Pass 2)

در این مرحله، مصاحبه‌کننده دیگر دنبال تعریف نیست.  
می‌خواهد بفهمد:

- آیا **تصمیم طراحی** بلدی یا فقط syntax؟
    
- آیا می‌فهمی چرا ASP.NET Core این‌قدر **Interface-centric** است؟
    
- آیا می‌توانی بین **انعطاف، پیچیدگی، هزینه تغییر و تست‌پذیری** تعادل برقرار کنی؟
    

اینجا جایی است که Midها حذف می‌شوند و Seniorها دیده می‌شوند.

---

# 2️⃣ Revised Content (Senior-Level – Deep Dive)

## 🔹 نگاه معماری: Interface و Abstract ابزار کنترل «تغییر»

> اصل کلیدی:  
> **در نرم‌افزار، مشکل اصلی پیاده‌سازی نیست؛ تغییر است.**

### سؤال اصلی مهندس ارشد:

> «کدام بخش سیستم احتمال تغییر دارد و کدام نه؟»

---

## 🔹 سناریوی واقعی 1: سیستم پرداخت (Fintech / E-Commerce)

### Interface-centric design (انتخاب درست)

`public interface IPaymentProvider {     Task<PaymentResult> PayAsync(PaymentRequest request); }`

پیاده‌سازی‌ها:

`StripePaymentProvider ZarinpalPaymentProvider PayPalPaymentProvider`

### چرا Interface؟

- Providerها دائماً عوض می‌شوند
    
- تست بدون اتصال به gateway واقعی
    
- Feature toggle
    
- Failover
    

📌 **سیگنال Senior در مصاحبه:**

> «من نمی‌خواهم OrderService بداند Stripe چطور کار می‌کند.»

---

## 🔹 اگر Abstract Class استفاده می‌کردیم چه می‌شد؟

`public abstract class PaymentProviderBase {     protected ILogger Logger;      public abstract Task<PaymentResult> PayAsync(PaymentRequest request); }`

### مشکل؟

- coupling بالا
    
- inheritance constraint
    
- تست سخت‌تر
    
- تغییر base class = ریسک بالا
    

❗ **Hiring Manager Red Flag:**  
استفاده از Abstract فقط برای share code بدون تحلیل تغییرات آینده.

---

## 🔹 سناریوی واقعی 2: Pipeline در ASP.NET Core

Middlewareها:

`public interface IMiddleware {     Task InvokeAsync(HttpContext context, RequestDelegate next); }`

### چرا Interface و نه Abstract؟

- pipeline پویا
    
- composability
    
- lifetime management
    
- minimal coupling
    

📌 ASP.NET Core عمداً:

- abstract base class نداد
    
- حتی helper logic را extension method کرد
    

---

## 🔹 Default Interface Method — واقعاً کِی استفاده می‌شود؟

### مثال واقعی: Library public

فرض کن NuGet package داری:

`public interface ISerializer {     string Serialize(object obj); }`

بعد از 2 سال:

`string Serialize(object obj, SerializerOptions options);`

### گزینه‌های بد:

❌ breaking change  
❌ major version bump

### گزینه‌ی حرفه‌ای:

`public interface ISerializer {     string Serialize(object obj);      string Serialize(object obj, SerializerOptions options)         => Serialize(obj); }`

📌 **این تصمیم مخصوص نویسنده framework است، نه مصرف‌کننده.**

---

## 🔹 سناریوی واقعی 3: Domain Layer (DDD)

### Domain Entity + Abstract

`public abstract class Entity {     public Guid Id { get; protected set; } }`

### چرا Abstract اینجا خوب است؟

- invariant
    
- identity
    
- منطق مشترک واقعی
    
- تغییرناپذیر
    

📌 **Domain ≠ Infrastructure**

---

## 🔹 تست‌نویسی: جایی که تصمیم تو لو می‌رود

### Interface → Test-friendly

`Mock<IEmailSender>`

### Abstract → Fragile Test

`Mock<EmailSenderBase> // نیاز به virtual`

📌 **Hiring Manager Insight:**  
کسی که از Interface استفاده می‌کند، به تست فکر کرده.

---

## 🔹 از دید OOP (نه شعاری)

### Interface

- Polymorphism خالص
    
- Contract-first
    
- LSP واقعی
    

### Abstract

- Template Method Pattern
    
- Shared behavior
    
- Controlled extensibility
    

---

# 3️⃣ Key Interview Talking Points (Pass 2)

- ASP.NET Core عمداً Interface-centric است
    
- Interface ابزار مدیریت تغییر است
    
- Abstract ابزار کنترل رفتار مشترک
    
- Default interface method برای versioning است
    
- DI + Testability بدون Interface عملاً بی‌معناست
    

---

# 4️⃣ Common Mistakes & Red Flags (Hiring Manager View)

🚩 استفاده از Abstract برای DI  
🚩 استفاده از Interface برای share code  
🚩 ناتوانی در توضیح «چرا»  
🚩 جواب‌های حفظی بدون سناریو  
🚩 استفاده‌ی بی‌رویه از inheritance

---

# 5️⃣ When to Use / When NOT to Use (Final)

## ✅ Interface استفاده کن وقتی:

- سیستم رشد می‌کند
    
- تیم بزرگ است
    
- تست مهم است
    
- remote / distributed team داری
    
- library یا API می‌نویسی
    

## ⚠️ Abstract استفاده کن وقتی:

- hierarchy پایدار است
    
- رفتار مشترک واقعی داری
    
- domain rule مهم است
    
- inheritance منطقی است، نه اجباری


# 1️⃣ Interview Relevance Summary (Pass 2)

در این مرحله، مصاحبه‌کننده دیگر دنبال تعریف نیست.  
می‌خواهد بفهمد:

- آیا **تصمیم طراحی** بلدی یا فقط syntax؟
    
- آیا می‌فهمی چرا ASP.NET Core این‌قدر **Interface-centric** است؟
    
- آیا می‌توانی بین **انعطاف، پیچیدگی، هزینه تغییر و تست‌پذیری** تعادل برقرار کنی؟
    

اینجا جایی است که Midها حذف می‌شوند و Seniorها دیده می‌شوند.

---

# 2️⃣ Revised Content (Senior-Level – Deep Dive)

## 🔹 نگاه معماری: Interface و Abstract ابزار کنترل «تغییر»

> اصل کلیدی:  
> **در نرم‌افزار، مشکل اصلی پیاده‌سازی نیست؛ تغییر است.**

### سؤال اصلی مهندس ارشد:

> «کدام بخش سیستم احتمال تغییر دارد و کدام نه؟»

---

## 🔹 سناریوی واقعی 1: سیستم پرداخت (Fintech / E-Commerce)

### Interface-centric design (انتخاب درست)

`public interface IPaymentProvider {     Task<PaymentResult> PayAsync(PaymentRequest request); }`

پیاده‌سازی‌ها:

`StripePaymentProvider ZarinpalPaymentProvider PayPalPaymentProvider`

### چرا Interface؟

- Providerها دائماً عوض می‌شوند
    
- تست بدون اتصال به gateway واقعی
    
- Feature toggle
    
- Failover
    

📌 **سیگنال Senior در مصاحبه:**

> «من نمی‌خواهم OrderService بداند Stripe چطور کار می‌کند.»

---

## 🔹 اگر Abstract Class استفاده می‌کردیم چه می‌شد؟

`public abstract class PaymentProviderBase {     protected ILogger Logger;      public abstract Task<PaymentResult> PayAsync(PaymentRequest request); }`

### مشکل؟

- coupling بالا
    
- inheritance constraint
    
- تست سخت‌تر
    
- تغییر base class = ریسک بالا
    

❗ **Hiring Manager Red Flag:**  
استفاده از Abstract فقط برای share code بدون تحلیل تغییرات آینده.

---

## 🔹 سناریوی واقعی 2: Pipeline در ASP.NET Core

Middlewareها:

`public interface IMiddleware {     Task InvokeAsync(HttpContext context, RequestDelegate next); }`

### چرا Interface و نه Abstract؟

- pipeline پویا
    
- composability
    
- lifetime management
    
- minimal coupling
    

📌 ASP.NET Core عمداً:

- abstract base class نداد
    
- حتی helper logic را extension method کرد
    

---

## 🔹 Default Interface Method — واقعاً کِی استفاده می‌شود؟

### مثال واقعی: Library public

فرض کن NuGet package داری:

`public interface ISerializer {     string Serialize(object obj); }`

بعد از 2 سال:

`string Serialize(object obj, SerializerOptions options);`

### گزینه‌های بد:

❌ breaking change  
❌ major version bump

### گزینه‌ی حرفه‌ای:

`public interface ISerializer {     string Serialize(object obj);      string Serialize(object obj, SerializerOptions options)         => Serialize(obj); }`

📌 **این تصمیم مخصوص نویسنده framework است، نه مصرف‌کننده.**

---

## 🔹 سناریوی واقعی 3: Domain Layer (DDD)

### Domain Entity + Abstract

`public abstract class Entity {     public Guid Id { get; protected set; } }`

### چرا Abstract اینجا خوب است؟

- invariant
    
- identity
    
- منطق مشترک واقعی
    
- تغییرناپذیر
    

📌 **Domain ≠ Infrastructure**

---

## 🔹 تست‌نویسی: جایی که تصمیم تو لو می‌رود

### Interface → Test-friendly

`Mock<IEmailSender>`

### Abstract → Fragile Test

`Mock<EmailSenderBase> // نیاز به virtual`

📌 **Hiring Manager Insight:**  
کسی که از Interface استفاده می‌کند، به تست فکر کرده.

---

## 🔹 از دید OOP (نه شعاری)

### Interface

- Polymorphism خالص
    
- Contract-first
    
- LSP واقعی
    

### Abstract

- Template Method Pattern
    
- Shared behavior
    
- Controlled extensibility
    

---

# 3️⃣ Key Interview Talking Points (Pass 2)

- ASP.NET Core عمداً Interface-centric است
    
- Interface ابزار مدیریت تغییر است
    
- Abstract ابزار کنترل رفتار مشترک
    
- Default interface method برای versioning است
    
- DI + Testability بدون Interface عملاً بی‌معناست
    

---

# 4️⃣ Common Mistakes & Red Flags (Hiring Manager View)

🚩 استفاده از Abstract برای DI  
🚩 استفاده از Interface برای share code  
🚩 ناتوانی در توضیح «چرا»  
🚩 جواب‌های حفظی بدون سناریو  
🚩 استفاده‌ی بی‌رویه از inheritance

---

# 5️⃣ When to Use / When NOT to Use (Final)

## ✅ Interface استفاده کن وقتی:

- سیستم رشد می‌کند
    
- تیم بزرگ است
    
- تست مهم است
    
- remote / distributed team داری
    
- library یا API می‌نویسی
    

## ⚠️ Abstract استفاده کن وقتی:

- hierarchy پایدار است
    
- رفتار مشترک واقعی داری
    
- domain rule مهم است
    
- inheritance منطقی است، نه اجباری
- 
# 1️⃣ Interview Relevance Summary

تقریباً تمام سیستم‌های مدرن .NET (از ASP.NET Core گرفته تا EF Core و MediatR):

- inheritance-heavy نیستند
    
- abstract-base محور نیستند
    
- **composition + interface محور** هستند
    

مصاحبه‌کننده با این مبحث می‌سنجد:

- آیا می‌فهمی چرا inheritance در scale خطرناک می‌شود؟
    
- آیا می‌توانی سیستم را طوری طراحی کنی که **قابل تغییر، تست‌پذیر و قابل توسعه** بماند؟
    
- آیا اصول SOLID را «در عمل» می‌فهمی یا فقط تعریف بلدی؟
    

---

# 2️⃣ Revised Content (Senior-Level)

## 🔹 اصل کلیدی: Composition over Inheritance یعنی چه؟

> به‌جای این‌که رفتار را **به ارث ببری**  
> آن را **تزریق و ترکیب (Compose)** کن

---

## 🔹 مشکل inheritance در سیستم‌های واقعی

### مثال اشتباه (خیلی رایج در Mid-level)

`public abstract class FileLogger {     public abstract void Log(string message); }  public class DatabaseFileLogger : FileLogger {     public override void Log(string message)     {         // log to db     } }`

### مشکل کجاست؟

- hierarchy شکننده
    
- تغییر base class → ripple effect
    
- تست سخت
    
- reuse محدود
    

📌 **Senior Insight:**  
Inheritance coupling زمانی خطرناک است که **تغییر محتمل** باشد.

---

## 🔹 طراحی درست با Composition + Interface

`public interface ILogger {     void Log(string message); }`

پیاده‌سازی‌ها:

`FileLogger DatabaseLogger ElasticLogger`

مصرف‌کننده:

`public class OrderService {     private readonly ILogger _logger;      public OrderService(ILogger logger)     {         _logger = logger;     } }`

### مزایا:

- تغییر بدون بازنویسی
    
- تست آسان
    
- قابلیت تزریق
    
- loosely coupled
    

📌 **ASP.NET Core دقیقاً همین مسیر را رفته**

---

## 🔹 چرا Abstract + Inheritance هنوز کاملاً بد نیست؟

### سناریوی درست: Template Method

`public abstract class BackgroundJob {     public async Task ExecuteAsync()     {         PreExecute();         await RunAsync();         PostExecute();     }      protected abstract Task RunAsync();      protected virtual void PreExecute() { }     protected virtual void PostExecute() { } }`

### چرا اینجا Abstract منطقی است؟

- flow ثابت است
    
- extension کنترل‌شده
    
- invariant مهم است
    

📌 **Senior Rule:**  
Inheritance فقط وقتی خوب است که **رفتار کلی نباید تغییر کند**.

---

## 🔹 ASP.NET Core = Composition everywhere

### Middleware

`app.Use(async (ctx, next) => {     // before     await next();     // after });`

- نه inheritance
    
- نه abstract
    
- فقط composition
    

---

### Filters

`IActionFilter IAsyncActionFilter`

> به‌جای base controller

---

### Authentication

`IAuthenticationHandler`

نه:

`AuthenticationHandlerBase`

---

## 🔹 Interface vs Delegate (نکته طلایی مصاحبه)

### Delegate

`Func<Order, bool> rule;`

### Interface

`public interface IOrderRule {     bool IsValid(Order order); }`

### تفاوت از دید Senior:

|معیار|Delegate|Interface|
|---|---|---|
|Context|کم|زیاد|
|Testability|متوسط|بالا|
|Readability|پایین‌تر|بالاتر|
|Evolution|سخت|آسان|

📌 Delegate برای **behavior ساده**  
📌 Interface برای **concept پایدار**

---

## 🔹 ارتباط مستقیم با Testability

### Interface-based

`Mock<IPaymentGateway>`

### Inheritance-based

`Mock<PaymentGatewayBase> // virtual hell`

📌 **Hiring Manager Signal:**  
کسی که composition می‌فهمد، سیستم قابل تست می‌سازد.

---

## 🔹 از دید SOLID (واقعی، نه شعاری)

- **S**: هر implementation یک مسئولیت
    
- **O**: اضافه‌کردن بدون تغییر
    
- **L**: interface-based polymorphism
    
- **I**: interface کوچک
    
- **D**: وابستگی به abstraction
    

---

# 3️⃣ Key Interview Talking Points

- ASP.NET Core عمداً inheritance-heavy نیست
    
- Composition هزینه تغییر را کم می‌کند
    
- Interface ابزار contract است، نه code reuse
    
- Abstract فقط برای invariant واقعی
    
- Delegate جایگزین interface نیست
    

---

# 4️⃣ Common Mistakes & Red Flags

🚩 base class برای DI  
🚩 deep inheritance tree  
🚩 استفاده از abstract برای test  
🚩 overrideهای بیش‌ازحد  
🚩 ناتوانی در توضیح trade-off

---

# 5️⃣ When to Use / When NOT to Use

## ✅ Composition + Interface

- business logic
    
- services
    
- infrastructure
    
- integration
    
- distributed systems
    

## ⚠️ Inheritance + Abstract

- framework code
    
- domain invariant
    
- controlled extension
    
- template method