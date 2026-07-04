# Vertical Slice Architecture

**Vertical Slice Architecture** یه رویکرد نسبتاً جدید توی معماری نرم‌افزار هست که توسط **Jimmy Bogard** (همون کسی که MediatR و AutoMapper رو ساخته) معروف شد. اینجا می‌خوایم بفهمیم این معماری چیه، چه مسئله‌ای رو حل می‌کنه، و کی باید استفاده بشه.

**اهمیت در مصاحبه:** VSA در مصاحبه‌های بک‌اند و معماری مطرح می‌شود. انتظار می‌رود تفاوت با Layered، مدیریت ارتباط بین Slices، و ترکیب با CQRS/MediatR را بتوانید توضیح دهید.

---

## ۱. فلسفه: چرا Vertical Slice به وجود اومد؟

### مشکل معماری‌های لایه‌ای

توی معماری‌های سنتی مثل **لایه‌ای (Layered)** یا **Clean Architecture**، کد رو بر اساس مسئولیت فنی دسته‌بندی می‌کنیم:

```
Controllers/
Services/
Repositories/
Models/
```

این ساختار به ظاهر منظمه، ولی یه مشکل بزرگ داره: **وقتی می‌خوای یه Feature رو تغییر بدی، باید بین چندین لایه پرسه بزنی.** 

برای مثال، اگه بخوای قابلیت "ثبت سفارش" رو تغییر بدی، باید:
- بری توی `OrderController` (لایه Presentation)
- بعد بری توی `OrderService` (لایه Business)
- بعد بری توی `OrderRepository` (لایه Data)
- بعد شاید `OrderValidator` (لایه دیگه)

یعنی یه Feature ساده، توی ۴-۵ لایه پخش شده. این کار:
- باعث **پراکندگی دانش** میشه
- **context switching** رو زیاد می‌کنه
- درک جریان یه Feature رو سخت می‌کنه

### ایده Vertical Slice

جیمی بوگارد گفت: **"به جای لایه‌بندی افقی، بیایم عمودی فکر کنیم."** 

یعنی به جای اینکه کد رو بر اساس "نوع تکنیکال" دسته‌بندی کنیم، بر اساس **"Feature"** دسته‌بندی کنیم. هر Feature باید شامل همه چیزهایی باشه که برای اجراش نیاز داریم: از UI تا دیتابیس.

---

## ۲. تعریف Vertical Slice Architecture

> **Vertical Slice Architecture** یه رویکرد برای سازماندهی کد (معمولاً داخل یک مونولیت یا ماژول) هست که به جای لایه‌های افقی، کد رو بر اساس **Feature** یا **Use Case** دسته‌بندی می‌کنه. هر «برش عمودی» (Vertical Slice) شامل همه لایه‌های مورد نیاز برای اون Feature هست. 

به زبان ساده: **هر Feature یه پوشه جداست، و توی اون پوشه همه چی هست: کنترلر، کامند، هندلر، ولیدیشن، حتی کوئری دیتابیس.**

اینجا بیشتر از این‌که دربارهٔ نوع استقرار (Monolith/Microservices) حرف بزنیم، دربارهٔ **ساختاردهی داخلی کد** صحبت می‌کنیم؛ یعنی می‌تونی Vertical Slice رو هم داخل یه مونولیت لایه‌ای و هم در کنار معماری‌های دیگه به‌کار ببری.

### نقل قول کلیدی جیمی بوگارد

> هدف اینه که **"وابستگی بین Slices رو کم کنیم"** (Loose Coupling) و **"وابستگی درون هر Slice رو زیاد کنیم"** (High Cohesion). 

---

## ۳. ساختار: چطور سازماندهی می‌کنیم؟

### مقایسه با Clean Architecture

| جنبه | Clean Architecture | Vertical Slice Architecture |
|------|-------------------|------------------------------|
| **سازماندهی** | بر اساس لایه‌های فنی (Controllers, Services, Repositories) | بر اساس Feature (Order, Product, User) |
| **تغییر یک Feature** | باید چندین لایه رو تغییر بدی | فقط یه پوشه رو تغییر میدی |
| **وابستگی** | لایه‌ها به هم وابسته‌ان | Slices مستقل از هم هستن |
| **یادگیری پروژه** | باید کل ساختار لایه‌ها رو بفهمی | می‌تونی با یه Feature شروع کنی |

