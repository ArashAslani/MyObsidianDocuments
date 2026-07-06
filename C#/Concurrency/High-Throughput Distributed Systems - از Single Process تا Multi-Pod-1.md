# High-Throughput Systems در دنیای Multi-Pod: از Concurrency به Distributed Systems

## 0. چرا این فایل لازمه؟

سه فایل قبلی (Concurrency, Concurrency and multi Threading, Concurrency - Supplementary) دربارهٔ **همگام‌سازی داخل یک process** بودن: چند Thread که **حافظهٔ مشترک (Heap)** دارن و با `lock`، `Interlocked`، `SemaphoreSlim` هماهنگ می‌شن.

مشکل اینجاست: وقتی برنامه‌ت با Kubernetes روی **چند Pod** اجرا می‌شه، هر Pod یه **process جدا** با **Heap جدا**ست. `lock (_obj)` توی Pod شماره ۱ **هیچ ربطی** به `lock (_obj)` توی Pod شماره ۲ نداره — چون هر کدوم یه object جدا توی حافظهٔ جدای خودشون دارن. این دقیقاً همون نکته‌ای هست که خیلی از توسعه‌دهنده‌ها موقع scale-out کردن سرویس‌شون غافلگیر می‌شن.

عنوان **High-Throughput Systems & Clean Architecture Specialist** یعنی باید بلد باشی:
- چطور همون مسائل Race Condition و Concurrency رو وقتی **چند instance از سرویس** روی **چند Pod** اجرا می‌شه حل کنی (نه فقط چند Thread توی یه process)
- چطور با **Message Broker** (RabbitMQ) throughput بالا بیاری بدون از دست دادن پیام یا پردازش تکراری
- چطور با **چند دیتابیس/Replica** consistency رو بدون قربانی کردن performance حفظ کنی
- چطور همهٔ این‌ها رو توی **Clean Architecture** جا بدی بدون اینکه لایه‌ها قاطی بشن

---

## 1. نقشهٔ کلی مسئله: Concurrency تک‌process در برابر Distributed Concurrency

| | **تک Process (چیزی که تا اینجا خوندی)** | **چند Pod / Distributed** |
|---|---|---|
| واحد اجرا | Thread | Instance / Pod / Process |
| حافظهٔ مشترک | Heap مشترک بین Threadها | **هیچ حافظهٔ مشترکی نیست** — فقط از طریق شبکه، DB، یا Cache حرف می‌زنن |
| ابزار هماهنگی | `lock`, `Monitor`, `Interlocked`, `SemaphoreSlim` | Distributed Lock (Redis/DB-based)، Optimistic Concurrency، Message Queue |
| خطر اصلی | Race Condition روی یه متغیر مشترک | Race Condition روی یه **رکورد دیتابیس** یا **پردازش تکراری یک پیام** |
| خرابی جزئی (Partial Failure) | تقریباً نداریم — یا process زنده‌ست یا نیست | **همیشه هست** — یه Pod ممکنه وسط کار crash کنه در حالی که بقیه سالمن |
| Consistency | همیشه Strong (چون یه Heap مشترکه) | باید انتخاب کنی: Strong یا Eventual (CAP Theorem) |

**نکتهٔ کلیدی برای مصاحبه**: وقتی می‌گن «چطور جلوی race condition رو با چند instance می‌گیری؟»، جواب `lock` **غلطه**. باید بگی از **Distributed Lock**، **Optimistic Concurrency (RowVersion)**، یا **Idempotency** استفاده می‌کنم — بسته به سناریو.

---

## 2. Distributed Lock: وقتی `lock` معمولی جواب نمی‌ده

### مسئله
فرض کن یه Background Job داری که باید هر ۵ دقیقه یه بار گزارش تولید کنه. اگه ۳ تا Pod از همین سرویس رو اجرا کنی (برای HA)، هر ۳ تاشون هم‌زمان می‌خوان اون Job رو اجرا کنن. نتیجه: گزارش سه بار تولید می‌شه.

### راه‌حل‌ها

**۱. Distributed Lock با Redis (Redlock یا ساده‌تر: SET NX)**

