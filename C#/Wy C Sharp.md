# چرا C# و .NET؟ تاریخچه و ویژگی‌ها

این مقاله به بررسی تاریخچه، ویژگی‌ها و مزایای C# و .NET می‌پردازد - موضوعاتی که در مصاحبه‌های Backend Developer بسیار مهم هستند.

---

## 1. تاریخچه مایکروسافت و ظهور .NET

### مایکروسافت: از سیستم‌عامل تا اکوسیستم توسعه

مایکروسافت یکی از فعال‌ترین شرکت‌های حوزه نرم‌افزار است که از پروژه‌های بسیار بزرگ حمایت و توسعه می‌دهد. از سیستم‌عامل تا Cloud و SDKهای توسعه، مایکروسافت در صنعت نرم‌افزار بسیار قوی است.

### ظهور Framework و SDK

زمانی که مایکروسافت شروع به تولید سیستم‌عامل کرد، نیاز بود که هر چیزی که کاربران و خود شرکت نیاز داشتند، خودشان تولید کنند. اما با نیاز روزافزون مردم، از یک زمان به بعد مایکروسافت ابزارهای توسعه تولید کرد و به توسعه‌دهندگان گفت که برنامه‌هایی بنویسند و روی سیستم‌عامل‌های مایکروسافت اجرا کنند.

**اینجا مفهوم Framework ظاهر شد.**

در قالب یک سری چارچوب (Framework) روی بستر ویندوز، مایکروسافت اجازه داد تا برنامه‌نویسان برنامه تولید کنند. به این ابزارها **SDK** (Software Development Kit) می‌گویند.

### محدودیت اولیه: فقط ویندوز

در ابتدا، همه نرم‌افزارها با چارچوب مایکروسافت فقط روی ویندوز کار می‌کردند. این محدودیت باعث شد که:

- برنامه‌نویسان برای هر پلتفرم باید زبان و ابزار جداگانه‌ای یاد بگیرند
- یکپارچگی کد بین پلتفرم‌ها وجود نداشت
- هزینه توسعه بالا بود

### تولد C#: یک زبان برای همه پلتفرم‌ها

به مرور زمان مشخص شد که برای هر دیوایس و سیستم‌عاملی یک زبان خاص مورد نیاز است. مایکروسافت تصمیم گرفت یک زبان ایجاد کند که روی تمام نیازها را فراهم کند.

**پس C# را ایجاد کرد** تا هم روی ویندوز، هم Cloud و هم سایر پلتفرم‌ها بتوانند یکپارچگی را حفظ کنند.

---

## 2. Open Source شدن: نقطه عطف ASP.NET Core

### چالش: جذابیت لینوکس و Open Source

از یک زمانی با وجود لینوکس و پروژه‌های Open Source زیاد، شاهد این بودیم که برای خیلی از برنامه‌نویسان جذاب نبود که بیایند و روی C# و پلتفرم‌های مایکروسافت کار کنند.

**چرا؟**

چون آن‌ها می‌توانستند لایه‌های زیرین یک پلتفرم را به راحتی ببینند، توسعه دهند و از آن استفاده کنند. اما در .NET Framework قدیمی، این امکان وجود نداشت.

### راه‌حل: Open Source شدن ASP.NET Core

مایکروسافت **ASP.NET Core را Open Source عرضه کرد**. این تصمیم نقطه عطفی در تاریخ .NET بود.

**مزایا**:
- توسعه‌دهندگان می‌توانند کد منبع را ببینند
- می‌توانند مشارکت کنند
- می‌توانند از آن در لینوکس و macOS استفاده کنند
- با استفاده از این Framework و CLR، می‌توانیم کدهای C# را در هر پلتفرمی اجرا کنیم

---

## 3. چرا C#؟ مزایای انتخاب این زبان

### پشتیبانی شرکتی

زمانی که می‌خواهیم نرم‌افزاری را توسعه دهیم و به شرکتی ارائه دهیم، بحث **پشتیبانی** و **مدیریت پروژه در طول زمان** بسیار مهم است.