### مثال ساختار پوشه‌ها

```
src/
├── Features/
│   ├── Orders/
│   │   ├── CreateOrder/
│   │   │   ├── CreateOrderCommand.cs
│   │   │   ├── CreateOrderHandler.cs
│   │   │   ├── CreateOrderValidator.cs
│   │   │   ├── CreateOrderResponse.cs
│   │   │   └── CreateOrderController.cs (یا Minimal API Endpoint)
│   │   ├── GetOrder/
│   │   │   ├── GetOrderQuery.cs
│   │   │   ├── GetOrderHandler.cs
│   │   │   └── GetOrderResponse.cs
│   │   └── Common/
│   │       ├── IOrderRepository.cs
│   │       ├── OrderRepository.cs
│   │       └── Order.cs
│   └── Products/
│       ├── CreateProduct/
│       ├── GetProduct/
│       └── Common/
└── Shared/ (یا Infrastructure)
    ├── Persistence/
    ├── Logging/
    └── Messaging/
```

توی دنیای **ASP.NET Core** معمولاً این الگو رو همراه با `MediatR`، Minimal APIs و پوشهٔ `Features` می‌بینی؛ خیلی از تمپلیت‌ها و نمونه‌کدهای جامعهٔ .NET همین ساختار رو پیشنهاد می‌کنن.

### نکات مهم ساختار

1. **هر Use Case یه پوشه مجزا داره:** توی مثال بالا، `CreateOrder` و `GetOrder` پوشه‌های جدا هستن.
2. **هر پوشه شامل همه چیزهای مربوط به اون Use Case هست:** کامند، هندلر، ولیدیشن، حتی کنترلر.
3. **مشترکات (Shared) بیرون از Slices قرار می‌گیرن:** مثل DbContext، سرویس‌های زیرساخت، یا Domain Modelهای مشترک. 

---

## ۴. مزایا (چی به دست میاریم؟)

### ۴.۱. **High Cohesion (وابستگی درونی بالا)** ✅

همه چیزهایی که برای یه Feature نیاز داری، توی یه جا جمع شده. این باعث میشه:
- درک Feature راحت‌تر باشه
- تغییرات محدود به همون پوشه باشه
- تست کردن Feature مستقل امکان‌پذیر باشه 

### ۴.۲. **Low Coupling (وابستگی بیرونی کم)** ✅

هر Slice تا حد خوبی از بقیه مستقله؛ یعنی از نظر منطق برنامه‌نویسی، تغییر توی Slice `Orders` معمولاً روی Slice `Products` تأثیری نداره (حتی اگه زیرساخت و دیتابیس مشترک باشن). این یعنی:
- تیم‌ها می‌تونن موازی کار کنن
- ریسک تغییرات کاهش پیدا می‌کنه 

### ۴.۳. **کاهش Context Switching** ✅

وقتی می‌خوای یه Feature رو تغییر بدی، دیگه نیازی نیست بین ۵ تا لایه پرسه بزنی. همه چی توی یه پوشه هست و این خودش سرعت توسعه رو بالا می‌بره. 

### ۴.۴. **Merge Conflict کمتر** ✅

چون تیم‌ها روی Slices مختلف کار می‌کنن، احتمال اینکه دو نفر یه فایل رو تغییر بدن خیلی کم میشه. 

### ۴.۵. **هماهنگی با Agile و DDD** ✅

- هر Slice معمولاً معادل یک **User Story** یا **Use Case**ه.
- با **Domain-Driven Design** هم‌خوانی داره، چون معمولاً **مجموعه‌ای از Slices** یه Bounded Context رو شکل می‌دن و مرزهای دامنه‌ای توی ساختار پوشه‌ها هم قابل ردیابی می‌شه. 

### ۴.۶. **انعطاف در انتخاب تکنولوژی** ✅

از نظر فنی می‌تونی توی هر Slice از تکنولوژی متفاوتی استفاده کنی (مثلاً یه Slice از EF Core، یکی دیگه از Dapper)، به‌خصوص وقتی مرزهای transactional و performance بین Use Caseها متفاوته. فقط باید حواست باشه **زیاده‌روی در Polyglot شدن**، نگه‌داری سیستم رو سخت نکنه. 

---