```csharp
// با StackExchange.Redis
var lockKey = "job:daily-report:lock";
var lockValue = Guid.NewGuid().ToString();
var acquired = await db.StringSetAsync(
    lockKey, lockValue,
    expiry: TimeSpan.FromMinutes(2),
    when: When.NotExists // معادل SET NX
);

if (acquired)
{
    try
    {
        await RunReportJob();
    }
    finally
    {
        // فقط اگه خودت صاحب lock هستی آزادش کن (با یه اسکریپت Lua atomic)
        await ReleaseLockIfOwner(db, lockKey, lockValue);
    }
}
else
{
    // یه Pod دیگه داره کار رو انجام می‌ده؛ من صبر می‌کنم یا کاری نمی‌کنم
}
```

**نکات مهم که تو مصاحبه فرق سطح رو نشون می‌ده:**
- `expiry` (TTL) حیاتیه: اگه Pod نگه‌دارندهٔ lock crash کنه و lock رو آزاد نکنه، بدون TTL هیچ Pod دیگه‌ای هرگز نمی‌تونه اون کار رو انجام بده (Deadlock در سطح Distributed).
- آزادسازی lock باید چک کنه که **خودت صاحبشی** (با مقایسهٔ `lockValue`)، وگرنه ممکنه lock یکی دیگه رو (که بعد از expire شدن مال تو گرفته) پاک کنی.
- Redlock (الگوریتم رسمی Redis برای چند instance Redis) برای سناریوهای حساس‌تره؛ برای اکثر caseهای عملی همون یه Redis instance با replica کافیه.

**۲. Distributed Lock با دیتابیس**

```sql
-- SQL Server: sp_getapplock
EXEC sp_getapplock @Resource = 'daily-report-job', @LockMode = 'Exclusive', @LockTimeout = 0;
-- کار رو انجام بده
EXEC sp_releaseapplock @Resource = 'daily-report-job';
```

یا با یه جدول `Leases` و `UPDATE ... WHERE ExpiresAt < NOW()` (شبیه یه لیز/اجاره با TTL).

**۳. ساده‌ترین راه‌حل: اصلاً چند instance این کار رو اجرا نکنن**
توی Kubernetes می‌تونی یه `CronJob` جدا یا یه `Deployment` با `replicas: 1` فقط برای Job scheduler داشته باشی، به‌جای اینکه توی هر Pod از API سرویس یه Background Service بذاری. این خیلی وقت‌ها ساده‌تر از مدیریت Distributed Lock هست.

**سؤال مصاحبه که ممکنه بپرسن**: «چرا `Environment.MachineName` یا `if (Random.Shared.Next(3) == 0)` برای انتخاب یه Pod به‌عنوان leader جواب نمی‌ده؟» → چون non-deterministic و race-prone هست؛ اگه دو Pod هم‌زمان چک کنن، ممکنه هر دو تصمیم بگیرن leader باشن.

---

## 3. Idempotency: قلب هر سیستم High-Throughput

### چرا مهمه؟
توی سیستم‌های Distributed، **at-least-once delivery** رایج‌تر از **exactly-once** هست (RabbitMQ, Kafka, حتی HTTP retry با timeout). یعنی یه پیام یا درخواست ممکنه **بیشتر از یک بار** پردازش بشه. اگه عملیاتت idempotent نباشه (مثلاً "موجودی رو ۱۰ تا کم کن")، یه retry ساده می‌تونه داده‌ت رو خراب کنه.

### الگوها

**۱. Idempotency Key در سطح API**

```csharp
[HttpPost("orders")]
public async Task<IActionResult> CreateOrder(
    [FromHeader(Name = "Idempotency-Key")] string idempotencyKey,
    CreateOrderRequest request)
{
    var existing = await _db.IdempotencyRecords
        .FirstOrDefaultAsync(x => x.Key == idempotencyKey);

    if (existing is not null)
        return Ok(existing.CachedResponse); // همون جواب قبلی رو برگردون، دوباره پردازش نکن

    var result = await _orderService.CreateOrder(request);

    await _db.IdempotencyRecords.AddAsync(new IdempotencyRecord
    {
        Key = idempotencyKey,
        CachedResponse = result,
        CreatedAt = DateTime.UtcNow
    });
    await _db.SaveChangesAsync();

    return Ok(result);
}
```

**۲. Idempotent Consumer در RabbitMQ**

هر پیام یه `MessageId` منحصربه‌فرد داره. Consumer قبل از پردازش چک می‌کنه که این `MessageId` قبلاً پردازش شده یا نه (توی یه جدول `ProcessedMessages` یا Redis Set با TTL). این کار باید **atomic** با خود عملیات اصلی انجام بشه (توی همون transaction)، وگرنه بین چک کردن و ثبت کردن یه race condition دیگه باز می‌شه.

