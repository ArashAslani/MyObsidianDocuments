# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 18-Application-Contracts.md

Bounded Context: Catalog

---

# 1. Purpose

این سند قراردادهای لایه Application برای ماژول Catalog را تعریف می‌کند.

Application Layer مسئول هماهنگی بین Use Caseها، Domain و Infrastructure است.

Application Layer محل نگهداری قوانین دامنه نیست.

تمام قوانین کسب‌وکار داخل Domain پیاده‌سازی می‌شوند.

Application فقط جریان اجرای Use Caseها را مدیریت می‌کند.

---

# 2. Responsibilities

Application Layer مسئول موارد زیر است.

- اجرای Use Caseها
- بارگذاری Aggregateها از Repository
- فراخوانی Behaviorهای Domain
- ذخیره Aggregateها
- مدیریت Transaction
- هماهنگی بین Aggregateها
- انتشار Domain Eventها (در صورت وجود)

---

Application مسئول موارد زیر نیست.

- Validationهای دامنه
- محاسبات قیمت
- منطق Inventory
- Queryهای گزارش‌گیری
- Authorization
- Persistence

---

# 3. Product Use Cases

Catalog باید Use Caseهای زیر را پشتیبانی کند.

---

## Register Product

هدف

ثبت Product جدید.

---

ورودی

- Display Name
- Category Id
- Factory Id
- Registration Unit
- Sale Mode
- Out Of Stock Display Policy
- Specification Values
- Order Units

---

خروجی

Product ایجاد شده.

---

## Update Product

هدف

ویرایش اطلاعات عمومی Product.

---

## Change Product Sale Mode

هدف

تغییر Sale Mode.

---

## Change Product Out Of Stock Policy

هدف

تغییر نحوه نمایش Product هنگام ناموجود بودن.

---

## Change Registration Unit

هدف

تغییر Registration Unit.

---

## Add Order Unit

هدف

افزودن Order Unit جدید.

---

## Update Order Unit

هدف

ویرایش اطلاعات Order Unit.

---

## Remove Order Unit

هدف

حذف Order Unit.

---

## Set Default Order Unit

هدف

تعیین واحد پیش‌فرض سفارش.

---

## Update Specification Values

هدف

ثبت یا ویرایش Specificationهای Product.

---

## Change Product Category

هدف

انتقال Product به Category دیگر.

---

# 4. Category Use Cases

---

## Create Category

---

## Rename Category

---

## Change Parent Category

---

## Assign Specification Template

---

# 5. Factory Use Cases

---

## Register Factory

---

## Rename Factory

---

## Activate Factory

---

## Deactivate Factory

---

# 6. Specification Template Use Cases

---

## Create Template

---

## Rename Template

---

## Add Specification Definition

---

## Remove Specification Definition

---

## Change Specification Order

---

## Change Required State

---

## Change Searchable State

---

## Change Data Type

---

# 7. Application Workflow

تمام Use Caseها از الگوی زیر پیروی می‌کنند.

Step 1

دریافت درخواست.

↓

Step 2

بارگذاری Aggregate از Repository.

↓

Step 3

فراخوانی Behavior دامنه.

↓

Step 4

اعتبارسنجی توسط Domain.

↓

Step 5

ذخیره Aggregate.

↓

Step 6

اتمام Use Case.

---

هیچ مرحله‌ای نباید قوانین دامنه را دور بزند.

---

# 8. Commands

در نسخه اول Catalog تمام عملیات تغییر وضعیت به صورت Command انجام می‌شوند.

نمونه

Register Product

Rename Category

Deactivate Factory

Add Order Unit

---

این Commandها فقط درخواست اجرای رفتار Domain هستند.

---

# 9. Queries

Application Layer فقط مسئول Queryهای ساده مرتبط با Aggregateها است.

نمونه

Get Product

Get Category

Get Factory

Get Specification Template

---

جستجوی پیشرفته، Full Text Search و Queryهای تحلیلی خارج از محدوده این سند هستند.

---

# 10. Error Handling

اگر Domain عملیات را رد کند،

Application نباید Aggregate را ذخیره کند.

خطا بدون تغییر وضعیت Aggregate به لایه بالاتر بازگردانده می‌شود.

---

# 11. Transaction Boundary

هر Use Case یک Transaction مستقل محسوب می‌شود.

در پایان اجرای موفق،

تمام تغییرات Aggregate ذخیره می‌شوند.

در صورت بروز خطا،

هیچ بخشی از تغییرات نباید ذخیره شود.

---

# 12. Domain Independence

Application نباید وضعیت داخلی Aggregate را مستقیماً تغییر دهد.

تمام تغییرات باید از طریق متدهای Aggregate Root انجام شوند.

---

# 13. Dependencies

Application به موارد زیر وابسته است.

- Domain
- Repository Interfaces

Application نباید به Infrastructure وابسته باشد.

---

# 14. Summary

Application Layer هماهنگ‌کننده اجرای Use Caseهای Catalog است.

تمام قوانین کسب‌وکار در Domain اجرا می‌شوند.

Application فقط جریان اجرای عملیات را مدیریت می‌کند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

19-Implementation-Guideline.md