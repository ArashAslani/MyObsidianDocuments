# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 04-Business-Invariants.md

Bounded Context: Catalog

---

# 1. Purpose

این سند قوانین غیرقابل نقض (Business Invariants) دامنه Catalog را تعریف می‌کند.

Business Invariant قانونی است که در تمام طول عمر سیستم باید برقرار باشد.

این قوانین مستقل از تکنولوژی، دیتابیس، API و رابط کاربری هستند.

تمام پیاده‌سازی‌های Domain باید این قوانین را رعایت کنند.

---

# 2. Product Invariants

---

## CAT-I-001

هر Product باید دارای شناسه یکتا باشد.

این شناسه در طول عمر Product تغییر نمی‌کند.

---

## CAT-I-002

هر Product دقیقاً به یک Category تعلق دارد.

Product نمی‌تواند همزمان عضو چند Category باشد.

---

## CAT-I-003

هر Product دقیقاً یک Factory دارد.

Factory بخشی از هویت Product است.

اگر Factory تغییر کند Product جدید ایجاد می‌شود.

---

## CAT-I-004

هر Product دقیقاً یک Registration Unit دارد.

وجود چند Registration Unit برای یک Product مجاز نیست.

---

## CAT-I-005

هر Product حداقل باید یک Order Unit داشته باشد.

وجود Product بدون هیچ واحد سفارش مجاز نیست.

---

## CAT-I-006

هر Product دقیقاً یک Sale Mode دارد.

---

## CAT-I-007

هر Product دقیقاً یک Out Of Stock Display Policy دارد.

---

## CAT-I-008

Product مالک قیمت نیست.

هیچ مقدار قیمت نباید داخل Aggregate مربوط به Product نگهداری شود.

---

## CAT-I-009

Product مالک موجودی نیست.

هیچ مقدار موجودی نباید داخل Aggregate مربوط به Product نگهداری شود.

---

# 3. Category Invariants

---

## CAT-I-010

هر Category دارای شناسه یکتا است.

---

## CAT-I-011

هر Category دارای نام است.

---

## CAT-I-012

Category می‌تواند دارای Parent باشد.

Category ریشه Parent ندارد.

---

## CAT-I-013

ساختار Category باید یک درخت باشد.

ایجاد حلقه (Cycle) در سلسله مراتب Category مجاز نیست.

---

## CAT-I-014

هر Category دقیقاً به یک Specification Template متصل است.

---

# 4. Specification Template Invariants

---

## CAT-I-015

هر Specification Template دارای شناسه یکتا است.

---

## CAT-I-016

Template شامل مجموعه‌ای از Specification Definitionها است.

---

## CAT-I-017

Specification Definition فقط یک بار در هر Template می‌تواند وجود داشته باشد.

وجود دو Definition با نام یکسان در یک Template مجاز نیست.

---

## CAT-I-018

Product فقط مجاز به نگهداری Specificationهایی است که در Template مربوط به Category تعریف شده باشند.

ثبت Specification خارج از Template مجاز نیست.

---

# 5. Factory Invariants

---

## CAT-I-019

هر Factory دارای شناسه یکتا است.

---

## CAT-I-020

نام Factory باید یکتا باشد.

---

## CAT-I-021

Factory بخشی از هویت Product است.

تغییر Factory باعث تغییر هویت Product می‌شود.

---

# 6. Registration Unit Invariants

---

## CAT-I-022

Registration Unit واحد مرجع Product است.

---

## CAT-I-023

تمام Conversion Factorها نسبت به Registration Unit تعریف می‌شوند.

---

# 7. Order Unit Invariants

---

## CAT-I-024

هر Order Unit متعلق به دقیقاً یک Product است.

---

## CAT-I-025

هر Order Unit دارای یک Conversion Factor است.

---

## CAT-I-026

Conversion Factor باید بزرگ‌تر از صفر باشد.

---

## CAT-I-027

هر Order Unit دارای حداقل مقدار سفارش است.

---

## CAT-I-028

حداقل مقدار سفارش نمی‌تواند منفی باشد.

---

## CAT-I-029

هر Order Unit دارای حداکثر مقدار سفارش است.

---

## CAT-I-030

حداکثر مقدار سفارش باید بزرگ‌تر یا مساوی حداقل مقدار سفارش باشد.

---

## CAT-I-031

در هر Product فقط یک Order Unit می‌تواند Default باشد.

---

# 8. Sale Mode Invariants

---

## CAT-I-032

Sale Mode فقط یکی از مقادیر زیر می‌تواند باشد.

- FullyOnline

- SemiCustom

- FullyCustom

---

# 9. Out Of Stock Display Policy Invariants

---

## CAT-I-033

Out Of Stock Display Policy فقط یکی از مقادیر زیر می‌تواند باشد.

- Hidden

- TaggedNoAction

- ContactButton

---

## CAT-I-034

Out Of Stock Display Policy مستقل از Sale Mode است.

تغییر یکی نباید موجب تغییر دیگری شود.

---

# 10. Catalog Boundary Invariants

---

## CAT-I-035

Catalog مالک Product است.

---

## CAT-I-036

Catalog مالک Category است.

---

## CAT-I-037

Catalog مالک Factory است.

---

## CAT-I-038

Catalog مالک Specification System است.

---

## CAT-I-039

Catalog مالک Pricing نیست.

---

## CAT-I-040

Catalog مالک Inventory نیست.

---

## CAT-I-041

Catalog مالک Warehouse نیست.

---

## CAT-I-042

Catalog مالک Ordering نیست.

---

## CAT-I-043

Catalog مالک Payment نیست.

---

## CAT-I-044

Catalog مالک Shipping نیست.

---

## CAT-I-045

Catalog مالک Discount نیست.

---

# 11. Search Invariants

---

## CAT-I-046

تمام Productها باید بر اساس Display Name قابل جستجو باشند.

---

## CAT-I-047

تمام Productها باید بر اساس Factory قابل جستجو باشند.

---

## CAT-I-048

تمام Productها باید بر اساس Category قابل جستجو باشند.

---

## CAT-I-049

Product باید قابلیت جستجو بر اساس Specificationهای قابل جستجوی خود را داشته باشد.

---

# 12. Stability

تمام Invariantهای این سند بخشی از Baseline دامنه Catalog هستند.

هر تغییری در این قوانین نیازمند تغییر در مدل دامنه است.

هیچ پیاده‌سازی نباید این قوانین را دور بزند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

05-Domain-Model.md