**۳. طراحی عملیات به‌صورت ذاتاً Idempotent**
به‌جای «موجودی رو ۱۰ تا کم کن» (`stock -= 10`، که هر بار اجرا اثر داره)، بگو «موجودی رو **دقیقاً** ۹۰ کن» یا از یه `OrderId` به‌عنوان کلید استفاده کن که تضمین کنه یه سفارش فقط یه بار موجودی کم می‌کنه (با `UNIQUE constraint` روی `OrderId` توی جدول تراکنش‌های موجودی).

**نکتهٔ Clean Architecture**: منطق idempotency معمولاً توی **Application Layer** (نه Domain) جا می‌گیره، چون به‌جزئیات infrastructure (چطور پیام تکراری تشخیص داده می‌شه) وابسته‌ست؛ ولی قانون کسب‌وکار «یه سفارش نباید دو بار موجودی کم کنه» خودش یه Domain Invariant هست که باید با یه constraint (UNIQUE) یا Aggregate boundary تضمین بشه، نه صرفاً با چک برنامه‌نویسی.

---

## 4. RabbitMQ در عمل: الگوهایی که فرق Junior و Senior رو نشون می‌ده

### Competing Consumers (پایه)
چند Consumer از یه Queue می‌خونن؛ RabbitMQ خودش پیام‌ها رو بین‌شون پخش می‌کنه (round-robin پیش‌فرض). این خودش نوعی **Parallel Processing در سطح Distributed** هست.

```csharp
channel.BasicQos(prefetchSize: 0, prefetchCount: 10, global: false);
// prefetchCount: هر Consumer حداکثر ۱۰ پیام رو هم‌زمان می‌گیره و بعد از ack کردن، پیام بعدی میاد
```

**چرا `prefetchCount` مهمه؟** اگه `0` یا خیلی بزرگ بذاری، یه Consumer ممکنه همهٔ پیام‌ها رو بگیره و بقیه بی‌کار بمونن (Load Imbalance). اگه `1` بذاری، throughput پایین میاد چون هر پیام باید ack بشه قبل از اینکه بعدی برسه. باید بر اساس سرعت پردازش و تعداد Consumer تنظیمش کنی.

### Manual Ack و مدیریت خطا

```csharp
consumer.Received += async (sender, ea) =>
{
    try
    {
        await ProcessMessage(ea.Body);
        channel.BasicAck(ea.DeliveryTag, multiple: false);
    }
    catch (TransientException) // خطای موقت (مثلاً DB موقتاً در دسترس نیست)
    {
        channel.BasicNack(ea.DeliveryTag, multiple: false, requeue: true);
    }
    catch (Exception) // خطای دائمی (داده خراب، منطق قابل‌بازیابی نیست)
    {
        channel.BasicNack(ea.DeliveryTag, multiple: false, requeue: false);
        // پیام میره به Dead-Letter Queue (اگه تنظیم شده باشه)
    }
};
```

**اشتباه رایج**: `autoAck: true`. یعنی به محض دریافت پیام (قبل از پردازش کاملش)، RabbitMQ فکر می‌کنه پیام تحویل داده شده. اگه Consumer وسط پردازش crash کنه، پیام **برای همیشه گم می‌شه**. برای هر چیزی که مهمه، `manual ack` استفاده کن.

### Dead-Letter Queue (DLQ) + Retry with Backoff

پیامی که چند بار fail می‌شه نباید تا ابد توی صفِ اصلی بچرخه و بقیهٔ پیام‌ها رو بلاک کنه. الگوی استاندارد:

1. Queue اصلی → روی fail، به یه **Retry Queue** با TTL (مثلاً ۳۰ ثانیه) می‌ره.
2. وقتی TTL تموم شد، پیام به‌طور خودکار برمی‌گرده به Queue اصلی (با `x-dead-letter-exchange`).
3. بعد از N بار retry (با شمارش توی header پیام)، پیام می‌ره به **DLQ نهایی** برای بررسی دستی یا alerting.

این الگو دقیقاً معادل **Exponential Backoff** هست ولی در سطح صف پیام به‌جای در سطح کد.

### Outbox Pattern: حل مشکل "هم DB بنویس هم پیام بفرست"

