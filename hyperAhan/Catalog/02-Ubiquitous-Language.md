# Catalog Domain Specification

Version: 1.0

Status: Draft (Business Validated)

Document: 02-Ubiquitous-Language.md

Bounded Context: Catalog

Last Updated: 2026-07

---

# 1. Purpose

هدف این سند، تعریف زبان مشترک (Ubiquitous Language) مورد استفاده در Bounded Context مربوط به Catalog است.

تمام اصطلاحات دامنه باید تنها بر اساس این سند استفاده شوند.

هدف از این سند جلوگیری از استفاده همزمان از چند واژه برای یک مفهوم و جلوگیری از تفسیرهای متفاوت در طراحی، پیاده‌سازی، تست و مستندسازی است.

این سند مرجع رسمی نام‌گذاری مفاهیم دامنه Catalog است.

---

# 2. Ubiquitous Language Principles

در Catalog هر مفهوم کسب‌وکاری دقیقاً یک نام رسمی دارد.

هیچ مترادفی نباید در کد، مستندات یا طراحی استفاده شود.

نمونه:

اگر اصطلاح رسمی

Product

است،

استفاده از واژه‌هایی مانند

Item

Commodity

Goods

InventoryItem

Material

برای اشاره به همان مفهوم مجاز نیست.

---

اگر اصطلاح رسمی

Factory

است،

استفاده از

Manufacturer

Producer

Vendor

Supplier

برای اشاره به همان مفهوم مجاز نیست.

هر کدام از این واژه‌ها در صورت نیاز، باید مفهوم مستقلی داشته باشند.

---

# 3. Product Group

تعریف

Product Group مجموعه‌ای از محصولات هم‌خانواده است که از دید کسب‌وکار ویژگی‌های مشترک دارند اما خودشان قابل فروش نیستند.

نمونه‌ها

- میلگرد
- ورق
- پروفیل
- سپری
- قالب بتن
- زانو جوشی
- اتصالات گاز

Product Group صرفاً یک مفهوم تجاری برای سازماندهی محصولات است.

Product Group یک Product نیست.

---

# 4. Category

Category ساختار دسته‌بندی محصولات را تعریف می‌کند.

Category مسئول سازماندهی محصولات است.

Category ساختار سلسله‌مراتبی دارد.

نمونه

ورق

↓

ورق سیاه

↓

ورق سیاه ST37

---

Category مسئول تعریف مشخصات محصول نیست.

Category فقط تعیین می‌کند از کدام Specification Template استفاده می‌شود.

---

# 5. Product

Product یک کالای قابل فروش است.

Product کوچک‌ترین واحد مستقلی است که می‌تواند:

- نمایش داده شود.
- جستجو شود.
- سفارش داده شود.
- قیمت دریافت کند.

هر Product دارای هویت مستقل است.

نمونه

میلگرد ۱۴ ذوب‌آهن

یک Product است.

---

میلگرد ۱۴ ابرکوه

یک Product دیگر است.

---

ورق ST37 ضخامت ۱۰ کارخانه اصفهان

یک Product است.

---

Product نماینده یک خانواده محصول نیست.

---

# 6. Factory

Factory کارخانه تولیدکننده Product است.

Factory بخشی از هویت تجاری Product محسوب می‌شود.

Factory یک Entity مستقل است.

Factory متن آزاد نیست.

نمونه‌ها

- ذوب‌آهن
- ابرکوه
- ظفر بناب
- فولاد مبارکه

---

# 7. Specification

Specification یک ویژگی توصیفی Product است.

Specification ساختار محصول را توصیف می‌کند.

Specification هویت Product را تعریف نمی‌کند.

نمونه‌ها

- ضخامت
- عرض
- طول
- سایز
- آنالیز
- استاندارد
- حالت
- محل بارگیری

---

هر Product مجموعه‌ای از Specificationها را دارد.

اما نوع Specificationها توسط Category تعیین می‌شود.

---

# 8. Specification Template

Specification Template ساختار Specificationهای قابل استفاده برای یک یا چند Category را تعریف می‌کند.

Template متعلق به Product نیست.

Template مستقل از Product است.

Category فقط Template مناسب را انتخاب می‌کند.

---

نمونه

Template ورق

↓

ضخامت

عرض

طول

حالت

---

Template میلگرد

