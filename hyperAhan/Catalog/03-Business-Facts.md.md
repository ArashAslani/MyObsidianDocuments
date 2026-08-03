# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 03-Business-Facts.md

Bounded Context: Catalog

---

# 1. Purpose

این سند تنها شامل حقایق تأییدشده کسب‌وکار است.

این سند شامل موارد زیر نیست:

- تصمیمات طراحی
- تصمیمات DDD
- پیشنهادهای معماری
- ساختار Aggregate
- Entity
- Value Object
- Repository
- Domain Service

تمام مطالب این سند مستقیماً از تحلیل‌های انجام‌شده با صاحب کسب‌وکار استخراج شده‌اند.

هر Fact یک حقیقت مستقل است.

هیچ Fact نباید بر اساس حدس ثبت شود.

---

# CAT-F-001

Status

Confirmed Fact

Business Statement

Catalog تنها مرجع نگهداری اطلاعات پایه محصولات است.

Evidence

این مسئولیت در تحلیل مرزهای Bounded Context تأیید شده است.

Catalog مالک اطلاعات پایه Product است و سایر ماژول‌ها تنها مصرف‌کننده این اطلاعات هستند.

Impacts

Catalog مالک اطلاعات پایه Product خواهد بود.

Pricing، Inventory و Ordering اجازه مالکیت این اطلاعات را ندارند.

Affected Concepts

Product

Catalog

---

# CAT-F-002

Status

Confirmed Fact

Business Statement

Product کوچک‌ترین واحد مستقل قابل فروش در سیستم است.

Evidence

در تحلیل دامنه مشخص شد هر محصول باید بتواند:

- قیمت مستقل داشته باشد.
- جستجو شود.
- سفارش داده شود.
- نمایش داده شود.

Impacts

Product نباید نماینده یک خانواده محصول باشد.

Affected Concepts

Product

---

# CAT-F-003

Status

Confirmed Fact

Business Statement

اگر کارخانه تولیدکننده تغییر کند، Product نیز تغییر می‌کند.

Evidence

نمونه‌های بررسی‌شده:

میلگرد ۱۴ ذوب‌آهن

میلگرد ۱۴ ابرکوه

دو محصول مستقل محسوب می‌شوند.

Impacts

Factory بخشی از هویت Product است.

Affected Concepts

Product

Factory

---

# CAT-F-004

Status

Confirmed Fact

Business Statement

Category ساختار دسته‌بندی محصولات را نگهداری می‌کند.

Evidence

تحلیل دسته‌بندی سایت‌های مرجع نشان داد ساختار دسته‌بندی حداقل دو و معمولاً سه سطح دارد.

نمونه

ورق

↓

ورق سیاه

↓

ورق سیاه ST37

Impacts

Category باید مستقل از Product نگهداری شود.

Affected Concepts

Category

---

# CAT-F-005

Status

Confirmed Fact

Business Statement

Specificationهای هر گروه محصول با گروه‌های دیگر متفاوت هستند.

Evidence

نمونه‌های بررسی‌شده:

ورق

↓

ضخامت

عرض

طول

حالت

---

میلگرد

↓

سایز

استاندارد

وزن

طول

---

سپری

↓

سایز

طول

وزن

آنالیز

محل بارگیری

---

قالب بتن

↓

Specification متفاوت

---

اتصالات گاز

↓

Specification متفاوت

Impacts

ساختار Specification باید قابل توسعه باشد.

Affected Concepts

Specification

Category

---

# CAT-F-006

Status

Confirmed Fact

Business Statement

همه محصولات دارای Loading Location نیستند.

Evidence

نمونه‌های بررسی‌شده.

ورق

دارد.

---

میلگرد

دارد.

---

سپری

دارد.

---

قالب بتن

ندارد.

---

زانو جوشی

ندارد.

Impacts

Loading Location نباید Property ثابت Product باشد.

Affected Concepts

Specification

