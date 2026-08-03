# Pricing Domain Model

Version: 1.0

Status: Confirmed

Bounded Context: Pricing

Document Type: Domain Model

Depends On

Pricing Business Facts v1.0

---

# 1. Purpose

این سند مدل دامنه (Domain Model) ماژول Pricing را تعریف می‌کند.

تمام اجزای این سند مستقیماً از Business Facts استخراج شده‌اند.

این سند مشخص می‌کند:

- Aggregate ها
- Entity ها
- Value Object ها
- Aggregate Boundary
- Domain Invariants
- روابط بین اجزا

این سند هیچ قانون کسب‌وکار جدیدی معرفی نمی‌کند.

---

# 2. Aggregate Boundary

Pricing دارای یک Aggregate Root است.

## Price

تمام عملیات تغییر قیمت باید از طریق این Aggregate انجام شود.

هیچ Entity خارج از Aggregate مجاز به تغییر اطلاعات قیمت نیست.

---

# Aggregate Responsibilities

Aggregate مسئول حفظ سازگاری اطلاعات قیمت است.

از جمله

- نگهداری Base Price
- مدیریت چرخه عمر Price
- مدیریت Price Tier ها
- اعتبارسنجی Threshold ها
- اعتبارسنجی Registration Unit
- مدیریت تاریخچه تغییرات قیمت
- جلوگیری از وجود بیش از یک Price فعال

---

# Aggregate Root

Price

Price تنها نقطه ورود به دامنه Pricing است.

تمام تغییرات باید از طریق رفتارهای Price انجام شوند.

---

# 3. Entity Model

## Price

Aggregate Root

---

Responsibilities

- نگهداری اطلاعات قیمت
- نگهداری وضعیت قیمت
- نگهداری تاریخ اعتبار
- مدیریت Tier های قیمت
- مدیریت تاریخچه تغییرات
- مدیریت Obsolete شدن قیمت
- مدیریت Expiration
- اعتبارسنجی قوانین دامنه

---

Identity

هر Price دارای شناسه یکتا است.

Price Identity مستقل از Product Identity است.

---

Lifecycle

Draft وجود ندارد.

Price پس از ثبت مستقیماً وارد چرخه عمر قیمت می‌شود.

چرخه عمر توسط قوانین کسب‌وکار کنترل می‌شود.

---

Relationship

هر Price دقیقاً متعلق به یک Product است.

هر Product در هر لحظه فقط یک Price فعال دارد.

---

# 4. Child Entity

## Price Tier

Price Tier بخشی از Aggregate Price است.

خارج از Aggregate وجود مستقل ندارد.

---

Responsibilities

- نگهداری Threshold
- نگهداری Base Price مربوط به Threshold
- تعیین قیمت مناسب هنگام محاسبه

---

Identity

Price Tier فقط داخل Price شناسه دارد.

خارج از Aggregate قابل ارجاع نیست.

---

Relationship

هر Price

↓

دارای

↓

صفر یا چند Price Tier

وجود Tier اختیاری است.

اگر Tier تعریف نشود،

Price فقط دارای یک Base Price خواهد بود.
---

# 5. Value Objects

تمام Value Object های این بخش بدون هویت مستقل هستند.

هیچ‌یک خارج از Aggregate زندگی نمی‌کنند.

---

## Base Price

### Purpose

نگهداری قیمت پایه محصول.

---

### Description

Base Price مبلغی است که مدیر سیستم برای Registration Unit محصول ثبت می‌کند.

این مقدار همیشه متعلق به Registration Unit است.

هیچ استثنایی وجود ندارد.

---

### Business Rules

همیشه بر اساس Registration Unit ثبت می‌شود.

نمونه

میلگرد

Registration Unit

Kilogram

↓

Base Price

105,910 Toman

---

پیچ

Registration Unit

Piece

↓

Base Price

12,000 Toman

---

Base Price هرگز بر اساس

- Branch
- Bundle
- Capacity

ثبت نمی‌شود.

