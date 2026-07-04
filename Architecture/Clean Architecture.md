# Clean Architecture

**Clean Architecture** یک الگوی معماری نرم‌افزار است که توسط **Robert C. Martin (Uncle Bob)** معرفی شد. هدف آن ایجاد سیستمی است که:
- **مستقل از فریم‌ورک** باشد
- **تست‌پذیر** باشد
- **مستقل از UI و دیتابیس** باشد
- **مستقل از عوامل خارجی** باشد

این یادداشت به شما کمک می‌کند تا فلسفه، ساختار، مزایا، معایب و موارد استفاده Clean Architecture را به‌صورت عمیق درک کنید.

**اهمیت در مصاحبه:** در مصاحبه‌های بک‌اند، سؤال Clean Architecture و تفاوتش با لایه‌ای و Hexagonal زیاد مطرح می‌شود. انتظار می‌رود بتوانید Dependency Rule، جایگاه هر لایه و نقش آن در تست‌پذیری و استقلال از فریم‌ورک را توضیح دهید.

---

## ۱. چرا Clean Architecture به وجود آمد؟

### مشکل معماری‌های سنتی
در معماری‌های قدیمی مثل **لایه‌ای (Layered)**، وابستگی‌ها معمولاً به سمت پایین هستند: Presentation → Business → Data Access → Database. این وابستگی باعث می‌شود:

- **تغییر در دیتابیس** کل برنامه را تحت تأثیر قرار دهد.
- **تست‌نویسی** سخت شود (چون Business به Data Access وابسته است).
- **جایگزینی فریم‌ورک** تقریباً غیرممکن شود.
- **منطق کسب‌وکار** با کدهای زیرساختی قاطی شود.

### ایده اصلی
طبق صحبت های Uncle Bob و دیگران (مانند Alistair Cockburn با Hexagonal Architecture و Jeffrey Palermo با Onion Architecture) به این نتیجه رسیدند که باید **وابستگی‌ها را معکوس کنیم**: لایه‌های داخلی (مانند Domain) نباید به لایه‌های خارجی (مانند دیتابیس، UI) وابسته باشند. بلکه همه باید به **انتزاع** وابسته باشند.

Clean Architecture تلفیقی از این ایده‌هاست با نام‌گذاری مشخص و قانونی ساده: **قانون وابستگی (Dependency Rule)**.

---

## ۲. قانون وابستگی (Dependency Rule)

> **کد منبع فقط می‌تواند به سمت داخل وابسته باشد. هیچ‌چیز در لایهٔ داخلی نباید از چیزی در لایهٔ خارجی اطلاع داشته باشد.**

این قانون به این معناست:
- لایه‌های بیرونی می‌توانند به لایه‌های داخلی وابسته باشند.
- لایه‌های داخلی هرگز نباید به لایه‌های بیرونی وابسته باشند.
- وابستگی‌ها همیشه به سمت **مرکز** (Domain) هستند.

---

## ۳. لایه‌های Clean Architecture

معماری تمیز از چهار لایه اصلی تشکیل شده است (البته می‌توانید زیرلایه‌های بیشتری هم داشته باشید):

```text
┌─────────────────────────────────────┐
│    Frameworks & Drivers (External)   │
│  ┌─────────────────────────────────┐ │
│  │   Interface Adapters (Present.)  │ │
│  │  ┌─────────────────────────────┐ │ │
│  │  │    Application (Use Cases)   │ │ │
│  │  │  ┌─────────────────────────┐ │ │ │
│  │  │  │      Entities (Domain)   │ │ │ │
│  │  │  └─────────────────────────┘ │ │ │
│  │  └─────────────────────────────┘ │ │
│  └─────────────────────────────────┘ │
└─────────────────────────────────────┘
```

### ۳.۱. Entities (Domain / Enterprise Business Rules)

