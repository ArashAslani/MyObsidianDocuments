# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 07-Category-Aggregate.md

Bounded Context: Catalog

---

# 1. Purpose

این سند طراحی کامل Category Aggregate را تعریف می‌کند.

هدف این سند مشخص کردن مسئولیت‌ها، مرز Aggregate، قوانین دامنه، ساختار سلسله‌مراتبی Category و ارتباط آن با Product و Specification Template است.

Category مسئول سازماندهی محصولات است.

Category مسئول تعریف اطلاعات محصول نیست.

Category مسئول نگهداری قیمت، موجودی یا سفارش نیست.

---

# 2. Aggregate Root

Aggregate Root

Category

Category تنها نقطه ورود به Aggregate است.

تمام تغییرات ساختار دسته‌بندی باید از طریق Aggregate Root انجام شوند.

---

# 3. Responsibilities

Category Aggregate مسئول موارد زیر است.

- نگهداری ساختار دسته‌بندی
- مدیریت سلسله مراتب Category
- تعیین Template مورد استفاده
- مدیریت اطلاعات نمایشی دسته

---

Category Aggregate مسئول موارد زیر نیست.

- Product
- Price
- Inventory
- Warehouse
- Ordering
- Discount
- Shipping

---

# 4. Aggregate Composition

Aggregate Root

Category

↓

Referenced Entity

Specification Template

Category هیچ Child Entity ندارد.

---

# 5. Category Identity

هر Category دارای شناسه یکتا است.

شناسه Category در طول عمر آن تغییر نمی‌کند.

---

# 6. Category Name

هر Category دارای یک نام است.

نمونه

ورق

ورق سیاه

ورق سیاه ST37

میلگرد

پروفیل ساختمانی

سپری

قالب بتن

زانو جوشی

اتصالات گاز

---

نام Category بخشی از اطلاعات نمایشی سیستم است.

---

# 7. Category Hierarchy

Categoryها دارای ساختار درختی هستند.

هر Category می‌تواند دارای Parent باشد.

وجود Parent الزامی نیست.

Category ریشه Parent ندارد.

---

نمونه

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

هیچ Category نمی‌تواند چند Parent داشته باشد.

---

# 8. Category Depth

دامنه محدودیتی برای تعداد سطوح Category تعیین نکرده است.

نمونه‌های تحلیل‌شده معمولاً دارای دو یا سه سطح هستند.

اما مدل دامنه نباید تعداد سطوح را محدود کند.

---

# 9. Specification Template Reference

هر Category دقیقاً به یک Specification Template متصل است.

Category مالک Template نیست.

Category فقط به Template ارجاع می‌دهد.

---

یک Template می‌تواند توسط چند Category استفاده شود.

نمونه

ورق سیاه ST37

↓

Template ورق

---

ورق سیاه ST52

↓

Template ورق

---

# 10. Products

Category مالک Product نیست.

Productها داخل Aggregate مربوط به Category نگهداری نمی‌شوند.

Category هیچ Collection از Productها ندارد.

ارتباط بین Product و Category از سمت Product برقرار می‌شود.

---

# 11. Aggregate Invariants

همیشه باید شرایط زیر برقرار باشند.

هر Category دارای شناسه یکتا است.

هر Category دارای نام است.

هر Category حداکثر یک Parent دارد.

ساختار Category باید درخت باشد.

ایجاد حلقه (Cycle) مجاز نیست.

هر Category دقیقاً به یک Specification Template متصل است.

---

# 12. Aggregate Boundary

Category Aggregate مالک موارد زیر است.

✓ Name

✓ Parent

✓ Template Reference

---

Category Aggregate مالک موارد زیر نیست.

✗ Product

✗ Specification Values

✗ Price

✗ Inventory

✗ Warehouse

---

# 13. References

Category فقط به موجودیت زیر ارجاع می‌دهد.

Specification Template

هیچ ارجاع مستقیمی به Product وجود ندارد.

---

# 14. Lifecycle

Category از طریق Aggregate Root ایجاد می‌شود.

تغییر Parent فقط از طریق Aggregate Root انجام می‌شود.

تغییر Template فقط از طریق Aggregate Root انجام می‌شود.

---

# 15. Repository

Category دارای Repository مستقل است.

Repository مسئول بازیابی و ذخیره Aggregate کامل است.

نام قرارداد Repository

ICategoryRepository

---

# 16. Persistence

نحوه نگهداری Category در دیتابیس بخشی از این سند نیست.

---

# 17. Summary

Category مسئول سازماندهی ساختار محصولات است.

Category هیچ اطلاعاتی درباره قیمت، موجودی، سفارش یا مشخصات محصولات نگهداری نمی‌کند.

Category فقط ساختار دسته‌بندی را مدیریت می‌کند و تعیین می‌کند هر Product از کدام Specification Template استفاده خواهد کرد.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

08-Specification-Template-Aggregate.md