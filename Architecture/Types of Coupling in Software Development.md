# Types of Coupling in Software Development

Coupling یکی از **مهم‌ترین مفاهیم** در طراحی نرم‌افزار است. درک انواع Coupling و دانستن چگونه آن را کاهش دهیم، برای نوشتن کد قابل نگهداری و قابل تست ضروری است. این موضوع در مصاحبه‌های Senior بسیار پرسیده می‌شود.

---

## فهرست مطالب

1. [مقدمه: Coupling چیست؟](#مقدمه-coupling-چیست)
2. [انواع Coupling (از بد به خوب)](#انواع-coupling-از-بد-به-خوب)
3. [Content Coupling](#content-coupling)
4. [Common Coupling](#common-coupling)
5. [External Coupling](#external-coupling)
6. [Control Coupling](#control-coupling)
7. [Stamp Coupling](#stamp-coupling)
8. [Data Coupling](#data-coupling)
9. [Message Coupling](#message-coupling)
10. [چگونه Coupling را کاهش دهیم؟](#چگونه-coupling-را-کاهش-دهیم)
11. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: Coupling چیست؟

### تعریف

**Coupling (در هم‌تنیدگی) میزان وابستگی بین ماژول‌ها، کلاس‌ها یا Component‌ها را نشان می‌دهد.**

### هدف

**هدف ما در طراحی نرم‌افزار: Low Coupling and High Cohesion**

- **Low Coupling**: وابستگی کم بین ماژول‌ها
- **High Cohesion**: پیوستگی بالا در یک ماژول

### چرا Coupling مهم است؟

1. **Maintainability**: کد با Coupling کم، نگهداری آسان‌تر است
2. **Testability**: تست کردن آسان‌تر است
3. **Reusability**: قابلیت استفاده مجدد بیشتر است
4. **Flexibility**: تغییرات آسان‌تر اعمال می‌شوند

---

## انواع Coupling (از بد به خوب)

### ترتیب Coupling (از بدترین به بهترین)

1. **Content Coupling** (بدترین) ❌
2. **Common Coupling** ❌
3. **External Coupling** ⚠️
4. **Control Coupling** ⚠️
5. **Stamp Coupling** ⚠️
6. **Data Coupling** ✅
7. **Message Coupling** (بهترین) ✅

---

## Content Coupling

### تعریف

**Content Coupling زمانی است که یک ماژول مستقیماً به محتوای داخلی ماژول دیگر دسترسی دارد.**

### مثال

```csharp
// ❌ Content Coupling - دسترسی مستقیم به Private Members
public class ModuleA
{
    private int _internalData = 10;
    
    public int GetInternalData() => _internalData;
}

public class ModuleB
{
    public void Process(ModuleA moduleA)
    {
        // دسترسی مستقیم به محتوای داخلی
        var data = moduleA.GetInternalData(); // Content Coupling
        // استفاده از Reflection برای دسترسی به Private Members
        var field = typeof(ModuleA).GetField("_internalData", 
            BindingFlags.NonPublic | BindingFlags.Instance);
        field.SetValue(moduleA, 20); // بدترین نوع Coupling
    }
}
```

### چرا بد است؟

- **Encapsulation را نقض می‌کند**: دسترسی به Private Members
- **تغییرات خطرناک**: تغییر در یک ماژول، ماژول دیگر را می‌شکند
- **تست کردن سخت**: وابستگی شدید

### راه‌حل

```csharp
// ✅ استفاده از Public Interface
public interface IDataProvider
{
    int GetData();
}

public class ModuleA : IDataProvider
{
    private int _internalData = 10;
    
    public int GetData() => _internalData; // Public Interface
}

public class ModuleB
{
    public void Process(IDataProvider provider)
    {
        var data = provider.GetData(); // وابستگی به Interface
    }
}
```

---

## Common Coupling

### تعریف

**Common Coupling زمانی است که چند ماژول به یک داده Global مشترک دسترسی دارند.**

### مثال

```csharp
// ❌ Common Coupling - Global State
public static class GlobalState
{
    public static int Counter { get; set; }
    public static string CurrentUser { get; set; }
}

public class ModuleA
{
    public void Process()
    {
        GlobalState.Counter++; // وابستگی به Global State
        GlobalState.CurrentUser = "User1";
    }
}

public class ModuleB
{
    public void Process()
    {
        var counter = GlobalState.Counter; // وابستگی به Global State
        var user = GlobalState.CurrentUser;
    }
}

// مشکل: تغییر در ModuleA روی ModuleB تأثیر می‌گذارد
```

### چرا بد است؟

- **Side Effects**: تغییرات غیرمنتظره
- **Testing سخت**: باید Global State را Mock کنیم
- **Thread Safety**: مشکل در Multi-Threading

### راه‌حل

```csharp
// ✅ Dependency Injection
public class ModuleA
{
    private readonly IStateService _stateService;
    
    public ModuleA(IStateService stateService)
    {
        _stateService = stateService;
    }
    
    public void Process()
    {
        _stateService.IncrementCounter();
        _stateService.SetCurrentUser("User1");
    }
}

public class ModuleB
{
    private readonly IStateService _stateService;
    
    public ModuleB(IStateService stateService)
    {
        _stateService = stateService;
    }
    
    public void Process()
    {
        var counter = _stateService.GetCounter();
        var user = _stateService.GetCurrentUser();
    }
}
```

---

## External Coupling

### تعریف

**External Coupling زمانی است که ماژول‌ها به یک سیستم خارجی مشترک وابسته هستند.**

### مثال

```csharp
// ⚠️ External Coupling - وابستگی به سیستم خارجی
public class DatabaseModule
{
    public void Save(string connectionString)
    {
        // وابستگی به SQL Server
        using var connection = new SqlConnection(connectionString);
        // ...
    }
}

public class LoggingModule
{
    public void Log(string connectionString)
    {
        // وابستگی به همان Database
        using var connection = new SqlConnection(connectionString);
        // ...
    }
}

// مشکل: اگر Database تغییر کند، همه ماژول‌ها باید تغییر کنند
```

### چرا مشکل‌ساز است؟

- **وابستگی به Infrastructure**: تغییر Infrastructure مشکل‌ساز است
- **Testing**: تست کردن سخت است

### راه‌حل

```csharp
// ✅ Abstraction
public interface IDatabase
{
    void Save(string data);
}

public class SqlServerDatabase : IDatabase
{
    public void Save(string data) { }
}

public class DatabaseModule
{
    private readonly IDatabase _database;
    
    public DatabaseModule(IDatabase database)
    {
        _database = database;
    }
    
    public void Save(string data)
    {
        _database.Save(data);
    }
}
```

---

## Control Coupling

### تعریف

**Control Coupling زمانی است که یک ماژول با ارسال Flag یا Control Data به ماژول دیگر، رفتار آن را کنترل می‌کند.**

### مثال

```csharp
// ⚠️ Control Coupling - کنترل رفتار با Flag
public class OrderProcessor
{
    public void Process(Order order, bool sendEmail, bool sendSms, bool generateInvoice)
    {
        // کنترل رفتار با Flag
        if (sendEmail)
        {
            SendEmail(order);
        }
        
        if (sendSms)
        {
            SendSms(order);
        }
        
        if (generateInvoice)
        {
            GenerateInvoice(order);
        }
    }
}

// استفاده
processor.Process(order, true, false, true); // Control Coupling
```

### چرا مشکل‌ساز است؟

- **وابستگی به Implementation**: Client باید بداند چه Flag‌هایی وجود دارد
- **انعطاف‌ناپذیر**: اضافه کردن رفتار جدید سخت است

### راه‌حل

```csharp
// ✅ Strategy Pattern
public interface IOrderAction
{
    void Execute(Order order);
}

public class EmailAction : IOrderAction
{
    public void Execute(Order order) { }
}

public class SmsAction : IOrderAction
{
    public void Execute(Order order) { }
}

public class OrderProcessor
{
    private readonly IEnumerable<IOrderAction> _actions;
    
    public OrderProcessor(IEnumerable<IOrderAction> actions)
    {
        _actions = actions;
    }
    
    public void Process(Order order)
    {
        foreach (var action in _actions)
        {
            action.Execute(order);
        }
    }
}
```

---

## Stamp Coupling

### تعریف

**Stamp Coupling زمانی است که یک ماژول یک Object کامل را به ماژول دیگر می‌فرستد، اما فقط از بخشی از آن استفاده می‌کند.**

### مثال

```csharp
// ⚠️ Stamp Coupling - ارسال Object کامل
public class User
{
    public string Name { get; set; }
    public string Email { get; set; }
    public string Address { get; set; }
    public string Phone { get; set; }
    // ... 20 Property دیگر
}

public class EmailService
{
    public void SendEmail(User user) // فقط Email نیاز دارد
    {
        // فقط از user.Email استفاده می‌کند
        Send(user.Email, "Hello");
    }
}

public class AddressService
{
    public void ValidateAddress(User user) // فقط Address نیاز دارد
    {
        // فقط از user.Address استفاده می‌کند
        Validate(user.Address);
    }
}
```

### چرا مشکل‌ساز است؟

- **وابستگی غیرضروری**: وابستگی به Property‌های غیرضروری
- **تغییرات غیرمنتظره**: تغییر در User روی همه ماژول‌ها تأثیر می‌گذارد

### راه‌حل

```csharp
// ✅ فقط داده‌های مورد نیاز را ارسال کنید
public class EmailService
{
    public void SendEmail(string email) // فقط Email
    {
        Send(email, "Hello");
    }
}

public class AddressService
{
    public void ValidateAddress(string address) // فقط Address
    {
        Validate(address);
    }
}

// یا با DTO
public class EmailDto
{
    public string Email { get; set; }
}

public class EmailService
{
    public void SendEmail(EmailDto dto)
    {
        Send(dto.Email, "Hello");
    }
}
```

---

## Data Coupling

### تعریف

**Data Coupling زمانی است که ماژول‌ها فقط داده‌های مورد نیاز را با هم به اشتراک می‌گذارند.**

### مثال

```csharp
// ✅ Data Coupling - فقط داده‌های مورد نیاز
public class Calculator
{
    public int Add(int a, int b) // فقط داده‌های مورد نیاز
    {
        return a + b;
    }
}

public class OrderService
{
    public decimal CalculateTotal(decimal subtotal, decimal tax) // فقط داده‌های مورد نیاز
    {
        return subtotal + tax;
    }
}
```

### چرا خوب است؟

- **وابستگی کم**: فقط به داده‌های مورد نیاز وابسته است
- **تست کردن آسان**: می‌توانیم داده‌های ساده را Test کنیم
- **Reusable**: می‌توانیم در جاهای مختلف استفاده کنیم

---

## Message Coupling

### تعریف

**Message Coupling بهترین نوع Coupling است. ماژول‌ها از طریق Message با هم ارتباط برقرار می‌کنند و هیچ وابستگی مستقیمی ندارند.**

### مثال

```csharp
// ✅ Message Coupling - ارتباط از طریق Message
public class OrderCreatedEvent
{
    public int OrderId { get; set; }
    public string CustomerEmail { get; set; }
    public decimal Total { get; set; }
}

public class OrderService
{
    private readonly IEventBus _eventBus;
    
    public void CreateOrder(Order order)
    {
        // Business Logic
        var orderCreated = new OrderCreatedEvent
        {
            OrderId = order.Id,
            CustomerEmail = order.CustomerEmail,
            Total = order.Total
        };
        
        _eventBus.Publish(orderCreated); // ارسال Message
    }
}

public class EmailService
{
    public void Handle(OrderCreatedEvent @event) // دریافت Message
    {
        SendEmail(@event.CustomerEmail, "Order created");
    }
}

public class InvoiceService
{
    public void Handle(OrderCreatedEvent @event) // دریافت Message
    {
        GenerateInvoice(@event.OrderId, @event.Total);
    }
}
```

### چرا بهترین است؟

- **وابستگی صفر**: ماژول‌ها مستقیماً به هم وابسته نیستند
- **Loose Coupling**: وابستگی بسیار کم
- **Scalable**: می‌توانیم Handler جدید اضافه کنیم
- **Testable**: تست کردن آسان است

---

## چگونه Coupling را کاهش دهیم؟

### 1. استفاده از Interface

```csharp
// ❌ Tight Coupling
public class OrderService
{
    private readonly SqlServerRepository _repository;
    
    public OrderService()
    {
        _repository = new SqlServerRepository();
    }
}

// ✅ Loose Coupling
public class OrderService
{
    private readonly IOrderRepository _repository;
    
    public OrderService(IOrderRepository repository)
    {
        _repository = repository;
    }
}
```

### 2. Dependency Injection

```csharp
// ✅ Dependency Injection
public class OrderService
{
    private readonly IOrderRepository _repository;
    private readonly IEmailService _emailService;
    
    public OrderService(
        IOrderRepository repository,
        IEmailService emailService)
    {
        _repository = repository;
        _emailService = emailService;
    }
}
```

### 3. Event-Driven Architecture

```csharp
// ✅ Event-Driven - Message Coupling
public class OrderService
{
    private readonly IEventBus _eventBus;
    
    public void CreateOrder(Order order)
    {
        // Business Logic
        _eventBus.Publish(new OrderCreatedEvent(order));
    }
}
```

### 4. DTO Pattern

```csharp
// ✅ DTO - فقط داده‌های مورد نیاز
public class OrderDto
{
    public int Id { get; set; }
    public decimal Total { get; set; }
}

public class OrderService
{
    public OrderDto GetOrder(int id)
    {
        var order = _repository.GetById(id);
        return new OrderDto { Id = order.Id, Total = order.Total };
    }
}
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. Coupling چیست و چرا مهم است؟

**پاسخ**:
- میزان وابستگی بین ماژول‌ها
- **هدف**: Low Coupling and High Cohesion
- **مزایا**: Maintainability، Testability، Reusability

#### 2. انواع Coupling را نام ببرید (از بد به خوب).

**پاسخ**:
1. Content Coupling (بدترین)
2. Common Coupling
3. External Coupling
4. Control Coupling
5. Stamp Coupling
6. Data Coupling
7. Message Coupling (بهترین)

#### 3. تفاوت Stamp Coupling و Data Coupling چیست؟

**پاسخ**:
- **Stamp Coupling**: ارسال Object کامل (فقط بخشی استفاده می‌شود)
- **Data Coupling**: ارسال فقط داده‌های مورد نیاز
- **Data Coupling بهتر است**

#### 4. چگونه Coupling را کاهش دهیم؟

**پاسخ**:
- استفاده از Interface
- Dependency Injection
- Event-Driven Architecture
- DTO Pattern
- Abstraction

#### 5. Message Coupling چیست و چرا بهترین است؟

**پاسخ**:
- ارتباط از طریق Message (Event)
- وابستگی صفر
- Loose Coupling
- Scalable و Testable

#### 6. مثال Common Coupling را بزنید.

**پاسخ**:
- Global State
- Static Variables
- Singleton (اگر بد استفاده شود)
- **راه‌حل**: Dependency Injection

### نکات مهم برای مصاحبه

1. **ترتیب**: از بدترین به بهترین بدانید
2. **مثال**: برای هر نوع مثال بزنید
3. **راه‌حل**: راه‌حل کاهش Coupling را بدانید
4. **Trade-off**: همیشه Trade-off وجود دارد

---

## خلاصه

### انواع Coupling (از بد به خوب)

1. **Content Coupling** ❌ - دسترسی مستقیم به Private Members
2. **Common Coupling** ❌ - Global State
3. **External Coupling** ⚠️ - وابستگی به سیستم خارجی
4. **Control Coupling** ⚠️ - کنترل با Flag
5. **Stamp Coupling** ⚠️ - ارسال Object کامل
6. **Data Coupling** ✅ - فقط داده‌های مورد نیاز
7. **Message Coupling** ✅ - ارتباط از طریق Message

### راه‌حل‌های کاهش Coupling

- Interface و Abstraction
- Dependency Injection
- Event-Driven Architecture
- DTO Pattern

### هدف

**Low Coupling and High Cohesion**

**موفق باشید!** 🚀
