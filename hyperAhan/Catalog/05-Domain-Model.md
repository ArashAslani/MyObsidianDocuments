# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 05-Domain-Model.md

Bounded Context: Catalog

---

# 1. Purpose

این سند ساختار مدل دامنه Catalog را تعریف می‌کند.

هدف این سند تعیین Entityها، Value Objectها، Aggregateها و Enumerationهای دامنه است.

این سند رفتار (Behavior) را تعریف نمی‌کند.

جزئیات هر Aggregate در اسناد بعدی ارائه خواهد شد.

---

# 2. Domain Model Overview

در نسخه اول Catalog دامنه شامل مفاهیم زیر است.

Aggregates

- Product
- Category
- Specification Template

Entities

- Product
- Factory
- Category
- Specification Template
- Specification Definition
- Order Unit

Value Objects

- Specification Value
- Registration Unit
- Conversion Factor
- Sale Mode
- Out Of Stock Display Policy

Enumerations

- SaleMode
- OutOfStockDisplayPolicy

---

# 3. Aggregate Overview

سه Aggregate در نسخه اول دامنه وجود دارند.

## Product Aggregate

مسئول نگهداری اطلاعات پایه محصول است.

مالک:

- Product
- Order Unit
- Specification Value

---

## Category Aggregate

مسئول ساختار دسته‌بندی محصولات است.

مالک:

- Category

---

## Specification Template Aggregate

مسئول تعریف ساختار Specificationها است.

مالک:

- Specification Template
- Specification Definition

---

هیچ Aggregate دیگری در نسخه اول وجود ندارد.

---

# 4. Product Aggregate

Aggregate Root

Product

---

Child Entities

- Order Unit

---

Referenced Entities

- Category
- Factory
- Specification Template

---

Owned Value Objects

- Registration Unit
- Sale Mode
- Out Of Stock Display Policy
- Specification Values

---

Responsibilities

- نگهداری اطلاعات پایه محصول
- نگهداری واحد ثبت
- نگهداری واحدهای سفارش
- نگهداری مقادیر Specification
- نگهداری سیاست فروش
- نگهداری سیاست نمایش در زمان ناموجود بودن

---

Does Not Own

- Price
- Inventory
- Warehouse
- Discount
- Shipping
- Ordering

---

# 5. Category Aggregate

Aggregate Root

Category

---

Child Entities

ندارد.

---

Referenced Entities

Specification Template

---

Responsibilities

- مدیریت ساختار دسته‌بندی
- مدیریت سلسله مراتب دسته‌ها
- اتصال Category به Template مناسب

---

Does Not Own

- Product
- Specification Values
- Price

---

# 6. Specification Template Aggregate

Aggregate Root

Specification Template

---

Child Entities

Specification Definition

---

Responsibilities

- تعریف ساختار Specificationها
- تعیین نوع هر Specification
- تعیین قوانین هر Specification
- تعیین قابلیت جستجوی هر Specification

---

Does Not Own

- Product
- Category
- Price

---

# 7. Entity List

در نسخه اول Catalog موجودیت‌های زیر وجود دارند.

Product

Factory

Category

Specification Template

Specification Definition

Order Unit

---

هیچ Entity دیگری در این نسخه تعریف نشده است.

---

# 8. Value Object List

در نسخه اول Catalog Value Objectهای زیر وجود دارند.

Registration Unit

Conversion Factor

Sale Mode

Out Of Stock Display Policy

Specification Value

---

تمام Value Objectها Immutable هستند.

---

# 9. Enumeration List

SaleMode

Members

- FullyOnline
- SemiCustom
- FullyCustom

---

OutOfStockDisplayPolicy

Members

- Hidden
- TaggedNoAction
- ContactButton

---

# 10. Aggregate References

وابستگی Aggregateها به صورت زیر است.

Product

↓

Category

---

Product

↓

Factory

---

Category

↓

Specification Template

---

Product

↓

Specification Template
(از طریق Category)

---

هیچ وابستگی چرخه‌ای بین Aggregateها مجاز نیست.

---

# 11. Aggregate Ownership

Product Aggregate مالک موارد زیر است.

- Display Name
- Registration Unit
- Order Units
- Sale Mode
- Out Of Stock Display Policy
- Specification Values

---

Category Aggregate مالک موارد زیر است.

- Name
- Parent
- Template Reference

---

Specification Template Aggregate مالک موارد زیر است.

- Template Name
- Specification Definitions

---

# 12. Cross Aggregate Rules

Aggregateها فقط از طریق شناسه به یکدیگر ارجاع می‌دهند.

هیچ Aggregate نباید Aggregate دیگری را به صورت کامل در خود نگهداری کند.

نمونه

Product فقط CategoryId را نگهداری می‌کند.

Product فقط FactoryId را نگهداری می‌کند.

Category فقط TemplateId را نگهداری می‌کند.

---

# 13. Domain Services

در نسخه اول Catalog هیچ Domain Service تعریف نشده است.

در صورتی که در آینده قانونی یافت شود که به چند Aggregate تعلق داشته باشد، Domain Service ایجاد خواهد شد.

---

# 14. Domain Events

در نسخه اول Catalog هیچ Domain Event به عنوان بخشی از مدل دامنه تعریف نشده است.

در صورت ایجاد نیاز کسب‌وکاری، Domain Eventها در نسخه‌های بعدی معرفی خواهند شد.

---

# 15. Repository Contracts

هر Aggregate Root دارای Repository مستقل است.

Repositories

- IProductRepository
- ICategoryRepository
- ISpecificationTemplateRepository

هیچ Repository برای Child Entityها تعریف نمی‌شود.

---

# 16. Version

این سند ساختار پایه Domain Model نسخه 1.0 را تعریف می‌کند.

اسناد بعدی جزئیات هر Aggregate را مشخص خواهند کرد.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

06-Product-Aggregate.md