- **مسئولیت:** شامل **مهم‌ترین قوانین کسب‌وکار** (Enterprise-wide business rules) است. این لایه مستقل از هر چیز دیگری است.
- **محتویات:**
  - Entity‌ها (با Rich Model، رفتار و قوانین)
  - Value Object‌ها
  - Aggregate‌ها (اگر از DDD استفاده کنید)
  - Domain Events
  - Domain Services (برای منطقی که در یک Entity جا نمی‌شود)
- **ویژگی:** هیچ وابستگی به هیچ فریم‌ورک، دیتابیس یا ابزار خارجی ندارد. کاملاً خالص (Plain Old Objects).

**مثال مفهومی:**
```java
public class Order {
    private List<OrderItem> items;
    
    public void addItem(Product product, int quantity) {
        if (quantity <= 0) throw new DomainException("Quantity must be positive");
        items.add(new OrderItem(product, quantity));
    }
    
    public Money calculateTotal() {
        return items.stream()
                .map(OrderItem::getSubtotal)
                .reduce(Money.ZERO, Money::add);
    }
}
```

### ۳.۲. Application (Use Cases / Application Business Rules)

- **مسئولیت:** شامل **قوانین مختص به کاربرد** (application-specific business rules) است. این لایه **ارکستراسیون** (هماهنگی) جریان داده بین Entities را انجام می‌دهد.
- **محتویات:**
  - Command/Query Handler‌ها
  - Use Case classes
  - DTO‌ها (Data Transfer Objects)
  - اینترفیس‌هایی که در لایه‌های بالاتر پیاده‌سازی می‌شوند (مانند `IOrderRepository`)
- **ویژگی:** فقط به لایه Entities وابسته است. از جزئیات زیرساخت (مانند دیتابیس) بی‌خبر است.

**نکته:** این لایه نباید شامل منطق کسب‌وکار پیچیده باشد؛ آن منطق باید در Entities باشد. اینجا فقط جریان کار را کنترل می‌کند.

**مثال مفهومی:**
```java
public class CreateOrderUseCase {
    private final OrderRepository orderRepository;
    private final PaymentService paymentService;
    
    public CreateOrderUseCase(OrderRepository orderRepository, PaymentService paymentService) {
        this.orderRepository = orderRepository;
        this.paymentService = paymentService;
    }
    
    public OrderOutput execute(CreateOrderInput input) {
        Order order = new Order(input.getCustomerId());
        input.getItems().forEach(item -> order.addItem(item.getProductId(), item.getQuantity()));
        
        paymentService.charge(order.getTotal(), input.getPaymentDetails());
        orderRepository.save(order);
        
        return new OrderOutput(order.getId());
    }
}
```

### ۳.۳. Interface Adapters (Presenters, Controllers, Gateways)

- **مسئولیت:** تبدیل داده‌ها بین فرمت مناسب برای لایه‌های داخلی و خارجی. این لایه **واسط** بین Use Case و دنیای خارج است.
- **محتویات:**
  - Controllers (دریافت درخواست HTTP و تبدیل به Input DTO)
  - Presenters (تبدیل Output DTO به فرمت مناسب برای نمایش، مثلاً JSON)
  - Gateways (پیاده‌سازی اینترفیس‌های تعریف‌شده در لایه Application، مانند Repository‌های واقعی)
  - ViewModels
- **ویژگی:** وابسته به لایه Application است و ممکن است به فریم‌ورک‌های خارجی (مانند ASP.NET MVC) وابسته باشد.

### ۳.۴. Frameworks & Drivers (External)

- **مسئولیت:** شامل همه جزئیات زیرساخت و ابزارهای خارجی است: دیتابیس، وب سرور، صف پیام، ایمیل، و غیره.
- **محتویات:**
  - پیاده‌سازی واقعی Repository‌ها (مثلاً با EF Core)
  - تنظیمات DI Container
  - کدهای مربوط به API‌های خارجی
- **ویژگی:** این لایه بیرونی‌ترین لایه است و به لایه‌های داخلی وابسته است (نه برعکس).

### ۳.۵. یک نمونهٔ معمول در ASP.NET Core

