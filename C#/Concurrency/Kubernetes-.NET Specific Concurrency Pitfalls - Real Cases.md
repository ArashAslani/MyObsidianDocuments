# چالش‌های تازه: از شناسهٔ تکراری تا OOMKilled شدن Pod به‌خاطر GC

## چرا این فایل؟

این فایل فقط شامل مطالبیه که تا الان توی فایل‌های قبلی (High-Throughput Distributed Systems و Classic Distributed Concurrency Problems) نیومده. این‌ها مسائلی هستن که وقتی یه اپ ASP.NET Core رو از حالت «یه سرور» به «۳ Pod با دیتابیس مشترک» می‌بری، غافلگیرت می‌کنن — و برخلاف Distributed Lock و Idempotency (که قبلاً مفصل گفتیم)، این‌ها بیشتر **جزئیات پیاده‌سازی .NET/EF Core/Kubernetes** هستن، نه تئوری کلی Distributed Systems.

هر بخش با یه نمونهٔ واقعی و مستند (خطای واقعی، GitHub Issue رسمی، یا مستندات Microsoft) همراهه.

---

## 1. تولید شناسهٔ تکراری با الگوی `MAX(id) + 1`

### مشکل
یه الگوی خیلی رایج (و خطرناک) برای تولید شمارهٔ فاکتور، کد رهگیری، یا هر شناسهٔ ترتیبی اینه:

```csharp
// ❌ خطرناک در محیط چند Pod
var lastInvoiceNumber = await _db.Invoices
    .OrderByDescending(x => x.InvoiceNumber)
    .Select(x => x.InvoiceNumber)
    .FirstOrDefaultAsync();

var newInvoice = new Invoice { InvoiceNumber = lastInvoiceNumber + 1 };
await _db.Invoices.AddAsync(newInvoice);
await _db.SaveChangesAsync();
```

### چرا خطرناکه
بین خط اول (خوندن آخرین شماره) و خط آخر (ذخیرهٔ رکورد جدید)، یه پنجرهٔ زمانی وجود داره. اگه دو تا Request هم‌زمان به دو تا Pod مختلف برسن، هر دو ممکنه **همون** `lastInvoiceNumber` رو بخونن (مثلاً ۱۰۰۰) و هر دو سعی کنن شمارهٔ ۱۰۰۱ رو بسازن. نتیجه یا یه خطای `UNIQUE constraint violation` (اگه ایندکس یکتا داشته باشی — که خوش‌شانسیه) یا بدتر، **دو فاکتور با شمارهٔ یکسان** توی سیستم (اگه ایندکس یکتا نداشته باشی — که فاجعه‌ست، چون توی حسابداری و مالیات، شمارهٔ فاکتور تکراری یه مشکل قانونی جدیه).

این باگ رو تشخیص دادنش سخته چون توی تست‌های local (با یه instance) هرگز رخ نمی‌ده — فقط زیر بار واقعی و با چند Pod خودش رو نشون می‌ده، دقیقاً همون الگویی که قبلاً هم در مورد Race Conditionهای دیگه دیدیم.

### راه‌حل درست
از مکانیزم اتمیک خود دیتابیس استفاده کن، نه منطق برنامه:

```sql
-- SQL Server: SEQUENCE (بهترین گزینه، چون از IDENTITY انعطاف‌پذیرتره)
CREATE SEQUENCE InvoiceNumberSequence
    START WITH 1000
    INCREMENT BY 1;

-- در کد:
SELECT NEXT VALUE FOR InvoiceNumberSequence;
```

یا اگه شمارهٔ فاکتور دقیقاً همون Primary Key هست، از `IDENTITY`/`SERIAL` (Postgres) استفاده کن. نکتهٔ مهم: **تولید عدد بعدی** باید توی خود دیتابیس و به‌صورت atomic انجام بشه، نه با خوندن max و اضافه کردن یک واحد توی C#. اگه فرمت خاصی نیاز داری (مثلاً `INV-2026-001000`)، عدد رو از Sequence بگیر و فرمت‌دهی رو توی Application Layer انجام بده — فقط بخش «تولید عدد یکتا» باید atomic باشه.

---

## 2. Data Protection Keys: چرا کاربر بین Podها گاهی «Logout» می‌شه

### مشکل
ASP.NET Core برای رمزنگاری کوکی‌های احراز هویت، Anti-Forgery Token، و TempData از یه سیستم به‌نام **Data Protection API** استفاده می‌کنه. به‌صورت پیش‌فرض، این کلیدهای رمزنگاری **روی دیسک محلی هر instance** ذخیره می‌شن و **هر Pod به‌طور مستقل کلید خودش رو می‌سازه**.

