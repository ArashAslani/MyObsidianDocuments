# Pricing Business Facts

Version: 1.0

Status: Confirmed

Bounded Context: Pricing

Document Type: Business Discovery

---

# 1. Purpose

هدف این سند ثبت تمام قواعد کسب‌وکار مربوط به قیمت‌گذاری در HyperAhan است.

این سند فقط شامل حقایق کسب‌وکار تأییدشده است.

در این سند:

- هیچ طراحی نرم‌افزاری انجام نمی‌شود.
- هیچ Aggregate تعریف نمی‌شود.
- هیچ Entity تعریف نمی‌شود.
- هیچ Value Object تعریف نمی‌شود.
- هیچ تصمیم پیاده‌سازی گرفته نمی‌شود.

تمام اطلاعات این سند مستقیماً از گفتگوهای انجام‌شده استخراج شده‌اند و هیچ قانون جدیدی به آن اضافه نشده است.

---

# 2. Scope

این سند فقط رفتارهای کسب‌وکار مربوط به قیمت‌گذاری را پوشش می‌دهد.

مسئولیت‌های این ماژول شامل موارد زیر است:

- نگهداری قیمت پایه محصولات
- مدیریت چرخه عمر قیمت
- اعمال قوانین قیمت‌گذاری
- مدیریت قیمت‌های پلکانی
- اعمال مالیات بر ارزش افزوده (اختیاری)
- محاسبه مبلغ نهایی سفارش

این ماژول مسئول هیچ‌یک از موارد زیر نیست:

- مدیریت محصولات
- مدیریت دسته‌بندی‌ها
- نگهداری Specification محصولات
- محاسبه وزن
- محاسبات مهندسی
- مدیریت سفارش
- مدیریت موجودی

---

# 3. Bounded Context

Pricing یک Bounded Context مستقل است.

Pricing مالک اطلاعات قیمت است.

Catalog مالک اطلاعات محصول است.

Calculation مسئول انجام محاسبات مهندسی است.

Order مسئول ایجاد سفارش است.

Pricing فقط با استفاده از اطلاعات این ماژول‌ها مبلغ نهایی را محاسبه می‌کند.

---

# 4. Relationship With Other Contexts

## Catalog

Catalog اطلاعات زیر را در اختیار Pricing قرار می‌دهد.

- Product
- Registration Unit
- Order Units
- Conversion Factors
- Specifications

Pricing مالک هیچ‌یک از این اطلاعات نیست.

---

## Calculation

Calculation مسئول انجام تمام محاسبات مهندسی است.

نمونه

- محاسبه وزن
- محاسبه متراژ
- محاسبه حجم
- سایر فرمول‌های مهندسی

Pricing فرمول‌ها را نگهداری نمی‌کند.

Pricing فقط نتیجه محاسبه را دریافت می‌کند.

---

## Order

Order از Pricing مبلغ نهایی را دریافت می‌کند.

Pricing سفارش ایجاد نمی‌کند.

Pricing وضعیت سفارش را مدیریت نمی‌کند.

---

# 5. Business Terminology

## Product

محصولی که در Catalog تعریف شده است.

Pricing هیچ محصولی ایجاد نمی‌کند.

---

## Base Price

قیمت پایه‌ای که مدیر سیستم برای یک Product ثبت می‌کند.

این قیمت همیشه بر اساس Registration Unit محصول تعریف می‌شود.

---

## Registration Unit

واحد اصلی ثبت قیمت محصول.

نمونه

- Kilogram
- Piece
- Branch

تمام قیمت‌های پایه بر اساس این واحد ثبت می‌شوند.

هیچ استثنایی وجود ندارد.

---

## Order Unit

واحدی که مشتری با آن خرید انجام می‌دهد.

نمونه

- Kilogram
- Branch
- Bundle
- Capacity

Order Unit ممکن است با Registration Unit یکسان یا متفاوت باشد.

---

## Quantity Threshold

حداقل مقداری که باعث انتخاب یک Price Tier خاص می‌شود.

Threshold توسط مدیر سیستم تعریف می‌شود.

Threshold بخشی از Product نیست.

Threshold بخشی از قوانین قیمت‌گذاری است.

---

## Quantity Tier

سطح قیمت وابسته به مقدار سفارش.

نمونه

کمتر از 143 شاخه

↓

115,000 تومان

143 شاخه یا بیشتر

↓

114,000 تومان

---

## VAT

مالیات بر ارزش افزوده.

اعمال آن در سیستم اختیاری است.

---

## Price History

تاریخچه تمام قیمت‌هایی که قبلاً فعال بوده‌اند.

---

## Obsolete Price