در پروژه‌های ASP.NET Core معمولاً Clean Architecture به‌صورت چند پروژه در یک Solution پیاده‌سازی می‌شود، مثلاً:

- `MyApp.Domain`  
  - Entityها، Value Objectها، Domain Services، Domain Events
- `MyApp.Application`  
  - Use Caseها، Command/Query Handlerها (مثلاً با MediatR)، اینترفیس‌های Repository و سرویس‌ها
- `MyApp.Infrastructure`  
  - پیاده‌سازی Repositoryها با EF Core یا Dapper، تنظیمات DbContext، ایمیل، لاگینگ، Access به صف پیام
- `MyApp.Api` (یا `MyApp.Web`)  
  - Controllers یا Minimal APIs، فیلترها، Middlewareها، کانفیگ DI Container

به این ترتیب، **وابستگی‌ها از `Api` و `Infrastructure` به سمت `Application` و از `Application` به سمت `Domain`** است و لایهٔ Domain از همه‌چیز دیگر مستقل می‌ماند.

---

## ۴. جریان داده در Clean Architecture

1. **ورودی** از طریق یک Controller (لایه Interface Adapters) دریافت می‌شود.
2. Controller ورودی را به یک Input DTO تبدیل کرده و Use Case مربوطه را فراخوانی می‌کند.
3. Use Case (لایه Application) منطق کسب‌وکار را با کمک Entities اجرا می‌کند و در صورت نیاز از طریق اینترفیس‌ها (مثلاً Repository) داده را ذخیره/بازیابی می‌کند.
4. نتیجه توسط Use Case به یک Output DTO تبدیل و به Presenter بازگردانده می‌شود.
5. Presenter Output DTO را به فرمت مناسب (مثلاً JSON) تبدیل کرده و به کلاینت تحویل می‌دهد.

**مهم:** هیچ یک از لایه‌های داخلی از وجود لایه‌های بیرونی خبر ندارند. برای مثال، Use Case نمی‌داند که Controller وجود دارد یا Presenter چگونه کار می‌کند.

---

## ۵. مزایا (چی به دست می‌آوریم؟)

### ۵.۱. استقلال از فریم‌ورک
- می‌توانید فریم‌ورک خود را عوض کنید (مثلاً از ASP.NET MVC به Blazor) بدون اینکه به لایه‌های Domain و Application دست بزنید.

### ۵.۲. تست‌پذیری بالا
- لایه Domain و Application را می‌توانید بدون وابستگی به دیتابیس، وب، یا هر زیرساخت دیگری تست کنید (Unit Test).

### ۵.۳. استقلال از UI
- می‌توانید UI را به‌راحتی تغییر دهید (مثلاً از Web به Mobile) بدون تأثیر بر لایه‌های داخلی.

### ۵.۴. استقلال از دیتابیس
- می‌توانید دیتابیس را عوض کنید (مثلاً از SQL Server به MongoDB) با تغییر فقط لایه Infrastructure.

### ۵.۵. قابلیت نگهداری (Maintainability)
- با جداسازی مسئولیت‌ها، تغییرات محدود به یک لایه می‌شوند و تأثیر کمتری روی بقیه دارند.

### ۵.۶. هماهنگی با DDD
- Clean Architecture بستر ایده‌آلی برای پیاده‌سازی Domain-Driven Design است، زیرا Domain را در مرکز قرار می‌دهد.

---

## ۶. معایب و چالش‌ها (بهایی که می‌دهیم)

### ۶.۱. پیچیدگی اولیه
- برای پروژه‌های کوچک، این معماری می‌تواند Over-engineering باشد. تعداد لایه‌ها و مفاهیم ممکن است برای تیم جدید گیج‌کننده باشد.

### ۶.۲. منحنی یادگیری
- اعضای تیم باید با مفاهیمی مانند Dependency Inversion، Use Case‌ها، و جداسازی لایه‌ها آشنا باشند.