**مزیت C#**:
- پشت C# شرکتی وجود دارد که آن را Support می‌کند
- می‌دانیم که ورژن‌های جدید، ورژن‌های قبلی را Support می‌کنند
- می‌توانیم همگام با یک شرکت بزرگ روی این زبان سرمایه‌گذاری کنیم

این موضوع برای پروژه‌های Enterprise بسیار مهم است.

### Managed Language

C# یک زبان **Managed** است. این یعنی:

- **Memory Management**: Garbage Collector به صورت خودکار حافظه را مدیریت می‌کند
- **Type Safety**: Strong Type است - نمی‌توانیم قیمه‌ها را در ماست بریزیم!
- **Type Safety**: نوع‌ها نمی‌توانند ساختار یک Object را بهم بریزند

### Strong Typing

C# یک زبان **Strong Type** است:

```csharp
int number = 10;
string text = "Hello";
// number = text; // ❌ Compile Error - Type Safety
```

این ویژگی باعث می‌شود:
- خطاها در زمان Compile پیدا شوند، نه Runtime
- کد قابل اعتمادتر باشد
- Refactoring راحت‌تر باشد

---

## 4. معماری .NET

### CLR (Common Language Runtime)

.NET متشکل از دو بخش اصلی است:

**بخش اول: CLR**

CLR یک **ماشین مجازی** است که:
- در هر سیستم‌عامل به خصوص است
- کدها را تبدیل به یک زبان میانی می‌کند (IL - Intermediate Language)
- مدیریت اجرای برنامه را به عهده دارد

**مزایای CLR**:
- **Platform Independence**: کد یک بار نوشته می‌شود، در همه جا اجرا می‌شود
- **Memory Management**: Garbage Collection
- **Security**: Code Access Security
- **Exception Handling**: مدیریت یکپارچه Exception

### BCL (Base Class Library)

**بخش دوم: BCL**

BCL مجموعه بزرگی از کلاس‌ها و ساختارهای آماده است که در .NET استفاده می‌کنیم:

- **Collections**: List، Dictionary، Queue، Stack
- **IO**: File، Stream، Network
- **Threading**: Thread، Task، Async/Await
- **LINQ**: Language Integrated Query
- و هزاران کلاس دیگر

### Roslyn Compiler

**Roslyn** کامپایلر C# است که:
- Open Source است
- به صورت Real-time کد را تحلیل می‌کند
- امکان Code Analysis و Refactoring را فراهم می‌کند

---

## 5. ویژگی‌های C#

### Object-Oriented Programming

C# یک زبان **OOP** است که قابلیت‌های:
- **Encapsulation**: کپسوله‌سازی داده و رفتار
- **Inheritance**: ارث‌بری
- **Polymorphism**: چندریختی
- **Abstraction**: انتزاع

را پشتیبانی می‌کند.

### Functional Programming Features

از جهتی که C# یک زبان OOP است، **قابلیت‌های زبان Functional** را نیز دارد:

- **Delegates**: توابع به عنوان First-Class Citizen
- **Lambda Expressions**: توابع ناشناس
- **LINQ**: Language Integrated Query برای کار با Collections

```csharp
// Functional Style با LINQ
var numbers = new List<int> { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0).ToList();
```

---

## 6. مفاهیم مهم برای مصاحبه

### IEnumerable vs List

**تفاوت‌ها**:

| ویژگی | IEnumerable | List |
|---|---|---|
| **Indexer** | ندارد | دارد |
| **Iteration** | باید foreach زده شود | می‌توان با Index پرش کرد |
| **Performance** | O(n) برای دسترسی | O(1) برای دسترسی با Index |
| **Lazy Evaluation** | بله | خیر |

**IEnumerable**:
- قابلیت نشستن روی هر مدلی که قابلیت Iteration دارد
- عملیات Select بعد از اینکه به وسیله ToList() یا foreach صدا زده شود عمل می‌کند (Lazy Evaluation)

**List**:
- Indexer دارد
- سریع می‌پرد روی المان‌ها از این نظر O(1) است

### Lazy Load vs Eager Load

