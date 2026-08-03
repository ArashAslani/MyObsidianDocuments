# Calculation Engine Business Discovery

Version: 1.0

Status: Draft (Business Validated)

Bounded Context: Calculation Engine

Document Type: Business Discovery

---

# 1. Purpose

هدف این سند ثبت تمام قواعد کسب‌وکار مربوط به موتور محاسبات مهندسی در HyperAhan است.

این سند فقط شامل حقایق کسب‌وکار تأییدشده است.

در این سند:

- هیچ طراحی نرم‌افزاری انجام نمی‌شود.
- هیچ Aggregate تعریف نمی‌شود.
- هیچ Entity تعریف نمی‌شود.
- هیچ Value Object تعریف نمی‌شود.
- هیچ تصمیم پیاده‌سازی گرفته نمی‌شود.

تمام اطلاعات این سند مستقیماً از گفتگوهای انجام‌شده و تصمیمات قفل‌شده استخراج شده‌اند و هیچ قانون جدیدی به آن اضافه نشده است.

---

# 2. Scope

این سند فقط رفتارهای کسب‌وکار مربوط به محاسبات مهندسی را پوشش می‌دهد.

مسئولیت‌های این ماژول شامل موارد زیر است:

- انتخاب نوع فرمول مهندسی (Formula Type)
- اجرای فرمول‌های مهندسی
- اعتبارسنجی مقادیر ورودی فرمول
- تولید نتیجه محاسبه (Calculation Result)

این ماژول مسئول هیچ‌یک از موارد زیر نیست:

- مدیریت محصولات
- مدیریت دسته‌بندی‌ها
- نگهداری Specification محصولات
- تعریف ضرایب تبدیل واحد سفارش
- مدیریت سفارش
- مدیریت موجودی
- محاسبه قیمت
- اعمال مالیات
- مدیریت تخفیف

---

# 3. Bounded Context

Calculation Engine یک Bounded Context مستقل است.

Calculation Engine مالک فرمول‌های مهندسی است.

Catalog مالک اطلاعات محصول است.

Pricing مالک اطلاعات قیمت است.

Calculation Engine فقط با دریافت مقادیر ورودی، نتیجه محاسبه را تولید می‌کند.

---

# 4. Relationship With Other Contexts

## Catalog

Catalog می‌تواند ارتباط بین محصول و Formula Type را نگهداری کند.

Formula Type می‌تواند در یکی از سطوح زیر تعریف شود:

1. Product Level

```text
Product
|
Formula Type
```

2. Category Level

```text
Category
|
Formula Type
```

3. Parent Category Level

```text
Root Category
|
Formula Type
```

هنگام پیدا کردن Formula Type، اولویت انتخاب به ترتیب زیر است:

1. Formula تعریف‌شده روی Product
2. Formula تعریف‌شده روی Category محصول
3. Formula تعریف‌شده روی Parent Category
4. ادامه جستجو تا ریشه دسته‌بندی

اولین Formula Type معتبر انتخاب می‌شود.

---

## Pricing

Pricing مصرف‌کننده خروجی Calculation Engine است.

Pricing برای محصولات نیازمند محاسبه مهندسی، مقدار محاسبه‌شده (مثلاً وزن) را دریافت می‌کند.

Pricing هرگز فرمول مهندسی را اجرا نمی‌کند.

Pricing فقط نتیجه نهایی (مثلاً وزن کل بر حسب کیلوگرم) را در Base Price ضرب می‌کند.

---

## Public Website Tools

ابزارهای عمومی سایت (مانند ماشین‌حساب وزن) مستقیماً از Calculation Engine استفاده می‌کنند.

کاربران بدون ایجاد سفارش و بدون ورود به فرآیند Pricing می‌توانند از موتور محاسبه استفاده کنند.

رفتار Calculation Engine برای Pricing و ابزارهای عمومی کاملاً یکسان است.

---

# 5. Business Terminology

## Formula Type

تعریفی که مشخص می‌کند چه محاسباتی باید اجرا شود.

Formula Type می‌تواند توسط Admin ایجاد شود و شامل یک Formula مستقل یا ترکیبی از چند Formula و Operation باشد.

ساختار داخلی Formula Type توسط Admin تعیین می‌شود.

Calculation Engine فقط Formula Type نهایی را اجرا می‌کند.

Formula Type مالک منطق محاسباتی است و منبع ورودی‌ها را مشخص نمی‌کند.

---

## Formula Composition

یک محاسبه ممکن است از ترکیب چند Formula یا Operation تشکیل شود.

Calculation Engine باید بتواند عملیات زیر را در یک جریان محاسباتی اجرا کند:

- جمع (+)
- تفریق (-)
- ضرب (*)
- تقسیم (/)

ترتیب اجرای عملیات بخشی از تعریف Formula است.

