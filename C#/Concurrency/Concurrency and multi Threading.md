
# Concurrency و Multi-Threading در C#

## 1. Interview Relevance Summary

موضوع Concurrency و Multi-threading یکی از **بحرانی‌ترین** مباحث در مصاحبه‌های Backend Developer است. مصاحبه‌کنندگان انتظار دارند که کاندیدا:

- تفاوت بین Process و Thread را بداند
- مفهوم Thread safety و Race condition را درک کند
- بداند چه زمانی از Thread، Task یا async/await استفاده کند
- Synchronization primitives مختلف را بشناسد و trade-off‌های آن‌ها را بداند
- بتواند Deadlock را تشخیص دهد و از آن جلوگیری کند
- Performance implications را درک کند

**نکته مهم**: در مصاحبه‌های Senior-level، فقط دانستن syntax کافی نیست. باید بتوانید **چرا** و **چه زمانی** از هر ابزار استفاده کنید.

---

## 2. Process و Thread: مفاهیم پایه

### Process چیست؟

**Process** یک نمونه اجرا شده از یک برنامه در سیستم عامل است. هر Process:

- دارای **حافظه جداگانه** است (isolated memory space)
- می‌تواند **single-threaded** یا **multi-threaded** باشد
- توسط سیستم عامل مدیریت می‌شود

**چرا این مهم است؟** در مصاحبه، اگر بپرسند "چرا Processها حافظه جداگانه دارند؟" باید بگویید: **ایزولیشن و امنیت**. اگر یک Process crash کند، Processهای دیگر تحت تأثیر قرار نمی‌گیرند.

### Thread چیست؟

**Thread** کوچک‌ترین واحد اجرا (execution unit) است که توسط سیستم عامل زمان‌بندی می‌شود. هر Process حداقل یک Thread دارد که **Main Thread** نامیده می‌شود.

**تفاوت کلیدی**:
- Processها حافظه جداگانه دارند
- Threadهای یک Process **حافظه مشترک** دارند (Heap مشترک)
- هر Thread **Stack جداگانه** دارد

---

## 3. CPU Scheduling Algorithms

سیستم عامل از الگوریتم‌های مختلفی برای زمان‌بندی Processها استفاده می‌کند:

### FIFO (First In First Out)

- Processها به ترتیب ورود اجرا می‌شوند
- **مشکل**: اگر یک Process طولانی باشد، Processهای بعدی دچار **Starvation** می‌شوند
- **استفاده**: در سیستم‌های ساده یا real-time systems با deadline مشخص

### SJF (Shortest Job First)

- Process با کمترین زمان اجرا اول اجرا می‌شود
- **مشکل**: Processهای طولانی ممکن است هرگز اجرا نشوند (Starvation)
- **استفاده**: در سیستم‌هایی که می‌توان زمان اجرا را تخمین زد

### Round Robin

- هر Process یک **time quantum** (مثلاً 10ms) دریافت می‌کند
- بعد از تمام شدن quantum، Process به انتهای صف می‌رود
- **مزیت**: عادلانه‌تر است و از Starvation جلوگیری می‌کند
- **نکته مهم**: Round Robin **تصادفی نیست**، بلکه **deterministic** و **fair** است

**چرا این مهم است؟** در مصاحبه، اگر بگویید "Round Robin به صورت random اجرا می‌کند"، این یک **red flag** است. Round Robin کاملاً deterministic است.

---

## 4. Process States و Lifecycle

یک Process می‌تواند در حالت‌های مختلفی باشد:

```
New → Ready → Running → Waiting/Blocked → Terminated
         ↑         ↓            ↓
         └─────────┴────────────┘
```

### حالت‌های مهم:

1. **Ready**: Process آماده اجرا است و منتظر CPU است
2. **Running**: Process در حال اجرا روی CPU است
3. **Waiting/Blocked**: Process منتظر یک event است (مثلاً I/O)
4. **Suspended**: Process از حافظه اصلی به disk منتقل شده (swapping)

**I/O Bound vs CPU Bound**:

- **I/O Bound**: Process منتظر I/O است (مثلاً خواندن از فایل، network call)
- **CPU Bound**: Process نیاز به محاسبات CPU دارد

