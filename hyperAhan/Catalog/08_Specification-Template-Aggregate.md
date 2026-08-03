# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 08-Specification-Template-Aggregate.md

Bounded Context: Catalog

---

# 1. Purpose

این سند طراحی کامل Specification Template Aggregate را تعریف می‌کند.

هدف این Aggregate تعریف ساختار مشخصات (Specification Structure) برای گروه‌های مختلف محصول است.

Specification Template تعیین می‌کند که محصولات هر Category چه مشخصاتی می‌توانند داشته باشند.

این Aggregate هیچ مقداری از مشخصات محصولات را نگهداری نمی‌کند.

مقادیر واقعی Specificationها متعلق به Product Aggregate هستند.

---

# 2. Aggregate Root

Aggregate Root

Specification Template

Specification Template تنها نقطه ورود Aggregate است.

تمام تغییرات ساختار Specificationها باید از طریق Aggregate Root انجام شوند.

---

# 3. Responsibilities

Specification Template Aggregate مسئول موارد زیر است.

- تعریف ساختار Specificationها
- تعریف Specificationهای مجاز
- تعیین نوع هر Specification
- تعیین ترتیب نمایش Specificationها
- تعیین قابلیت جستجوی هر Specification
- تعیین اجباری یا اختیاری بودن هر Specification

---

Specification Template مسئول موارد زیر نیست.

- مقدار Specificationها
- Product
- Price
- Inventory
- Warehouse
- Ordering

---

# 4. Aggregate Composition

Aggregate Root

Specification Template

↓

Child Entity

Specification Definition

---

Specification Definition بدون Template وجود مستقل ندارد.

تمام تغییرات Specification Definition از طریق Template انجام می‌شود.

---

# 5. Template Identity

هر Specification Template دارای شناسه یکتا است.

شناسه Template در طول عمر آن تغییر نمی‌کند.

---

# 6. Template Name

هر Template دارای نام است.

نمونه

Template ورق

Template میلگرد

Template سپری

Template قالب بتن

Template اتصالات گاز

---

نام Template برای مدیریت ساختار مشخصات استفاده می‌شود.

---

# 7. Specification Definition

هر Template شامل مجموعه‌ای از Specification Definitionها است.

Specification Definition ساختار یک Specification را تعریف می‌کند.

Specification Definition مقدار واقعی محصول نیست.

---

نمونه

Thickness

---

Width

---

Length

---

Grade

---

Loading Location

---

Weight

---

Analysis

---

Standard

---

State

---

Specification Definition فقط تعریف ویژگی است.

---

# 8. Specification Definition Responsibilities

هر Specification Definition مسئول تعریف موارد زیر است.

نام Specification

نوع داده

ترتیب نمایش

اجباری یا اختیاری بودن

قابل جستجو بودن

---

Specification Definition هیچ مقدار واقعی از Product را نگهداری نمی‌کند.

---

# 9. Data Type

هر Specification Definition دارای نوع داده است.

نمونه

Text

Number

Decimal

Boolean

Date

Selection

---

نوع داده تعیین می‌کند مقدار قابل ثبت چه ساختاری داشته باشد.

---

# 10. Required Specification

هر Specification می‌تواند اجباری یا اختیاری باشد.

اگر Specification اجباری باشد،

Product بدون مقدار آن قابل ثبت نیست.

---

نمونه

قطر میلگرد

↓

اجباری

---

محل بارگیری

↓

اختیاری

(زیرا برای تمام گروه‌های محصول وجود ندارد.)

---

# 11. Searchable Specification

هر Specification می‌تواند قابل جستجو باشد.

نمونه

قطر

↓

Searchable

---

گرید

↓

Searchable

---

ضخامت

↓

Searchable

---

توضیحات تکمیلی

↓

Not Searchable

---

این ویژگی فقط مشخص می‌کند که Search می‌تواند از این فیلد استفاده کند.

پیاده‌سازی Search خارج از Catalog است.

---

# 12. Display Order

هر Specification دارای ترتیب نمایش است.

Template مسئول نگهداری ترتیب نمایش Specificationها است.

---

نمونه

1

ضخامت

---

2

عرض

---

3

طول

---

4

وزن

---

# 13. Shared Templates

یک Template می‌تواند توسط چند Category استفاده شود.

نمونه

Template ورق

↓

ورق سیاه ST37

↓

ورق سیاه ST52

↓

ورق آجدار

---

Template مالک Category نیست.

---

# 14. Aggregate Invariants

همیشه باید شرایط زیر برقرار باشند.

هر Template دارای شناسه یکتا است.

هر Template دارای نام است.

هر Specification Definition فقط یک بار در Template می‌تواند وجود داشته باشد.

نام دو Specification Definition در یک Template نباید یکسان باشد.

ترتیب نمایش نباید تکراری باشد.

هر Specification دارای نوع داده است.

---

# 15. Aggregate Boundary

Specification Template Aggregate مالک موارد زیر است.

✓ Template Name

✓ Specification Definitions

---

مالک موارد زیر نیست.

✗ Product

✗ Specification Values

✗ Price

✗ Inventory

✗ Category

---

# 16. References

Specification Template هیچ ارجاع مستقیمی به Product ندارد.

Specification Template هیچ ارجاع مستقیمی به Category ندارد.

Category به Template ارجاع می‌دهد.

Product از طریق Category از Template استفاده می‌کند.

---

# 17. Lifecycle

Template از طریق Aggregate Root ایجاد می‌شود.

Specification Definition فقط از طریق Aggregate Root اضافه، ویرایش یا حذف می‌شود.

Specification Definition به صورت مستقل قابل ایجاد نیست.

---

# 18. Repository

Aggregate دارای Repository مستقل است.

نام قرارداد Repository

ISpecificationTemplateRepository

هیچ Repository مستقلی برای Specification Definition وجود ندارد.

---

# 19. Persistence

نحوه نگهداری Aggregate در دیتابیس بخشی از این سند نیست.

این سند فقط مدل دامنه را تعریف می‌کند.

---

# 20. Summary

Specification Template Aggregate ساختار مشخصات محصولات را تعریف می‌کند.

این Aggregate هیچ مقدار واقعی از محصولات را نگهداری نمی‌کند.

Product فقط مقادیر Specificationها را ذخیره می‌کند.

Category فقط Template مناسب را انتخاب می‌کند.

به این ترتیب، ساختار مشخصات از اطلاعات واقعی محصولات کاملاً جدا می‌شود و Catalog بدون تغییر مدل دامنه می‌تواند از خانواده‌های جدید محصول پشتیبانی کند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

09-Factory-Aggregate.md