مثال:

```text
Formula 1:
Calculate Area

Formula 2:
Calculate Weight

Final Result:
Area × Density
```

یا:

```text
Formula 1:
Diameter × Diameter

Formula 2:
Result × Length

Final Result:
Weight
```

Calculation Engine مسئول اجرای این زنجیره است.

---

## Formula Builder

قابلیتی که به Admin اجازه می‌دهد Formula Type جدید ایجاد کند یا چند Formula و Operation را با یکدیگر ترکیب کند.

Admin می‌تواند:

- Formula ایجاد کند.
- ترتیب اجرای Formulaها را تعیین کند.
- Operation بین Formulaها را مشخص کند.

Operationهای قابل استفاده شامل:

- جمع (+)
- تفریق (-)
- ضرب (*)
- تقسیم (/)

Formula Builder مسئول تعیین منطق محاسباتی است.

Formula Builder مسئول تعیین منبع ورودی‌ها نیست.

---

## Default Formula

فرمول پیش‌فرضی که زمانی اجرا می‌شود که Formula Resolution نتواند هیچ Formula Type معتبر از Product یا Category Hierarchy پیدا کند.

---

## Calculation Result

مقدار خروجی تولیدشده توسط Calculation Engine.

این مقدار یک کمیت مهندسی (مانند وزن، مساحت، حجم) است.

این مقدار هرگز شامل اطلاعات مالی (قیمت، تخفیف، مالیات) نیست.

---

## Source-Agnostic

ویژگی ذاتی Calculation Engine مبنی بر اینکه منشأ داده‌های ورودی (از Product، User Input، Order یا غیره) برای Engine اهمیتی ندارد.

Engine فقط وجود مقدار ورودی و اعتبار آن را بررسی می‌کند.

---

# 6. Confirmed Business Facts

## CF-001

فرمول‌های مهندسی فقط در داخل Calculation Engine وجود دارند.

هیچ فرمول مهندسی در Catalog، Pricing، Order یا Inventory وجود ندارد.

---

## CF-002

یک Formula Type می‌تواند مستقیماً روی Product یا روی Category تعریف شود.

Product و Category می‌توانند منبع تعیین Formula Type باشند.

---

## CF-003

Product ممکن است Formula Type اختصاصی داشته باشد.

در صورت نبود Formula Type روی Product، سیستم باید از تعریف موجود در Category hierarchy استفاده کند.

---

## CF-004

اگر در فرآیند Formula Resolution هیچ Formula Type معتبری در سطح Product یا Category Hierarchy پیدا نشود، Calculation Engine از Default Formula استفاده می‌کند.

---

## CF-005

Default Formula یک Formula معتبر برای سناریوهایی است که محاسبه مستقیم مقدار بر اساس ورودی اصلی انجام می‌شود.

Default Formula نباید شامل اطلاعات مالی مانند Base Price، Discount یا Tax باشد.

مثال:

```text
Quantity → Quantity
```

یا:

```text
Length × Quantity
```

---

## CF-006

Calculation Engine نسبت به منبع داده‌ها بی‌طرف است (Source-Agnostic).

Engine نمی‌داند مقدار یک ورودی از Product آمده یا توسط کاربر وارد شده یا از Order گرفته شده است.

---

## CF-007

ورودی‌های موردنیاز یک Formula Type می‌توانند از منابع مختلف تأمین شوند.

نمونه منابع تأییدشده:

- Product (مشخصات ثابت محصول)
- Order (مقدار سفارش)
- User (ورودی کاربر در ابزارهای عمومی)

---

## CF-008

یک Formula Type واحد می‌تواند هم در ابزارهای عمومی سایت و هم در فرآیند قیمت‌گذاری استفاده شود.

تفاوت فقط در منبع تأمین ورودی‌هاست.

فرمول اجراشده یکسان است.

---

## CF-009

Calculation Engine از مقادیر تبدیل‌شده استفاده می‌کند.

تبدیل واحد سفارش (Order Unit به Registration Unit) پیش از ورود به فرمول مهندسی انجام شده است.

---

## CF-010

نتایج محاسبه هرگز حاوی اطلاعات قیمت‌گذاری نیستند.

---

## CF-011

هر Formula Type مستقل است.

تغییر در یک Formula Type نباید روی Formula Type های دیگر اثر بگذارد.

---

## CF-012

یک Formula Type می‌تواند توسط چندین Product مورد استفاده قرار گیرد.

---

## CF-013

Formula Type می‌تواند شامل چند مرحله محاسباتی باشد.

این مراحل توسط Admin هنگام تعریف Formula Type تعیین می‌شوند.

هر مرحله می‌تواند نتیجه مرحله قبل را به عنوان ورودی دریافت کند.

Example:

```text
Step 1:
Calculate Volume

Step 2:
Volume × Density

Step 3:
Convert Result Unit
```

