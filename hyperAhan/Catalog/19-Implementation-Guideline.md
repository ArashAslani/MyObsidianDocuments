# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 19-Implementation-Guideline.md

Bounded Context: Catalog

---

# 1. Purpose

این سند دستورالعمل رسمی پیاده‌سازی Domain Layer ماژول Catalog را تعریف می‌کند.

هدف این سند یکسان‌سازی شیوه پیاده‌سازی تمام توسعه‌دهندگان و ابزارهای هوش مصنوعی است.

تمام کلاس‌های Domain باید مطابق این سند پیاده‌سازی شوند.

این سند درباره Business Rules نیست.

Business Rules در اسناد Domain تعریف شده‌اند.

این سند فقط نحوه تبدیل آن مدل به کد را مشخص می‌کند.

---

# 2. Architectural Principles

پیاده‌سازی باید از اصول زیر پیروی کند.

- Rich Domain Model
- DDD Tactical Patterns
- Persistence Ignorance
- Encapsulation
- High Cohesion
- Low Coupling

Domain نباید وابسته به هیچ تکنولوژی باشد.

---

# 3. Aggregate Implementation

هر Aggregate باید دارای یک Aggregate Root باشد.

تمام تغییرات Aggregate فقط از طریق Aggregate Root انجام می‌شود.

هیچ Entity داخلی نباید توسط کلاس‌های بیرونی تغییر کند.

---

نمونه

صحیح

Product.AddOrderUnit()

---

اشتباه

product.OrderUnits.Add(...)

---

# 4. Constructors

تمام Aggregate Root ها باید Constructor غیر Public داشته باشند.

Constructor فقط برای ایجاد وضعیت معتبر استفاده می‌شود.

ایجاد Aggregate باید از طریق Factory Method انجام شود.

---

نمونه

Product.Create(...)

Category.Create(...)

Factory.Create(...)

SpecificationTemplate.Create(...)

---

استفاده مستقیم از Constructor مجاز نیست.

---

# 5. Entity Design

تمام Entity ها باید دارای Identity باشند.

Identity پس از ایجاد تغییر نمی‌کند.

Entity ها باید رفتار داشته باشند.

Entity صرفاً Container داده نیست.

---

# 6. Value Object Design

تمام Value Object ها باید Immutable باشند.

---

Value Object ها

Setter عمومی ندارند.

Identity ندارند.

بر اساس مقدار مقایسه می‌شوند.

پس از ایجاد تغییر نمی‌کنند.

---

# 7. Collections

Collectionهای داخلی Aggregate باید محافظت شوند.

نباید Collection قابل تغییر در اختیار لایه بیرونی قرار گیرد.

---

نمونه

صحیح

IReadOnlyCollection<OrderUnit>

---

اشتباه

List<OrderUnit>

---

# 8. Encapsulation

تمام تغییرات باید از طریق متدهای دامنه انجام شوند.

هیچ Property نباید Setter عمومی داشته باشد.

---

صحیح

private set

یا

init

---

اشتباه

public set

---

# 9. Domain Validation

تمام Validationهای دامنه داخل Domain انجام می‌شوند.

Application Layer نباید Validationهای Business انجام دهد.

Infrastructure نباید Validationهای Business انجام دهد.

---

# 10. Repository Design

برای هر Aggregate Root فقط یک Repository وجود دارد.

Repository فقط Aggregate Root را مدیریت می‌کند.

---

Repositoryهای مجاز

IProductRepository

ICategoryRepository

IFactoryRepository

ISpecificationTemplateRepository

---

Repository عمومی (Generic Repository) بخشی از طراحی Domain نیست.

---

# 11. Persistence Ignorance

Domain نباید هیچ وابستگی به موارد زیر داشته باشد.

EF Core

SQL Server

PostgreSQL

MongoDB

Dapper

ORM

Attributeهای Mapping

Data Annotation

---

تمام Mappingها در Infrastructure انجام می‌شوند.

---

# 12. Domain Events

در نسخه اول Catalog،

