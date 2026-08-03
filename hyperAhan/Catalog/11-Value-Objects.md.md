# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 11-Entity-And-ValueObject-Definitions.md

Bounded Context: Catalog

---

# 1. Purpose

این سند تعریف دقیق Entityها و Value Objectهای Catalog Domain را ارائه می‌کند.

هدف این سند تعیین ساختار دامنه است.

این سند شامل موارد زیر است.

- موجودیت‌های دامنه
- Value Objectهای دامنه
- فیلدهای هر کدام
- مسئولیت هر کدام
- قوانین مالکیت

این سند رفتار (Behavior) را تعریف نمی‌کند.

---

# 2. Entities

در نسخه اول Catalog موجودیت‌های زیر وجود دارند.

- Product
- Category
- Factory
- Specification Template
- Specification Definition
- Order Unit

هیچ Entity دیگری در نسخه اول وجود ندارد.

---

# 3. Product

نوع

Aggregate Root

---

مسئولیت

نگهداری اطلاعات پایه محصول.

---

فیلدها

Id

DisplayName

CategoryId

FactoryId

RegistrationUnit

SaleMode

OutOfStockDisplayPolicy

SpecificationValues

OrderUnits

---

توضیح

Product مالک اطلاعات پایه محصول است.

Product مالک قیمت نیست.

Product مالک موجودی نیست.

---

# 4. Category

نوع

Aggregate Root

---

مسئولیت

مدیریت ساختار دسته‌بندی.

---

فیلدها

Id

Name

ParentCategoryId

SpecificationTemplateId

---

توضیح

Category فقط ساختار دسته‌بندی را مدیریت می‌کند.

---

# 5. Factory

نوع

Aggregate Root

---

مسئولیت

مدیریت کارخانه تولیدکننده.

---

فیلدها

Id

Name

Status

---

توضیح

Factory مرجع کارخانه‌ها است.

---

# 6. Specification Template

نوع

Aggregate Root

---

مسئولیت

تعریف ساختار Specificationها.

---

فیلدها

Id

Name

Definitions

---

---

# 7. Specification Definition

نوع

Child Entity

---

مسئولیت

تعریف یک Specification.

---

فیلدها

Id

Name

DataType

DisplayOrder

IsRequired

IsSearchable

---

توضیح

Specification Definition مقدار واقعی Product را نگهداری نمی‌کند.

---

# 8. Order Unit

نوع

Child Entity

---

مسئولیت

تعریف یک واحد سفارش.

---

فیلدها

Id

Unit

ConversionFactor

MinimumOrderQuantity

MaximumOrderQuantity

IsDefault

---

توضیح

Order Unit بدون Product وجود مستقل ندارد.

---

# 9. Value Objects

در نسخه اول Value Objectهای زیر وجود دارند.

RegistrationUnit

ConversionFactor

SaleMode

OutOfStockDisplayPolicy

SpecificationValue

---

تمام Value Objectها Immutable هستند.

---

# 10. Registration Unit

نوع

Value Object

---

مسئولیت

تعریف واحد مرجع Product.

---

فیلد

Unit

---

نمونه

Kilogram

Piece

Package

---

Registration Unit مبنای تمام Conversion Factorها است.

---

# 11. Conversion Factor

نوع

Value Object

---

مسئولیت

تعریف نسبت تبدیل Order Unit به Registration Unit.

---

فیلد

Value

---

نمونه

1000

18.95

9000

---

همیشه بزرگ‌تر از صفر است.

---

# 12. Sale Mode

نوع

Value Object

---

فیلد

Value

---

مقادیر

FullyOnline

SemiCustom

FullyCustom

---

# 13. Out Of Stock Display Policy

نوع

Value Object

---

فیلد

Value

---

مقادیر

Hidden

TaggedNoAction

ContactButton

---

# 14. Specification Value

نوع

Value Object

---

مسئولیت

نگهداری مقدار واقعی یک Specification.

---

فیلدها

SpecificationDefinitionId

Value

---

نمونه

Definition

Thickness

↓

Value

10

---

Definition

Grade

↓

Value

ST37

---

Definition

LoadingLocation

↓

Value

کارخانه اصفهان

---

Specification Value ساختار Specification را تعیین نمی‌کند.

ساختار توسط Specification Template تعریف می‌شود.

---

# 15. Ownership

Product مالک موارد زیر است.

- Specification Values
- Order Units
- Registration Unit
- Sale Mode
- Out Of Stock Display Policy

---

Category مالک Specification Template نیست.

Factory مالک Product نیست.

Specification Template مالک Product نیست.

---

# 16. Identity Rules

Entityها دارای شناسه هستند.

Value Objectها شناسه ندارند.

Entityها قابل شناسایی هستند.

Value Objectها فقط با مقدارشان شناخته می‌شوند.

---

# 17. Equality Rules

Entityها بر اساس Identity مقایسه می‌شوند.

Value Objectها بر اساس تمام مقادیر داخلی خود مقایسه می‌شوند.

---

# 18. Immutability

تمام Value Objectها Immutable هستند.

هیچ Setter عمومی ندارند.

بعد از ایجاد تغییر نمی‌کنند.

---

# 19. Summary

مدل دامنه Catalog شامل شش Entity و پنج Value Object است.

تمام اطلاعات هویتی داخل Entityها قرار دارند.

تمام مفاهیم بدون هویت به صورت Value Object مدل شده‌اند.

این مدل مبنای پیاده‌سازی Domain Layer خواهد بود.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

12-Domain-Behaviors.md