**چرا این مهم است؟** در async programming، باید بدانید که async/await برای **I/O-bound** کارها مناسب است، نه CPU-bound. در **ASP.NET Core** اگر کار CPU-bound را با `Task.Run` به thread pool بفرستید، در عمل همان poolی را که برای سرو کردن درخواست‌ها استفاده می‌شود مشغول می‌کنید و می‌تواند به **thread pool starvation** و کاهش throughput منجر شود؛ برای کار سنگین CPU در سمت سرور معمولاً راه‌حل‌های جدا (مثلاً background service روی thread جدا، یا queue و worker) در نظر گرفته می‌شود.

---

## 5. Thread در .NET

### Main Thread

هر Process در .NET حداقل یک Thread دارد که **Main Thread** نامیده می‌شود. این Thread:

- توسط CLR ایجاد می‌شود
- مسئول اجرای کد اصلی برنامه است
- در Console apps، UI apps و Web apps وجود دارد

**نکته**: در کنار Main Thread، CLR Threadهای دیگری هم برای **GC** و **Finalization** ایجاد می‌کند، اما این Threadها برای منطق برنامه نیستند.

### Single-Threaded vs Multi-Threaded

- **Single-threaded**: فقط Main Thread کار را انجام می‌دهد
- **Multi-threaded**: چند Thread همزمان کار می‌کنند

**مثال**:
```csharp
// Single-threaded
void DoWork() {
    // همه کارها روی Main Thread
}

// Multi-threaded
void DoWork() {
    var thread = new Thread(() => {
        // کار روی Thread جداگانه
    });
    thread.Start();
}
```

---

## 6. Thread Memory Model: Stack و Heap

### Stack (Local State)

- **هر Thread یک Stack جداگانه دارد**
- متغیرهای local و پارامترهای function در Stack ذخیره می‌شوند
- Stack توسط Thread خودش مدیریت می‌شود

### Heap (Shared State)

- **همه Threadهای یک Process Heap مشترک دارند**
- Objects در Heap ذخیره می‌شوند
- **نکته مهم**: Heap مشترک است نه به خاطر GC، بلکه به خاطر **طراحی Process model**. GC فقط Heap مشترک را مدیریت می‌کند.

**چرا این مهم است؟** اگر در مصاحبه بگویید "Heap مشترک است چون GC نمی‌تواند به Stack هر Thread دسترسی داشته باشد"، این **نادرست** است. Heap مشترک است چون همه Threadها بخشی از یک Process هستند.

### Context Switching

وقتی سیستم عامل از یک Thread به Thread دیگر می‌رود:

1. State فعلی Thread (register values, stack pointer) ذخیره می‌شود
2. State Thread جدید بارگذاری می‌شود
3. **PC Register** آدرس آخرین دستور اجرا شده را نگه می‌دارد

**هزینه Context Switch**: Context switching هزینه دارد. به همین دلیل است که Thread Pool استفاده می‌شود تا از ایجاد/حذف مکرر Threadها جلوگیری شود.

---

## 7. Shared State و Race Conditions

### مشکل Race Condition

وقتی چند Thread به یک shared resource دسترسی دارند، ممکن است **Race Condition** رخ دهد:

```csharp
int counter = 0;

void Increment() {
    counter++; // این operation atomic نیست!
}
```

**چرا مشکل است؟** `counter++` در واقع سه operation است:
1. خواندن مقدار counter
2. افزایش آن
3. نوشتن مقدار جدید

اگر دو Thread همزمان این کار را انجام دهند، ممکن است یکی از incrementها از دست برود.

**راه حل**: برای عملیات ساده (مثل counter) از **Interlocked** استفاده کنید؛ برای critical sectionهای پیچیده‌تر از lock یا سایر primitiveها (بعداً توضیح داده می‌شود).

### Interlocked و volatile

برای عملیات **تک‌گام** روی یک متغیر، استفاده از `Interlocked` از lock سبک‌تر و مناسب‌تر است:

```csharp
int counter = 0;

void SafeIncrement() {
    Interlocked.Increment(ref counter);
}
```

- **Interlocked**: متدهایی مثل `Increment`, `Decrement`, `Add`, `CompareExchange` — همه **atomic** روی آن نوع هستند. برای counter و فلگ ساده در مصاحبه و production هر دو گزینهٔ درست است.
- **volatile**: به compiler و runtime می‌گوید که این متغیر ممکن است توسط چند thread عوض شود؛ از cache کردن خواندن در یک thread جلوگیری می‌کند. **نکته**: برای visibility کافی است، ولی برای read-modify-write (مثل `counter++`) کافی نیست — آنجا باید از Interlocked یا lock استفاده کنید.