قیمتی که به اشتباه توسط مدیر ثبت شده است.

این قیمت حذف نمی‌شود.

اما در گزارش‌ها و نمودارها نمایش داده نمی‌شود.

---

# 6. Confirmed Business Facts

## PF-001

Catalog مالک اطلاعات قیمت نیست.

Pricing مالک اطلاعات قیمت است.

---

## PF-002

هر Product دارای قیمت پایه است.

---

## PF-003

قیمت پایه همیشه بر اساس Registration Unit تعریف می‌شود.

نمونه

میلگرد

↓

Kilogram

↓

قیمت پایه بر اساس Kilogram

نمونه

میخ

↓

Piece

↓

قیمت پایه بر اساس Piece

هیچ استثنایی وجود ندارد.

---

## PF-004

مدیر سیستم هنگام ثبت قیمت ابتدا Product را انتخاب می‌کند.

محصول از Catalog انتخاب می‌شود.

Pricing محصول جدید ایجاد نمی‌کند.

---

## PF-005

پس از انتخاب Product،

مدیر Registration Unit مربوط به همان Product را مشاهده می‌کند.

قیمت پایه بر اساس همان واحد ثبت می‌شود.

---

## PF-006

Pricing فقط قیمت پایه را نگهداری می‌کند.

مبلغ سفارش ذخیره نمی‌شود.

مبلغ سفارش هنگام محاسبه سفارش تولید می‌شود.

---

## PF-007

هر Product ممکن است چند Order Unit داشته باشد.

نمونه

قوطی صنعتی

Branch

Bundle

Capacity

---

## PF-008

هر Order Unit دارای Conversion مشخص نسبت به Registration Unit است.

نمونه

1 Branch = 7 kg

1 Bundle = 1000 kg

1 Capacity = 25 Bundle

این اطلاعات متعلق به Catalog هستند.

---

## PF-009

برخی محصولات دارای Price Tier هستند.

برخی محصولات فقط یک قیمت دارند.

وجود Price Tier اختیاری است.

---

## PF-010

Threshold توسط مدیر سیستم تعریف می‌شود.

Threshold از Product استخراج نمی‌شود.

Threshold از Registration Unit استخراج نمی‌شود.

---

## PF-011

مدیر هنگام تعریف Price Tier،

واحد Threshold را نیز مشخص می‌کند.

نمونه

143 Branch

یا

1000 Kilogram
---

## PF-012

Quantity-based pricing is Threshold Based.

It is not Progressive Pricing.

When the ordered quantity reaches a configured Threshold, the selected Price Tier applies to the entire order.

Example

Industrial Box Profile

Price Tier A

Less than 143 Branches

↓

115,000 Toman per Kilogram

Price Tier B

143 Branches or more

↓

114,000 Toman per Kilogram

Customer Order

143 Branches

The system first determines that the Threshold has been satisfied.

Then the second Base Price is selected.

The entire order is calculated using 114,000 Toman.

The order is never split across multiple pricing tiers.

---

## PF-013

Pricing never calculates engineering values.

Pricing consumes calculated engineering values.

Examples include

- Weight
- Length
- Area
- Volume

These values are provided by the Calculation module.

---

## PF-014

Some Products require engineering calculations before pricing.

Example

Profile

The customer enters

- Width
- Height
- Thickness
- Length
- Quantity

The Calculation module calculates the approximate weight.

Pricing receives the calculated weight.

Pricing multiplies the calculated weight by the Base Price.

The engineering formula itself is not part of Pricing.

---

## PF-015

The Calculation module is also available as an independent feature of HyperAhan.

Customers can use the engineering calculators without purchasing products.

Examples

- Weight Calculator
- Profile Calculator
- Pipe Calculator

Pricing uses the same calculation capability.

---

## PF-016

Not every Product requires engineering calculations.

Some Products are priced directly using the ordered quantity.

Examples

- Nail
- Bolt
- Nut

For these Products

Final Price

=

Ordered Quantity

×

Base Price

No engineering calculation is required.

---

## PF-017

The Base Price is always entered by the Administrator.

Customers never enter prices.

Pricing never calculates a Base Price automatically.

---

## PF-018

A Product may support one or multiple Order Units.

The available Order Units depend on the Product.

Examples

Industrial Box Profile

- Branch
- Bundle
- Capacity

Column Box Profile

- Branch

Z Profile

- Kilogram
- Branch

---

## PF-019

Changing the selected Order Unit does not change the Base Price.

The Base Price always remains defined using the Registration Unit.

Changing the Order Unit only changes the quantity used during price calculation.

---

## PF-020

Conversion between Order Units and Registration Units is performed using Conversion information defined in Catalog.

