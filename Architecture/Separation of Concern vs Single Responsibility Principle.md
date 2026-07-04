# Separation of Concerns (SOC) و Single Responsibility Principle (SRP)

SOC و SRP دو مفهوم **مهم و مرتبط** اما **متفاوت** در طراحی نرم‌افزار هستند. درک تفاوت بین این دو و دانستن چه زمانی از کدام استفاده کنیم، در مصاحبه‌ها بسیار مهم است.

---

## فهرست مطالب

1. [مقدمه: SOC و SRP چیست؟](#مقدمه-soc-و-srp-چیست)
2. [Separation of Concerns (SOC)](#separation-of-concerns-soc)
3. [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
4. [تفاوت SOC و SRP](#تفاوت-soc-و-srp)
5. [مثال عملی: Notification Service](#مثال-عملی-notification-service)
6. [ارتباط با Design Patterns](#ارتباط-با-design-patterns)
7. [نکات مصاحبه](#نکات-مصاحبه)

---

## مقدمه: SOC و SRP چیست؟

### تاریخچه

- **1998**: SOC (Separation of Concerns) معرفی شد
- **2003**: SRP (Single Responsibility Principle) توسط Uncle Bob معرفی شد

### ارتباط

**هر دوی آن‌ها درباره یک انتزاع و نگاه Abstract صحبت می‌کنند:**

- هر دو هدف یکسانی دارند: کاهش Coupling و افزایش Cohesion
- اما در **سطح‌های مختلف** اعمال می‌شوند

---

## Separation of Concerns (SOC)

### تعریف

**SOC به جدا‌سازی دغدغه‌ها اشاره می‌کند که Concern به یک Feature و یک ماژول است.**

### درک SOC

**زمانی که یک Application را توسعه می‌دهیم:**

- هر Feature یا ماژول باید دغدغه‌های مخصوص خودش را داشته باشد
- دو ماژول نباید دغدغه‌های یکسانی داشته باشند

### سطح اعمال

**SOC در سطح بالا و System Design صحبت می‌کند:**

- **سطح**: ماژول، Feature، Component
- **هدف**: جدا کردن Concerns مختلف (UI، Business Logic، Data Access)

### مثال: SOC در Architecture

```csharp
// ✅ رعایت SOC - جدا کردن Concerns
// Presentation Layer
public class OrderController
{
    private readonly IOrderService _orderService;
    
    public IActionResult CreateOrder(OrderDto dto)
    {
        var order = _orderService.CreateOrder(dto);
        return Ok(order);
    }
}

// Business Layer
public class OrderService : IOrderService
{
    private readonly IOrderRepository _repository;
    
    public Order CreateOrder(OrderDto dto)
    {
        // Business Logic
        var order = new Order { /* ... */ };
        return _repository.Save(order);
    }
}

// Data Access Layer
public class OrderRepository : IOrderRepository
{
    public Order Save(Order order)
    {
        // Database Operations
    }
}
```

### Concerns مختلف

1. **Presentation Concern**: UI، API Controllers
2. **Business Logic Concern**: Business Rules، Domain Logic
3. **Data Access Concern**: Database Operations
4. **Infrastructure Concern**: Logging، Email، File System

---

## Single Responsibility Principle (SRP)

### تعریف

**SRP می‌گوید هر کلاس باید فقط و فقط یک دلیل برای تغییر داشته باشد.**

### درک SRP

**اما تشخیص دلیل نیاز به تجربه دارد:**

- یک کلاس ممکن است چند متد داشته باشد
- اما همه آن‌ها باید به یک "دلیل تغییر" مربوط باشند

### سطح اعمال

**SRP در سطح پایین نرم‌افزار صحبت می‌کند:**

- **سطح**: کلاس، متد
- **هدف**: یک کلاس = یک مسئولیت

### مثال: SRP در کلاس

```csharp
// ❌ نقض SRP - چند دلیل برای تغییر
public class Order
{
    public void CalculateTotal() { } // دلیل تغییر: Business Logic
    public void SaveToDatabase() { } // دلیل تغییر: Database Schema
    public void SendEmail() { } // دلیل تغییر: Email Service
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
```

---

## تفاوت SOC و SRP

### جدول مقایسه

| ویژگی | SOC | SRP |
|---|---|---|
| **سطح** | بالا (ماژول، Feature) | پایین (کلاس، متد) |
| **هدف** | جدا کردن Concerns مختلف | یک کلاس = یک مسئولیت |
| **مقیاس** | System Design | Class Design |
| **مثال** | UI، Business، Data Access | Order، Repository، EmailService |

### درک تفاوت

**SOC در سطح Architecture:**

- جدا کردن Presentation از Business Logic
- جدا کردن Business Logic از Data Access
- **مقیاس**: ماژول‌ها و Component‌ها

**SRP در سطح Class Design:**

- یک کلاس = یک مسئولیت
- یک کلاس = یک دلیل برای تغییر
- **مقیاس**: کلاس‌ها و متدها

### مثال: تفاوت در عمل

```csharp
// SOC: جدا کردن Layers
// Presentation Layer
public class OrderController { } // UI Concern

// Business Layer  
public class OrderService { } // Business Logic Concern

// Data Access Layer
public class OrderRepository { } // Data Access Concern

// SRP: هر کلاس یک مسئولیت
public class OrderService
{
    // فقط Business Logic
    public Order CreateOrder(OrderDto dto) { }
    public Order UpdateOrder(int id, OrderDto dto) { }
    public void DeleteOrder(int id) { }
}

// اگر بخواهیم Email اضافه کنیم:
public class OrderNotificationService // کلاس جدید - SRP
{
    public void NotifyOrderCreated(Order order) { }
}
```

---

## مثال عملی: Notification Service

### مشکل: SOC vs SRP

**از نظر SRP، کلاس Notification کار درستی انجام می‌دهد و مشکلی از نظر دلیل تغییر نداریم:**

```csharp
// از نظر SRP: OK - یک دلیل برای تغییر (Notification)
public class NotificationService
{
    public void SendEmail(string to, string message)
    {
        // SMTP Implementation
        var smtpClient = new SmtpClient();
        smtpClient.Send(to, message);
    }
    
    public void SendSms(string phone, string message)
    {
        // HTTP Client Implementation
        var httpClient = new HttpClient();
        httpClient.Post("sms-api", new { phone, message });
    }
}
```

**اما از نظر SOC، جزئیات SMTP و HttpClient با هم همخوانی ندارند:**

- SMTP و HTTP Client دغدغه‌های متفاوتی دارند
- باید جدا شوند
- از یک Abstraction بهره ببریم

### راه‌حل: Strategy Pattern

**برای استفاده در Process و دغدغه آن برای انتخاب سرویس Notification را کم کنیم، در اصل یک Strategy Pattern پیاده‌سازی می‌کنیم:**

```csharp
// ✅ رعایت SOC - جدا کردن Concerns
public interface INotificationChannel
{
    void Send(string recipient, string message);
}

public class EmailChannel : INotificationChannel
{
    public void Send(string recipient, string message)
    {
        // SMTP Implementation
        var smtpClient = new SmtpClient();
        smtpClient.Send(recipient, message);
    }
}

public class SmsChannel : INotificationChannel
{
    public void Send(string recipient, string message)
    {
        // HTTP Client Implementation
        var httpClient = new HttpClient();
        httpClient.Post("sms-api", new { phone = recipient, message });
    }
}

// Process Class - فقط Business Logic
public class OrderProcessor
{
    private readonly INotificationChannel _notificationChannel;
    
    public OrderProcessor(INotificationChannel notificationChannel)
    {
        _notificationChannel = notificationChannel;
    }
    
    public void ProcessOrder(Order order)
    {
        // Business Logic
        // ...
        
        // Notification - وابستگی به Abstraction
        _notificationChannel.Send(order.CustomerEmail, "Order processed");
    }
}
```

### مزایا

1. **SOC**: Email و SMS Concerns جدا شده‌اند
2. **SRP**: هر کلاس یک مسئولیت دارد
3. **OCP**: می‌توانیم Channel جدید اضافه کنیم بدون تغییر Process
4. **DIP**: وابستگی به Abstraction

---

## ارتباط با Design Patterns

### Strategy Pattern

**Strategy Pattern برای پیاده‌سازی SOC استفاده می‌شود:**

```csharp
// Strategy Pattern برای Notification Channels
public interface INotificationStrategy
{
    void Send(string recipient, string message);
}

public class EmailStrategy : INotificationStrategy { }
public class SmsStrategy : INotificationStrategy { }
public class PushStrategy : INotificationStrategy { }
```

### Factory Pattern

**Factory Pattern برای انتخاب Strategy:**

```csharp
public class NotificationChannelFactory
{
    public INotificationChannel Create(NotificationType type)
    {
        return type switch
        {
            NotificationType.Email => new EmailChannel(),
            NotificationType.Sms => new SmsChannel(),
            NotificationType.Push => new PushChannel(),
            _ => throw new ArgumentException()
        };
    }
}
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت SOC و SRP چیست؟

**پاسخ**:
- **SOC**: در سطح بالا (ماژول، Feature) - جدا کردن Concerns مختلف
- **SRP**: در سطح پایین (کلاس) - یک کلاس = یک مسئولیت
- **SOC**: System Design
- **SRP**: Class Design

#### 2. آیا SOC و SRP با هم در تضاد هستند؟

**پاسخ**:
- خیر، مکمل یکدیگر هستند
- SOC در سطح Architecture
- SRP در سطح Implementation
- هر دو هدف یکسانی دارند: کاهش Coupling

#### 3. مثال Notification Service را توضیح دهید.

**پاسخ**:
- از نظر SRP: OK - یک دلیل برای تغییر
- از نظر SOC: مشکل - SMTP و HTTP Client باید جدا شوند
- **راه‌حل**: Strategy Pattern با Interface

#### 4. چه زمانی SOC اعمال می‌شود؟

**پاسخ**:
- در طراحی Architecture
- جدا کردن Layers (Presentation، Business، Data)
- جدا کردن Features و Modules

#### 5. چه زمانی SRP اعمال می‌شود؟

**پاسخ**:
- در طراحی کلاس‌ها
- وقتی یک کلاس چند دلیل برای تغییر دارد
- برای کاهش Coupling در سطح کلاس

### نکات مهم برای مصاحبه

1. **سطح**: SOC در سطح بالا، SRP در سطح پایین
2. **هدف**: هر دو کاهش Coupling
3. **مکمل**: با هم کار می‌کنند، نه در تضاد
4. **مثال**: Notification Service مثال خوبی است
5. **Pattern**: Strategy Pattern برای SOC

---

## خلاصه

### Separation of Concerns (SOC)

- **سطح**: بالا (ماژول، Feature)
- **هدف**: جدا کردن Concerns مختلف
- **مقیاس**: System Design
- **مثال**: UI، Business Logic، Data Access

### Single Responsibility Principle (SRP)

- **سطح**: پایین (کلاس، متد)
- **هدف**: یک کلاس = یک مسئولیت
- **مقیاس**: Class Design
- **مثال**: Order، Repository، EmailService

### ارتباط

- هر دو درباره Abstraction صحبت می‌کنند
- هر دو کاهش Coupling را هدف دارند
- مکمل یکدیگر هستند
- در سطح‌های مختلف اعمال می‌شوند

**موفق باشید!** 🚀
