# Catalog Domain Specification

Version: 1.0

Status: Business Validated

Document: 10-Order-Unit.md

Bounded Context: Catalog

---

# 1. Purpose

این سند مدل دامنه Order Unit را تعریف می‌کند.

هدف از Order Unit فراهم کردن امکان فروش یک Product با واحدهای مختلف است، بدون اینکه هویت Product تغییر کند.

Order Unit مشخص می‌کند مشتری با چه واحدهایی مجاز به سفارش محصول است.

این واحدها می‌توانند حداقل و حداکثر مقدار سفارش مستقل داشته باشند.

تمام محاسبات سفارش بر اساس Conversion Factor نسبت به Registration Unit انجام می‌شود.

---

# 2. Business Motivation

در دامنه فروش آهن، یک محصول ممکن است با واحدهای مختلف فروخته شود.

نمونه‌ها

یک میلگرد

- کیلوگرم
- شاخه
- بندیل

---

یک ورق

- کیلوگرم
- شیت

---

یک محصول فله‌ای

- کیلوگرم
- تن
- ظرفیت نیسان
- ظرفیت خاور
- ظرفیت تریلی

---

بنابراین واحد فروش بخشی از Product است و موجودیت مستقلی خارج از Product محسوب نمی‌شود.

---

# 3. Aggregate Ownership

Order Unit یک Child Entity در Product Aggregate است.

بدون Product وجود مستقل ندارد.

تمام تغییرات آن فقط از طریق Product انجام می‌شود.

هیچ Repository مستقلی برای Order Unit وجود ندارد.

---

# 4. Identity

هر Order Unit دارای شناسه داخلی است.

این شناسه فقط در محدوده همان Product معتبر است.

---

# 5. Responsibilities

Order Unit مسئول موارد زیر است.

- تعریف واحد سفارش
- تعریف ضریب تبدیل
- تعریف حداقل سفارش
- تعریف حداکثر سفارش
- مشخص کردن واحد پیش‌فرض سفارش

Order Unit مسئول قیمت نیست.

Order Unit مسئول موجودی نیست.

Order Unit مسئول محاسبه مالی نیست.

---

# 6. Unit

هر Order Unit دقیقاً یک واحد فروش را نمایش می‌دهد.

نمونه

کیلوگرم

تن

شاخه

بندیل

شیت

بسته

نیسان

خاور

تریلی

---

نوع واحد توسط Registration Unit تعیین نمی‌شود.

Order Unit فقط به Registration Unit تبدیل می‌شود.

---

# 7. Conversion Factor

هر Order Unit دارای Conversion Factor است.

Conversion Factor مقدار معادل یک واحد سفارش نسبت به Registration Unit را مشخص می‌کند.

---

نمونه

Registration Unit

کیلوگرم

---

Order Unit

تن

Conversion Factor

1000

---

Order Unit

نیسان

Conversion Factor

9000

---

Order Unit

شاخه

Conversion Factor

18.95

---

Order Unit

بندیل

Conversion Factor

2100

---

Conversion Factor همیشه عددی بزرگ‌تر از صفر است.

---

# 8. Registration Unit Relationship

تمام Order Unitها نسبت به Registration Unit تعریف می‌شوند.

نمونه

Registration Unit

کیلوگرم

↓

Order Unit

تن

↓

1000 کیلوگرم

---

Registration Unit

کیلوگرم

↓

Order Unit

نیسان

↓

9000 کیلوگرم

---

Registration Unit

کیلوگرم

↓

Order Unit

شاخه

↓

18.95 کیلوگرم

---

# 9. Minimum Order

هر Order Unit دارای حداقل مقدار سفارش است.

حداقل سفارش مستقل از سایر واحدها تعریف می‌شود.

نمونه

کیلوگرم

حداقل

500

---

شاخه

حداقل

5

---

نیسان

حداقل

1

---

# 10. Maximum Order

هر Order Unit دارای حداکثر مقدار سفارش است.

حداکثر سفارش مستقل از سایر واحدها تعریف می‌شود.

نمونه

کیلوگرم

حداکثر

30000

---

شاخه

حداکثر

300

---

نیسان

حداکثر

3

---

# 11. Default Order Unit

هر Product دقیقاً یک Default Order Unit دارد.

این واحد هنگام نمایش اولیه به کاربر انتخاب می‌شود.

وجود بیش از یک Default مجاز نیست.

---

# 12. Calculation Principle

Catalog قیمت را محاسبه نمی‌کند.

Catalog فقط اطلاعات لازم برای محاسبه را فراهم می‌کند.

Pricing می‌تواند با استفاده از Conversion Factor قیمت هر واحد سفارش را محاسبه کند.

نمونه

Price Base

68,500 تومان

بر حسب

کیلوگرم

---

Order Unit

نیسان

↓

9000

↓

Price = Base Price × Conversion Factor

---

Catalog هیچ اطلاعی از فرمول قیمت ندارد.

---

# 13. Aggregate Invariants

همیشه باید شرایط زیر برقرار باشند.

هر Order Unit دارای واحد است.

هر Order Unit دارای Conversion Factor است.

Conversion Factor باید بزرگ‌تر از صفر باشد.

حداقل سفارش نباید منفی باشد.

حداکثر سفارش نباید کمتر از حداقل سفارش باشد.

در هر Product فقط یک Default Order Unit وجود دارد.

تمام Conversion Factorها نسبت به Registration Unit تعریف می‌شوند.

---

# 14. Lifecycle

Order Unit فقط از طریق Product ایجاد می‌شود.

ویرایش Order Unit فقط از طریق Product انجام می‌شود.

حذف Order Unit فقط از طریق Product انجام می‌شود.

Order Unit به صورت مستقل ایجاد یا حذف نمی‌شود.

---

# 15. Summary

Order Unit امکان فروش یک Product با واحدهای مختلف را فراهم می‌کند.

این موجودیت مسئول تعریف واحد سفارش، ضریب تبدیل و محدودیت‌های سفارش است.

قیمت، موجودی و محاسبات مالی خارج از مسئولیت Order Unit هستند.

Order Unit تنها اطلاعات پایه لازم برای ماژول Pricing و Ordering را فراهم می‌کند.

---

# End Of Document

Status

Business Validated

Document Version

1.0

Next Document

11-Value-Objects.md