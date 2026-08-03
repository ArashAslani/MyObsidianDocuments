# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 13-Repository-Contracts.md

Bounded Context: Catalog

---

# 1. Purpose

این سند قرارداد (Contract) Repositoryهای Domain در ماژول Catalog را تعریف می‌کند.

هدف این Repositoryها فراهم کردن دسترسی به Aggregate Rootها است.

Repositoryها بخشی از Domain هستند و پیاده‌سازی آن‌ها در Infrastructure انجام خواهد شد.

این سند فقط قرارداد دامنه را تعریف می‌کند و هیچ وابستگی به EF Core، SQL Server یا ORM خاصی ندارد.

---

# 2. Design Principles

تمام Repositoryهای Catalog باید از اصول زیر پیروی کنند.

- هر Repository متعلق به یک Aggregate Root است.
- Repository فقط Aggregate Root را مدیریت می‌کند.
- Child Entityها Repository مستقل ندارند.
- Value Objectها Repository ندارند.
- Repository نباید منطق کسب‌وکار داشته باشد.
- Repository مسئول Queryهای گزارش‌گیری نیست.
- Repository فقط برای بازیابی و نگهداری Aggregateها استفاده می‌شود.

---

# 3. Aggregate Roots

در نسخه اول Catalog چهار Aggregate Root وجود دارد.

- Product
- Category
- Factory
- SpecificationTemplate

بنابراین فقط چهار Repository وجود خواهد داشت.

---

# 4. Product Repository

Interface

IProductRepository

---

مسئولیت

مدیریت Product Aggregate

---

بازیابی Product

ذخیره Product

بررسی وجود Product

حذف Product (در صورت پشتیبانی در نسخه‌های آینده)

---

Repository هیچ مسئولیتی در قبال Price ندارد.

Repository هیچ مسئولیتی در قبال Inventory ندارد.

Repository هیچ مسئولیتی در قبال Search ندارد.

---

Repository باید Aggregate کامل Product را بازگرداند.

به عبارت دیگر هنگام Load شدن Product، اطلاعات زیر نیز باید قابل دسترس باشند.

- Registration Unit
- Order Units
- Specification Values
- Sale Mode
- Out Of Stock Display Policy

---

Repository نباید Factory یا Category را داخل Aggregate بارگذاری کند.

Product فقط شناسه آن‌ها را نگهداری می‌کند.

---

# 5. Category Repository

Interface

ICategoryRepository

---

مسئولیت

مدیریت Category Aggregate

---

Repository مسئول بازیابی سلسله مراتب Category است.

Repository مسئول نگهداری Parent Category Reference است.

Repository مسئول نگهداری Template Reference است.

---

Repository مالک Productها نیست.

---

# 6. Factory Repository

Interface

IFactoryRepository

---

مسئولیت

مدیریت Factory Aggregate

---

Repository فقط اطلاعات Factory را مدیریت می‌کند.

هیچ Productی از طریق این Repository مدیریت نمی‌شود.

---

# 7. Specification Template Repository

Interface

ISpecificationTemplateRepository

---

مسئولیت

مدیریت Specification Template Aggregate

---

Repository مسئول بازیابی Template و تمام Specification Definitionهای آن است.

Repository هیچ Productی را مدیریت نمی‌کند.

---

# 8. Repository Rules

Repositoryها فقط Aggregate Root را ذخیره می‌کنند.

هیچ Repository مستقلی برای موارد زیر وجود ندارد.

- Order Unit
- Specification Definition
- Specification Value
- Registration Unit
- Conversion Factor

تمام این مفاهیم از طریق Aggregate Root مدیریت می‌شوند.

---

# 9. Identity Lookup

هر Repository باید قابلیت بازیابی Aggregate بر اساس شناسه را داشته باشد.

Identity تنها روش قطعی شناسایی Aggregate است.

---

# 10. Business Lookup

دامنه Catalog در برخی سناریوها نیازمند بازیابی Aggregate بر اساس داده‌های کسب‌وکاری است.

نمونه‌ها

Product بر اساس Display Name

Category بر اساس Name

Factory بر اساس Name

Specification Template بر اساس Name

پیاده‌سازی این عملیات باید همچنان Aggregate کامل را بازگرداند.

---

# 11. Persistence Ignorance

هیچ Repository نباید وابستگی به تکنولوژی ذخیره‌سازی داشته باشد.

Domain نباید از موارد زیر اطلاعی داشته باشد.

- EF Core
- SQL Server
- PostgreSQL
- MongoDB
- Dapper

تمام این وابستگی‌ها در Infrastructure قرار می‌گیرند.

---

# 12. Transactions

مدیریت Transaction مسئول Repository نیست.

Repository فقط عملیات ذخیره و بازیابی Aggregate را انجام می‌دهد.

مدیریت Unit Of Work خارج از این سند است.

---

# 13. Domain Integrity

Repository نباید Aggregate ناقص برگرداند.

Aggregate باید در وضعیت معتبر (Consistent) بازیابی شود.

تمام Child Entityها و Value Objectهای متعلق به Aggregate باید در زمان بازیابی در دسترس باشند.

---

# 14. Forbidden Responsibilities

Repository نباید مسئول موارد زیر باشد.

- Validation
- Business Rules
- Price Calculation
- Inventory Calculation
- Search Engine
- Reporting
- DTO Mapping
- Authorization

---

# 15. Summary

Repositoryها تنها دروازه دسترسی به Aggregate Rootهای Catalog هستند.

در نسخه اول Catalog فقط چهار Repository وجود دارد.

- IProductRepository
- ICategoryRepository
- IFactoryRepository
- ISpecificationTemplateRepository

هیچ Repository دیگری در Domain تعریف نمی‌شود.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

14-Aggregate-Creation-Rules.md