این شاید مهم‌ترین الگویی باشه که باید بلد باشی. مسئله:

```csharp
// ❌ خطرناک: بین این دو خط ممکنه Pod crash کنه
await _db.SaveChangesAsync();           // سفارش ثبت شد
await _rabbitMq.PublishAsync(orderEvent); // ولی پیام هرگز نرفت (یا برعکس: پیام رفت ولی DB rollback شد)
```

این یه **Dual-Write Problem** هست: دو تا سیستم مستقل (DB و Message Broker) که نمی‌تونی با یه transaction واحد هر دو رو atomic کنی.

**راه‌حل: Outbox Pattern**

```csharp
using var transaction = await _db.Database.BeginTransactionAsync();

_db.Orders.Add(order);
_db.OutboxMessages.Add(new OutboxMessage
{
    Type = "OrderCreated",
    Payload = JsonSerializer.Serialize(orderEvent),
    CreatedAt = DateTime.UtcNow,
    ProcessedAt = null
});

await _db.SaveChangesAsync(); // هر دو تو یه transaction، هر دو یا هیچ‌کدوم
await transaction.CommitAsync();
```

یه **Background Worker جدا** (یا Pod جدا) دوره‌ای جدول `OutboxMessages` رو می‌خونه، پیام‌های `ProcessedAt == null` رو به RabbitMQ می‌فرسته، و بعد `ProcessedAt` رو ست می‌کنه. اگه Worker وسط کار crash کنه، پیام دوباره فرستاده می‌شه (پس Consumer باید **Idempotent** باشه — دوباره به بخش ۳ وصل می‌شه).

**چرا این جواب سؤال "Consistency بین DB و Message Queue" رو می‌ده**: چون به‌جای تلاش برای Strong Consistency بین دو سیستم مستقل (که تقریباً غیرممکنه بدون Two-Phase Commit که خودش کند و شکننده‌ست)، از **Eventual Consistency + Idempotency** استفاده می‌کنیم که هم قابل‌اعتمادتره هم throughput بالاتری داره.

---

## 5. چند سرور دیتابیس: Scaling، Replica، و Concurrency در سطح داده

### Read Replica: مراقب Staleness باش
وقتی نوشتن روی Primary و خوندن روی Replica انجام می‌شه، بین نوشتن و replicate شدن یه **تأخیر (replication lag)** هست. اگه یه کاربر سفارشی ثبت کنه و بلافاصله بخواد لیست سفارش‌هاش رو ببینه، ممکنه سفارش تازه رو نبینه (چون از Replica می‌خونه که هنوز sync نشده).

**راه‌حل‌ها**:
- **Read-your-writes consistency**: بعد از نوشتن، همون request (یا برای چند ثانیه بعدش) از Primary بخونه.
- **Session Stickiness** با یه شناسه (مثلاً یه timestamp یا LSN) که مشخص کنه «حداقل تا این نسخه از داده رو بخون».
- قبول کردن Eventual Consistency برای بخش‌هایی که حساس نیستن (مثلاً لیست محصولات).

### Optimistic Concurrency به‌جای Distributed Lock برای رکوردهای DB

برای جلوگیری از Lost Update وقتی چند Pod هم‌زمان یه رکورد رو آپدیت می‌کنن، نیازی به Distributed Lock نیست — همون چیزی که EF Core با `RowVersion` می‌ده کافیه:

```csharp
public class Product
{
    public int Id { get; set; }
    public int Stock { get; set; }
    [Timestamp]
    public byte[] RowVersion { get; set; }
}

// وقتی دو Pod هم‌زمان همین رکورد رو آپدیت کنن،
// دومی که SaveChanges می‌زنه یه DbUpdateConcurrencyException می‌گیره
// چون RowVersion دیگه match نمی‌کنه
try
{
    await _db.SaveChangesAsync();
}
catch (DbUpdateConcurrencyException)
{
    // یا retry با خوندن دوبارهٔ رکورد، یا merge، یا خطا به کاربر
}
```

این خیلی **سبک‌تر و مقیاس‌پذیرتر** از گرفتن Distributed Lock روی هر رکورده، چون هیچ lock واقعی نگه‌داشته نمی‌شه — فقط موقع commit چک می‌شه.

