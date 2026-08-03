# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 09-Factory-Aggregate.md

Bounded Context: Catalog

---

# 1. Purpose

این سند طراحی کامل Factory Aggregate را تعریف می‌کند.

Factory نماینده کارخانه تولیدکننده محصول است.

Factory بخشی از هویت تجاری Product محسوب می‌شود.

Factory مسئول تولید محصول است اما مالک Product نیست.

Product فقط به Factory ارجاع می‌دهد.

---

# 2. Aggregate Root

Aggregate Root

Factory

تمام تغییرات اطلاعات Factory باید از طریق Aggregate Root انجام شوند.

---

# 3. Responsibilities

Factory Aggregate مسئول موارد زیر است.

- نگهداری اطلاعات کارخانه
- مدیریت وضعیت فعال یا غیرفعال بودن کارخانه
- ارائه اطلاعات مرجع برای Product

---

Factory مسئول موارد زیر نیست.

- Product
- Price
- Inventory
- Warehouse
- Ordering
- Specification

---

# 4. Aggregate Composition

Aggregate Root

Factory

Factory در نسخه اول هیچ Child Entity ندارد.

هیچ Value Object اختصاصی نیز در نسخه اول تعریف نشده است.

---

# 5. Factory Identity

هر Factory دارای شناسه یکتا است.

شناسه Factory در طول عمر آن تغییر نمی‌کند.

---

# 6. Factory Name

هر Factory دارای نام رسمی است.

نمونه

- ذوب‌آهن اصفهان
- فولاد مبارکه
- ظفر بناب
- ابرکوه
- کویر کاشان

نام Factory باید در سطح Catalog یکتا باشد.

---

# 7. Factory Status

هر Factory دارای وضعیت است.

وضعیت مشخص می‌کند که آیا Factory برای ایجاد Product جدید قابل استفاده است یا خیر.

مقادیر وضعیت در نسخه اول

Active

Inactive

غیرفعال بودن Factory باعث حذف Productهای موجود نمی‌شود.

---

# 8. Relationship With Product

هر Product دقیقاً به یک Factory تعلق دارد.

یک Factory می‌تواند توسط چندین Product استفاده شود.

نمونه

Factory

↓

ذوب‌آهن اصفهان

↓

Products

- میلگرد ۱۴
- میلگرد ۱۶
- میلگرد ۱۸
- میلگرد ۲۰

---

Factory مالک Product نیست.

هیچ Collection از Productها داخل Aggregate نگهداری نمی‌شود.

ارتباط از سمت Product برقرار می‌شود.

---

# 9. Factory As Product Identity

Factory بخشی از هویت تجاری Product است.

نمونه

میلگرد ۱۴ ذوب‌آهن

↓

Product شماره یک

---

میلگرد ۱۴ ابرکوه

↓

Product شماره دو

---

این دو Product مستقل هستند.

حتی اگر سایر Specificationها یکسان باشند.

---

# 10. Aggregate Invariants

همیشه باید شرایط زیر برقرار باشند.

هر Factory دارای شناسه یکتا است.

هر Factory دارای نام است.

نام Factory نباید تکراری باشد.

هر Factory دارای وضعیت است.

---

# 11. Aggregate Boundary

Factory Aggregate مالک موارد زیر است.

✓ Name

✓ Status

---

مالک موارد زیر نیست.

✗ Product

✗ Price

✗ Inventory

✗ Warehouse

✗ Specification

---

# 12. References

Factory به هیچ Aggregate دیگری ارجاع نمی‌دهد.

Product به Factory ارجاع می‌دهد.

---

# 13. Lifecycle

Factory از طریق Aggregate Root ایجاد می‌شود.

تغییر نام یا وضعیت فقط از طریق Aggregate Root انجام می‌شود.

حذف Factory خارج از محدوده این سند است.

---

# 14. Repository

Factory دارای Repository مستقل است.

نام قرارداد Repository

IFactoryRepository

---

# 15. Persistence

نحوه نگهداری Factory در دیتابیس بخشی از این سند نیست.

---

# 16. Summary

Factory مرجع رسمی کارخانه‌های تولیدکننده در Catalog است.

Factory اطلاعات محصولات را نگهداری نمی‌کند.

Product فقط به Factory ارجاع می‌دهد.

تغییر Factory باعث ایجاد Product جدید می‌شود و هویت Product قبلی حفظ می‌شود.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

10-Order-Unit.md