این واحدها فقط واحد سفارش هستند.

---

## Threshold

### Purpose

تعریف مرز ورود به یک Tier قیمت.

---

### Description

Threshold مقدار مشخصی است که مدیر سیستم هنگام تعریف قوانین قیمت وارد می‌کند.

Threshold از Product استخراج نمی‌شود.

Threshold از Registration Unit استخراج نمی‌شود.

Threshold توسط مدیر تعیین می‌شود.

---

### Examples

143 Branch

1000 Kilogram

25 Bundle

---

### Business Rules

Threshold همیشه متعلق به یک Price Tier است.

Threshold خارج از Tier معنایی ندارد.

---

## Threshold Unit

### Purpose

واحد اندازه‌گیری Threshold.

---

### Description

هنگام تعریف Tier،

مدیر واحد Threshold را نیز انتخاب می‌کند.

نمونه

Threshold

143

Threshold Unit

Branch

---

نمونه

Threshold

1000

Threshold Unit

Kilogram

---

Threshold Unit یکی از Order Unit های مجاز همان Product خواهد بود.

---

## Validity Period

### Purpose

نگهداری بازه اعتبار Price.

---

### Description

هر Price می‌تواند تاریخ شروع و پایان اعتبار داشته باشد.

تاریخ پایان اختیاری است.

اگر مدیر تاریخ پایان وارد نکند،

سیستم به صورت خودکار

یک هفته

اعتبار در نظر می‌گیرد.

---

### Business Rules

اگر اعتبار پایان یابد

↓

Price منقضی می‌شود.

↓

Product حذف نمی‌شود.

↓

وضعیت فروش محصول

Contact Us

خواهد شد.

---

## VAT Configuration Snapshot

### Purpose

تعیین نحوه اعمال VAT هنگام محاسبه قیمت.

---

### Description

Pricing باید بداند

آیا هنگام محاسبه

VAT

اعمال شود

یا خیر.

---

### Business Modes

Mode A

Base Price

Without VAT

↓

Pricing

Calculates VAT

---

Mode B

Base Price

Already Includes VAT

↓

Pricing

Does Not Calculate VAT Again

---

این Value Object فقط وضعیت مورد استفاده در محاسبه را نگهداری می‌کند.

قانون فعال بودن VAT توسط تنظیمات سیستم تعیین می‌شود.

---

# 6. Relationships

Price

↓

Belongs To

↓

Exactly One Product

---

Price

↓

Contains

↓

Zero Or Many Price Tiers

---

Price Tier

↓

Contains

↓

One Threshold

---

Price Tier

↓

Contains

↓

One Base Price

---

Price

↓

Contains

↓

One Validity Period

---

Price

↓

Uses

↓

Registration Unit

(Reference From Catalog)

---

Price

↓

Uses

↓

Order Units

(Reference From Catalog)

---

Price

↓

Uses

↓

Conversion Factors

(Reference From Catalog)

---

Price

↓

Consumes

↓

Calculation Result

(Provided By Calculation Module)

---

# 7. Aggregate Invariants

تمام قوانین زیر باید همیشه برقرار باشند.

---

## INV-001

هر Product در هر لحظه فقط یک Price فعال دارد.

---

## INV-002

ثبت Price جدید،

Price فعال قبلی را به صورت خودکار غیرفعال می‌کند.

---

## INV-003

Price هرگز بدون Product وجود ندارد.

---

## INV-004

Base Price همیشه متعلق به Registration Unit است.

---

## INV-005

Threshold همیشه متعلق به یک Tier است.

---

## INV-006

Threshold توسط مدیر تعریف می‌شود.

---

## INV-007

Price Tier ها هرگز سفارش را تقسیم نمی‌کنند.

پس از انتخاب Tier،

همان Tier روی کل سفارش اعمال می‌شود.

---

## INV-008

Pricing هرگز وزن را محاسبه نمی‌کند.

---

## INV-009

Pricing هرگز فرمول‌های مهندسی را نگهداری نمی‌کند.

