# فاز ۱ - نمای کلی معماری‌ها

## معماری نرم‌افزار یعنی چی؟

یه جورایی مثل نقشه ساختمونه. وقتی می‌خوای خونه بسازی، اول نقشه می‌کشی. معماری نرم‌افزار هم همینه: قبل از کد زدن، مشخص می‌کنی اجزا کجا باشن، چطور به هم وصل باشن، و چه کسی مسئول چی باشه.

**چرا اصلاً به معماری فکر کنیم؟**  
سوال خوبیه. یه وقتی پروژه کوچیکه و همه چیز تو یه فایل یا چند کلاس خلاصه میشه. ولی وقتی رشد می‌کنه، اگه از اول فکر نکرده باشی کد کجا بره و منطق کجا باشه، بعداً گیر می‌کنی. تغییر یک چیز، ده تا چیز دیگه رو خراب می‌کنه. تست نوشتن غیرممکن میشه. تیم جدید نمی‌تونه سر در بیاره. معماری خوب این دردها رو کم می‌کنه.

**یک معماری خوب چیه؟**  
با یه نگاه به ساختارش باید حدس بزنی برای چه بیزنسی نوشته شده. هدفش اینه که درک، نگهداری و deploy رو راحت‌تر کنه. در نهایت هزینه کل پروژه کمتر بشه و تیم راحت‌تر کار کنه.

**معمار نرم‌افزار چیکار می‌کنه؟**  
روی بیزنس و use case ها تمرکز می‌کنه. انتخاب ابزار و تصمیمات فنی رو معمولاً با تیم انجام میده، نه تنهایی. معماری که همیشه حرف آخر رو میزنه و کسی رو گوش نمیده، معمولاً نتیجه‌اش بد میشه.

---

## طبقه‌بندی معماری‌ها

### معماری‌های اصلی (ساختاری)
این‌ها ساختار کلی سیستم را تعیین می‌کنند.

| معماری                        | توضیح کوتاه                                       |
| ----------------------------- | ------------------------------------------------- |
| [[Monolith Architecture]]     | همه چیز در یک deployment واحد                     |
| [[Data-Centric Architecture]] | دیتابیس مرکز است                                  |
| [[Layered - N-Tier]]          | لایه‌های افقی مشخص (Presentation, Business, Data) |
| [[Hexagonal Architecture]]    | Domain در مرکز، Port و Adapter                    |
| [[Onion Architecture]]        | Domain در هسته، لایه‌های دایره‌ای                 |
| [[Clean Architecture]]        | نسخه Uncle Bob از Domain-Centric                  |
| [[Modular Monolith]]          | Monolith با ماژول‌های جداشده                      |
| [[Microservices]]             | سرویس‌های کوچک مستقل                              |

### الگوهای معماری (رفتاری/تکمیلی)
این‌ها رفتار و تعامل اجزا را مشخص می‌کنند.

| الگو             | توضیح کوتاه             |
| ---------------- | ----------------------- |
| [[CQRS]]         | جداسازی Command و Query |
| [[Event-Driven]] | ارتباط از طریق رویداد   |

---

## یادداشت‌های معماری‌ها

در ادامه هر معماری به صورت جداگانه توضیح داده شده است. این یادداشت‌ها با تگ `#معماری/الگو` در Obsidian قابل دسته‌بندی هستند.

---

### [[Monolith Architecture]]

**تعریف دقیق:**  
یک برنامه واحد که همه ماژول‌ها و عملکردها در یک فرآیند یا کانتینر واحد اجرا می‌شوند. کد منبع، رابط کاربری، منطق کسب‌وکار و دسترسی به داده همه در یک پروژه قرار دارند.  
*منبع: "Building Microservices" از Sam Newman*

**هدف اصلی:**  
سادگی در توسعه اولیه، استقرار و مقیاس‌دهی عمودی. مناسب برای پروژه‌های کوچک و تیم‌های کم‌نفر.

**مثال واقعی:**  
بسیاری از استارت‌آپ‌ها با مونولیت شروع می‌کنند، مثلاً نسخه اولیه Shopify یا Netflix (قبل از مهاجرت به میکروسرویس).