↓

سایز

استاندارد

طول

وزن

---

# 9. Specification Definition

Specification Definition تعریف یک Specification است.

نمونه

ضخامت

---

یا

عرض

---

یا

استاندارد

---

Specification Definition مقدار محصول نیست.

Specification Definition فقط تعریف آن ویژگی است.

---

# 10. Specification Value

Specification Value مقدار واقعی یک Specification برای یک Product است.

نمونه

Specification Definition

ضخامت

↓

Value

۱۰

---

Specification Definition

عرض

↓

Value

۱٫۵ متر

---

Specification Value متعلق به Product است.
---

# 11. Registration Unit

Registration Unit واحد مرجع ثبت Product در Catalog است.

تمام مقادیر کمی Product بر اساس Registration Unit تعریف می‌شوند.

Registration Unit مبنای تمام تبدیل واحدهای سفارش است.

هر Product دقیقاً یک Registration Unit دارد.

وجود بیش از یک Registration Unit برای یک Product مجاز نیست.

---

Registration Unit به معنی واحد خرید نیست.

Registration Unit به معنی واحد نمایش نیز نیست.

Registration Unit فقط واحد مرجع محصول است.

---

نمونه

Product

میلگرد ۱۴ ذوب‌آهن

↓

Registration Unit

کیلوگرم

---

نمونه

Product

قالب بتن

↓

Registration Unit

عدد

---

Registration Unit مالک هیچ قانون سفارش نیست.

حداقل سفارش

حداکثر سفارش

ضریب تبدیل

متعلق به Registration Unit نیستند.

---

# 12. Order Unit

Order Unit یک واحد قابل سفارش برای Product است.

Order Unit بخشی از Product محسوب می‌شود.

Order Unit بدون Product معنا ندارد.

---

هر Product می‌تواند دارای چندین Order Unit باشد.

وجود چند واحد سفارش یکی از نیازهای تأییدشده دامنه است.

نمونه

Registration Unit

کیلوگرم

↓

Order Units

کیلوگرم

شاخه

بندیل

نیسان

---

یا

Registration Unit

عدد

↓

Order Units

عدد

بسته

پالت

---

هر Order Unit دارای قوانین مستقل خود است.

از جمله

- Conversion Factor
- Minimum Order Quantity
- Maximum Order Quantity
- Display Order
- Default Order Unit

---

Order Unit مسئول محاسبه قیمت نیست.

Pricing بر اساس Conversion Factor محاسبات خود را انجام می‌دهد.

---

# 13. Conversion Factor

Conversion Factor ضریب تبدیل یک Order Unit به Registration Unit است.

Conversion Factor متعلق به Order Unit است.

Conversion Factor متعلق به Product نیست.

---

نمونه

Registration Unit

کیلوگرم

---

Order Unit

شاخه

↓

Conversion Factor

۱۴

---

یعنی

هر شاخه

معادل

۱۴ کیلوگرم

است.

---

نمونه

Registration Unit

کیلوگرم

---

Order Unit

نیسان

↓

Conversion Factor

۹۰۰۰

---

یعنی

هر واحد سفارش "نیسان"

معادل

۹۰۰۰ کیلوگرم

است.

---

Conversion Factor یک مقدار ثابت است.

Catalog هیچ فرمولی برای محاسبه آن نگهداری نمی‌کند.

---

# 14. Sale Mode

Sale Mode سیاست فروش Product را مشخص می‌کند.

Sale Mode تعیین می‌کند فرآیند خرید محصول چگونه انجام می‌شود.

Sale Mode درباره قیمت تصمیم نمی‌گیرد.

Sale Mode درباره موجودی تصمیم نمی‌گیرد.

Sale Mode فقط نوع فرآیند فروش را مشخص می‌کند.

---

مقادیر تأییدشده

FullyOnline

↓

تمام مراحل خرید به صورت آنلاین انجام می‌شود.

---

SemiCustom

↓

بخشی از فرآیند آنلاین و بخشی نیازمند بررسی توسط کارشناسان است.

---

FullyCustom

↓

فرآیند فروش به صورت کاملاً سفارشی انجام می‌شود.

---

Sale Mode رفتار رابط کاربری را نیز تحت تأثیر قرار می‌دهد.

اما اجرای آن خارج از مسئولیت Catalog است.

