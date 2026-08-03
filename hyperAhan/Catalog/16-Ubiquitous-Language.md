# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 16-Ubiquitous-Language.md

Bounded Context: Catalog

---

# 1. Purpose

این سند زبان مشترک (Ubiquitous Language) ماژول Catalog را تعریف می‌کند.

تمام توسعه‌دهندگان، تحلیلگران، معماران و ذی‌نفعان کسب‌وکار باید از این واژگان با همین معانی استفاده کنند.

هیچ واژه‌ای نباید با دو معنی متفاوت در Catalog استفاده شود.

این سند مرجع رسمی اصطلاحات دامنه Catalog است.

---

# 2. Product

## Definition

Product یک کالای قابل معرفی در کاتالوگ است.

هر Product نماینده یک کالای مشخص از یک کارخانه مشخص است.

Product دارای هویت مستقل است.

---

## Examples

میلگرد ۱۴ ذوب‌آهن اصفهان

میلگرد ۱۴ ابرکوه

ورق ST37 کارخانه فولاد مبارکه

ورق ST37 کارخانه اکسین

پروفیل 40×40 کارخانه جهان پروفیل

---

## Business Meaning

اگر کارخانه تغییر کند،

Product جدید ایجاد می‌شود.

اگر Specificationهای اصلی تغییر کنند،

Product جدید ایجاد می‌شود.

---

Product مالک موارد زیر است.

- اطلاعات پایه
- Specification Values
- Order Units
- Sale Mode
- Registration Unit

---

Product مالک موارد زیر نیست.

- قیمت
- موجودی
- سفارش
- انبار

---

# 3. Category

## Definition

Category ساختار دسته‌بندی محصولات را مشخص می‌کند.

Category برای سازماندهی Catalog استفاده می‌شود.

Category مشخص می‌کند که یک Product باید از چه Specification Template استفاده کند.

---

## Examples

میلگرد

ورق

پروفیل

سپری

قالب بتن

اتصالات گاز

زانو جوشی

---

Category خودش محصول نیست.

---

# 4. Specification Template

## Definition

Specification Template ساختار مشخصات فنی یک گروه محصول را تعریف می‌کند.

Template تعیین می‌کند چه مشخصاتی برای محصولات آن گروه مجاز هستند.

Template مقدار هیچ Productی را نگهداری نمی‌کند.

---

## Example

Template ورق

↓

ضخامت

عرض

طول

حالت

وزن

محل بارگیری

---

Template میلگرد

↓

سایز

استاندارد

طول

وزن

محل بارگیری

---

# 5. Specification Definition

## Definition

Specification Definition تعریف یک مشخصه قابل استفاده در Template است.

این موجودیت فقط ساختار را تعریف می‌کند.

---

## Examples

Thickness

Length

Width

Grade

Weight

Loading Location

Delivery Location

Analysis

Package Count

---

Specification Definition مقدار ندارد.

---

# 6. Specification Value

## Definition

Specification Value مقدار واقعی یک Specification برای یک Product است.

---

## Example

Thickness

↓

10 mm

---

Grade

↓

ST37

---

Loading Location

↓

کارخانه اصفهان

---

هر Product مجموعه‌ای از Specification Valueها را نگهداری می‌کند.

---

# 7. Factory

## Definition

Factory کارخانه تولیدکننده Product است.

Factory بخشی از هویت Product محسوب می‌شود.

---

## Examples

ذوب‌آهن اصفهان

فولاد مبارکه

ابرکوه

کویر کاشان

ظفر بناب

---

Factory تولیدکننده است.

Factory محل بارگیری نیست.

Factory مالک Product نیست.

---

# 8. Registration Unit

## Definition

Registration Unit واحد مرجع Product است.

تمام محاسبات تبدیل بر اساس این واحد انجام می‌شوند.

---

## Examples

Kilogram

Piece

Package

---

Registration Unit توسط Catalog تعیین می‌شود.

---

# 9. Order Unit

## Definition

Order Unit واحدی است که مشتری می‌تواند با آن سفارش ثبت کند.

---

## Examples

کیلوگرم

شاخه

شیت

بندیل

بسته

نیسان

