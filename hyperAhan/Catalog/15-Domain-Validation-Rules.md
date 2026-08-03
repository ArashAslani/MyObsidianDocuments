# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 15-Domain-Validation-Rules.md

Bounded Context: Catalog

---

# 1. Purpose

این سند تمام قوانین اعتبارسنجی دامنه (Domain Validation Rules) ماژول Catalog را تعریف می‌کند.

هدف این سند جلوگیری از ورود وضعیت‌های نامعتبر به Domain است.

تمام Aggregateها موظف به رعایت این قوانین هستند.

هیچ Validation مرتبط با قوانین کسب‌وکار نباید در UI، Application یا Infrastructure پیاده‌سازی شود.

اعتبارسنجی‌های این سند بخشی از Domain Model هستند.

---

# 2. Validation Philosophy

Catalog بر اساس اصل زیر طراحی شده است.

هر Aggregate باید همیشه در وضعیت معتبر (Valid State) قرار داشته باشد.

هیچ Aggregate نباید حتی برای مدت کوتاهی وارد وضعیت نامعتبر شود.

در صورت نقض هر قانون، عملیات باید متوقف شود و Aggregate تغییر نکند.

---

# 3. Product Validation Rules

---

## Rule P-001

Display Name الزامی است.

Display Name نمی‌تواند خالی باشد.

Display Name نمی‌تواند فقط شامل فاصله باشد.

---

## Rule P-002

Category الزامی است.

هر Product باید دقیقاً به یک Category تعلق داشته باشد.

---

## Rule P-003

Factory الزامی است.

هر Product باید دقیقاً یک Factory داشته باشد.

---

## Rule P-004

Registration Unit الزامی است.

هر Product باید دقیقاً یک Registration Unit داشته باشد.

---

## Rule P-005

Sale Mode الزامی است.

Sale Mode فقط یکی از مقادیر زیر می‌تواند باشد.

- FullyOnline
- SemiCustom
- FullyCustom

---

## Rule P-006

Out Of Stock Display Policy الزامی است.

فقط یکی از مقادیر زیر مجاز است.

- Hidden
- TaggedNoAction
- ContactButton

---

## Rule P-007

هر Product باید حداقل یک Order Unit داشته باشد.

Product بدون Order Unit معتبر نیست.

---

## Rule P-008

در هر Product فقط یک Default Order Unit وجود دارد.

وجود بیش از یک Default مجاز نیست.

نبود Default نیز مجاز نیست.

---

## Rule P-009

تمام Specification Valueها باید در Template مربوط به Category تعریف شده باشند.

ثبت Specification خارج از Template ممنوع است.

---

## Rule P-010

تمام Specificationهای Required باید مقدار داشته باشند.

---

## Rule P-011

Specificationهای Optional می‌توانند بدون مقدار باشند.

---

## Rule P-012

Product نمی‌تواند به Factory نامعتبر ارجاع دهد.

---

## Rule P-013

Product نمی‌تواند به Category نامعتبر ارجاع دهد.

---

# 4. Category Validation Rules

---

## Rule C-001

Name الزامی است.

---

## Rule C-002

Name نمی‌تواند خالی باشد.

---

## Rule C-003

Category نباید Parent خودش باشد.

---

## Rule C-004

ساختار Category نباید Cycle ایجاد کند.

نمونه نامعتبر

A

↓

B

↓

C

↓

A

---

## Rule C-005

هر Category باید دقیقاً به یک Specification Template متصل باشد.

---

# 5. Factory Validation Rules

---

## Rule F-001

Factory Name الزامی است.

---

## Rule F-002

نام Factory نباید تکراری باشد.

---

## Rule F-003

Status فقط یکی از مقادیر زیر است.

Active

Inactive

---

# 6. Specification Template Validation Rules

---

## Rule ST-001

Template Name الزامی است.

---

## Rule ST-002

هر Specification Definition باید شناسه یکتا داشته باشد.

---

## Rule ST-003

Display Order نباید تکراری باشد.

---

## Rule ST-004

Name نباید تکراری باشد.

---

## Rule ST-005

Data Type الزامی است.

---

# 7. Order Unit Validation Rules

---

## Rule OU-001

Unit الزامی است.

---

## Rule OU-002

Conversion Factor الزامی است.

---

## Rule OU-003

Conversion Factor باید بزرگ‌تر از صفر باشد.

---

## Rule OU-004

Minimum Order Quantity نباید منفی باشد.

---

## Rule OU-005

Maximum Order Quantity نباید منفی باشد.

---

## Rule OU-006

Maximum Order Quantity نباید کمتر از Minimum Order Quantity باشد.

---

## Rule OU-007

Order Unitهای یک Product نباید Unit تکراری داشته باشند.

نمونه نامعتبر

کیلوگرم

کیلوگرم

---

# 8. Registration Unit Validation Rules

---

Registration Unit الزامی است.

Registration Unit باید یکی از واحدهای تعریف‌شده سیستم باشد.

---

# 9. Sale Mode Validation Rules

---

Sale Mode فقط از مقادیر تعریف‌شده سیستم انتخاب می‌شود.

هیچ مقدار آزاد مجاز نیست.

---

# 10. Out Of Stock Display Policy Validation Rules

---

فقط سه مقدار تعریف‌شده مجاز هستند.

هیچ مقدار دیگری معتبر نیست.

---

# 11. Specification Value Validation Rules

---

Specification Value باید با Data Type تعریف‌شده در Specification Definition سازگار باشد.

نمونه

اگر نوع داده Number باشد،

ثبت مقدار متنی معتبر نیست.

---

اگر نوع داده Boolean باشد،

ثبت متن آزاد معتبر نیست.

---

اگر نوع داده Selection باشد،

مقدار باید یکی از گزینه‌های تعریف‌شده باشد.

---

# 12. Aggregate Validation

Aggregate تنها زمانی معتبر است که تمام قوانین این سند برقرار باشند.

اعتبار یک بخش از Aggregate برای معتبر بودن کل Aggregate کافی نیست.

---

# 13. Validation Responsibility

تمام Validationهای این سند مسئولیت Domain هستند.

UI می‌تواند اعتبارسنجی اولیه انجام دهد.

Application می‌تواند اعتبارسنجی اولیه انجام دهد.

اما اعتبارسنجی نهایی همیشه توسط Domain انجام می‌شود.

---

# 14. Failure Policy

در صورت نقض هر Validation Rule

هیچ تغییری در Aggregate اعمال نمی‌شود.

عملیات باید Fail شود.

Aggregate باید در وضعیت قبلی باقی بماند.

---

# 15. Summary

تمام قوانین این سند بخشی از Business Rules ماژول Catalog هستند.

این قوانین باید در تمام پیاده‌سازی‌های Domain بدون استثنا رعایت شوند.

هیچ لایه‌ای خارج از Domain مجاز به دور زدن این قوانین نیست.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

16-Ubiquitous-Language.md