## ۵. معایب و چالش‌ها (بهایی که می‌دیم)

### ۵.۱. **افزایش حجم کد (Duplication)** ❌

ممکنه مجبور بشی یه منطق تکراری رو توی چند Slice تکرار کنی. مثلاً validation ایمیل هم توی `CreateUser` و هم توی `UpdateUser`. 

**راه‌حل:** از **Shared Kernel** استفاده کن؛ یعنی یه پوشهٔ مشترک برای چیزهایی که واقعاً باید بین Slices به اشتراک گذاشته بشن. فقط مراقب باش Shared Kernel کم‌حجم و کنترل‌شده بمونه، وگرنه عملاً دوباره به همون معماری لایه‌ای و وابستگی‌های سفت برمی‌گردی. 

### ۵.۲. **ارتباط بین Slices** ❌

وقتی یه Slice نیاز به داده‌های Slice دیگه داره (مثلاً `CreateOrder` نیاز به اطلاعات کاربر داره)، نمی‌تونه مستقیم به Repository اون دسترسی پیدا کنه. 

**راه‌حل‌ها:** 
- **Shared Services:** یه سرویس مشترک که هر دو Slice ازش استفاده کنن.
- **Event-Driven Communication:** با رویدادها ارتباط برقرار کنن (مثلاً `OrderCreated` رویداد بفرسته، `UserHistory` بهش گوش بده).
- **Internal API:** هر Slice یه API داخلی داشته باشه که بقیه بتونن صدا بزنن (شبیه میکروسرویس‌های کوچیک).

### ۵.۳. **پیچیدگی در ابتدای کار** ❌

برای تیم‌هایی که به معماری لایه‌ای عادت دارن، تغییر ذهنیت به Vertical Slice زمان می‌بره. 

### ۵.۴. **نیاز به Refactoring مداوم** ❌

جیمی بوگارد تأکید می‌کنه که Vertical Slice بدون Refactoring مداوم به «گلولهٔ گِلی بزرگ» (Big Ball of Mud) تبدیل می‌شه. باید مرتب کد رو تمیز کنی و Slices رو بهبود بدی. 

---

## ۶. مقایسه با معماری‌های دیگه

### Vertical Slice vs Clean Architecture

| ویژگی | Clean Architecture (در پیاده‌سازی‌های رایج) | Vertical Slice |
|-------|--------------------------------------------|----------------|
| **دسته‌بندی** | بر اساس لایه‌های فنی و حلقه‌های درونی | بر اساس Featureها / Use Caseها |
| **وابستگی** | وابستگی به سمت داخل (Dependency Rule) | Slices مستقل |
| **تست** | تست هر لایه جدا | تست End-to-End هر Slice |
| **تغییرات** | ممکنه چند لایه درگیر بشه | فقط همون Slice |
| **یادگیری** | نیاز به درک کل معماری | با یه Slice میشه شروع کرد |

### Vertical Slice vs Modular Monolith

شباهت زیادی دارن. توی Modular Monolith هم ماژول‌های مستقل داریم. تفاوت اینجاست که توی Vertical Slice، هر Use Case می‌تونه یه Slice مستقل باشه، حتی توی یه ماژول. 

---

## ۷. کی استفاده کنیم؟ کی نه؟

### ✅ موارد مناسب

- **پروژه‌های با پیچیدگی متوسط تا بالا:** جایی که Features مشخص و قابل تفکیک هستن.
- **تیم‌های چندنفره:** هر تیم می‌تونه روی یه مجموعه از Slices کار کنه.
- **سیستم‌هایی که نیاز به تغییرات مکرر دارن:** چون تغییرات محدود به یه Slice هست.
- **پروژه‌های Agile:** هماهنگ با User Stories و Sprints. 

### ❌ موارد نامناسب

- **پروژه‌های خیلی کوچیک (CRUD ساده):** شاید Over-engineering باشه.
- **تیم‌های کوچیک با تجربه محدود:** ممکنه در مدیریت ارتباط بین Slices مشکل پیدا کنن.
- **سیستم‌هایی که نیاز به اشتراک زیاد بین Features دارن:** ممکنه duplication آزاردهنده بشه.

---

## ۸. نکات مصاحبه (برای سینیورها)

### سوالات رایج

**۱. Vertical Slice Architecture چیه و چه فرقی با Layered Architecture داره؟**