### Connection Pool: یه چالش که با تعداد Pod بدتر می‌شه
هر Pod برای خودش یه Connection Pool به DB باز می‌کنه (پیش‌فرض EF Core/ADO.NET معمولاً ۱۰۰ تا). اگه ۱۰ تا Pod داشته باشی، پتانسیل تا ۱۰۰۰ کانکشن هم‌زمان به DB داری — که خیلی از دیتابیس‌ها (خصوصاً SQL Server با license محدود یا Postgres با `max_connections` پیش‌فرض حدود ۱۰۰) نمی‌تونن تحملش کنن.

**راه‌حل‌ها**:
- محدود کردن `Max Pool Size` توی connection string، متناسب با تعداد Pod.
- استفاده از یه **External Connection Pooler** مثل **PgBouncer** (برای Postgres) که بین Podها و DB می‌شینه و کانکشن‌ها رو multiplex می‌کنه.
- Horizontal Pod Autoscaler (HPA) رو با در نظر گرفتن این محدودیت تنظیم کنی، نه فقط بر اساس CPU.

---

## 6. Caching توزیع‌شده: Cache Stampede و Invalidation

### Cache Stampede (Thundering Herd)
وقتی یه cache key پرطرفدار expire می‌شه، ممکنه **صدها Request هم‌زمان** به DB بزنن تا همون مقدار رو دوباره محاسبه کنن — دقیقاً وقتی که DB باید کمترین فشار رو داشته باشه.

```csharp
// راه‌حل: Distributed Lock (همون بخش ۲) دور محاسبهٔ مقدار جدید
var value = await _cache.GetAsync(key);
if (value is null)
{
    var lockAcquired = await AcquireDistributedLock($"lock:{key}", TimeSpan.FromSeconds(10));
    if (lockAcquired)
    {
        value = await ComputeExpensiveValue();
        await _cache.SetAsync(key, value, TimeSpan.FromMinutes(5));
        await ReleaseLock($"lock:{key}");
    }
    else
    {
        await Task.Delay(100);
        value = await _cache.GetAsync(key); // احتمالاً تا الان یه Pod دیگه پرش کرده
    }
}
```

راه‌حل ساده‌تر: **Probabilistic Early Expiration** (کمی قبل از expire واقعی، به‌طور تصادفی یکی از Requestها رفرشش کنه) یا استفادهٔ **Stale-While-Revalidate**.

### Cache Invalidation در چند Pod
اگه هر Pod یه **In-Memory Cache محلی** (مثل `IMemoryCache`) داشته باشه، وقتی داده توسط یه Pod آپدیت می‌شه، بقیهٔ Podها از این تغییر خبر ندارن. راه‌حل‌ها:
- استفاده از **Distributed Cache** (Redis) به‌جای In-Memory، تا همه یه منبع واحد بخونن.
- اگه In-Memory لازمه (برای سرعت)، از **Pub/Sub** (مثلاً Redis Pub/Sub یا RabbitMQ) استفاده کن تا وقتی یه Pod داده رو آپدیت کرد، به بقیه پیام بده «این key رو invalidate کن».

---

## 7. CAP Theorem در عمل (نه فقط تعریف کتابی)

تعریف کتابی: توی حضور Network Partition، باید بین Consistency و Availability یکی رو انتخاب کنی. ولی تو مصاحبهٔ سطح Senior باید بتونی این رو به **تصمیم معماری واقعی** وصل کنی:

| سناریو | انتخاب | چرا |
|---|---|---|
| ثبت سفارش / پرداخت | **Consistency (CP)** | نمی‌تونی دو بار پول یه نفر رو کم کنی یا موجودی منفی بشه |
| لیست محصولات / Feed خبری | **Availability (AP)** | بهتره یه نسخهٔ کمی قدیمی نشون بدی تا اصلاً چیزی نشون ندی |
| شمارندهٔ لایک/ویو | **AP با Eventual Consistency** | دقت لحظه‌ای مهم نیست، throughput مهمه |

**نکتهٔ مهم برای دفاع از عنوانت**: بگو که تصمیم CP/AP رو **در سطح هر Bounded Context** جدا می‌گیری، نه برای کل سیستم یه‌جا. این دقیقاً همون‌جاییه که Clean Architecture و Distributed Systems به هم وصل می‌شن — هر Aggregate/Bounded Context می‌تونه consistency model خودش رو داشته باشه.

---

## 8. Backpressure و Rate Limiting: وقتی تولیدکننده سریع‌تر از مصرف‌کننده‌ست