Loading Location

---

# CAT-F-007

Status

Confirmed Fact

Business Statement

هر Product دقیقاً یک Registration Unit دارد.

Evidence

Registration Unit مبنای تمام تبدیل واحدها است.

Impacts

وجود بیش از یک Registration Unit برای Product مجاز نیست.

Affected Concepts

Registration Unit

Product

---

# CAT-F-008

Status

Confirmed Fact

Business Statement

هر Product می‌تواند چندین Order Unit داشته باشد.

Evidence

نمونه تأییدشده:

کیلوگرم

شاخه

بندیل

نیسان

Impacts

Product باید از چند واحد سفارش پشتیبانی کند.

Affected Concepts

Order Unit

Product

---

# CAT-F-009

Status

Confirmed Fact

Business Statement

هر Order Unit حداقل و حداکثر مقدار سفارش مستقل خود را دارد.

Evidence

این نیاز در تحلیل فرآیند سفارش تأیید شد.

Impacts

Minimum Order Quantity و Maximum Order Quantity متعلق به Order Unit هستند.

Affected Concepts

Order Unit

---

# CAT-F-010

Status

Confirmed Fact

Business Statement

Conversion Factor مقدار ثابتی است که Order Unit را به Registration Unit تبدیل می‌کند.

Evidence

نمونه

نیسان

↓

۹۰۰۰ کیلوگرم

Impacts

Catalog فرمول محاسبه Conversion Factor را نگهداری نمی‌کند.

Affected Concepts

Conversion Factor

Order Unit

---

# End Of Part 1

# CAT-F-011

Status

Confirmed Fact

Business Statement

هر واحد سفارش دارای حداقل مقدار سفارش مستقل است.

Evidence

در فرآیند خرید توافق شد که محدودیت سفارش برای هر واحد فروش به صورت مستقل قابل تنظیم باشد.

نمونه

کیلوگرم

حداقل ۵۰۰ کیلوگرم

---

شاخه

حداقل ۱۰ شاخه

---

بندیل

حداقل ۱ بندیل

Business Consequence

محدودیت حداقل سفارش وابسته به واحد سفارش است و بین واحدهای مختلف مشترک نیست.

Affected Concepts

Order Unit

---

# CAT-F-012

Status

Confirmed Fact

Business Statement

هر واحد سفارش دارای حداکثر مقدار سفارش مستقل است.

Evidence

کسب‌وکار باید بتواند برای هر واحد فروش سقف سفارش متفاوت تعریف کند.

نمونه

کیلوگرم

حداکثر ۳۰٬۰۰۰ کیلوگرم

---

نیسان

حداکثر ۳ نیسان

Business Consequence

حداکثر سفارش نیز وابسته به واحد سفارش است.

Affected Concepts

Order Unit

---

# CAT-F-013

Status

Confirmed Fact

Business Statement

هر واحد سفارش دارای ضریب تبدیل مستقل به Registration Unit است.

Evidence

نمونه

Registration Unit

کیلوگرم

---

واحد سفارش

نیسان

---

ضریب تبدیل

۹۰۰۰

---

واحد سفارش

شاخه

---

ضریب تبدیل

۱۴

Business Consequence

ضریب تبدیل برای هر واحد سفارش به صورت مستقل نگهداری می‌شود.

Affected Concepts

Order Unit

Registration Unit

---

# CAT-F-014

Status

Confirmed Fact

Business Statement

Sale Mode مستقل از وضعیت موجودی محصول است.

Evidence

در تحلیل دامنه مشخص شد نوع فرآیند فروش و نحوه نمایش محصول در زمان ناموجود بودن دو مفهوم مستقل هستند.

Business Consequence

تغییر یکی نباید باعث تغییر دیگری شود.

Affected Concepts

Sale Mode

Out Of Stock Display Policy

---

# CAT-F-015

Status

Confirmed Fact

Business Statement

