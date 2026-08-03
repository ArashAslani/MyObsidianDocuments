# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 06-Product-Aggregate.md

Bounded Context: Catalog

---

# 1. Purpose

این سند طراحی کامل Product Aggregate را تعریف می‌کند.

هدف این سند تعیین مسئولیت‌های Aggregate، ساختار داخلی، مرز Aggregate، قوانین مالکیت، موجودیت‌های داخلی و Value Objectهای متعلق به Product است.

این سند مرجع اصلی پیاده‌سازی Product Aggregate در Domain Layer است.

---

# 2. Aggregate Root

Aggregate Root

Product

Product تنها نقطه ورود به Aggregate است.

تمام تغییرات اطلاعات داخلی Aggregate باید از طریق Product انجام شوند.

هیچ موجودیت داخلی اجازه تغییر مستقل خارج از Aggregate را ندارد.

---

# 3. Responsibilities

Product Aggregate مسئول نگهداری موارد زیر است.

- اطلاعات پایه محصول
- نام محصول
- کارخانه تولیدکننده
- دسته‌بندی محصول
- واحد ثبت
- واحدهای سفارش
- مقادیر Specification
- سیاست فروش
- سیاست نمایش در زمان ناموجود بودن

---

Product Aggregate مسئول موارد زیر نیست.

- قیمت
- موجودی
- رزرو موجودی
- انبار
- تخفیف
- حمل
- سفارش
- پرداخت

---

# 4. Aggregate Composition

Product Aggregate از اجزای زیر تشکیل شده است.

Aggregate Root

Product

↓

Child Entity

Order Unit

↓

Owned Value Objects

Registration Unit

Sale Mode

Out Of Stock Display Policy

Specification Values

↓

Referenced Entities

Category

Factory

Specification Template

---

# 5. Product Identity

هر Product دارای یک شناسه یکتا است.

شناسه Product در طول عمر آن تغییر نمی‌کند.

شناسه بخشی از هویت Aggregate است.

---

# 6. Product State

Product دارای اطلاعات پایه زیر است.

Identity

Display Name

Category Reference

Factory Reference

Registration Unit

Sale Mode

Out Of Stock Display Policy

Specification Values

Order Units

---

تمام این اطلاعات متعلق به Product هستند.

---

# 7. Display Name

Display Name نام قابل نمایش محصول است.

Display Name برای نمایش در سایت، جستجو و صفحات محصول استفاده می‌شود.

Display Name می‌تواند شامل اصطلاحات رایج بازار باشد.

نمونه

میلگرد ۱۴ ذوب‌آهن

ورق سیاه ST37 ضخامت ۱۰

سپری ۸۰

---

Display Name شناسه محصول نیست.

---

# 8. Category Reference

هر Product دقیقاً به یک Category تعلق دارد.

Category خارج از Aggregate قرار دارد.

Product فقط شناسه Category را نگهداری می‌کند.

Product اجازه تغییر ساختار Category را ندارد.

---

# 9. Factory Reference

هر Product دقیقاً به یک Factory تعلق دارد.

Factory خارج از Aggregate قرار دارد.

Product فقط شناسه Factory را نگهداری می‌کند.

Factory بخشی از هویت تجاری Product محسوب می‌شود.

در صورت تغییر Factory، Product جدید ایجاد می‌شود.

---

# 10. Registration Unit

هر Product دقیقاً یک Registration Unit دارد.

Registration Unit واحد مرجع Product است.

تمام Order Unitها نسبت به Registration Unit تعریف می‌شوند.

Registration Unit در طول عمر Product به ندرت تغییر می‌کند.

---

نمونه

کیلوگرم

عدد

بسته

---

# 11. Order Units

هر Product دارای یک یا چند Order Unit است.

Order Unit موجودیت داخلی Product Aggregate است.

Order Unit بدون Product وجود مستقل ندارد.

Order Unit فقط از طریق Product ایجاد، ویرایش یا حذف می‌شود.