- **Lazy Load**: داده‌ها فقط زمانی Load می‌شوند که به آن‌ها نیاز باشد
- **Eager Load**: داده‌ها از قبل Load می‌شوند

(برای جزئیات بیشتر به فایل مربوطه مراجعه کنید)

### Unified Type System

در .NET، همه چیز از `System.Object` ارث‌بری می‌کند. این باعث می‌شود:
- Type System یکپارچه باشد
- Boxing/Unboxing ممکن باشد
- Reflection قدرتمند باشد

---

## 7. مفاهیم پیشرفته

### CAP Theorem

**CAP Theorem** می‌گوید که در یک Distributed System، نمی‌توانیم همزمان هر سه ویژگی را داشته باشیم:

- **C**onsistency: سازگاری
- **A**vailability: در دسترس بودن
- **P**artition Tolerance: تحمل تقسیم‌بندی

(برای جزئیات بیشتر به منابع مراجعه کنید)

### Saga Pattern و Two-Phase Commit

- **Saga Pattern**: الگویی برای مدیریت Transaction‌های توزیع‌شده
- **Two-Phase Commit**: پروتکلی برای تضمین Consistency در Distributed Transactions

### Factory Pattern و Dependency Injection

- **Factory Pattern**: الگوی طراحی برای ایجاد Object
- **Dependency Injection**: تزریق وابستگی‌ها به جای Hard-coding

### Service-Oriented Architecture (SOA)

معماری مبتنی بر سرویس که در آن:
- سیستم به سرویس‌های مستقل تقسیم می‌شود
- سرویس‌ها از طریق Interface با هم ارتباط برقرار می‌کنند
- قابلیت Reuse و Scalability افزایش می‌یابد

---

## 8. Collections در .NET

### انواع Collections

- **List**: لیست پویا با Index
- **Dictionary**: Key-Value Pair
- **Stack**: LIFO (Last In First Out)
- **Queue**: FIFO (First In First Out)
- **HashSet**: مجموعه یکتا بدون ترتیب

هر کدام کاربرد خاص خود را دارند و باید بر اساس نیاز انتخاب شوند.

---

## 9. منابع یادگیری

### کتاب‌ها

- **C# 10 in a Nutshell**: مرجع جامع C#
- **Designing Data-Intensive Applications** (Martin Kleppmann): برای درک System Design
- **فصل 8 و 10 البهاری**: برای LINQ و XML

### ویدیوها

- **CAP Theorem**: https://www.youtube.com/watch?v=eWMgsk7mpFc
- **ASP.NET Core Tutorials**: https://www.youtube.com/playlist?list=PL6n9fhu94yhWi8K02Eqxp3Xyh_OmQ0Rp6
- **Dependency Injection**: https://www.youtube.com/watch?v=QM1iUe6IofM
- **LINQ Tutorial**: https://www.pragimtech.com/courses/linq-tutorial/

### منابع دیگر

- **LibGen**: https://libgen.is/ (برای دانلود کتاب)
- **System Group Events**: https://www.systemgroup.net/events/understanding-dependency-injection/
- **همکاران سیستم**: منابع فارسی

---

## خلاصه و نکات نهایی

### چرا C# را انتخاب کنیم؟

1. **پشتیبانی شرکتی**: مایکروسافت پشتیبانی بلندمدت می‌کند
2. **Cross-Platform**: با .NET Core می‌توانیم روی هر پلتفرمی اجرا کنیم
3. **Open Source**: ASP.NET Core و .NET Core Open Source هستند
4. **Type Safety**: Strong Typing باعث کاهش خطا می‌شود
5. **Rich Ecosystem**: BCL و NuGet Package‌های فراوان
6. **Performance**: .NET Core Performance بالایی دارد
7. **Community**: جامعه بزرگ و فعال

### نکات برای مصاحبه

- تاریخچه .NET و Open Source شدن را بدانید
- تفاوت CLR و BCL را توضیح دهید
- Strong Typing و Managed Language را توضیح دهید
- IEnumerable vs List را بدانید
- CAP Theorem و Distributed Systems را درک کنید

**موفق باشید!** 🚀