Pricing does not define Conversion values.

Pricing only consumes them.

---

## PF-021

The system supports optional VAT calculation.

VAT is a configurable system behavior.

---

## PF-022

Two operating modes exist for VAT.

Mode One

The Administrator enters Base Prices excluding VAT.

The system calculates VAT during Final Price calculation.

Mode Two

The Administrator enters Base Prices already including VAT.

In this mode the VAT calculation is disabled.

The system does not add VAT again.

---

## PF-023

VAT behavior is controlled by system configuration.

Administrators choose which operating mode is active.

The Pricing business rules remain unchanged.

---

## PF-024

Every Product can have only one Active Base Price.

Multiple Active Base Prices for the same Product are not allowed.

---

## PF-025

When a new Base Price becomes Active,

the previously Active Base Price automatically leaves the Active state.

The previous Price is preserved as historical information.

Administrators do not manually deactivate previous Prices.

The system performs this transition automatically.

---

## PF-026

Every Price change must be preserved.

Price History is a business requirement.

Historical Prices are used for

- Administrative review
- Customer Price History
- Price Trend Charts

Historical information must remain available.

---

## PF-027

A Price may have an Expiration Date.

The Expiration Date is optional.

If no Expiration Date is specified,

the system automatically considers the Price valid for one week from its activation.

---

## PF-028

When a Price expires,

the Product remains available in Catalog.

The Product is not removed.

The Product is not unpublished.

Instead, its sales status changes to **"Contact Us"**.

Customers can still view the Product.

Customers can no longer see an active sellable price.

---

## PF-029

Prices are never physically deleted.

All Prices remain stored for auditing purposes.

Historical data must always remain recoverable.

---

## PF-030

A Price may become Obsolete.

An Obsolete Price represents a Price that was entered incorrectly by an Administrator.

An Obsolete Price is not considered part of the valid pricing history.

---

## PF-031

Obsolete Prices are excluded from customer-facing Price History.

Customers must never see incorrect Prices.

---

## PF-032

Obsolete Prices are excluded from analytical reports.

Reports must only use valid historical Prices.

---

## PF-033

Obsolete Prices are excluded from Price Trend Charts.

Incorrect Prices must not affect business analytics.

---

## PF-034

Although Obsolete Prices are excluded from reports and charts,

they remain stored in the system for auditing purposes.

The system never physically removes them.

---

## PF-035

Price Expiration and Price Obsolescence are two different business concepts.

Expiration means

The Price was valid.

Its validity period has ended.

Obsolescence means

The Price was entered incorrectly.

It should not participate in business reporting.

These two concepts must never be treated as the same business state.

---

## PF-036

The Final Price is always calculated at runtime.

The system never stores calculated Final Prices as pricing data.

Only the information required to calculate the Final Price is stored.

---

## PF-037

The Base Price is always defined using the Product Registration Unit.

There are no exceptions.

Examples

Rebar

Registration Unit

Kilogram

↓

Base Price is entered per Kilogram.

---

Nail

Registration Unit

Piece

↓

Base Price is entered per Piece.

---

Industrial Box Profile

Registration Unit

Kilogram

↓

Base Price is entered per Kilogram.

Even if customers purchase using

- Branch
- Bundle
- Capacity

the Base Price always remains defined using the Registration Unit.

---

# 7. Business Scenarios

This section records confirmed business scenarios extracted directly from business discussions.

These scenarios are not implementation examples.

They describe real business behavior.

---

## Scenario 1

Simple Quantity-Based Product

Product

Nail

Registration Unit

Piece

Base Price

12,000 Toman per Piece

Customer Order

10 Pieces

Final Price

10 × 12,000

No engineering calculation is required.

---

## Scenario 2

Weight-Based Product

Product

Rebar 14

Registration Unit

Kilogram

Base Price

105,910 Toman per Kilogram

Customer specifies the required quantity.

The payable amount is determined using the ordered weight multiplied by the Base Price.

---

## Scenario 3

Engineering Calculation Before Pricing

Product

Industrial Profile

Registration Unit

Kilogram

Customer enters

- Width
- Height
- Thickness
- Length
- Quantity

The Calculation module calculates the approximate weight.

Pricing receives the calculated weight.

Pricing multiplies the calculated weight by the Base Price.

Pricing never executes the engineering formula itself.

---

## Scenario 4

Threshold-Based Pricing

Product

Industrial Box Profile

Registration Unit

Kilogram

Order Unit

Branch

Pricing Rules

Less than 143 Branches

↓

115,000 Toman per Kilogram

143 Branches or more

↓

114,000 Toman per Kilogram

Customer selects

143 Branches