محصول ممکن است دارای سیاست نمایش متفاوت در زمان ناموجود بودن باشد.

Evidence

رفتارهای تأیید شده

- عدم نمایش
- نمایش بدون امکان خرید
- نمایش همراه با دکمه تماس

Business Consequence

سیاست نمایش محصول بخشی از اطلاعات پایه Catalog است.

Affected Concepts

Out Of Stock Display Policy

---

# CAT-F-016

Status

Confirmed Fact

Business Statement

کاربر باید بتواند محصولات را بر اساس نام کارخانه جستجو کند.

Evidence

نمونه‌های بررسی شده

ذوب‌آهن

ابرکوه

فولاد مبارکه

Business Consequence

اطلاعات کارخانه باید در داده‌های قابل جستجوی Product وجود داشته باشد.

Affected Concepts

Factory

Product

---

# CAT-F-017

Status

Confirmed Fact

Business Statement

کاربر باید بتواند محصولات را بر اساس نام محصول جستجو کند.

Evidence

نمونه

میلگرد

Business Consequence

نام محصول بخشی از اطلاعات قابل جستجو است.

Affected Concepts

Product

---

# CAT-F-018

Status

Confirmed Fact

Business Statement

کاربر باید بتواند محصولات را بر اساس Specificationهای مهم جستجو کند.

Evidence

نمونه

میلگرد ۱۴

ورق ۱۰

ورق ST52

Business Consequence

برخی Specificationها باید قابلیت جستجو داشته باشند.

اینکه کدام Specificationها قابل جستجو هستند توسط Template مربوط به همان Category تعیین می‌شود.

Affected Concepts

Specification

Specification Template

---

# End Of Part 2

# CAT-F-011

Status

Confirmed Fact

Business Statement

هر Product می‌تواند با چند واحد مختلف قابل سفارش باشد.

Evidence

در تحلیل فرآیند سفارش تأیید شد که کاربر بتواند یک محصول را با واحدهایی مانند:

- کیلوگرم
- شاخه
- بندیل
- نیسان

سفارش دهد.

---

# CAT-F-012

Status

Confirmed Fact

Business Statement

برای هر واحد سفارش می‌توان حداقل مقدار سفارش مستقل تعریف کرد.

Evidence

نمونه‌های بررسی شده:

کیلوگرم

↓

حداقل ۵۰۰ کیلوگرم

---

شاخه

↓

حداقل ۱۰ شاخه

---

بندیل

↓

حداقل ۱ بندیل

---

# CAT-F-013

Status

Confirmed Fact

Business Statement

برای هر واحد سفارش می‌توان حداکثر مقدار سفارش مستقل تعریف کرد.

Evidence

نمونه‌های بررسی شده:

کیلوگرم

↓

حداکثر ۳۰٬۰۰۰ کیلوگرم

---

نیسان

↓

حداکثر ۳ نیسان

---

# CAT-F-014

Status

Confirmed Fact

Business Statement

هر واحد سفارش دارای ضریب تبدیل مستقل نسبت به Registration Unit است.

Evidence

نمونه‌های بررسی شده:

Registration Unit

کیلوگرم

↓

Order Unit

شاخه

↓

۱۴ کیلوگرم

---

Registration Unit

کیلوگرم

↓

Order Unit

نیسان

↓

۹۰۰۰ کیلوگرم

---

# CAT-F-015

Status

Confirmed Fact

Business Statement

ضریب تبدیل یک مقدار ثابت است و به صورت فرمول محاسبه نمی‌شود.

Evidence

در تحلیل دامنه توافق شد که Conversion Factor به صورت مقدار ثابت برای هر واحد سفارش تعریف شود.

---

# CAT-F-016

Status

Confirmed Fact

Business Statement

Sale Mode و سیاست نمایش محصول در زمان ناموجود بودن دو مفهوم مستقل هستند.

Evidence