---

# 15. Out Of Stock Display Policy

Out Of Stock Display Policy تعیین می‌کند اگر Inventory محصول را ناموجود اعلام کند، رابط کاربری چه رفتاری داشته باشد.

Catalog وضعیت موجودی را تعیین نمی‌کند.

Catalog فقط سیاست نمایش را نگهداری می‌کند.

---

مقادیر تأییدشده

Hidden

↓

محصول نمایش داده نشود.

---

TaggedNoAction

↓

محصول نمایش داده شود اما امکان اقدام برای خرید وجود نداشته باشد.

---

ContactButton

↓

محصول نمایش داده شود و امکان تماس با کارشناسان فراهم باشد.

---

این مفهوم مستقل از Sale Mode است.

این دو مفهوم نباید با یکدیگر ادغام شوند.

این تصمیم یکی از تصمیمات قفل‌شده دامنه است.

---

# 16. Loading Location

Loading Location محل بارگیری یا محل تحویل محصول را توصیف می‌کند.

در نسخه فعلی دامنه، این دو اصطلاح یک مفهوم واحد در نظر گرفته می‌شوند.

---

نمونه‌ها

- کارخانه اصفهان
- کارخانه یزد
- کارخانه اهواز
- بنگاه تهران
- بنگاه اصفهان

---

Loading Location برای تمام گروه‌های محصول وجود ندارد.

نمونه

میلگرد

دارای Loading Location است.

---

ورق

دارای Loading Location است.

---

سپری

دارای Loading Location است.

---

قالب بتن

ممکن است این مفهوم را نداشته باشد.

---

زانو جوشی

ممکن است این مفهوم را نداشته باشد.

---

اتصالات گاز

ممکن است این مفهوم را نداشته باشند.

---

به همین دلیل

Loading Location

یک Property ثابت روی Product نیست.

وجود آن توسط Specification Template تعیین می‌شود.

---

# 17. Display Name

Display Name نام قابل نمایش Product برای کاربران است.

Display Name همان نامی است که در صفحات سایت، نتایج جستجو و صفحات محصول نمایش داده می‌شود.

Display Name می‌تواند شامل اصطلاحات متداول بازار باشد.

نمونه

- میلگرد ۱۴ ذوب‌آهن
- ورق سیاه ST37 ضخامت ۱۰
- پروفیل صنعتی ۱۴۰×۱۴۰

---

اصطلاحاتی مانند

- سبک
- سنگین
- نیمه

در صورت نیاز، بخشی از Display Name محسوب می‌شوند.

تا این لحظه هیچ حقیقت کسب‌وکاری مبنی بر قیمت‌ساز بودن این اصطلاحات تأیید نشده است.

بنابراین این مفاهیم به عنوان Specification مستقل مدل نشده‌اند.

---

# 18. Searchable Information

Catalog اطلاعات لازم برای جستجوی Product را تولید می‌کند.

بر اساس تصمیمات تأییدشده، کاربر باید بتواند محصولات را بر اساس اطلاعات زیر پیدا کند.

- نام محصول
- گروه محصول
- Category
- Factory
- Specificationهای قابل جستجو

---

نمونه

کاربر

میلگرد

را جستجو می‌کند.

تمام Productهای مرتبط باید قابل یافتن باشند.

---

کاربر

میلگرد ۱۴

را جستجو می‌کند.

تمام Productهای دارای سایز ۱۴ باید قابل بازیابی باشند.

---

کاربر

ذوب‌آهن

را جستجو می‌کند.

تمام Productهای متعلق به آن Factory باید نمایش داده شوند.

---

روش پیاده‌سازی Search خارج از این سند است.

این سند فقط اصطلاحات دامنه را تعریف می‌کند.

---

# End of Part 2

---

# 19. Business Term Consistency

تمام اصطلاحات این سند بخشی از زبان رسمی دامنه Catalog هستند.

تمام اعضای تیم توسعه، تحلیل، تست و مستندسازی باید از همین اصطلاحات استفاده کنند.

استفاده از اصطلاحات جایگزین برای یک مفهوم مجاز نیست.

نمونه

صحیح

Product

نادرست

Item

Material

Goods

Inventory Item

Commodity

---

صحیح

Factory

نادرست

Manufacturer

Vendor

Producer