**چرا در مصاحبه مهم است؟** اگر بگویید «همیشه برای thread safety از lock استفاده می‌کنم» و برای یک counter ساده lock بزنید، مصاحبه‌گر ممکن است بپرسد آیا راه سبک‌تری سراغ دارید؛ جواب درست **Interlocked** است.

---

## 8. Synchronization Primitives

### Lock (Monitor.Enter/Exit)

**Lock** ساده‌ترین روش برای synchronization است:

```csharp
private readonly object _lockObject = new object();

void SafeIncrement() {
    lock (_lockObject) {
        counter++;
    }
}
```

**نکات مهم**:
- `lock` در compile time به `Monitor.Enter` و `Monitor.Exit` تبدیل می‌شود
- همیشه از `try-finally` استفاده می‌شود تا در صورت exception، lock آزاد شود
- **هرگز** روی `this` یا `typeof(MyClass)` lock نکنید (deadlock risk)

**مثال اشتباه**:
```csharp
// ❌ بد
lock (this) { ... }

// ✅ خوب
private readonly object _lock = new object();
lock (_lock) { ... }
```

### Monitor

`Monitor` همان چیزی است که `lock` در پشت صحنه از آن استفاده می‌کند:

```csharp
Monitor.Enter(_lockObject);
try {
    // critical section
} finally {
    Monitor.Exit(_lockObject);
}
```

**تفاوت**: `Monitor` قابلیت‌های بیشتری دارد مثل `Monitor.Wait()` و `Monitor.Pulse()`.

### Mutex

**Mutex** برای synchronization بین **Processها** استفاده می‌شود (نه فقط Threadها):

```csharp
using var mutex = new Mutex(false, "MyAppMutex");
if (mutex.WaitOne(1000)) {
    try {
        // critical section
    } finally {
        mutex.ReleaseMutex();
    }
}
```

**تفاوت Monitor vs Mutex**:
- **Monitor**: فقط در یک Process (internal threads)
- **Mutex**: بین Processها (external/OS-wide)

**استفاده**: وقتی می‌خواهید مطمئن شوید فقط یک instance از برنامه در حال اجرا است.

### Semaphore و SemaphoreSlim

**Semaphore** اجازه می‌دهد **چند Thread** همزمان به یک resource دسترسی داشته باشند:

```csharp
// SemaphoreSlim برای internal threads (سریع‌تر)
private readonly SemaphoreSlim _semaphore = new SemaphoreSlim(3, 3); // حداکثر 3 Thread

async Task DoWork() {
    await _semaphore.WaitAsync();
    try {
        // حداکثر 3 Thread می‌توانند اینجا باشند
    } finally {
        _semaphore.Release();
    }
}

// Semaphore برای external threads (بین Processها)
using var semaphore = new Semaphore(3, 3, "MySemaphore");
```

**استفاده**: وقتی می‌خواهید تعداد محدودی Thread به یک resource دسترسی داشته باشند (مثلاً connection pool).

### Concurrent Collections

وقتی چند thread همزمان به یک **collection** (مثل Dictionary یا List) خواندن/نوشتن می‌کنند، lock دستی یکی از گزینه‌هاست؛ گزینهٔ دیگر استفاده از **concurrent collections** است:

- `ConcurrentDictionary<TKey, TValue>` — برای cache و نگاشت‌های مشترک
- `ConcurrentBag<T>`, `ConcurrentQueue<T>`, `ConcurrentStack<T>` — برای producer-consumer و صف کار

**Trade-off**: این نوع مجموعه‌ها lockهای ریزدانه (fine-grained) داخلی دارند؛ برای سناریوهای read-heavy یا رقابت متوسط مناسب‌اند. اگر critical section خیلی کوچک است، گاهی یک lock ساده روی `Dictionary` ساده‌تر و قابل‌دفاع‌تر است. در مصاحبه انتظار می‌رود نام آن‌ها و زمان استفاده را بدانید.

---

## 9. Deadlock

**Deadlock** زمانی رخ می‌دهد که دو یا چند Thread منتظر یکدیگر هستند:

```
Thread 1: Lock A → منتظر Lock B
Thread 2: Lock B → منتظر Lock A
```