در تحلیل دامنه توافق شد که نوع فرآیند فروش و نحوه نمایش محصول هنگام ناموجود بودن نباید در یک مفهوم ادغام شوند.

---

# CAT-F-017

Status

Confirmed Fact

Business Statement

کاربر باید بتواند محصولات را بر اساس نام گروه محصول جستجو کند.

Evidence

نمونه‌های بررسی شده:

- میلگرد
- ورق
- سپری
- پروفیل
- قالب بتن

---

# CAT-F-018

Status

Confirmed Fact

Business Statement

کاربر باید بتواند محصولات را بر اساس نام کارخانه جستجو کند.

Evidence

نمونه‌های بررسی شده:

- ذوب‌آهن
- ابرکوه
- فولاد مبارکه

---

# CAT-F-019

Status

Confirmed Fact

Business Statement

کاربر باید بتواند محصولات را بر اساس مشخصات مهم محصول جستجو کند.

Evidence

نمونه‌های بررسی شده:

- میلگرد ۱۴
- ورق ST37
- ورق ST52
- ورق ۱۰ میلی‌متر

---

# CAT-F-020

Status

Confirmed Fact

Business Statement

همه گروه‌های محصول دارای مجموعه Specification یکسان نیستند.

Evidence

در تحلیل دامنه مشخص شد که گروه‌های مختلف محصول دارای مشخصات متفاوت هستند.

نمونه‌ها:

ورق

↓

ضخامت

عرض

طول

حالت

---

میلگرد

↓

سایز

استاندارد

طول

وزن

---

سپری

↓

سایز

طول

وزن

آنالیز

محل بارگیری

---

قالب بتن

↓

مشخصات اختصاصی

---

اتصالات گاز

↓

مشخصات اختصاصی

---

# CAT-F-021

Status

Confirmed Fact

Business Statement

وجود یا عدم وجود برخی Specificationها به نوع محصول بستگی دارد.

Evidence

محل بارگیری برای برخی گروه‌های محصول وجود دارد و برای برخی دیگر وجود ندارد.

نمونه:

- میلگرد
- ورق
- سپری

دارای محل بارگیری هستند.

در مقابل:

- قالب بتن
- زانو جوشی
- اتصالات گاز

ممکن است محل بارگیری نداشته باشند.

---

# CAT-F-022

Status

Confirmed Fact

Business Statement

Catalog تنها مالک اطلاعات پایه محصولات است.

Evidence

قیمت، موجودی، سفارش، پرداخت، حمل و تخفیف در ماژول‌های دیگر مدیریت می‌شوند و جزو مسئولیت Catalog نیستند.

---

# End Of Business Facts

# CAT-F-023

Status

Confirmed Fact

Business Statement

Factory بخشی از هویت تجاری Product است.

Evidence

در تحلیل دامنه تأیید شد که:

- میلگرد ۱۴ ذوب‌آهن
- میلگرد ۱۴ ابرکوه

دو Product مستقل هستند، حتی اگر سایر مشخصات آن‌ها یکسان باشد.

---

# CAT-F-024

Status

Confirmed Fact

Business Statement

هر Product دقیقاً متعلق به یک Factory است.

Evidence

تمام نمونه‌های بررسی‌شده در دامنه دارای یک کارخانه تولیدکننده مشخص بودند.

در صورت تغییر Factory، Product جدیدی ایجاد می‌شود و Product قبلی تغییر هویت نمی‌دهد.

---

# CAT-F-025

Status

Confirmed Fact

Business Statement

Product دارای یک Display Name است که برای نمایش به کاربر استفاده می‌شود.

Evidence

Display Name همان عنوانی است که در لیست محصولات، صفحه محصول و نتایج جستجو نمایش داده می‌شود.

نمونه‌ها

- میلگرد ۱۴ ذوب‌آهن
- ورق سیاه ST37 ضخامت ۱۰
- پروفیل صنعتی ۱۴۰×۱۴۰

---

# CAT-F-026

Status