> VSA کد رو بر اساس Feature سازماندهی می‌کنه، نه لایه‌های فنی. هر Feature شامل همه لایه‌ها (کنترلر، سرویس، ریپازیتوری) توی یه پوشه هست. این باعث میشه تغییرات محدود به همون پوشه باشه و درک Feature راحت‌تر بشه. 

**۲. مشکل اصلی معماری‌های لایه‌ای چیه که VSA حل می‌کنه؟**

> مشکل اصلی پراکندگی یه Feature توی چندین لایه است. برای تغییر یه Feature کوچیک، باید چندین فایل توی لایه‌های مختلف رو تغییر بدی. این باعث Context Switching زیاد و کاهش بهره‌وری میشه. 

**۳. ارتباط بین Slices رو چطور مدیریت می‌کنی؟**

> چند راه داریم:
> - **Shared Kernel:** برای منطق واقعاً مشترک
> - **Event-Driven:** با رویدادها بین Slices ارتباط برقرار کنیم
> - **Internal API:** هر Slice API داخلی داشته باشه 

**۴. مشکل Duplication رو توی VSA چطور حل می‌کنی؟**

> اولاً باید بپذیریم که **تکرار گاهی بهتر از وابستگی نادرست هست** (Rule of Three). اگه یه منطق توی سه تا Slice تکرار شد، اون موقع می‌تونیم به Shared Kernel منتقلش کنیم. 

**۵. VSA چطور با CQRS و MediatR ترکیب میشه؟**

> ترکیبش عالیه. هر Slice می‌تونه یه Command یا Query داشته باشه که با MediatR پردازش بشه. این باعث میشه کد تمیزتر و تست‌پذیرتر بشه. 

### اشتباهات رایج (Red Flag)

- **ایجاد وابستگی مستقیم بین Slices:** مثلاً `OrderSlice` مستقیماً از `UserSlice.Repository` استفاده کنه. ❌
- **Over-engineering برای پروژه‌های کوچیک:** استفاده از VSA برای یه CRUD ساده. ❌
- **عدم Refactoring:** نادیده گرفتن کدهای تکراری و تبدیل تدریجی به "گلوله گلی". ❌
- **Shared Kernel خیلی بزرگ:** گذاشتن همه چیز توی Shared Kernel و از بین بردن independence Slices. ❌

---

## ۹. جمع‌بندی

**Vertical Slice Architecture** یه رویکرد عملی و feature-centric هست که:

- **وابستگی درونی (Cohesion)** رو زیاد می‌کنه (همه چیز یه Feature توی یه جاست)
- **وابستگی بیرونی (Coupling)** رو کم می‌کنه (Slices مستقل هستن)
- **توسعه موازی** رو آسان می‌کنه
- با **CQRS** و **MediatR** ترکیب عالی داره
- با **Agile** و **DDD** هم‌خوانی داره

**نکته نهایی:** VSA قرار نیست جایگزین همه معماری‌ها بشه. می‌تونی ترکیبی کار کنی. مثلاً زیرساخت رو لایه‌ای داشته باشی، ولی Features رو Vertical Slice. 

> جیمی بوگارد می‌گه: "هدف اینه که وابستگی بین Slices رو کم کنیم و وابستگی درون هر Slice رو زیاد کنیم." همینه که باعث میشه تغییرات سریع‌تر و ایمن‌تر اتفاق بیفتن.

---

## منابع و یادداشت‌های مرتبط

- **مطالب مرتبط:** [[Clean Architecture]]، [[Layered - N-Tier]]، [[CQRS]]، [[Domain-Driven Design]]
- Jimmy Bogard: [Vertical Slice Architecture](https://jimmybogard.com/vertical-slice-architecture/) (پست اصلی)
- [GitHub - VerticalSliceArchitecture Example](https://github.com/MK1985/VerticalSliceArchitecture) 
- [NDC Talk: Designing for change with Vertical Slice Architecture](https://ndclondon.com/agenda/designing-for-change-with-vertical-slice-architecture/0qaoanr3vhh) 
- [Caravel Template - Vertical Slice](https://github.com/Talento90/Caravel-Template-Vertical-Slice) 
- Packt Book: *An Atypical ASP.NET Core 6 Design Patterns Guide* (فصل Vertical Slice) 