Domain Eventها بخشی از پیاده‌سازی اجباری نیستند.

در صورت نیاز در نسخه‌های آینده اضافه خواهند شد.

بنابراین Aggregateها نباید وابستگی به Event Bus داشته باشند.

---

# 13. Exceptions

در صورت نقض قوانین دامنه،

Domain باید Domain Exception تولید کند.

نوع Exceptionها بخشی از Domain هستند.

نوع نمایش Exception به کاربر خارج از Domain است.

---

# 14. Enumerations

Enumerationهای دامنه باید Strongly Typed باشند.

از استفاده از رشته‌های آزاد برای مفاهیم دامنه خودداری شود.

نمونه‌ها

SaleMode

OutOfStockDisplayPolicy

RegistrationUnit

---

# 15. Equality

Entityها بر اساس Identity مقایسه می‌شوند.

Value Objectها بر اساس تمام مقادیر داخلی مقایسه می‌شوند.

---

# 16. Null Handling

هیچ Aggregate نباید در وضعیت Null یا ناقص ایجاد شود.

Propertyهای الزامی همیشه مقدار معتبر دارند.

---

# 17. Navigation Properties

Aggregateها فقط شناسه Aggregateهای دیگر را نگهداری می‌کنند.

نمونه

Product

↓

CategoryId

FactoryId

---

Product نباید Category را در خود نگهداری کند.

Product نباید Factory را در خود نگهداری کند.

---

# 18. Child Entity Rules

Child Entityها فقط توسط Aggregate Root ایجاد می‌شوند.

Child Entityها Repository مستقل ندارند.

Child Entityها خارج از Aggregate قابل استفاده نیستند.

---

# 19. Specification Rules

Specification Value فقط زمانی قابل ثبت است که Specification Definition در Template مربوطه وجود داشته باشد.

Template مالک ساختار است.

Product مالک مقدار است.

---

# 20. Order Unit Rules

Order Unit فقط از طریق Product مدیریت می‌شود.

وجود حداقل یک Order Unit برای هر Product الزامی است.

در هر Product فقط یک Default Order Unit وجود دارد.

---

# 21. Naming Convention

نام کلاس‌ها باید از زبان مشترک (Ubiquitous Language) پیروی کنند.

از نام‌های عمومی مانند

Item

Object

Data

Info

Record

Model

استفاده نشود.

نام کلاس باید مستقیماً بیانگر مفهوم دامنه باشد.

---

# 22. Dependency Direction

وابستگی‌ها فقط در جهت زیر مجاز هستند.

Presentation

↓

Application

↓

Domain

↑

Infrastructure

Infrastructure به Domain وابسته است.

Domain به Infrastructure وابسته نیست.

---

# 23. Testing Guideline

تمام Behaviorهای Aggregate باید قابل تست واحد باشند.

تست Domain نباید به Database وابسته باشد.

تست Domain نباید به EF Core وابسته باشد.

تست Domain نباید به Repository واقعی وابسته باشد.

---

# 24. Code Style

تمام کلاس‌های Domain باید خوانا باشند.

متدهای طولانی شکسته شوند.

منطق دامنه داخل Entityها قرار گیرد.

کلاس‌های Anemic Domain Model ایجاد نشوند.

---

# 25. Future Compatibility

پیاده‌سازی باید به گونه‌ای انجام شود که افزودن قابلیت‌های زیر بدون شکستن مدل فعلی امکان‌پذیر باشد.

- Pricing
- Inventory
- Warehouse
- Search
- ElasticSearch
- Order
- Discount
- Shipment

Catalog نباید وابستگی به هیچ‌یک از این ماژول‌ها داشته باشد.

---

# 26. Final Guideline

در صورت وجود هرگونه ابهام بین کد و مستندات،

Business Rules اولویت دارند.

در صورت وجود تعارض بین Infrastructure و Domain،

مدل Domain مرجع نهایی است.

در صورت نیاز به تغییر مدل،

ابتدا مستندات Domain باید اصلاح شوند،

سپس پیاده‌سازی تغییر کند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Implementation Ready