---

## INV-010

Pricing فقط نتیجه محاسبات مهندسی را مصرف می‌کند.

---

## INV-011

Price حذف فیزیکی نمی‌شود.

---

## INV-012

Price History همیشه حفظ می‌شود.

---

## INV-013

Obsolete Price باید برای Audit باقی بماند.

---

## INV-014

Obsolete Price نباید در

- Reports
- Charts
- Customer History

نمایش داده شود.

---

## INV-015

Expired Price و Obsolete Price دو مفهوم متفاوت هستند.

---

## INV-016

تغییر Order Unit هرگز Base Price را تغییر نمی‌دهد.

---

## INV-017

Final Price داخل Aggregate ذخیره نمی‌شود.

Final Price فقط هنگام محاسبه تولید می‌شود.
---

# 8. Domain Behaviors

این بخش رفتارهای دامنه را تعریف می‌کند.

این‌ها عملیات Application Layer نیستند.

این‌ها رفتارهای خود Aggregate هستند.

---

## Register Price

### Purpose

ایجاد یک Price جدید برای یک Product.

---

### Preconditions

Product باید در Catalog وجود داشته باشد.

Registration Unit باید از Catalog قابل دریافت باشد.

Base Price باید بر اساس Registration Unit وارد شود.

---

### Behavior

Aggregate یک Price جدید ایجاد می‌کند.

اگر Product قبلاً Price فعال داشته باشد،

آن Price به صورت خودکار از حالت Active خارج می‌شود.

Price جدید به عنوان Active ثبت می‌شود.

Price قبلی وارد Price History می‌شود.

---

### Result

Exactly One Active Price

---

## Add Price Tier

### Purpose

افزودن یک Tier قیمت.

---

### Preconditions

Price باید وجود داشته باشد.

Threshold باید توسط Administrator مشخص شده باشد.

Threshold Unit باید یکی از Order Unit های همان Product باشد.

Base Price Tier باید بر اساس Registration Unit تعریف شود.

---

### Behavior

Tier جدید به Price اضافه می‌شود.

Price مسئول اعتبارسنجی سازگاری Tier ها است.

---

### Result

Price دارای یک Tier جدید خواهد بود.

---

## Replace Active Price

### Purpose

جایگزینی Price فعال.

---

### Behavior

Price جدید Active می‌شود.

Price قبلی به History منتقل می‌شود.

هیچ زمانی نباید دو Price Active وجود داشته باشد.

---

### Result

Exactly One Active Price

---

## Expire Price

### Purpose

پایان اعتبار یک Price.

---

### Trigger

رسیدن Expiration Date.

---

### Behavior

Price دیگر قابل استفاده برای قیمت‌گذاری نیست.

Product همچنان در Catalog باقی می‌ماند.

وضعیت فروش Product به

Contact Us

تغییر می‌کند.

---

### Result

Price

↓

Expired

---

Product

↓

Visible

---

Sales Status

↓

Contact Us

---

## Mark Price As Obsolete

### Purpose

ثبت اینکه یک Price به اشتباه ایجاد شده است.

---

### Behavior

Price از تاریخچه قابل نمایش مشتری حذف می‌شود.

Price از گزارش‌ها حذف می‌شود.

Price از نمودارها حذف می‌شود.

Price همچنان برای Audit در سیستم باقی می‌ماند.

---

### Result

Price

↓

Obsolete

---

## Resolve Applicable Tier

### Purpose

انتخاب Tier مناسب برای سفارش.

---

### Input

Calculated Quantity

+

Threshold Rules

---

### Behavior

Aggregate بررسی می‌کند

کدام Tier

با مقدار سفارش مطابقت دارد.

پس از انتخاب Tier

همان Tier

برای کل سفارش استفاده می‌شود.

Aggregate هرگز سفارش را بین چند Tier تقسیم نمی‌کند.

---

### Result

Selected Base Price

---

## Resolve Base Price

### Purpose