**راه‌حل‌ها**:
1. **همیشه locks را به یک ترتیب بگیرید** (lock ordering)
2. از `Monitor.TryEnter` با timeout استفاده کنید
3. از `SemaphoreSlim.WaitAsync` استفاده کنید (async-friendly)

**مثال Deadlock**:
```csharp
// ❌ Deadlock risk
void Method1() {
    lock (lockA) {
        lock (lockB) { ... }
    }
}

void Method2() {
    lock (lockB) {
        lock (lockA) { ... } // Deadlock!
    }
}
```

**راه حل**:
```csharp
// ✅ همیشه به یک ترتیب
void Method1() {
    lock (lockA) {
        lock (lockB) { ... }
    }
}

void Method2() {
    lock (lockA) { // همان ترتیب
        lock (lockB) { ... }
    }
}
```

---

## 10. Thread Pool

### مشکل Thread Overhead

ایجاد و حذف Thread هزینه دارد:
- Allocate کردن Stack (معمولاً 1MB)
- ایجاد metadata
- Context switching

**راه حل**: **Thread Pool** - یک استخر از Threadها که از قبل ایجاد شده‌اند و reuse می‌شوند.

### Thread Pool در .NET

- Thread Pool به صورت خودکار توسط CLR مدیریت می‌شود
- تعداد Threadها **dynamic** است (نه ثابت!)
- Threadهای Thread Pool **background threads** هستند

**نکته مهم**: Thread Pool size ثابت نیست. CLR بر اساس workload آن را تنظیم می‌کند (معمولاً از تعداد CPU cores شروع می‌کند).

### Foreground vs Background Threads

- **Foreground Thread**: Process تا زمانی که foreground threadها در حال اجرا هستند، زنده می‌ماند
- **Background Thread**: وقتی همه foreground threadها تمام شدند، background threadها terminate می‌شوند

**نکته**: Thread Pool threads همیشه background هستند. در سرویس‌های وب، اگر threadهای این pool را با کار blocking یا CPU سنگین اشغال کنید، **thread pool starvation** رخ می‌دهد و درخواست‌های جدید در صف می‌مانند؛ در production با متریک‌های مربوط به thread count و queue length قابل مشاهده است.

---

## 11. Task و Task-Based Asynchronous Pattern (TAP)

### Task چیست؟

**Task** یک abstraction سطح بالا برای asynchronous work است. Task یک Thread نیست، بلکه یک **promise** برای یک کار است که ممکن است در آینده کامل شود.

**تفاوت Task vs Thread**:
- **Thread**: سطح پایین، مستقیم با OS
- **Task**: سطح بالا، از Thread Pool استفاده می‌کند

### Task و Thread Pool

Taskها معمولاً روی Thread Pool اجرا می‌شوند:

```csharp
Task.Run(() => {
    // این کار روی Thread Pool اجرا می‌شود
});
```

**مزایا**:
- Overhead کمتری نسبت به Thread دارد
- می‌تواند cancelled شود
- می‌تواند result return کند
- می‌تواند exception handle کند

### Long-Running Tasks

اگر یک Task **long-running** است، باید به CLR بگویید که Thread جداگانه ایجاد کند:

```csharp
Task.Factory.StartNew(() => {
    // Long-running work
}, TaskCreationOptions.LongRunning);
```

**چرا مهم است؟** اگر یک Task long-running روی Thread Pool اجرا شود، یک Thread Pool thread را برای مدت طولانی اشغال می‌کند و ممکن است Thread Pool را exhaust کند.

---

## 12. Async/Await

### چرا Async/Await؟

**Async/await** برای **I/O-bound** کارها طراحی شده است، نه CPU-bound.

**مثال I/O-bound**:
```csharp
// ❌ Synchronous - Thread block می‌شود
var data = File.ReadAllText("file.txt");

// ✅ Asynchronous - Thread آزاد می‌شود
var data = await File.ReadAllTextAsync("file.txt");
```

### چگونه کار می‌کند؟

1. وقتی `await` می‌رسد، Thread به Thread Pool برمی‌گردد
2. وقتی I/O operation کامل شد، یک callback اجرا می‌شود
3. ادامه کار روی یک Thread Pool thread اجرا می‌شود

**State Machine**: Compiler متد `async` را به یک state machine تبدیل می‌کند که state را track می‌کند.

### ConfigureAwait

```csharp
await SomeMethodAsync().ConfigureAwait(false);
```