### اتفاق واقعی و خطای دقیقش
این یکی از رایج‌ترین مشکلات مستند‌شده در انجمن‌های ASP.NET Core هست. خطای دقیقی که توسعه‌دهنده‌ها می‌بینن این‌طوریه:

```
System.Security.Cryptography.CryptographicException:
The key {3470d9c3-e59d-4cd8-8668-56ba709e759d} was not found in the key ring.
   at Microsoft.AspNetCore.DataProtection.KeyManagement.KeyRingBasedDataProtector.UnprotectCore(...)
   at Microsoft.AspNetCore.Antiforgery.DefaultAntiforgeryTokenSerializer.Deserialize(...)
```

**سناریوی دقیق**: کاربر یه فرم رو از Pod شمارهٔ ۱ می‌گیره (که یه Anti-Forgery Token با کلید خودش رمزنگاری کرده). وقتی فرم رو submit می‌کنه، Load Balancer درخواست رو به Pod شمارهٔ ۲ می‌فرسته. Pod شمارهٔ ۲ کلید Pod شمارهٔ ۱ رو **اصلاً نداره**، پس نمی‌تونه توکن رو decrypt کنه و کاربر با یه خطای عجیب (یا logout ناخواسته) مواجه می‌شه.

یه نمونهٔ مستند دیگه از همین مشکل: تیم‌هایی که اپ‌شون رو روی Azure App Service با **Deployment Slots** اجرا می‌کردن، متوجه شدن حتی توی همون یه پلتفرم Azure هم اگه از Slot جداگانه استفاده کنی (که در پس‌زمینه instanceهای متفاوتی داره)، دقیقاً همین خطای Key Ring رخ می‌ده — چون هر Slot کلیدهای جدای خودش رو داره، مگر اینکه صریحاً پیکربندی بشه.

### راه‌حل
باید صریحاً به Data Protection بگی کلیدها رو یه‌جای **مشترک** ذخیره کنه، نه دیسک محلی هر Pod:

```csharp
// در Program.cs
services.AddDataProtection()
    .PersistKeysToStackExchangeRedis(redisConnection, "DataProtection-Keys")
    .SetApplicationName("MyApp"); // این باید بین همهٔ Podها یکسان باشه
```

یا از Azure Blob Storage، یه فایل مشترک روی Network Share، یا هر storage مشترک دیگه‌ای استفاده کنی. نکتهٔ مهم `SetApplicationName`: اگه این مقدار بین Podها متفاوت باشه (مثلاً یکی از اسم Assembly پیش‌فرض استفاده کنه)، حتی با Redis مشترک هم باز مشکل باقی می‌مونه، چون Data Protection کلیدها رو بر اساس نام اپلیکیشن هم isolate می‌کنه.

---

## 3. Race Condition در اجرای Migration موقع Startup چند Pod

### مشکل
یه الگوی رایج اینه که `Database.Migrate()` رو توی `Program.cs` صدا بزنی تا موقع استارت اپ، migrationهای جدید خودکار اعمال بشن:

```csharp
using var scope = app.Services.CreateScope();
scope.ServiceProvider.GetRequiredService<AppDbContext>().Database.Migrate();
```

### اتفاق واقعی
این دقیقاً همون مشکلیه که در یه Issue رسمی روی مخزن GitHub خود EF Core گزارش شده: یه تیم که اپ‌شون رو روی AWS با چند instance و دیتابیس Aurora MySQL اجرا می‌کردن، متوجه شدن هر بار که Deployment جدید می‌زنن و همهٔ instanceها هم‌زمان بالا میان، به‌طور مکرر با خطاهایی مثل **«Column already exists»** مواجه می‌شن — چون چند Pod هم‌زمان سعی می‌کردن همون migration رو اعمال کنن، و دومی که به یه دستور `ALTER TABLE ADD COLUMN` می‌رسید، می‌دید ستون قبلاً توسط Pod اول اضافه شده.

نکتهٔ جالب توجه: خود توسعه‌دهنده توی اون گزارش نوشته بود «چرا کسی قبلاً به این مشکل برنخورده؟» — که نشون می‌ده این یه اشتباه خیلی رایجه که خیلی از تیم‌ها فقط بعد از رخ دادنش متوجهش می‌شن.

### وضعیت فعلی (خبر خوب)
طبق مستندات رسمی Microsoft برای EF Core، **از نسخهٔ EF Core 9 به بعد**، متدهای `Migrate()` و `MigrateAsync()` به‌طور خودکار قبل از اعمال migration یه **قفل سراسری در سطح دیتابیس** می‌گیرن، دقیقاً برای جلوگیری از همین مشکل. این قفل تا پایان اجرای migration نگه داشته می‌شه و بعد آزاد می‌شه. ولی این قابلیت **provider-dependent** هست (روی SQL Server و PostgreSQL خوب کار می‌کنه، ولی روی SQLite به‌خاطر ماهیت فایلی‌ش می‌تونه در صورت crash ناگهانی، قفل رها (abandoned) بمونه).

