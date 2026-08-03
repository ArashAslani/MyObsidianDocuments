# Pricing Business Discovery

Version: 0.1

Status: In Progress

Bounded Context: Pricing

---

# 1. Purpose

هدف این سند جمع‌آوری تمام حقایق کسب‌وکار مربوط به ماژول Pricing است.

در این مرحله هیچ طراحی فنی انجام نمی‌شود.

هیچ Aggregate، Entity، Value Object یا ساختار پیاده‌سازی تعریف نمی‌شود.

این سند فقط حقایق کسب‌وکار، ابهامات و پرسش‌های باز را ثبت می‌کند.

---

# 2. Confirmed Facts

## PF-001

Catalog مسئول نگهداری قیمت نیست.

Pricing یک Bounded Context مستقل است.

Status: Confirmed

Source: Business Decision

---

## PF-002

هر Product دارای یک قیمت پایه است.

Status: Confirmed

Source: Business Discussion

---

## PF-003

قیمت پایه بر اساس Registration Unit محصول تعریف می‌شود.

نمونه

- کیلوگرم
- شاخه
- بسته

Status: Confirmed

---

## PF-004

Product می‌تواند چند Order Unit داشته باشد.

Pricing باید بتواند قیمت قابل خرید هر Order Unit را محاسبه کند.

Status: Confirmed

---

## PF-005

Conversion Factor در Catalog نگهداری می‌شود.

Pricing فقط از آن برای محاسبه استفاده می‌کند.

Status: Confirmed

---

## PF-006

محاسبه قیمت واحدهای فروش نباید داخل Catalog انجام شود.

Status: Confirmed

---

## PF-007

Catalog فقط اطلاعات پایه محصول را نگهداری می‌کند.

Pricing مالک اطلاعات قیمت است.

Status: Confirmed

---

# 3. Unknown

در حال حاضر موارد زیر هنوز مشخص نشده‌اند.

---

## U-001

آیا هر Product در هر لحظه فقط یک قیمت فعال دارد؟

Status: Unknown

---

## U-002

آیا قیمت دارای تاریخ شروع و پایان اعتبار است؟

Status: Unknown

---

## U-003

آیا تاریخچه قیمت‌ها باید نگهداری شود؟

Status: Unknown

---

## U-004

آیا قیمت می‌تواند زمان‌بندی‌شده باشد؟

Status: Unknown

---

## U-005

آیا قیمت برای مشتریان مختلف می‌تواند متفاوت باشد؟

Status: Unknown

---

## U-006

آیا تخفیف بخشی از Pricing است یا ماژول مستقل؟

Status: Unknown

---

## U-007

آیا هزینه حمل بخشی از Pricing است؟

Status: Unknown

---

## U-008

آیا مالیات داخل Pricing محاسبه می‌شود؟

Status: Unknown

---

## U-009

آیا قیمت می‌تواند بر اساس محل بارگیری متفاوت باشد؟

Status: Unknown

---

## U-010

آیا امکان ثبت قیمت آینده وجود دارد؟

Status: Unknown

---

# 4. Open Questions

## Question 1

قیمت دقیقاً متعلق به چیست؟

Product؟

یا ترکیبی از Product و اطلاعات دیگر؟

---

## Question 2

آیا برای یک Product می‌توان همزمان چند قیمت فعال داشت؟

---

## Question 3

تعریف «قیمت پایه» از دید کسب‌وکار چیست؟

---

## Question 4

آیا کاربر قیمت را مستقیماً وارد می‌کند یا سیستم آن را محاسبه می‌کند؟

---

## Question 5

چه عواملی باعث ایجاد یک قیمت جدید می‌شوند؟

---

## Question 6

چه عواملی باعث منقضی شدن یک قیمت می‌شوند؟

---

## Question 7

آیا تغییر قیمت باید روی سفارش‌های قبلی اثر بگذارد یا خیر؟

---

## Question 8

قیمت نمایش‌داده‌شده به مشتری شامل چه مواردی است؟

فقط قیمت پایه؟

یا مالیات؟

یا سایر هزینه‌ها؟

---

# Current Status

Business Discovery

In Progress

No Domain Modeling Started.