### ۶.۳. افزایش تعداد فایل‌ها و کلاس‌ها
- هر Use Case معمولاً چندین کلاس (Command, Handler, DTO, Validator, ...) دارد که باعث افزایش حجم پروژه می‌شود.

### ۶.۴. دشواری در تغییر مرزها
- اگر مرزهای Bounded Context را اشتباه تعریف کنید، تغییر آنها در Clean Architecture ممکن است پرهزینه باشد (اما همچنان از معماری لایه‌ای بهتر است).

### ۶.۵. ممکن است به **صلبیت** منجر شود
- اگر تیم بیش از حد به لایه‌بندی پایبند باشد و از ابسترکشن‌های غیرضروری استفاده کند، توسعه کند می‌شود.

---

## ۷. مقایسه با معماری‌های مشابه

| ویژگی | Clean Architecture | Hexagonal (Ports & Adapters) | Onion Architecture | Layered |
|--------|---------------------|-------------------------------|---------------------|---------|
| **تمرکز** | لایه‌های مشخص با Dependency Rule | پورت‌ها و Adapterها | لایه‌های دایره‌ای دور هسته | لایه‌های افقی |
| **جهت وابستگی** | به سمت داخل | به سمت هسته | به سمت هسته | به سمت پایین |
| **تست‌پذیری** | بالا | بالا | بالا | متوسط |
| **جداسازی Domain** | کامل | کامل | کامل | ممکن است قاطی شود |
| **محبوبیت** | بسیار بالا | بالا | بالا (در .NET) | بسیار بالا (سنتی) |

در عمل، این سه معماری (Clean, Hexagonal, Onion) بسیار شبیه هم هستند و تفاوت‌ها بیشتر در نام‌گذاری و تأکید است. می‌توان گفت Clean Architecture نسخه‌ای تکامل‌یافته و نام‌گذاری‌شده از این ایده‌هاست.

---

## ۸. چه زمانی از Clean Architecture استفاده کنیم؟ چه زمانی نه؟

### ✅ موارد مناسب

- **پروژه‌های بلندمدت و پیچیده** که نیاز به نگهداری و توسعه مداوم دارند.
- **سیستم‌هایی با منطق کسب‌وکار پیچیده** (مناسب برای DDD).
- **پروژه‌هایی که احتمال تغییر تکنولوژی (دیتابیس، فریم‌ورک) وجود دارد**.
- **تیم‌های با تجربه و متوسط به بالا** که اصول SOLID و معماری را می‌دانند.
- **پروژه‌هایی که نیاز به تست‌پذیری بالا دارند** (مثلاً سیستم‌های مالی، پزشکی).

### ❌ موارد نامناسب

- **پروژه‌های خیلی کوچک** (MVP، پروتوتایپ، یا ابزار ساده).
- **پروژه‌های CRUD با منطق ساده** (یک لایه‌بندی ساده با Repository کافی است).
- **تیم‌های تازه‌کار** (اگر اعضای تیم SOLID را خوب درک نکرده‌اند، ممکن است معماری را خراب کنند).
- **زمان محدود** (اگر ددلاین بسیار نزدیک است، شاید نتوانید زمان کافی برای طراحی لایه‌ها صرف کنید).

---

## ۹. نکات مصاحبه

### سوالات رایج

**۱. تفاوت Clean Architecture با Onion و Hexagonal چیست؟**
> این سه معماری تفاوت‌های جزئی در نام‌گذاری و تأکید دارند، اما همگی قانون وابستگی یکسان (به سمت داخل) را پیروی می‌کنند. Clean Architecture لایه‌های مشخصی با نام‌های Entities, Use Cases, Interface Adapters, Frameworks دارد.

**۲. Dependency Rule را توضیح دهید.**
> Dependency Rule می‌گوید وابستگی‌ها فقط می‌توانند به سمت داخل باشند. لایه‌های بیرونی می‌توانند به لایه‌های داخلی وابسته باشند، اما لایه‌های داخلی هرگز نباید به لایه‌های بیرونی وابسته باشند.