اگه روی نسخهٔ EF Core قدیمی‌تر (۸ یا پایین‌تر) هستی، این قفل خودکار وجود نداره و باید خودت مدیریتش کنی.

### راه‌حل توصیه‌شده (صرف‌نظر از نسخهٔ EF Core)
بهترین practice اینه که اصلاً Migration رو از چرخهٔ startup اپلیکیشن خارج کنی:
- یه **Kubernetes Job** یا **Init Container** جداگانه که فقط یه بار قبل از rollout اصلی اجرا می‌شه.
- یا یه مرحلهٔ جدا توی CI/CD Pipeline که قبل از deploy کردن نسخهٔ جدید کد، migration رو روی دیتابیس اعمال می‌کنه.

این هم مشکل Race Condition رو کاملاً حذف می‌کنه، هم مطابق توصیهٔ خود مستندات EF Core هست که می‌گه دادن دسترسی تغییر Schema به همون اکانتی که اپلیکیشن باهاش به دیتابیس وصل می‌شه، از نظر امنیتی هم توصیه نمی‌شه.

---

## 4. Server GC و OOMKilled شدن Pod: چرا فقط تنظیم Memory Limit کافی نیست

### مشکل
.NET به‌صورت پیش‌فرض روی ماشین‌های چندهسته‌ای از **Server GC** استفاده می‌کنه که برای throughput بالا بهینه شده ولی heap بیشتری مصرف می‌کنه (چون برای هر هسته یه heap جدا نگه می‌داره). توی Kubernetes، هر Pod یه `resources.limits.memory` مشخص داره؛ اگه GC این محدودیت رو رعایت نکنه، Kubernetes بدون هیچ اخطار نرمی، Pod رو با کد خروج ۱۳۷ (`OOMKilled`) می‌کشه.

### اتفاق واقعی و مستند: باگ ۷۰٪ در dotnet/runtime
یه Issue رسمی و دقیق روی مخزن `dotnet/runtime` (شمارهٔ ۶۱۰۷۶) این مشکل رو با جزئیات دقیق نشون می‌ده: یه تیم روی Azure Kubernetes Service (AKS)، با انجام تست‌های تکرارشونده، متوجه شدن که برنامهٔ .NET‌شون **دقیقاً در ۷۰٪ از Memory Limit تنظیم‌شده**، با خطای Out of Memory متوقف می‌شه — نه در ۱۰۰٪. مثلاً اگه Limit روی ۲۰۰۰ مگابایت تنظیم بشه، برنامه فقط تا ۱۴۰۰ مگابایت می‌تونه allocate کنه. این عدد ۷۰٪ توی چند سناریوی مختلف (با Limitهای متفاوت) به‌طور مداوم تکرار شد.

**یه مشکل قدیمی‌تر و جدی‌تر**: یه Issue دیگه (شمارهٔ ۸۵۱) نشون می‌ده که در نسخه‌های قدیمی‌تر .NET Core (۲.۰ و ۲.۱)، وقتی swap غیرفعال بود (که در Kubernetes همیشه همینطوره، چون K8s اصلاً از swap پشتیبانی نمی‌کنه)، برنامه‌های .NET Core حتی **قبل از رسیدن به Memory Limit تعیین‌شده هم OOMKilled می‌شدن** — چون CLR نمی‌تونست فایل `memory.limit_in_bytes` مربوط به cgroup رو درست بخونه، و در نتیجه GC اصلاً در زمان درست فعال نمی‌شد.

### راه‌حل
از نسخهٔ .NET Core 3.0 به بعد، GC به‌طور پیش‌فرض به Container Memory Limit احترام می‌ذاره (از طریق تنظیمی به‌نام `System.GC.HeapHardLimitPercent` که پیش‌فرضش، طبق مستندات، **بزرگتر از ۲۰ مگابایت یا ۷۵٪ از Memory Limit کانتینر** است). ولی طبق باگ بالا، این رفتار همیشه بی‌نقص نیست، پس توصیه‌های عملی:

```xml
<!-- csproj: به GC اجازه بده صریحاً بفهمه توی کانتینره -->
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
  <ConcurrentGarbageCollection>true</ConcurrentGarbageCollection>
</PropertyGroup>
```

```yaml
# در deployment.yaml: به‌جای فقط تنظیم limit، به GC صریحاً بگو چقدر heap مجازه
env:
  - name: DOTNET_GCHeapHardLimitPercent
    value: "70" # مقداری واضح‌تر و کنترل‌شده‌تر از پیش‌فرض بذار
resources:
  limits:
    memory: "512Mi"
  requests:
    memory: "512Mi" # یکسان گذاشتن request و limit (QoS: Guaranteed) از throttling غیرمنتظره جلوگیری می‌کنه
```