---

## CF-014

انتخاب Formula Type دارای اولویت سلسله مراتبی است.

اولویت:

1. Product Formula
2. Product Category Formula
3. Parent Category Formula
4. Root Category Formula

اولین Formula Type پیدا شده استفاده می‌شود.

---

## CF-015

Formula Type ورودی‌های موردنیاز خود را مشخص می‌کند.

منبع تأمین این ورودی‌ها خارج از Formula Type تعیین می‌شود.

مثال:

```text
Formula Type:

Required Inputs:
- Diameter
- Length
- Quantity

Source Mapping:

Diameter → Product Specification

Length → User Input

Quantity → Order
```

---

## CF-016

Admin می‌تواند Formula Type ایجاد یا تغییر دهد.

Admin می‌تواند:

- Formula جدید تعریف کند.
- Formulaهای موجود را ترکیب کند.
- ترتیب اجرای عملیات را مشخص کند.

---

## CF-017

Formula Type ایجادشده توسط Admin باید Input Contract مشخص داشته باشد.

Input Contract تعیین می‌کند Formula Type برای اجرا به چه ورودی‌هایی نیاز دارد.

منبع تأمین Inputها در Formula Type تعریف نمی‌شود.

مثال:

```text
Formula Type:

Round Bar Weight


Required Inputs:

Diameter
Length
Quantity
```

---

# 7. Business Invariants

قوانین زیر همیشه باید برقرار باشند.

---

## INV-CALC-001

فرمول‌های مهندسی فقط داخل Calculation Engine قرار دارند.

---

## INV-CALC-002

Pricing هرگز فرمول مهندسی را اجرا نمی‌کند.

---

## INV-CALC-003

Catalog هرگز مالک فرمول‌های مهندسی نیست.

---

## INV-CALC-004

در هر اجرای Calculation یک Formula Type نهایی انتخاب می‌شود.

Formula Type انتخاب‌شده می‌تواند شامل چند Operation یا Formula Step باشد.

---

## INV-CALC-005

اگر هیچ Formula Type اختصاص داده نشده باشد،

Default Formula باید اجرا شود.

---

## INV-CALC-007

خروجی محاسبه هرگز نباید حاوی اطلاعات قیمت باشد.

---

## INV-CALC-008

Formula Type ها قابل استفاده مجدد هستند.

چندین Product می‌توانند به یک Formula Type ارجاع دهند.

---

## INV-CALC-009

اجرای فرمول باید تعیین‌پذیر (Deterministic) باشد.

ورودی‌های یکسان باید همیشه خروجی‌های یکسان تولید کنند.

---

## INV-CALC-010

Calculation Engine تنها منبع حقیقت برای اجرای Formulaهای مهندسی است.

---

## INV-CALC-011

Formula Resolution باید همیشه طبق اولویت زیر انجام شود:

```text
Product
→ Category
→ Parent Category
→ Root Category
```

Formula تعریف‌شده در سطح پایین‌تر دارای اولویت بالاتر است و Formula سطح بالاتر را Override می‌کند.

---

## INV-CALC-012

Formula Type بدون تعریف کامل ساختار محاسباتی قابل اجرا نیست.

هر Formula Type باید:

- مراحل محاسبه مشخص داشته باشد.
- Operationهای بین مراحل مشخص باشد.
- Inputهای موردنیاز خود را مشخص کند.

---

# 8. Out of Scope

موارد زیر صراحتاً خارج از مسئولیت Calculation Engine هستند.

---

## Product Management

ایجاد Products.

بروزرسانی Products.

تعریف Specification.

این مسئولیت‌ها متعلق به Catalog است.

---

## Pricing Management

محاسبه مبلغ نهایی.

تعیین Base Price.

اعمال VAT.

مدیریت Price Tier.

این مسئولیت‌ها متعلق به Pricing است.

---

## Unit Conversion Management

تعریف ضرایب تبدیل واحد.

تبدیل Order Unit به Registration Unit.

این اطلاعات متعلق به Catalog است.

(Calculation Engine فقط از مقادیر نهایی تبدیل‌شده استفاده می‌کند).

---

## Order Management

ایجاد سفارش.

مدیریت سبد خرید.

این مسئولیت‌ها متعلق به Order است.

---

# 9. Consistency Validation

این سند فقط از حقایق تأییدشده کسب‌وکار و تصمیمات قفل‌شده تولید شده است.

هیچ قانون جدیدی استنتاج نشده است.

هیچ طراحی نرم‌افزاری در این سند دخالت داده نشده است.

این سند مبنای ورودی برای سند بعدی خواهد بود:

**Calculation Business Facts v1.0**

---

# Document Status

Version: 1.0

Status: Completed

Next Document:

Calculation-Business-Facts.md