**چرا مهم است؟**
- `ConfigureAwait(false)`: ادامه کار روی هر Thread Pool thread می‌تواند اجرا شود
- `ConfigureAwait(true)` (default): ادامه کار روی **SynchronizationContext** اصلی (مثلاً UI thread)

**قانون**: در library code، همیشه از `ConfigureAwait(false)` استفاده کنید (مگر اینکه نیاز به SynchronizationContext داشته باشید).

**نکتهٔ ASP.NET Core**: در ASP.NET Core به‌طور پیش‌فرض **SynchronizationContext** وجود ندارد؛ یعنی ادامهٔ بعد از `await` روی هر thread pool thread اجرا می‌شود و `ConfigureAwait(false)` در این محیط عملاً تغییری در رفتار ایجاد نمی‌کند. با این حال استفاده از آن در library code توصیه می‌شود تا اگر همان کد در WinForms/WPF یا جاهای دیگر استفاده شد، از deadlock و وابستگی به context جلوگیری شود.

---

## 13. Cancellation Token

**CancellationToken** برای cancel کردن async operations استفاده می‌شود:

```csharp
var cts = new CancellationTokenSource();
var token = cts.Token;

// در متد async
async Task DoWorkAsync(CancellationToken cancellationToken) {
    while (!cancellationToken.IsCancellationRequested) {
        // کار
        await Task.Delay(1000, cancellationToken);
    }
}

// Cancel کردن
cts.Cancel();
```

**نکات مهم**:
- Cancellation Token **cooperative** است - باید در کد چک شود
- `OperationCanceledException` را handle کنید
- در long-running operations حتماً از cancellation token استفاده کنید

**استفاده در Production**: در Web APIs، وقتی client connection را قطع می‌کند، باید background tasks را cancel کنید تا resources هدر نروند.

---

## 14. Synchronous vs Asynchronous

### Synchronous (Blocking)

```csharp
void DoWork() {
    var data = File.ReadAllText("file.txt"); // Thread block می‌شود
    Process(data);
}
```

- Thread منتظر می‌ماند تا I/O کامل شود
- Thread نمی‌تواند کار دیگری انجام دهد
- **مشکل**: در Web apps، Thread Pool را exhaust می‌کند

### Asynchronous (Non-Blocking)

```csharp
async Task DoWorkAsync() {
    var data = await File.ReadAllTextAsync("file.txt"); // Thread آزاد می‌شود
    Process(data);
}
```

- Thread به Thread Pool برمی‌گردد
- وقتی I/O کامل شد، ادامه کار اجرا می‌شود
- **مزیت**: Thread Pool threads بهتر استفاده می‌شوند

**قانون طلایی**: در async code، **هرگز** `.Result` یا `.Wait()` استفاده نکنید (deadlock risk).

---

## 15. Key Interview Talking Points

### باید بتوانید توضیح دهید:

1. **تفاوت Process و Thread**: Process حافظه جداگانه دارد، Threadها حافظه مشترک دارند
2. **چرا Thread Pool**: کاهش overhead ایجاد/حذف Thread
3. **چرا async/await**: برای I/O-bound کارها، Thread را block نمی‌کند
4. **تفاوت lock, Monitor, Mutex, Semaphore**: 
   - lock/Monitor: internal threads
   - Mutex: external threads (بین Processها)
   - Semaphore: چند Thread همزمان
5. **Interlocked vs lock**: برای عملیات تک‌گام (مثل counter) Interlocked؛ برای critical section پیچیده lock.
6. **Concurrent collections**: چه زمانی `ConcurrentDictionary` / `ConcurrentQueue` به‌جای lock دستی — وقتی چند thread به یک collection مشترک دسترسی دارند.
7. **Deadlock prevention**: lock ordering، timeout
8. **ConfigureAwait(false)**: در library code؛ و در ASP.NET Core که SynchronizationContext نداریم عملاً اثرش در همان محیط صفر است.
9. **Cancellation Token**: برای cancel کردن async operations و آزاد کردن resource وقتی client قطع می‌کند.
10. **Task.Run در ASP.NET Core برای CPU-bound**: چرا معمولاً anti-pattern است (همان thread pool درخواست‌ها).

---

## 16. Common Mistakes & Red Flags

### ❌ اشتباهات رایج:

1. **استفاده از `.Result` یا `.Wait()` در async code**
   ```csharp
   // ❌ Deadlock risk
   var result = SomeAsync().Result;
   ```