انتخاب Base Price مناسب برای محاسبه.

---

### Behavior

اگر Tier وجود نداشته باشد

↓

Default Base Price

استفاده می‌شود.

اگر Tier وجود داشته باشد

↓

Tier مربوطه انتخاب می‌شود.

---

### Result

Resolved Base Price

---

## Calculate Final Price

### Purpose

محاسبه مبلغ نهایی سفارش.

---

### Input

Resolved Base Price

+

Calculated Quantity

+

VAT Configuration

---

### Behavior

Aggregate

Base Price

را در

Calculated Quantity

ضرب می‌کند.

در صورت فعال بودن VAT

مالیات نیز اعمال می‌شود.

Aggregate هیچ فرمول مهندسی اجرا نمی‌کند.

Aggregate فقط از مقدار محاسبه‌شده استفاده می‌کند.

---

### Result

Final Price

---

# 9. Domain Events

این بخش فقط رویدادهای دامنه را تعریف می‌کند.

نحوه انتشار Event ها در این سند بررسی نمی‌شود.

---

## PriceRegistered

زمانی منتشر می‌شود که Price جدید ایجاد شود.

---

## ActivePriceReplaced

زمانی منتشر می‌شود که Price فعال جایگزین شود.

---

## PriceExpired

زمانی منتشر می‌شود که اعتبار Price پایان یابد.

---

## PriceMarkedAsObsolete

زمانی منتشر می‌شود که Price به عنوان اشتباه علامت‌گذاری شود.

---

## PriceTierAdded

زمانی منتشر می‌شود که Tier جدیدی اضافه شود.

---

## PriceTierRemoved

زمانی منتشر می‌شود که Tier حذف شود.

---

## VATCalculationModeChanged

زمانی منتشر می‌شود که تنظیمات محاسبه VAT تغییر کند.

---

# 10. External Dependencies

Pricing برای انجام مسئولیت‌های خود به اطلاعات زیر وابسته است.

این وابستگی‌ها مالکیت داده ایجاد نمی‌کنند.

---

## Catalog

Pricing فقط اطلاعات زیر را از Catalog مصرف می‌کند.

- Product Identifier
- Registration Unit
- Order Units
- Conversion Factors

---

## Calculation Module

Pricing فقط نتیجه محاسبات را مصرف می‌کند.

نمونه

- Weight
- Area
- Length
- Volume

Pricing هیچ اطلاعی از فرمول‌های مهندسی ندارد.

---

## System Configuration

Pricing وضعیت تنظیمات VAT را از تنظیمات سیستم دریافت می‌کند.

این تنظیمات متعلق به Pricing نیستند.

---

# 11. Aggregate Boundary Validation

برای جلوگیری از نشت مسئولیت‌ها، مرز Aggregate بررسی می‌شود.

---

## داخل Aggregate

- Base Price
- Active Price
- Price History
- Price Tier
- Threshold
- Validity Period
- Price Lifecycle
- Obsolete State

---

## خارج از Aggregate

- Product
- Category
- Factory
- Specification
- Registration Unit Definition
- Order Unit Definition
- Conversion Definition
- Weight Formula
- Engineering Formula
- Shopping Cart
- Order
- Inventory

---

هیچ‌یک از موارد فوق نباید وارد Aggregate شوند.

---

# 12. Consistency Validation

تمام اجزای این مدل مستقیماً از سند

Pricing Business Facts v1.0

استخراج شده‌اند.

هیچ رفتار دامنه‌ای خارج از قواعد کسب‌وکار اضافه نشده است.

هیچ Entity یا Value Object بدون نیاز کسب‌وکار معرفی نشده است.

هیچ مسئولیتی از Catalog، Calculation یا Order به داخل Pricing منتقل نشده است.

این مدل دامنه مبنای مستقیم پیاده‌سازی لایه Domain ماژول Pricing خواهد بود.

---

# Document Status

Version

1.0

Status

Completed

Next Document

Pricing Calculation Rules v1.0