خاور

تریلی

---

یک Product می‌تواند چندین Order Unit داشته باشد.

---

# 10. Conversion Factor

## Definition

Conversion Factor نسبت تبدیل یک Order Unit به Registration Unit است.

---

## Examples

تن

↓

1000 کیلوگرم

---

نیسان

↓

9000 کیلوگرم

---

شاخه میلگرد

↓

18.95 کیلوگرم

---

Catalog فقط این نسبت را نگهداری می‌کند.

محاسبه قیمت بر عهده Pricing است.

---

# 11. Sale Mode

## Definition

Sale Mode نحوه انجام فرآیند فروش Product را مشخص می‌کند.

---

## Values

FullyOnline

تمام فرآیند خرید آنلاین انجام می‌شود.

---

SemiCustom

بخشی از فرآیند نیازمند بررسی کارشناس است.

---

FullyCustom

تمام فرآیند فروش توسط کارشناس انجام می‌شود.

---

Sale Mode درباره قیمت نیست.

Sale Mode درباره موجودی نیست.

Sale Mode فقط فرآیند فروش را مشخص می‌کند.

---

# 12. Out Of Stock Display Policy

## Definition

این مفهوم نحوه نمایش Product در زمان ناموجود بودن را مشخص می‌کند.

---

## Values

Hidden

نمایش داده نشود.

---

TaggedNoAction

نمایش داده شود اما امکان اقدام وجود نداشته باشد.

---

ContactButton

نمایش داده شود و امکان تماس با کارشناس فراهم باشد.

---

این مفهوم مستقل از Sale Mode است.

---

# 13. Loading Location

## Definition

Loading Location محل بارگیری یا محل تحویل محصول است.

این مفهوم یکی از Specificationهای Product محسوب می‌شود.

---

## Examples

کارخانه اصفهان

بنگاه تهران

کارخانه یزد

بنگاه اصفهان

---

Loading Location برای تمام گروه‌های محصول الزامی نیست.

وجود یا عدم وجود آن توسط Specification Template تعیین می‌شود.

Loading Location موجودیت مستقل نیست.

---

# 14. Required Specification

## Definition

Specificationای که Product بدون مقدار آن معتبر نیست.

وجود مقدار آن هنگام ایجاد Product الزامی است.

---

# 15. Optional Specification

## Definition

Specificationای که مقدار آن می‌تواند خالی باشد.

---

# 16. Aggregate Root

## Definition

تنها نقطه مجاز برای تغییر وضعیت Aggregate.

تمام تغییرات Domain باید از طریق Aggregate Root انجام شوند.

---

# 17. Entity

## Definition

مفهومی که دارای هویت مستقل است.

بر اساس شناسه خود شناسایی می‌شود.

---

## Catalog Entities

Product

Category

Factory

Specification Template

Specification Definition

Order Unit

---

# 18. Value Object

## Definition

مفهومی که هویت مستقل ندارد.

تنها با مقدار خود شناخته می‌شود.

---

## Catalog Value Objects

Registration Unit

Sale Mode

Conversion Factor

Out OfStockDisplayPolicy

Specification Value

---

# 19. Business Identity

در Catalog هویت تجاری Product از ترکیب اطلاعات دامنه شکل می‌گیرد.

تغییر Factory باعث ایجاد Product جدید می‌شود.

تغییر Category باعث تغییر هویت Product می‌شود.

تغییر Specificationهای هویتی باعث ایجاد Product جدید می‌شود.

---

# 20. Catalog

Catalog مسئول نگهداری اطلاعات پایه محصولات است.

Catalog مسئول قیمت نیست.

Catalog مسئول موجودی نیست.

Catalog مسئول سفارش نیست.

Catalog مسئول پرداخت نیست.

Catalog مسئول حمل نیست.

---

# 21. Summary

این سند زبان رسمی دامنه Catalog را تعریف می‌کند.

تمام اصطلاحات استفاده‌شده در مستندات، کد، تست‌ها و گفتگوهای فنی باید مطابق تعاریف این سند باشند.

استفاده از یک واژه با معنایی متفاوت از این سند مجاز نیست.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

17-Domain-Lifecycle.md