Confirmed Fact

Business Statement

Display Name می‌تواند شامل اصطلاحات رایج بازار باشد.

Evidence

در تحلیل دامنه اصطلاحاتی مانند:

- سبک
- سنگین
- نیمه

به عنوان اصطلاحات بازاری شناسایی شدند.

تا این لحظه هیچ حقیقت کسب‌وکاری مبنی بر اثرگذاری مستقیم این اصطلاحات بر قیمت تأیید نشده است.

---

# CAT-F-027

Status

Confirmed Fact

Business Statement

Categoryها دارای ساختار سلسله‌مراتبی هستند.

Evidence

نمونه تأییدشده:

ورق

↓

ورق سیاه

↓

ورق سیاه ST37

---

پروفیل

↓

پروفیل ساختمانی

↓

پروفیل ضخامت ۲

---

# CAT-F-028

Status

Confirmed Fact

Business Statement

یک Specification Template می‌تواند توسط چند Category استفاده شود.

Evidence

در تحلیل دامنه مشخص شد که:

ورق سیاه ST37

و

ورق سیاه ST52

هر دو از ساختار Specification یکسانی استفاده می‌کنند.

همچنین چند Category مشابه می‌توانند از یک Template مشترک استفاده کنند.

---

# CAT-F-029

Status

Confirmed Fact

Business Statement

Product فقط مقادیر Specification را نگهداری می‌کند.

Evidence

ساختار Specificationها توسط Template تعیین می‌شود و Product فقط مقادیر مربوط به همان Template را ذخیره می‌کند.

---

# CAT-F-030

Status

Confirmed Fact

Business Statement

Catalog باید قابلیت توسعه برای گروه‌های محصول جدید را بدون تغییر مدل دامنه فراهم کند.

Evidence

در تحلیل دامنه گروه‌های زیر بررسی شدند:

- میلگرد
- ورق
- پروفیل
- سپری
- قالب بتن
- زانو جوشی
- اتصالات گاز

توافق شد که در آینده امکان اضافه شدن گروه‌های جدید بدون بازطراحی Catalog وجود داشته باشد.

---

# CAT-F-031

Status

Confirmed Fact

Business Statement

Pricing مسئول نگهداری قیمت محصولات است.

Evidence

در تحلیل مرزهای Bounded Context تأیید شد که قیمت جزو مسئولیت Catalog نیست و در ماژول Pricing مدیریت می‌شود.

---

# CAT-F-032

Status

Confirmed Fact

Business Statement

Inventory مسئول نگهداری موجودی محصولات است.

Evidence

در تحلیل مرزهای Bounded Context تأیید شد که Catalog موجودی را نگهداری نمی‌کند و تنها اطلاعات پایه محصول را ارائه می‌دهد.

---

# CAT-F-033

Status

Confirmed Fact

Business Statement

Warehouse بخشی از Catalog نیست.

Evidence

مدیریت انبار، موجودی، رزرو کالا، انتقال کالا و ظرفیت انبار خارج از مسئولیت Catalog قرار دارند.

---

# CAT-F-034

Status

Confirmed Fact

Business Statement

Catalog منبع اصلی اطلاعات مورد استفاده Search است.

Evidence

جستجو از اطلاعات پایه Product شامل:

- نام محصول
- Factory
- Category
- Specificationهای قابل جستجو

استفاده می‌کند.

پیاده‌سازی موتور جستجو خارج از مسئولیت Catalog است.

---

# CAT-F-035

Status

Confirmed Fact

Business Statement

Catalog هیچ دانشی درباره فرآیند سفارش، پرداخت یا ارسال کالا ندارد.

Evidence

در تحلیل مرزهای سیستم توافق شد که این مسئولیت‌ها متعلق به ماژول‌های مستقل هستند و Catalog تنها اطلاعات پایه محصولات را مدیریت می‌کند.

---

# End Of Document

Status

Business Validated

Document Version

1.0