اگه Producer (مثلاً یه API که پیام به RabbitMQ می‌فرسته) سریع‌تر از Consumer پیام تولید کنه، صف بی‌نهایت بزرگ می‌شه و در نهایت حافظهٔ Broker پر می‌شه.

**راه‌حل‌ها**:
- **Bounded Queue** (مثل `Channel.CreateBounded` که توی فایل Supplementary دیدی) در سطح صف داخلی سرویس.
- **Queue Length Limit** توی RabbitMQ (`x-max-length`) + سیاست reject/DLQ وقتی پر شد.
- **Rate Limiting** روی API که پیام تولید می‌کنه (مثلاً با `System.Threading.RateLimiting` توی .NET 8+).
- **Horizontal Scaling Consumer** بر اساس طول صف (KEDA توی Kubernetes می‌تونه بر اساس RabbitMQ queue length، تعداد Pod Consumer رو خودکار زیاد/کم کنه — این خودش یه نکتهٔ خیلی قوی برای این عنوانه).

---

## 9. Distributed Tracing: دیباگ کردن وقتی یه Request از ۵ تا Pod و ۲ تا Queue رد می‌شه

توی یه process، یه Exception با Stack Trace کافیه. توی Distributed System، یه Request ممکنه از API Pod → RabbitMQ → Worker Pod → DB Pod دیگه رد بشه. بدون یه **Correlation ID** که از اول تا آخر همراه Request/Message بمونه، پیدا کردن اینکه کجا مشکل پیش اومده تقریباً غیرممکنه.

```csharp
// وقتی پیام به RabbitMQ می‌فرستی، Correlation ID رو توی header بذار
properties.CorrelationId = Activity.Current?.Id ?? Guid.NewGuid().ToString();

// Consumer وقتی پیام رو می‌گیره، همون Correlation ID رو ادامه بده (نه یه ID جدید بساز)
using var activity = ActivitySource.StartActivity(
    "ProcessOrderMessage",
    ActivityKind.Consumer,
    parentContext: ExtractContext(properties)
);
```

با **OpenTelemetry** (که هم .NET هم RabbitMQ instrumentation داره) می‌تونی یه Trace واحد از اول تا آخر زنجیره ببینی — این توی مصاحبه یه امتیاز قویه چون نشون می‌ده فقط بلد نیستی سیستم رو بسازی، بلد هستی وقتی خراب شد پیداش کنی.

---

## 10. همه‌چیز کنار هم: یه سناریوی کامل برای مصاحبه

**سؤال احتمالی**: «یه سیستم سفارش طراحی کن که باید ۵۰۰۰ سفارش در ثانیه رو تحمل کنه، روی ۱۰ Pod در Kubernetes اجرا بشه، و موجودی رو هم دقیق نگه داره.»

**جواب ساختاریافته (این چیزیه که سطح Senior رو نشون می‌ده):**

1. **API Layer (چند Pod)**: درخواست سفارش رو می‌گیره، فقط validation سبک انجام می‌ده، و به‌جای پردازش سنگین همون‌جا، یه پیام `OrderRequested` با یه `Idempotency-Key` منحصربه‌فرد به RabbitMQ می‌فرسته. این جواب سریع می‌ده (Response Time کم) و throughput رو با decoupling بالا می‌بره.

2. **Outbox Pattern**: ثبت اولیهٔ سفارش (status: Pending) و پیام outbox توی یه transaction واحد ذخیره می‌شن؛ یه Worker جدا پیام رو منتشر می‌کنه.

3. **Order Worker (چند Pod، Competing Consumers)**: از صف می‌خونن، با `prefetchCount` متناسب تنظیم شده. هر Worker قبل از پردازش چک می‌کنه که `Idempotency-Key` قبلاً پردازش نشده (Idempotent Consumer).

4. **کم کردن موجودی**: به‌جای Distributed Lock روی هر محصول (که با ۵۰۰۰ req/s bottleneck می‌شه)، از **Optimistic Concurrency (RowVersion)** استفاده می‌شه؛ در صورت conflict، retry با backoff کوتاه.

5. **دیتابیس**: نوشتن روی Primary، خوندن گزارش‌ها/لیست سفارش‌ها از Read Replica (چون brief inconsistency اونجا قابل قبوله). Connection Pool محدود شده و از PgBouncer/مشابه استفاده می‌شه چون ۱۰ Pod به یه DB وصلن.

