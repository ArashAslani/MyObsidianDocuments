# Concurrency — موضوعات تکمیلی

این فایل **مکمل** دو تا فایل دیگه است؛ اول اون‌ها رو بخون بعد این رو:
- **[[Concurrency]]** — از CPU تا Thread و Task
- **[[Concurrency and multi Threading]]** — lock، async/await، Deadlock و بقیه

اینجا یه **راهنمای انتخاب** داریم (کی Thread، کی Task، کی async)، چند ابزار اضافه (مثل Channel و ReaderWriterLockSlim)، و یه توضیح کوتاه دربارهٔ **مدل حافظه** تا بفهمی چرا `volatile` و `Interlocked` و `lock` این‌طوری رفتار می‌کنن.

---

## 1. راهنمای انتخاب: چه زمانی از چه استفاده کنیم؟

تو مصاحبه حتماً می‌پرسن «کی از Thread استفاده می‌کنی، کی Task، کی async؟» جدول زیر جوابش رو یه جا جمع کرده.

| سناریو | ابزار پیشنهادی | دلیل کوتاه |
|--------|-----------------|------------|
| کار **I/O-bound** (فایل، شبکه، دیتابیس) | **async/await** | Thread در زمان انتظار block نمی‌شود؛ به Thread Pool برمی‌گردد. |
| کار **CPU-bound** سبک که می‌خواهیم در پس‌زمینه انجام شود (مثلاً در اپ UI) | **Task.Run** | کار روی Thread Pool اجرا می‌شود؛ UI thread آزاد می‌ماند. |
| کار **CPU-bound** در **ASP.NET Core** | معمولاً **نه Task.Run** | همان Thread Pool درخواست‌ها را مشغول می‌کنیم؛ بهتر است از Background Service یا صف/Worker جدا استفاده شود. |
| **موازی‌سازی روی یک مجموعه** (مثلاً پردازش ۱۰۰۰ آیتم با محاسبهٔ سنگین روی هر کدام) | **Parallel.For / Parallel.ForEach** | برای همین سناریو طراحی شده؛ تقسیم کار روی هسته‌ها و مدیریت صف داخلی. |
| نیاز به **کنترل دقیق Thread** (مثلاً priority، foreground/background) | **Thread** | در اکثر موارد لازم نیست؛ ترجیح با Task و Thread Pool است. |
| **Long-running** که نباید Thread Pool را اشغال کند | **Task.Factory.StartNew(..., LongRunning)** یا **Thread** | به CLR می‌گوییم thread جدا بسازد نه از استخر. |

**یه خطی برای جواب مصاحبه**: کار I/O → async/await. کار CPU توی اپ UI → Task.Run. کار CPU توی وب → صف یا worker جدا. می‌خوای یه مجموعه رو موازی پردازش کنی → Parallel. به کنترل خاص روی thread نیاز داری → Thread یا LongRunning.

---

## 2. ReaderWriterLockSlim

اگه **خوندن خیلی زیاده** و **نوشتن کم**، با `lock` معمولی فقط یه thread می‌تونه بخونه و بقیه صف می‌شن؛ یعنی throughput خوندن کم می‌شه. **ReaderWriterLockSlim** این مشکل رو حل می‌کنه: **چند تا reader** هم‌زمان می‌تونن داخل باشن، ولی **نوشتن** فقط وقتی مجاز است که هیچ reader یا writer دیگه‌ای نباشه.

```csharp
private readonly ReaderWriterLockSlim _rw = new ReaderWriterLockSlim();

string Read() {
    _rw.EnterReadLock();
    try {
        return _sharedData;
    } finally {
        _rw.ExitReadLock();
    }
}

void Write(string value) {
    _rw.EnterWriteLock();
    try {
        _sharedData = value;
    } finally {
        _rw.ExitWriteLock();
    }
}
```

**نکته**: برای جایی که واقعاً read خیلی زیاد و write کمه خوبه؛ اگه رقابت کمه یا write زیاد باشه، همون `lock` ساده گاهی کافیه یا حتی سریع‌تره. تو مصاحبه خوبه بدونیش که این گزینه وجود داره و کی به‌درد می‌خوره.

---

## 3. Channel\<T\>

وقتی یه طرف **تولید می‌کنه** و یه طرف **مصرف**، و یکی یا هر دو **async** هستن، به‌جای `BlockingCollection` می‌تونی از **Channel** استفاده کنی: صفی که با async راحت کار می‌کنه و back-pressure و لغو رو هم پشتیبانی می‌کنه.

```csharp
var channel = Channel.CreateBounded<int>(100);

// Producer
await channel.Writer.WriteAsync(42, cancellationToken);

// Consumer
await foreach (var item in channel.Reader.ReadAllAsync(cancellationToken)) {
    Process(item);
}
```

**کی به‌درد می‌خوره**: وقتی چند تا producer داری که آیتم می‌سازن و یک یا چند consumer که async اون‌ها رو می‌خورن. توی pipelineها و صف‌های داخلی سرویس خیلی کاربرد داره. برای مصاحبه همون نام و کاربرد کلی کافیه.