The system selects the second Price Tier.

The entire calculated quantity is priced using

114,000 Toman per Kilogram.

The order is never split between multiple pricing tiers.

---

## Scenario 5

Bundle Purchase

Product

Industrial Box Profile

Registration Unit

Kilogram

Order Unit

Bundle

Conversion

1 Bundle = 1000 Kilograms

Configured Pricing Rule

143 Branches or more

↓

114,000 Toman

When the customer purchases using Bundle,

the applicable Price Tier is determined according to the configured Threshold rules.

After selecting the applicable Base Price,

the payable amount is calculated using the Registration Unit.

```

---

## Scenario 6

VAT Enabled

System Configuration

VAT Calculation

Enabled

Administrator enters

Base Price

100,000 Toman per Kilogram

The stored Base Price excludes VAT.

During price calculation,

the system calculates VAT according to the configured VAT percentage.

The customer sees the Final Price including VAT.

---

## Scenario 7

VAT Disabled

System Configuration

VAT Calculation

Disabled

Administrator enters

Base Price

110,000 Toman per Kilogram

The entered Base Price already includes VAT.

The Pricing module does not calculate VAT again.

The entered Base Price is used directly during price calculation.

---

## Scenario 8

Price Replacement

Product

Rebar 14

Current Active Price

105,000 Toman

Administrator registers

108,000 Toman

The new Price becomes Active.

The previous Active Price automatically leaves the Active state.

The previous Price is preserved in Price History.

No manual action is required from the Administrator.

---

## Scenario 9

Expired Price

Product

Rebar 14

Expiration Date

Reached

The Product remains available inside Catalog.

The Product remains searchable.

The Product page remains accessible.

Instead of displaying a sellable Price,

the Product displays

"Contact Us"

The customer can still request a quotation.

---

## Scenario 10

Obsolete Price

Administrator accidentally enters

15,000 Toman

instead of

150,000 Toman

The mistake is identified.

The incorrect Price becomes Obsolete.

The Price remains stored for auditing purposes.

The Obsolete Price is excluded from

- Customer Price History
- Administrative Reports
- Price Trend Charts

The incorrect Price is never physically deleted.

---

# 8. Business Invariants

The following business rules are mandatory.

Violating any of these rules means the Pricing model is inconsistent with the agreed business domain.

---

## INV-001

Every Base Price belongs to exactly one Product.

---

## INV-002

Every Base Price is defined using the Product Registration Unit.

There are no exceptions.

---

## INV-003

A Product can have only one Active Base Price.

---

## INV-004

Registering a new Active Price automatically deactivates the previous Active Price.

---

## INV-005

Historical Prices must always remain preserved.

---

## INV-006

Prices are never physically deleted.

---

## INV-007

Obsolete Prices remain stored but are excluded from customer-facing history, reports and charts.

---

## INV-008

Expired Prices and Obsolete Prices represent different business concepts.

They must never be treated as the same state.

---

## INV-009

Threshold values are defined by the Administrator.

Threshold values are never derived from Product information.

---

## INV-010

Threshold evaluation applies one Price Tier to the entire order.

Orders are never divided across multiple Price Tiers.

---

## INV-011

Pricing never performs engineering calculations.

Pricing consumes engineering calculation results produced by the Calculation module.

---

## INV-012

Changing the Order Unit never changes the Base Price.

Only the calculated quantity changes.

---

## INV-013

VAT calculation is controlled by system configuration.

The business behavior changes according to the selected operating mode.

---

# 9. Out of Scope

The following responsibilities are explicitly outside the Pricing Bounded Context.

---

## Product Management

Creating Products.

Updating Products.

Deleting Products.

Category Management.

Factory Management.

Specification Management.

These responsibilities belong to Catalog.

---

## Engineering Calculations

Weight Calculation.

Length Calculation.

Area Calculation.

Volume Calculation.

Steel Engineering Formulas.

These responsibilities belong to the Calculation module.

---

## Order Management

Shopping Cart.

Checkout.

Order Creation.

Order Approval.

Order Workflow.

These responsibilities belong to the Order module.

---

## Inventory Management

Warehouse Stock.

Inventory Reservation.

Inventory Availability.

These responsibilities belong to the Inventory module.

---

# 10. Consistency Validation

This document was produced exclusively from confirmed business discussions.

No business rule has been inferred.

No software design decisions have been introduced.

No implementation assumptions have been added.

No Domain Model concepts have been defined.

This document represents the agreed business behavior of the Pricing Bounded Context and serves as the only input for the next document:

**Pricing Domain Model v1.0**

---

# Document Status

Version

1.0

Status

Completed

Next Document

Pricing Domain Model v1.0