**۳. چه چیزی را در لایه Entities قرار می‌دهیم و چه چیزی را در Application؟**
> Entities شامل قوانین کلی کسب‌وکار (مثل محاسبه مالیات، اعتبارسنجی سفارش) است. Application شامل قوانین مختص به Use Case (مثل هماهنگی بین چند Entity) است. اگر منطق به Entity خاصی وابسته نیست، می‌تواند در Application باشد.

**۴. چگونه Repository را در Clean Architecture پیاده‌سازی می‌کنید؟**
> اینترفیس Repository را در لایه Domain (یا Application) تعریف می‌کنیم. پیاده‌سازی آن در لایه Infrastructure انجام می‌شود. به این ترتیب، لایه‌های داخلی از دیتابیس بی‌خبر می‌مانند.

**۵. آیا می‌توان در Clean Architecture از ORM استفاده کرد؟**
> بله، اما ORM باید در لایه Infrastructure باقی بماند. Entities شما نباید به کلاس‌های ORM وابسته باشند. می‌توانید از الگوی Repository برای جداسازی استفاده کنید.

**۶. چطور تست‌پذیری در Clean Architecture افزایش می‌یابد؟**
> به دلیل جداسازی و وارونگی وابستگی، می‌توانیم لایه‌های داخلی را بدون نیاز به زیرساخت (دیتابیس، شبکه) تست کنیم. به سادگی می‌توانیم وابستگی‌ها را با Mock جایگزین کنیم.

**۷. آیا Clean Architecture برای میکروسرویس‌ها مناسب است؟**
> بله، هر میکروسرویس می‌تواند معماری داخلی خود را Clean داشته باشد. این به استقلال و تست‌پذیری هر سرویس کمک می‌کند.

### اشتباهات رایج (Red Flag)

- **تعریف اینترفیس‌ها در لایه Infrastructure** (اینترفیس باید در لایه Domain/Application تعریف شود).
- **نشت دیتابیس به Use Case** (مثلاً استفاده از DbContext یا SQL Query در لایه Application).
- **قرار دادن منطق کسب‌وکار در Controller** (کنترلر فقط باید ورودی را به Use Case بدهد).
- **استفاده از Static Method‌ها که وابستگی را مخفی می‌کنند**.
- **عدم رعایت Dependency Rule** (مثلاً استفاده از کلاس‌های لایه خارجی در لایه Domain).
- **ایجاد لایه‌های اضافی و بی‌فایده** (Over-engineering).

---

## ۱۰. جمع‌بندی

**Clean Architecture** یک رویکرد قدرتمند برای طراحی سیستم‌های نرم‌افزاری است که:

- **قابلیت نگهداری** را افزایش می‌دهد.
- **تست‌پذیری** را بهبود می‌بخشد.
- **استقلال از فریم‌ورک** و زیرساخت را تضمین می‌کند.
- با **DDD** و **CQRS** هم‌خوانی عالی دارد.

اما بهای آن **پیچیدگی اولیه** و **منحنی یادگیری** است. استفاده از آن برای پروژه‌های ساده توصیه نمی‌شود.

**نکته نهایی:** Clean Architecture یک الگو است، نه قانون آهنین. می‌توانید آن را با نیازهای پروژه خود تطبیق دهید. مهم درک اصول و رعایت Dependency Rule است.

---

## منابع و مطالعه بیشتر

- **کتاب:** *Clean Architecture: A Craftsman's Guide to Software Structure and Design* – Robert C. Martin
- **مقاله:** [The Clean Architecture (Uncle Bob)](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- **مطالب مرتبط:** [[Hexagonal Architecture]]، [[Onion Architecture]]، [[CQRS]]، [[SOLID]]
- **نمونه پروژه:** [eShopOnWeb (Microsoft)](https://github.com/dotnet-architecture/eShopOnWeb) – یک مثال عملی از Clean Architecture با ASP.NET Core
- **ویدیو:** [Clean Architecture with ASP.NET Core (NDC Conference)](https://www.youtube.com/watch?v=dK4Yb6-LxAk)