2. **Lock کردن روی `this` یا `typeof(MyClass)`**
   ```csharp
   // ❌ بد
   lock (this) { ... }
   ```

3. **استفاده از async/await برای CPU-bound کارها بدون offload** — async/await به‌خودی‌خود کار را سبک نمی‌کند. برای CPU-bound یا باید از `Task.Run` استفاده کنید (و آن هم در **ASP.NET Core** اغلب anti-pattern است چون همان thread pool درخواست‌ها را exhaust می‌کند) یا کار را به backend/worker جدا بسپارید.
   ```csharp
   // ❌ در وب: Task.Run فقط همان thread pool را مشغول می‌کند
   async Task<int> CalculateAsync() {
       return await Task.Run(() => HeavyComputation());
   }
   ```

4. **فراموش کردن `ConfigureAwait(false)` در library code**

5. **استفاده از `async void`** — فقط برای event handlers (مثلاً دکمه در UI). در `async void` exceptionها را نمی‌توانید catch کنید و در صورت بروز خطا می‌تواند process را به پایین بیندازد یا خطا گم شود؛ در کد غیر از event handler همیشه `async Task` استفاده کنید.

6. **عدم استفاده از Cancellation Token در long-running operations**

7. **Race condition در shared state** — برای یک counter یا عملیات ساده، استفاده از lock به‌جای **Interlocked** هم اشتباه فنی نیست ولی در مصاحبه و در کد واقعی نشان می‌دهد گزینهٔ سبک‌تر را نمی‌شناسید.

8. **Thread pool starvation** — در سرویس‌های وب، block کردن thread pool (مثلاً با `.Result` یا `.Wait()` یا کار CPU سنگین روی همان pool) می‌تواند باعث شود درخواست‌های جدید نتوانند thread بگیرند و سرویس به‌ظاهر «گیر کند»؛ در production با monitoring و دیدن queue length و thread count قابل تشخیص است.

---

## 17. When to Use / When NOT to Use

### Thread
- ✅ **استفاده**: وقتی نیاز به کنترل دقیق Thread دارید (مثلاً priority)
- ❌ **عدم استفاده**: در اکثر موارد - از Task استفاده کنید

### Task
- ✅ **استفاده**: برای async work، parallel processing
- ❌ **عدم استفاده**: برای long-running work (از `TaskCreationOptions.LongRunning` استفاده کنید)

### async/await
- ✅ **استفاده**: I/O-bound operations (file, network, database)
- ❌ **عدم استفاده**: CPU-bound operations (از `Task.Run` استفاده کنید)

### lock/Monitor
- ✅ **استفاده**: synchronization در یک Process
- ❌ **عدم استفاده**: وقتی نیاز به coordination بین Processها دارید (از Mutex استفاده کنید)

### Semaphore
- ✅ **استفاده**: وقتی می‌خواهید تعداد محدودی Thread به resource دسترسی داشته باشند
- ❌ **عدم استفاده**: وقتی فقط یک Thread باید دسترسی داشته باشد (از lock استفاده کنید)

### Interlocked
- ✅ **استفاده**: عملیات atomic ساده (increment، compare-exchange) روی یک متغیر؛ بدون نیاز به lock.
- ❌ **عدم استفاده**: وقتی منطق critical section چند خط یا چند متغیر است (از lock استفاده کنید).

### Concurrent collections
- ✅ **استفاده**: چند thread به یک dictionary/queue/stack مشترک؛ وقتی lock دستی روی هر عملیات سنگین یا پرتکرار می‌شود.
- ❌ **عدم استفاده**: وقتی فقط یک thread می‌نویسد یا رقابت خیلی کم است؛ گاهی یک lock ساده ساده‌تر است.

---

## خلاصه

Concurrency و Multi-threading موضوع پیچیده‌ای است که نیاز به درک عمیق دارد. در مصاحبه:

1. **مفاهیم پایه** را بدانید (Process, Thread, Stack, Heap)
2. **Synchronization primitives** را بشناسید و trade-off‌ها را بدانید
3. **async/await** را درست استفاده کنید
4. **Deadlock** را تشخیص دهید و از آن جلوگیری کنید
5. **Real-world scenarios** را درک کنید

**نکته نهایی**: در مصاحبه‌های Senior-level، فقط syntax کافی نیست. باید بتوانید **چرا** و **چه زمانی** از هر ابزار استفاده کنید و **trade-off‌ها** را توضیح دهید.