---

## 4. Parallel.For و Parallel.ForEach

وقتی یه **مجموعه** داری (مثلاً آرایه یا لیست) و روی هر آیتم کار **CPU-bound** انجام می‌دی، **Parallel.For** یا **Parallel.ForEach** دقیقاً برای همون ساخته شدن: runtime کار رو بین چند thread از Thread Pool پخش می‌کنه و از هسته‌ها درست استفاده می‌کنه.

```csharp
Parallel.For(0, items.Length, i => {
    Process(items[i]);
});

Parallel.ForEach(items, item => {
    Process(item);
});
```

**فرقش با Task**: برای «یه حلقهٔ بزرگ که روی هر آیتم کار یکنواخت انجام می‌دی» بهینه‌ان. اگه به‌جاش برای هر آیتم یه `Task.Run` بزنی، overhead صف و زمان‌بندی بیشتر می‌شه. **یادت باشه**: اگه کار I/O-bound است (مثلاً برای هر آیتم یه API صدا می‌زنی)، معمولاً `Task.WhenAll` با async از Parallel بهتره.

---

## 5. ValueTask (خلاصه)

**Task** reference type است؛ هر بار متد async یه نتیجه برمی‌گردونه، یه object روی heap ساخته می‌شه. توی **hot path** (مثلاً متدی که خیلی صدا زده می‌شه) اگه اکثر وقت‌ها نتیجه **همون لحظه** آماده است (مثلاً از cache می‌خونه)، این allocation به GC فشار می‌آره. **ValueTask** یه struct است و وقتی نتیجه معمولاً هم‌زمانه، از این allocation جلوگیری می‌کنه.

**خلاصه**: توی library اگه متد async تو بیشتر وقت‌ها همون‌جا تمام می‌شه (مثلاً از cache می‌خونه)، برگردوندن **ValueTask** به‌جای **Task** می‌تونه بهینه باشه. برای بقیهٔ موارد **Task** ساده‌تر و کافیه. تو مصاحبه همون قدر بدون که ValueTask برای کم کردن allocation توی سناریوهای خاص به‌کار می‌ره.

---

## 6. مدل حافظه و Visibility (خلاصه)

چرا «با lock ببندیم» یا «Interlocked بزنیم» کافیه؟ چون علاوه بر **atomic بودن**، یه مشکل دیگه هم هست به اسم **visibility**: ممکنه یه thread یه مقدار رو توی register یا cache خودش عوض کنه و thread دیگه تا یه مدت **اصلاً نبینه**. CPU و compiler گاهی دستورات رو **جابه‌جا** می‌کنن تا سریع‌تر اجرا بشه؛ پس بدون یه سری تضمین، یه thread ممکنه نوشتهٔ thread دیگه رو دیر یا هرگز نبینه.

- **lock**: با ورود و خروج از lock، علاوه بر اینکه فقط یکی داخل باشه، یه **memory barrier** هم ایجاد می‌شه؛ یعنی هر چی قبل از بیرون اومدن از lock نوشتی، کسی که بعداً وارد lock بشه می‌بینه.
- **Interlocked**: عملیات‌هاش هم **atomic** هستن هم با barrier؛ پس بقیهٔ threadها تغییر رو می‌بینن.
- **volatile**: به compiler و runtime می‌گه این متغیر رو برای بهینه‌سازی توی register قفل نکن و خوندن/نوشتنش طوری باشه که بقیه بتونن ببینن. برای **read-modify-write** (مثل `counter++`) کافی نیست؛ اونجا حتماً Interlocked یا lock لازمه.

**تو مصاحبه**: اگه بگی «با یه bool فلگ بدون lock هماهنگ می‌کنیم» و از volatile یا Interlocked استفاده نکنی، ممکنه thread دیگه تغییر فلگ رو دیر ببینه. پس یا از همون primitiveهای درست استفاده کن یا صریح بگو مدل حافظه رو در نظر گرفتی.

---

## 7. خلاصهٔ تکمیلی برای مصاحبه

- **کی چی**: I/O → async/await؛ CPU توی UI → Task.Run؛ CPU توی وب → صف/worker؛ حلقهٔ موازی روی مجموعه → Parallel؛ کنترل خاص روی thread → Thread یا LongRunning.
- **ReaderWriterLockSlim**: وقتی read خیلی زیاده و write کم.
- **Channel**: producer-consumer با async.
- **Parallel.For/ForEach**: پردازش موازی CPU-bound روی مجموعه؛ برای I/O-bound به‌کارش نبر.
- **ValueTask**: توی library وقتی نتیجه معمولاً هم‌زمانه، برای کم کردن allocation.
- **مدل حافظه**: lock و Interlocked هم atomicity می‌دن هم **visibility**؛ volatile فقط visibility می‌ده و برای چیزهایی مثل counter++ کافی نیست.

---

این فایل **مکمله** و جایگزین اون دو تا نمی‌شه. برای پایه و جزئیات sync/async/deadlock همون **[[Concurrency]]** و **[[Concurrency and multi Threading]]** رو بخون.