---

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

# 12. Specification Values

Product مقادیر Specification را نگهداری می‌کند.

ساختار Specificationها توسط Specification Template تعیین می‌شود.

Product اجازه نگهداری Specification خارج از Template مربوط به Category خود را ندارد.

---

نمونه

ضخامت

۱۰

---

عرض

۱٫۵ متر

---

طول

۶ متر

---

حالت

شیت

---

# 13. Sale Mode

هر Product دقیقاً یک Sale Mode دارد.

Sale Mode سیاست فروش محصول را مشخص می‌کند.

Sale Mode یکی از مقادیر زیر است.

FullyOnline

SemiCustom

FullyCustom

---

# 14. Out Of Stock Display Policy

هر Product دقیقاً یک Out Of Stock Display Policy دارد.

این سیاست مشخص می‌کند محصول هنگام ناموجود شدن چگونه نمایش داده شود.

مقادیر مجاز

Hidden

TaggedNoAction

ContactButton

---

Sale Mode و Out Of Stock Display Policy دو مفهوم مستقل هستند.

---

# 15. Child Entity

در نسخه اول Product Aggregate تنها یک Child Entity دارد.

Order Unit

هیچ موجودیت داخلی دیگری وجود ندارد.

---

# 16. Owned Value Objects

Product مالک Value Objectهای زیر است.

Registration Unit

Sale Mode

Out Of Stock Display Policy

Specification Value

---

تمام Value Objectها Immutable هستند.

---

# 17. References

Product فقط به موجودیت‌های زیر ارجاع می‌دهد.

Category

Factory

Specification Template
(به صورت غیرمستقیم از طریق Category)

هیچ Entity خارجی داخل Aggregate نگهداری نمی‌شود.

---

# 18. Aggregate Invariants

همیشه باید شرایط زیر برقرار باشد.

هر Product دارای یک Category است.

هر Product دارای یک Factory است.

هر Product دارای یک Registration Unit است.

هر Product حداقل یک Order Unit دارد.

هر Product دارای یک Sale Mode است.

هر Product دارای یک Out Of Stock Display Policy است.

تمام Specificationها باید در Template مربوطه تعریف شده باشند.

تمام Order Unitها باید نسبت به Registration Unit تعریف شوند.

---

# 19. Aggregate Boundary

Product Aggregate مالک موارد زیر است.

✓ Display Name

✓ Registration Unit

✓ Order Units

✓ Specification Values

✓ Sale Mode

✓ Out Of Stock Display Policy

---

Product Aggregate مالک موارد زیر نیست.

✗ Price

✗ Inventory

✗ Warehouse

✗ Ordering

✗ Payment

✗ Shipping

✗ Discount

---

# 20. Lifecycle

ایجاد Product از طریق Aggregate Root انجام می‌شود.

تمام تغییرات اطلاعات داخلی نیز فقط از طریق Aggregate Root انجام می‌شود.

حذف یا تغییر مستقیم موجودیت‌های داخلی مجاز نیست.

---

# 21. Repository

Product Aggregate دارای Repository مستقل است.

Repository مسئول بازیابی و ذخیره Aggregate کامل است.

نام قرارداد Repository

IProductRepository

هیچ Repository جداگانه‌ای برای Order Unit وجود ندارد.

---

# 22. Persistence

نحوه نگهداری Aggregate در دیتابیس بخشی از این سند نیست.

این سند فقط مدل دامنه را تعریف می‌کند.

---

# 23. Summary

Product Aggregate تنها مرجع معتبر نگهداری اطلاعات پایه محصول در Catalog است.

این Aggregate مالک اطلاعات هویتی، مشخصات، واحدهای سفارش و سیاست‌های مرتبط با محصول است.

تمام مفاهیم مربوط به قیمت، موجودی، سفارش و انبار خارج از مرز این Aggregate قرار دارند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

07-Category-Aggregate.md