**نکتهٔ مهمی که در پاسخ قبلی هم گفتم**: راه‌حل اول نباید «خاموش کردن کامل Server GC» باشه (که throughput رو قربانی می‌کنه)؛ اول `DOTNET_GCHeapHardLimitPercent` یا `DOTNET_GCHeapHardLimit` رو امتحان کن. فقط اگه با تعداد Pod خیلی زیاد (مثلاً بیش از ۲۰-۳۰ Pod روی یه Node با CPU محدود) به مشکل خوردی، اونجا Workstation GC (`<ServerGarbageCollection>false</ServerGarbageCollection>`) یه گزینهٔ معقول‌تر می‌شه.

---

## 5. نقد Redlock: چرا حتی Distributed Lock هم تضمین ۱۰۰٪ نیست

قبلاً از **RedLock.net** به‌عنوان یه ابزار برای Distributed Locking نام بردیم. ارزش داره بدونی این ابزار خودش موضوع یه بحث فنی معروف بین دو تا از شناخته‌شده‌ترین افراد این حوزه بوده:

**Martin Kleppmann** (نویسندهٔ کتاب معروف *Designing Data-Intensive Applications*) یه مقالهٔ فنی مفصل منتشر کرد و استدلال کرد که الگوریتم Redlock (که چندین instance مستقل Redis رو برای گرفتن یه lock هماهنگ می‌کنه) از نظر تئوری، تضمین **Safety** کامل نمی‌ده — یعنی در سناریوهایی با تأخیر شبکه غیرمنتظره، garbage collection pause طولانی، یا clock jump، دو Client می‌تونن **هم‌زمان** فکر کنن هر دو lock رو دارن، دقیقاً همون چیزی که Lock قرار بود جلوش رو بگیره.

**Salvatore Sanfilippo** (سازندهٔ خود Redis، معروف به antirez) یه پاسخ فنی مفصل نوشت و بخشی از انتقادها رو رد کرد، ولی این بحث نشون داد که حتی طراحی یه Distributed Lock "درست"، به‌قدری ظریفه که متخصص‌های درجه‌یک این حوزه هم روی جزئیاتش اختلاف‌نظر دارن.

### چرا این برای تو (به‌عنوان کسی که RedLock.net استفاده می‌کنه) مهمه
این بحث یعنی: Distributed Lock رو **صرفاً به‌عنوان یه بهینه‌سازی برای کاهش تداخل** ببین، نه یه تضمین ریاضی مطلق مثل چیزی که `lock` معمولی توی یه process بهت می‌ده. برای عملیات‌های واقعاً حیاتی (مثل تراکنش مالی)، همیشه یه **لایهٔ دوم دفاعی** هم داشته باش — مثلاً همون `RowVersion`/Optimistic Concurrency در سطح دیتابیس که قبلاً گفتیم، که مستقل از درستی Distributed Lock عمل می‌کنه. این دقیقاً چیزیه که Kleppmann هم توصیه می‌کنه: از Lock به‌عنوان **بهینه‌سازی efficiency** استفاده کن، نه به‌عنوان تنها خط دفاعی برای correctness.

---

## جمع‌بندی

| مسئله | خطای مشخصه/نشانه | راه‌حل |
|---|---|---|
| شناسهٔ تکراری با `MAX()+1` | دو فاکتور با شمارهٔ یکسان، یا `UNIQUE constraint violation` | `SEQUENCE`/`IDENTITY` دیتابیس |
| Data Protection Keys جدا در هر Pod | `CryptographicException: key not found in key ring` | `PersistKeysToStackExchangeRedis` + `SetApplicationName` یکسان |
| Race در `Database.Migrate()` هنگام Startup | `Column already exists` هنگام deploy با چند Pod هم‌زمان | K8s Job/Init Container جدا؛ یا EF Core 9+ با قفل خودکار |
| Server GC و OOMKilled | خروج با کد ۱۳۷ دقیقاً حوالی ۷۰٪ Memory Limit | `DOTNET_GCHeapHardLimitPercent` + request=limit (Guaranteed QoS) |
| محدودیت‌های Redlock | هیچ خطای مشخصی نداره — یه ریسک نظری در سناریوهای نادره | Lock رو مکمل، نه جایگزین، Optimistic Concurrency در DB بدون

این پنج مورد، برخلاف فایل‌های قبلی که بیشتر تئوری Distributed Systems بودن، دقیقاً همون جاهاییه که تیوری با پیاده‌سازی واقعی .NET/Kubernetes برخورد می‌کنه — و معمولاً دقیقاً همین جاهاست که یه توسعه‌دهندهٔ باتجربه از یه Junior که فقط تئوری خونده، تو مصاحبه یا حین کار واقعی فرق می‌کنه.
