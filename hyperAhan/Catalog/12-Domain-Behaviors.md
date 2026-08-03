# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 12-Domain-Behaviors.md

Bounded Context: Catalog

---

# 1. Purpose

این سند رفتارهای (Domain Behaviors) موجود در Catalog را تعریف می‌کند.

Behaviorها عملیات مجاز روی Aggregateها هستند.

تمام تغییرات Aggregateها باید از طریق این رفتارها انجام شوند.

هیچ کدی خارج از Aggregate نباید وضعیت داخلی Aggregate را مستقیماً تغییر دهد.

---

# 2. Product Aggregate Behaviors

Product Aggregate مسئول تمام تغییرات اطلاعات Product است.

---

## Create Product

هدف

ایجاد یک Product جدید.

نتیجه

یک Product معتبر ایجاد می‌شود.

---

## Change Display Name

هدف

تغییر نام نمایشی محصول.

شرط

Display Name جدید نباید خالی باشد.

---

## Change Category

هدف

انتقال Product به Category دیگر.

شرط

Category مقصد باید وجود داشته باشد.

پس از تغییر Category، Specificationهای Product باید با Template جدید سازگار باشند.

---

## Change Factory

هدف

تغییر Factory محصول.

قاعده کسب‌وکاری

Factory بخشی از هویت Product است.

در نسخه اول این عملیات مجاز نیست.

در صورت نیاز باید Product جدید ایجاد شود.

---

## Change Sale Mode

هدف

تغییر سیاست فروش Product.

---

## Change Out Of Stock Display Policy

هدف

تغییر نحوه نمایش Product هنگام ناموجود بودن.

---

## Change Registration Unit

هدف

تغییر Registration Unit.

این عملیات فقط زمانی مجاز است که تمام Order Unitها نیز متناسب با آن بازتعریف شوند.

---

## Add Order Unit

هدف

افزودن واحد سفارش جدید.

---

## Remove Order Unit

هدف

حذف واحد سفارش.

شرط

حداقل یک Order Unit باید همیشه باقی بماند.

---

## Change Order Unit

هدف

ویرایش اطلاعات یک Order Unit.

---

## Change Default Order Unit

هدف

تعیین واحد پیش‌فرض سفارش.

شرط

در هر Product فقط یک Default Order Unit وجود دارد.

---

## Set Specification Value

هدف

ثبت مقدار یک Specification.

شرط

Specification باید در Template مربوط به Category تعریف شده باشد.

---

## Remove Specification Value

هدف

حذف مقدار یک Specification اختیاری.

Specificationهای Required قابل حذف نیستند.

---

# 3. Category Aggregate Behaviors

---

## Create Category

هدف

ایجاد Category جدید.

---

## Rename Category

هدف

تغییر نام Category.

---

## Change Parent

هدف

تغییر Parent Category.

شرط

نباید Cycle ایجاد شود.

---

## Assign Specification Template

هدف

اتصال Category به Specification Template.

---

# 4. Specification Template Behaviors

---

## Create Template

هدف

ایجاد Template جدید.

---

## Rename Template

هدف

تغییر نام Template.

---

## Add Specification Definition

هدف

افزودن Specification جدید.

---

## Remove Specification Definition

هدف

حذف Specification.

---

## Change Display Order

هدف

تغییر ترتیب نمایش.

---

## Change Required State

هدف

اجباری یا اختیاری کردن Specification.

---

## Change Searchable State

هدف

فعال یا غیرفعال کردن قابلیت جستجو.

---

## Change Data Type

هدف

تغییر نوع داده Specification.

---

# 5. Factory Behaviors

---

## Create Factory

هدف

ثبت Factory جدید.

---

## Rename Factory

هدف

تغییر نام Factory.

---

## Activate Factory

هدف

فعال کردن Factory.

---

## Deactivate Factory

هدف

غیرفعال کردن Factory.

---

# 6. Forbidden Operations

عملیات زیر در Catalog مجاز نیستند.

ثبت قیمت

تغییر قیمت

محاسبه قیمت

ثبت موجودی

رزرو موجودی

انتقال انبار

محاسبه حمل

ثبت سفارش

پرداخت

تخفیف

تمام این عملیات متعلق به Bounded Contextهای دیگر هستند.

---

# 7. Aggregate Protection

تمام تغییرات باید از طریق Aggregate Root انجام شوند.

هیچ Entity داخلی نباید مستقیماً تغییر کند.

هیچ Setter عمومی نباید وضعیت Aggregate را تغییر دهد.

---

# 8. Summary

Behaviorهای تعریف‌شده در این سند تنها عملیات مجاز روی Aggregateهای Catalog هستند.

هر رفتار باید Invariantهای تعریف‌شده در سند Business Invariants را حفظ کند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

13-Repository-Contracts.md