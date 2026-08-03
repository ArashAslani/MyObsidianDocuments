# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 14-Aggregate-Creation-Rules.md

Bounded Context: Catalog

---

# 1. Purpose

این سند قوانین ایجاد (Creation Rules) تمام Aggregateهای Catalog را تعریف می‌کند.

هدف این سند مشخص کردن شرایط لازم برای ایجاد Aggregateها است.

هیچ Aggregate نباید در وضعیت نامعتبر ایجاد شود.

تمام Aggregateها باید از اولین لحظه ایجاد، تمام Business Invariantهای دامنه را رعایت کنند.

---

# 2. General Rules

تمام Aggregateها باید از قوانین زیر پیروی کنند.

- Aggregate Root تنها نقطه ایجاد Aggregate است.
- ایجاد Aggregate از طریق Constructor عمومی مجاز نیست.
- Aggregate باید در وضعیت معتبر ایجاد شود.
- ایجاد Aggregate ناقص مجاز نیست.
- هیچ Setter عمومی برای ایجاد وضعیت نامعتبر وجود ندارد.
- Validationهای دامنه هنگام ایجاد انجام می‌شوند.

---

# 3. Product Creation

هدف

ایجاد یک Product جدید در Catalog.

---

## Required Information

برای ایجاد Product وجود اطلاعات زیر الزامی است.

- Display Name
- Category
- Factory
- Registration Unit
- Sale Mode
- Out Of Stock Display Policy

---

Product بدون هیچ‌یک از اطلاعات فوق ایجاد نمی‌شود.

---

## Order Units

در زمان ایجاد Product حداقل یک Order Unit باید وجود داشته باشد.

Product بدون Order Unit معتبر نیست.

---

## Specification Values

Specificationهای Required باید هنگام ایجاد مقدار داشته باشند.

Specificationهای Optional می‌توانند خالی باشند.

---

## Category Validation

Category انتخاب‌شده باید وجود داشته باشد.

---

## Factory Validation

Factory انتخاب‌شده باید وجود داشته باشد.

---

## Template Validation

Template مربوط به Category باید وجود داشته باشد.

---

## Specification Validation

تمام Specificationها باید در Template تعریف شده باشند.

ثبت Specification خارج از Template مجاز نیست.

---

## Initial State

پس از ایجاد Product، Aggregate باید وضعیت معتبر داشته باشد.

هیچ عملیات تکمیلی برای معتبر شدن Product وجود ندارد.

---

# 4. Category Creation

هدف

ایجاد Category جدید.

---

## Required Information

- Name
- Specification Template

---

Parent Category اختیاری است.

---

اگر Parent مشخص شود، باید وجود داشته باشد.

---

Category جدید نباید باعث ایجاد Cycle شود.

---

# 5. Factory Creation

هدف

ثبت Factory جدید.

---

Required Information

- Name

---

Factory در زمان ایجاد در وضعیت Active قرار می‌گیرد.

---

نام Factory نباید تکراری باشد.

---

# 6. Specification Template Creation

هدف

ایجاد Template جدید.

---

Required Information

- Name

---

Template می‌تواند در ابتدا بدون هیچ Specification Definition ایجاد شود.

افزودن Definitionها در مراحل بعدی انجام می‌شود.

---

# 7. Specification Definition Creation

Specification Definition فقط از طریق Specification Template ایجاد می‌شود.

ایجاد مستقل مجاز نیست.

---

Required Information

- Name
- Data Type
- Display Order
- Required State
- Searchable State

---

# 8. Order Unit Creation

Order Unit فقط از طریق Product ایجاد می‌شود.

---

Required Information

- Unit
- Conversion Factor
- Minimum Order Quantity
- Maximum Order Quantity

---

اگر اولین Order Unit باشد،

به صورت خودکار Default Order Unit خواهد بود.

---

اگر Product قبلاً Default Order Unit داشته باشد،

Order Unit جدید Default نخواهد بود مگر اینکه به صورت صریح تغییر داده شود.

---

# 9. Value Object Creation

تمام Value Objectها باید هنگام ایجاد معتبر باشند.

Value Object نامعتبر ایجاد نمی‌شود.

---

نمونه

Registration Unit

باید دارای Unit معتبر باشد.

---

Conversion Factor

باید بزرگ‌تر از صفر باشد.

---

Sale Mode

باید یکی از مقادیر تعریف‌شده باشد.

---

Out Of Stock Display Policy

باید یکی از مقادیر تعریف‌شده باشد.

---

Specification Value

باید مطابق نوع داده تعریف‌شده در Template باشد.

---

# 10. Aggregate Consistency

پس از پایان عملیات Create،

Aggregate باید تمام Business Invariantهای Catalog را رعایت کند.

هیچ Aggregate ناقصی نباید ذخیره شود.

---

# 11. Failure Rules

اگر هر یک از قوانین ایجاد نقض شود،

Aggregate ایجاد نمی‌شود.

هیچ وضعیت نیمه‌کاره در Domain وجود ندارد.

---

# 12. Responsibilities

اعتبارسنجی قوانین دامنه هنگام ایجاد،

مسئول Aggregate Root است.

Repository مسئول اعتبارسنجی نیست.

Application Service مسئول اعتبارسنجی دامنه نیست.

Infrastructure مسئول اعتبارسنجی دامنه نیست.

---

# 13. Persistence

نحوه ذخیره Aggregate بخشی از این سند نیست.

پس از ایجاد موفق،

Aggregate آماده ذخیره توسط Repository خواهد بود.

---

# 14. Summary

تمام Aggregateهای Catalog باید از طریق Aggregate Root ایجاد شوند.

تمام اطلاعات الزامی باید در زمان ایجاد وجود داشته باشند.

هیچ Aggregate ناقص یا نامعتبر وارد سیستم نمی‌شود.

این قوانین تضمین می‌کنند که Catalog همیشه در وضعیت سازگار با دامنه باقی بماند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

15-Domain-Validation-Rules.md