6. **Cache**: موجودی محصولات پرتقاضا توی Redis cache می‌شه با Cache Stampede protection؛ وقتی موجودی آپدیت می‌شه، از Redis Pub/Sub برای invalidate کردن cache همهٔ Podها استفاده می‌شه.

7. **Scaling**: تعداد Order Worker Podها با **KEDA** بر اساس طول صف RabbitMQ خودکار بالا/پایین می‌ره.

8. **Observability**: هر پیام یه Correlation ID داره که از API تا Worker تا DB رد می‌شه؛ با OpenTelemetry یه Trace واحد قابل مشاهده‌ست.

9. **Consistency Trade-off**: ثبت اولیهٔ سفارش و کم کردن موجودی **Strong Consistency (CP)** هستن؛ اعلام «سفارش ثبت شد» به کاربر و آپدیت شدن dashboard گزارش‌ها **Eventual Consistency (AP)** هستن — و این تصمیم آگاهانه‌ست، نه یه محدودیت فنی که مجبور شدی باهاش کنار بیای.

**چرا این جواب قوی‌تر از یه جواب معمولیه**: چون هر تصمیم رو با یه **trade-off مشخص** توجیه کردی (چرا Lock نه، چرا این pattern آره)، نه فقط اسم بردن از تکنولوژی‌ها.

---

## 11. Common Mistakes & Red Flags (سطح Distributed)

1. **استفاده از `lock` یا `static` field برای هماهنگی بین Podها** — این فقط داخل یه process کار می‌کنه؛ نشون می‌ده مدل ذهنی هنوز single-process هست.
2. **فرض کردن Exactly-Once Delivery از RabbitMQ/Kafka بدون طراحی Idempotency** — At-least-once واقعیت پیش‌فرضه؛ Idempotency مسئولیت Consumer هست نه Broker.
3. **Dual-Write بدون Outbox Pattern** (اول DB، بعد جدا پیام بفرستی) — منبع کلاسیک ناسازگاری داده در سیستم‌های Distributed.
4. **نادیده گرفتن Connection Pool وقتی تعداد Pod زیاد می‌شه** — یکی از رایج‌ترین دلایل «چرا DB زیر بار می‌خوابه وقتی Scale می‌کنیم».
5. **Distributed Lock بدون TTL** — یه Deadlock در سطح کل کلاستر، نه فقط یه process.
6. **گرفتن Distributed Lock برای هر آپدیت رکورد DB به‌جای Optimistic Concurrency** — throughput رو غیرضروری پایین می‌آره.
7. **یکسان دونستن CAP برای کل سیستم** به‌جای تصمیم‌گیری per-Bounded-Context.

---

## 12. جمع‌بندی: نقشهٔ ذهنی برای دفاع از عنوان

- **Concurrency تک‌process** (فایل‌های قبلی) = پیش‌نیاز و پایه.
- **Distributed Concurrency** = وقتی چند process/Pod بدون حافظهٔ مشترک باید هماهنگ بشن → Distributed Lock, Optimistic Concurrency, Idempotency.
- **Messaging (RabbitMQ)** = decoupling + throughput، ولی با هزینهٔ at-least-once delivery و پیچیدگی error handling (DLQ, retry, backoff).
- **چند دیتابیس/Replica** = Scaling خوندن، با هزینهٔ replication lag و پیچیدگی connection management.
- **Caching توزیع‌شده** = سرعت، با هزینهٔ invalidation و stampede risk.
- **Clean Architecture** نقشش اینه که همهٔ این تصمیم‌های infrastructure (RabbitMQ, Redis, connection pooling) رو پشت Interfaceهای Application/Domain layer پنهان کنه، تا اگه فردا از RabbitMQ به Kafka عوض کردی، Domain Logic (مثلاً «چطور موجودی کم می‌شه») دست‌نخورده بمونه.

هر بخش از این فایل رو می‌تونی به یه سؤال مصاحبه وصل کنی: «چرا Outbox Pattern؟»، «فرق Distributed Lock و Optimistic Concurrency کِی کدوم؟»، «چطور Cache Stampede رو مدیریت می‌کنی؟» — اگه بتونی هر کدوم رو با یه trade-off واقعی (نه فقط تعریف) جواب بدی، دفاع از این عنوان قوی می‌شه.