Supplier

---

صحیح

Registration Unit

نادرست

Base Unit

Main Unit

Primary Unit

---

صحیح

Order Unit

نادرست

Purchase Unit

Selling Unit

Order Measure

---

صحیح

Specification

نادرست

Attribute

Property

Feature

Field

---

# 20. Naming Rules

نام‌گذاری مفاهیم دامنه باید مطابق قواعد زیر باشد.

---

## Entity

نام Entityها باید اسم باشند.

نمونه

Product

Category

Factory

---

## Value Object

نام Value Objectها باید بیان‌کننده یک مفهوم دامنه باشند.

نمونه

Registration Unit

Order Unit

Conversion Factor

Sale Mode

Out Of Stock Display Policy

---

## Enumeration

Enumerationها باید بیانگر مجموعه‌ای بسته از مفاهیم کسب‌وکاری باشند.

نمونه

SaleMode

FullyOnline

SemiCustom

FullyCustom

---

OutOfStockDisplayPolicy

Hidden

TaggedNoAction

ContactButton

---

# 21. Forbidden Terms

اصطلاحات زیر در نسخه فعلی Catalog نباید وارد مدل دامنه شوند، زیرا هنوز توسط کسب‌وکار تأیید نشده‌اند.

- Variant
- SKU
- Brand
- Manufacturer
- Supplier
- Inventory Item
- Warehouse Product
- Material Code
- Commercial Product

در صورت نیاز به اضافه شدن هر یک از این مفاهیم، ابتدا باید تحلیل دامنه انجام شود و شواهد کسب‌وکاری برای آن ارائه گردد.

---

# 22. Derived Terms

برخی اصطلاحات در گفتگوهای تحلیل استفاده شده‌اند اما اصطلاح رسمی دامنه نیستند.

نمونه

---

محصول مادر

اصطلاح رسمی نیست.

در مدل دامنه هیچ مفهومی با این نام وجود ندارد.

---

محصول فرزند

اصطلاح رسمی نیست.

---

نسخه کارخانه

اصطلاح رسمی نیست.

Factory بخشی از Product است و Product مستقل ایجاد می‌شود.

---

قیمت پایه

در Catalog وجود ندارد.

این مفهوم متعلق به Pricing است.

---

واحد فروش

اصطلاح رسمی دامنه نیست.

اصطلاح رسمی

Order Unit

است.

---

واحد ثبت

اصطلاح رسمی

Registration Unit

است.

---

# 23. Language Evolution

زبان دامنه ثابت نیست.

در صورت کشف حقیقت جدید کسب‌وکار، امکان اضافه شدن اصطلاحات جدید وجود دارد.

اما حذف یا تغییر اصطلاحات موجود تنها در صورت وجود دلیل کسب‌وکاری معتبر مجاز است.

تغییر صرفاً به دلیل سادگی پیاده‌سازی یا ترجیح توسعه‌دهنده مجاز نیست.

---

# 24. Validation Rule

قبل از اضافه شدن هر اصطلاح جدید به Ubiquitous Language باید موارد زیر بررسی شود.

آیا این مفهوم در کسب‌وکار وجود دارد؟

آیا تعریف مشخصی دارد؟

آیا با مفاهیم فعلی تداخل ندارد؟

آیا مسئولیت مستقلی دارد؟

آیا چند اصطلاح دیگر همین مفهوم را بیان نمی‌کنند؟

در صورت منفی بودن هر یک از موارد فوق، اصطلاح نباید وارد مدل دامنه شود.

---

# 25. Locked Vocabulary

واژگان زیر در نسخه 1.0 Catalog به عنوان واژگان رسمی دامنه قفل می‌شوند.

- Product
- Category
- Product Group
- Factory
- Specification
- Specification Template
- Specification Definition
- Specification Value
- Registration Unit
- Order Unit
- Conversion Factor
- Sale Mode
- Out Of Stock Display Policy
- Loading Location
- Display Name

این واژگان مبنای تمام اسناد بعدی Catalog خواهند بود.

تمام Entityها، Value Objectها، Repositoryها، Serviceها، Aggregateها و Use Caseها باید تنها از این واژگان استفاده کنند.

---

# End of Document

Document Status

Business Validated

Locked Vocabulary Version

1.0

Next Document

03-Business-Facts.md