**نمودار:**  
![Monolith Diagram](https://via.placeholder.com/400?text=Monolith+Diagram)  
*(می‌توانید با Draw.io رسم کنید)*

**وابستگی‌ها و پیش‌نیازها:**  
نیاز به دانش یکپارچه از کل سیستم دارد. برای جدا کردن بخش‌ها بعداً نیاز به بازنویسی است.

**اصول SOLID مرتبط:**  
- **SRP:** در مونولیت اغلب نقض می‌شود چون کلاس‌ها چندین مسئولیت دارند.  
- **DIP:** معمولاً وابستگی مستقیم به پیاده‌سازی‌ها وجود دارد.

**ارتباط با DDD:**  
می‌توان از Bounded Context در مونولیت استفاده کرد اما مرزها فیزیکی نیستند.

---

### [[Data-Centric Architecture]]

**تعریف دقیق:**  
دیتابیس مرکز معماری است و منطق کسب‌وکار بیشتر در پایگاه داده (با Stored Procedure) یا در لایه نازکی بالای آن پیاده می‌شود.  
*منبع: Patterns of Enterprise Application Architecture (Martin Fowler)*

**هدف اصلی:**  
سرعت بالا در پروژه‌های ساده و CRUD محور. استفاده حداکثری از قابلیت‌های پایگاه داده.

**مثال واقعی:**  
سیستم‌های قدیمی مبتنی بر Oracle Forms یا بسیاری از برنامه‌های داخلی سازمانی.

**نمودار:**  
![Data-Centric Diagram](https://via.placeholder.com/400?text=Data-Centric+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
نیاز به دانش قوی از پایگاه داده و Stored Procedure. تست‌پذیری پایین.

**اصول SOLID مرتبط:**  
- **DIP:** معمولاً نقض می‌شود زیرا لایه بالایی به دیتابیس وابسته است.  
- **OCP:** تغییر منطق کسب‌وکار نیاز به تغییر در دیتابیس دارد.

**ارتباط با DDD:**  
معمولاً مدل غنی وجود ندارد و مدل آنِمیك (Anemic) است.

---

### [[Layered Architecture]]

**تعریف دقیق:**  
سیستم به لایه‌های افقی مانند Presentation، Business Logic، Data Access تقسیم می‌شود. هر لایه فقط با لایه زیرین خود ارتباط دارد.  
*منبع: "Software Architecture Patterns" از Mark Richards*

**هدف اصلی:**  
جداسازی مسئولیت‌ها و افزایش قابلیت نگهداری.

**مثال واقعی:**  
اکثر پروژه‌های ASP.NET MVC قدیمی با لایه‌های Controller، Service، Repository.

**نمودار:**  
![Layered Diagram](https://via.placeholder.com/400?text=Layered+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
شناخت لایه‌ها و جلوگیری از عبور از لایه‌ها.

**اصول SOLID مرتبط:**  
- **SRP:** هر لایه یک مسئولیت دارد.  
- **DIP:** ممکن است لایه‌ها به پیاده‌سازی‌های مشخص وابسته باشند (نقض DIP).

**ارتباط با DDD:**  
می‌تواند لایه Domain داشته باشد اما اغلب با مدل آنِمیك همراه است.

---

### [[Hexagonal Architecture]]

**تعریف دقیق:**  
معماری پورت‌ها و Adapterها. Domain در مرکز است و از طریق پورت‌ها (اینترفیس) با جهان خارج ارتباط برقرار می‌کند. Adapterها پیاده‌سازی پورت‌ها را برای هر تکنولوژی (وب، دیتابیس، پیام‌رسان) فراهم می‌کنند.  
*منبع: Alistair Cockburn*

**هدف اصلی:**  
جداسازی کامل Domain از جزئیات زیرساخت و افزایش تست‌پذیری.

**مثال واقعی:**  
سیستم‌هایی که نیاز به تعویض پایگاه داده یا رابط کاربری دارند، مثلاً یک سامانه بانکی با Domain پیچیده.

**نمودار:**  
![Hexagonal Diagram](https://via.placeholder.com/400?text=Hexagonal+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
نیاز به طراحی دقیق پورت‌ها و درک اینورژن وابستگی.

**اصول SOLID مرتبط:**  
- **DIP:** وابستگی به انتزاع (پورت) وابسته است.  
- **OCP:** اضافه کردن Adapter جدید بدون تغییر Domain.

**ارتباط با DDD:**  
بستر عالی برای پیاده‌سازی Domain-Driven Design؛ Bounded Context ها در Domain قرار می‌گیرند.

---

### [[Onion Architecture]]

**تعریف دقیق:**  
مشابه Hexagonal اما با دید لایه‌ای دایره‌ای. Domain Model در هسته، سپس لایه‌های Domain Services، Application Services، Infrastructure و UI. وابستگی‌ها به سمت داخل است.  
*منبع: Jeffrey Palermo*

**هدف اصلی:**  
حفظ استقلال Domain از لایه‌های بیرونی و اعمال Dependency Inversion.

**مثال واقعی:**  
پروژه‌های .NET Core که از الگوی Onion استفاده می‌کنند، مانند بسیاری از پروژه‌های open-source.

**نمودار:**  
![Onion Diagram](https://via.placeholder.com/400?text=Onion+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
مشابه Hexagonal؛ نیاز به Domain Model قوی.

**اصول SOLID مرتبط:**  
- **DIP:** لایه‌های بیرونی به لایه‌های درونی وابسته‌اند.  
- **OCP:** تغییر در لایه‌های بیرونی بر هسته تأثیر نمی‌گذارد.

**ارتباط با DDD:**  
هسته می‌تواند شامل Entities، Value Objects و Aggregate ها باشد.

---

### [[Clean Architecture]]

**تعریف دقیق:**  
نسخه‌ای از Domain-Centric با لایه‌های مشخص: Entities (Enterprise Business Rules)، Use Cases (Application Business Rules)، Interface Adapters، Frameworks & Drivers. قانون وابستگی: وابستگی‌ها فقط به سمت داخل.  
*منبع: کتاب "Clean Architecture" از Robert C. Martin*

**هدف اصلی:**  
جداسازی کامل Business Rules از فریمورک‌ها و ابزارها، ایجاد سیستمی تست‌پذیر و مستقل از تکنولوژی.

**مثال واقعی:**  
بسیاری از پروژه‌های مدرن در شرکت‌های نرم‌افزاری که به دنبال نگهداری بلندمدت هستند.

**نمودار:**  
![Clean Architecture Diagram](https://via.placeholder.com/400?text=Clean+Architecture+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
نیاز به درک قوی از DIP و معماری لایه‌ای. داشتن Domain Model غنی (Rich Model) ضروری است.

**اصول SOLID مرتبط:**  
- **DIP:** لایه‌های داخلی اینترفیس تعریف می‌کنند، لایه‌های بیرونی پیاده‌سازی.  
- **OCP:** Use Case ها در برابر تغییرات تکنولوژی بسته هستند.  
- **SRP:** هر لایه یک دلیل برای تغییر دارد.

**ارتباط با DDD:**  
لایه Entities همان Domain Model است و Use Case ها می‌توانند با Aggregate ها کار کنند.

#### جزئیات لایه‌های Clean Architecture

**Entities Layer (Domain / Core)**  
- محتویات: Entity های غنی، Value Objectها، Aggregateها، Domain Services، Domain Events.  
- وابستگی به هیچ فریمورکی ندارد (Pure C#/Java).  
- منطق کسب‌وکار در این لایه پیاده می‌شود.  
- مثال:  
```csharp
public class Order
{
    private List<OrderItem> _items = new();
    public IReadOnlyList<OrderItem> Items => _items;
    public void AddItem(Product product, int quantity)
    {
        if (quantity <= 0) throw new DomainException("Quantity must be positive");
        _items.Add(new OrderItem(product, quantity));
    }
}
```

**Application Layer (Use Cases)**  
- محتویات: Commandها، Query‌ها، Handlerها، DTOها، اینترفیس‌های Repository (که در Domain هم تعریف می‌شوند).  
- نقش: هماهنگ‌کننده (Orchestrator)؛ بدون منطق کسب‌وکار.  
- مثال:  
```csharp
public class CreateOrderHandler : IRequestHandler<CreateOrderCommand, OrderDto>
{
    private readonly IOrderRepository _orderRepo;
    private readonly IUnitOfWork _unitOfWork;
    public CreateOrderHandler(IOrderRepository orderRepo, IUnitOfWork unitOfWork)
    {
        _orderRepo = orderRepo;
        _unitOfWork = unitOfWork;
    }
    public async Task<OrderDto> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        var order = new Order(request.CustomerId);
        // ...
        _orderRepo.Add(order);
        await _unitOfWork.SaveChangesAsync();
        return new OrderDto(order.Id);
    }
}
```

**Interface Adapters**  
- محتویات: Controllerها، Presenterها، ViewModelها، Gatewayها.  
- وظیفه: تبدیل داده‌های ورودی به فرمت مناسب برای Use Case و برعکس.  
- مثال:  
```csharp
[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    private readonly IMediator _mediator;
    public OrdersController(IMediator mediator) => _mediator = mediator;
    [HttpPost]
    public async Task<ActionResult<OrderDto>> Create(CreateOrderRequest request)
    {
        var command = new CreateOrderCommand(request.CustomerId, request.Items);
        var result = await _mediator.Send(command);
        return Ok(result);
    }
}
```

**Frameworks & Drivers**  
- محتویات: EF Core، ASP.NET Core، هر کتابخانه خارجی.  
- وظیفه: پیاده‌سازی اینترفیس‌های تعریف‌شده در لایه‌های داخلی.  
- مثال:  
```csharp
public class OrderRepository : IOrderRepository
{
    private readonly AppDbContext _context;
    public OrderRepository(AppDbContext context) => _context = context;
    public async Task<Order> GetByIdAsync(int id) => await _context.Orders.FindAsync(id);
    public void Add(Order order) => _context.Orders.Add(order);
}
```

**پیاده‌سازی Clean Architecture در ASP.NET Core**  
برای مطالعه بیشتر به یادداشت [[Implementation of Clean Architecture in ASP.NET Core]] مراجعه کنید. نمونه کد کامل را می‌توان در مخازن نمونهٔ Clean Architecture در GitHub جستجو کرد.

---

### [[Modular Monolith]]

**تعریف دقیق:**  
یک مونولیت که به ماژول‌های مستقل با مرزهای مشخص تقسیم شده است. هر ماژول می‌تواند به صورت جداگانه توسعه و تست شود اما در نهایت در یک فرآیند واحد استقرار می‌یابد.  
*منبع: "Modular Monolith: A Primer" از Simon Brown*

**هدف اصلی:**  
ترکیب سادگی مونولیت با مزایای ماژولار بودن، به عنوان گامی میانی پیش از میکروسرویس‌ها.

**مثال واقعی:**  
سیستم‌های بزرگی که به تدریج به سمت میکروسرویس می‌روند، مانند eBay در گذشته.

**نمودار:**  
![Modular Monolith Diagram](https://via.placeholder.com/400?text=Modular+Monolith+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
نیاز به معماری داخلی منظم و جلوگیری از وابستگی بین ماژول‌ها.

**اصول SOLID مرتبط:**  
- **SRP:** هر ماژول یک مسئولیت دارد.  
- **OCP:** ماژول‌ها برای توسعه باز و برای تغییر بسته.

**ارتباط با DDD:**  
ایده‌آل برای پیاده‌سازی Bounded Context در یک فرآیند واحد.

---

### [[CQRS]]

**تعریف دقیق:**  
جداسازی عملیات نوشتن (Command) و خواندن (Query) به مدل‌های متفاوت. هر کدام می‌توانند بهینه‌سازی جداگانه داشته باشند.  
*منبع: Martin Fowler*

**هدف اصلی:**  
مقیاس‌پذیری بالا در سیستم‌های با حجم خواندن و نوشتن نامتوازن، کاهش پیچیدگی در مدل‌های یکپارچه.

**مثال واقعی:**  
سیستم‌های گزارش‌گیری بزرگ، فروشگاه‌های اینترنتی با ترافیک خواندن بالا.

**نمودار:**  
![CQRS Diagram](https://via.placeholder.com/400?text=CQRS+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
اغلب با Event Sourcing همراه می‌شود. نیاز به مدیریت consistency نهایی.

**اصول SOLID مرتبط:**  
- **SRP:** جداسازی مسئولیت Command و Query.  
- **ISP:** اینترفیس‌های مجزا برای خواندن و نوشتن.

**ارتباط با Clean Architecture:**  
معمولاً Use Case ها به Command و Query Handler تبدیل می‌شوند. در ASP.NET Core با MediatR پیاده‌سازی می‌شود.

**مثال کد (Command Handler):**  
```csharp
public class CreateOrderCommand : IRequest<int> { /* ... */ }
public class CreateOrderHandler : IRequestHandler<CreateOrderCommand, int>
{
    public async Task<int> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        // logic
    }
}
```

---

### [[Event-Driven Architecture]]

**تعریف دقیق:**  
ارتباط بین سرویس‌ها از طریق رویدادها (Event) انجام می‌شود. سرویس‌ها رویداد منتشر می‌کنند و دیگر سرویس‌ها به آن واکنش نشان می‌دهند.  
*منبع: "Building Event-Driven Microservices" از Adam Bellemare*

**هدف اصلی:**  
کاهش وابستگی مستقیم، افزایش مقیاس‌پذیری و انعطاف‌پذیری.

**مثال واقعی:**  
سیستم‌های پردازش سفارش که پس از ثبت سفارش، رویدادهایی برای انبار، صورتحساب و ارسال ایمیل تولید می‌شود.

**نمودار:**  
![Event-Driven Diagram](https://via.placeholder.com/400?text=Event-Driven+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
نیاز به زیرساخت پیام‌رسان (مثل RabbitMQ، Kafka) و مدیریت ordering و idempotency.

**اصول SOLID مرتبط:**  
- **DIP:** سرویس‌ها به جای وابستگی مستقیم، به رویداد وابسته‌اند.  
- **OCP:** اضافه کردن واکنش‌گر جدید بدون تغییر مبدأ.

**ارتباط با Microservices:**  
اغلب در معماری میکروسرویس‌ها برای ارتباط ناهمگام استفاده می‌شود.

---

### [[Microservices]]

**تعریف دقیق:**  
سبکی از معماری که در آن برنامه به مجموعه‌ای از سرویس‌های کوچک، مستقل و قابل استقرار جداگانه تقسیم می‌شود. هر سرویس حول یک قابلیت کسب‌وکار ساخته می‌شود و داده خود را مدیریت می‌کند.  
*منبع: "Microservices" از James Lewis و Martin Fowler*

**هدف اصلی:**  
مقیاس‌پذیری مستقل، استقلال تیم‌ها، انعطاف در انتخاب تکنولوژی.

**مثال واقعی:**  
Netflix، Amazon، Uber.

**نمودار:**  
![Microservices Diagram](https://via.placeholder.com/400?text=Microservices+Diagram)

**وابستگی‌ها و پیش‌نیازها:**  
نیاز به فرهنگ DevOps، شبکه‌سازی، مدیریت سرویس‌ها (Service Discovery)، مانیتورینگ.

**اصول SOLID مرتبط:**  
- **SRP:** هر سرویس یک مسئولیت مشخص دارد.  
- **ISP:** سرویس‌ها اینترفیس‌های کوچک از طریق API ارائه می‌دهند.

**ارتباط با DDD:**  
هر سرویس می‌تواند یک Bounded Context را پیاده‌سازی کند.

---

## یادداشت‌های تکمیلی

### [[DDD in Modern Architecture]]

**تعریف:**  
Domain-Driven Design (DDD) مجموعه‌ای از اصول و الگوها برای مدل‌سازی حوزه‌های پیچیده کسب‌وکار است. در معماری‌های مدرن مانند Clean، Hexagonal و Microservices، DDD نقش اساسی در طراحی Domain ایفا می‌کند.

**ارکان اصلی:**  
- **Bounded Context:** مرزهای مشخص برای هر مدل.  
- **Entity:** موجودیت با هویت.  
- **Value Object:** شیء بدون هویت.  
- **Aggregate:** خوشه‌ای از موجودیت‌ها با ریشه (Aggregate Root).  
- **Repository:** مکانیزم دسترسی به Aggregate.  
- **Domain Service:** منطق کسب‌وکاری که در هیچ Entity یا Value Object جا نمی‌گیرد.

**ارتباط با Clean Architecture:**  
Domain در مرکز (لایه Entities) قرار دارد و همه اجزای DDD در آنجا پیاده می‌شوند. Use Case ها با Aggregate ها کار می‌کنند و Repository ها از طریق اینترفیس در Domain تعریف می‌شوند.

**مثال:**  
```csharp
public class Order : AggregateRoot
{
    public int Id { get; private set; }
    private List<OrderItem> _items = new();
    public void AddItem(Product product, int quantity) { /* ... */ }
}
```

---

### [[SOLID Principles in Architecture]]

مرور سریع اصول SOLID و کاربردشان در معماری:

- **S (Single Responsibility):** هر لایه/ماژول باید یک دلیل برای تغییر داشته باشد.  
- **O (Open/Closed):** لایه‌ها باید برای توسعه باز و برای تغییر بسته باشند.  
- **L (Liskov Substitution):** کلاس‌های مشتق‌شده باید بتوانند جایگزین پایه شوند.  
- **I (Interface Segregation):** اینترفیس‌های کوچک و خاص.  
- **D (Dependency Inversion):** وابستگی به انتزاع، نه به پیاده‌سازی.

در Clean Architecture، DIP و OCP به شدت رعایت می‌شوند.

---

### [[Comparison of Domain-Centric Architectures]]

| معیار | Hexagonal | Onion | Clean |
|-------|-----------|-------|-------|
| **تمرکز اصلی** | Ports & Adapters | لایه‌بندی دایره‌ای | لایه‌های مشخص با نام |
| **نحوه مدیریت وابستگی** | وابستگی به Domain از طریق پورت | وابستگی به سمت داخل | Dependency Rule |
| **میزان انعطاف** | بالا | بالا | بالا |
| **محبوبیت** | متوسط | بالا (در .NET) | بسیار بالا |
| **مناسب برای DDD** | عالی | عالی | عالی |

در عمل، این سه معماری بسیار شبیه هم هستند و انتخاب بین آنها سلیقه‌ای است.

---

### [[Common Anti-Patterns]]

**۱. وابستگی معکوس (Domain به Infrastructure)**  
❌ اشتباه:  
```csharp
public class Order
{
    public void Save() => new DbContext().SaveChanges(); // وابستگی به EF
}
```
✅ راه‌حل:  
```csharp
public interface IOrderRepository { void Save(Order order); }
public class Order
{
    // فقط منطق Domain
}
```

**۲. منطق بیزنس در لایه اشتباه (Controller چاق)**  
❌ اشتباه:  
```csharp
public IActionResult CreateOrder(CreateOrderRequest request)
{
    if (request.Quantity <= 0) return BadRequest();
    // محاسبات و ذخیره
}
```
✅ راه‌حل: انتقال به Domain و Application.

**۳. لایه Application چاق (انجام همه کارها)**  
❌ اشتباه:  
```csharp
public class CreateOrderHandler
{
    public void Handle(...)
    {
        // validation
        // محاسبه تخفیف
        // ذخیره در دیتابیس
        // ارسال ایمیل
    }
}
```
✅ راه‌حل: منطق محاسباتی به Domain، ایمیل به سرویس زیرساخت.

**۴. Entity های Anemic**  
❌ اشتباه:  
```csharp
public class Order
{
    public int Id { get; set; }
    public List<OrderItem> Items { get; set; }
}
```
✅ راه‌حل:  
```csharp
public class Order
{
    private List<OrderItem> _items = new();
    public IReadOnlyList<OrderItem> Items => _items;
    public void AddItem(Product product, int quantity) { /* validation and logic */ }
}
```

**۵. عبور از لایه‌ها (Controller مستقیم به Repository)**  
❌ اشتباه:  
```csharp
public class OrdersController : Controller
{
    private readonly AppDbContext _db;
    public IActionResult Get() => Ok(_db.Orders.ToList());
}
```
✅ راه‌حل: عبور از لایه Application.

---

### [[Decision Making in Architecture]]

#### چالش‌ها و تصمیم‌گیری‌ها

**سوالات مفهومی:**  
- **تفاوت Anemic و Rich Model چیست؟**  
  Anemic: موجودیت‌ها فقط داده دارند، منطق در سرویس‌ها. Rich: موجودیت‌ها رفتار دارند.  
- **چرا Domain-Centric بهتر از Data-Centric است؟**  
  برای سیستم‌های با منطق پیچیده و نیاز به تست‌پذیری و نگهداری بلندمدت.

**سوالات عملی:**  
- **چطور شروع کنم؟**  
  از یک پروژه ساده با Layered شروع کنید، سپس به Clean بروید. مطالعه کتاب‌ها و نمونه‌های GitHub.  
- **چطور تیم را قانع کنم؟**  
  با آمار: زمان رفع باگ، تعداد خطاها، سرعت افزودن feature جدید. refactor تدریجی.

**سوالات مدیریتی:**  
- **چطور به مدیر توضیح دهم؟**  
  بر اساس هزینه/فایده: معماری خوب هزینه اولیه دارد اما در بلندمدت بازدهی دارد. مثال‌های ملموس.

**معاوضه‌ها (Trade-offs):**  
- پیچیدگی در مقابل انعطاف  
- زمان توسعه اولیه در مقابل هزینه نگهداری  
- مقیاس‌پذیری در مقابل سادگی

---

## تصویر کلی (Big Picture)

```
                          ┌─────────────────────────────────────┐
                          │         انتخاب معماری               │
                          └─────────────────┬───────────────────┘
                                            │
                    ┌───────────────────────┼───────────────────────┐
                    │                       │                       │
              پروژه کوچک؟             منطق پیچیده؟            مقیاس بالا؟
                    │                       │                       │
                    ▼                       ▼                       ▼
            ┌───────────────┐       ┌───────────────┐       ┌───────────────┐
            │ Data-Centric  │       │ Clean/Onion/  │       │ Microservices │
            │ یا Layered    │       │ Hexagonal     │       │ Event-Driven  │
            └───────────────┘       └───────────────┘       └───────────────┘
                    │                       │                       │
                    │                 ┌─────┴─────┐                 │
                    │                 │ Modular   │                 │
                    │                 │ Monolith  │ ←───────────────┘
                    │                 │ (راه میانی)│   قبل از Microservices
                    │                 └───────────┘
                    │
                    └──► می‌توان با CQRS، Event-Driven ترکیب کرد
```

**کلید راهنما:**  
- فلش‌ها: مسیرهای پیشنهادی بر اساس نیازها.  
- دسته‌ها (از چپ به راست): ساده و سریع → متعادل → پیچیده و مقیاس‌پذیر.  

**محور ساده → پیچیده:**  
```
Data-Centric ──► Layered ──► Clean/Hexagonal/Onion ──► Modular Monolith ──► Microservices
     │               │              │                         │                    │
   CRUD          منطق متوسط     تست‌پذیری بالا           آمادگی scale        scale واقعی
   تیم 1–3       تیم 3–10        نگهداری بلندمدت          تیم 10+             تیم‌های مستقل
```

---

## منابع و مطالعه بیشتر

**کتاب‌ها:**  
- *Clean Architecture* – Robert C. Martin  
- *Domain-Driven Design* – Eric Evans  
- *Implementing Domain-Driven Design* – Vaughn Vernon  
- *Building Microservices* – Sam Newman  

**مقالات:**  
- [Microservices – Martin Fowler](https://martinfowler.com/articles/microservices.html)  
- [CQRS – Martin Fowler](https://martinfowler.com/bliki/CQRS.html)  
- [The Clean Architecture – Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)  

**ویدیوها:**  
- [کانال GOTO Conferences](https://www.youtube.com/user/GotoConferences)  
- [کانال IAmTimCorey (ویژه .NET)](https://www.youtube.com/user/IAmTimCorey)  

برای هر منبع می‌توانید یادداشت جداگانه با خلاصه و نکات کلیدی ایجاد کنید (مثلاً [[Book Summary - Clean Architecture]]).

---

## ساختار نهایی فاز ۱ در Obsidian

فایل اصلی `Phase 1 - Architectural Overview` با لینک به یادداشت‌های زیر:

- [[Monolith Architecture]]
- [[Data-Centric Architecture]]
- [[Layered Architecture]]
- [[Hexagonal Architecture]]
- [[Onion Architecture]]
- [[Clean Architecture]]
- [[Modular Monolith]]
- [[CQRS]]
- [[Event-Driven Architecture]]
- [[Microservices]]
- [[DDD in Modern Architecture]]
- [[SOLID Principles in Architecture]]
- [[Comparison of Domain-Centric Architectures]]
- [[Common Anti-Patterns]]
- [[Decision Making in Architecture]]
- [[Implementation of Clean Architecture in ASP.NET Core]] (اختیاری)
- [[Book Summary - Clean Architecture]] (و سایر کتاب‌ها)

---

## جمع‌بندی و نکات مصاحبه

**سوال رایج:** «تفاوت Clean Architecture و Onion چیه؟»  
- Onion روی لایه‌های دایره‌ای تمرکز دارد؛ Clean نام‌های مشخص‌تر برای لایه‌ها دارد. در عمل بسیار شبیه هستند.

**سوال:** «چه زمانی Clean Architecture overkill است؟»  
- پروژه‌های خیلی کوچک، CRUD ساده، تیم کم‌تجربه یا زمان کم.

**سوال:** «چطور تست‌پذیری با Clean Architecture بهتر می‌شود؟»  
- Domain و Application را می‌توان بدون وابستگی به زیرساخت تست کرد.

---

