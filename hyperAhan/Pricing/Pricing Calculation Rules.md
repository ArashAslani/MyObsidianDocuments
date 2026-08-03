# Pricing Calculation Rules

Version: 1.0

Status: Confirmed

Bounded Context: Pricing

Document Type: Business Calculation Rules

Depends On

- Pricing Business Facts v1.0
- Pricing Domain Model v1.0

---

# 1. Purpose

هدف این سند تعریف فرآیند محاسبه مبلغ نهایی سفارش است.

این سند فقط منطق محاسبات قیمت را تعریف می‌کند.

در این سند

- Aggregate تعریف نمی‌شود.
- Entity تعریف نمی‌شود.
- API تعریف نمی‌شود.
- Database طراحی نمی‌شود.

تمام مطالب این سند مستقیماً از Business Rules استخراج شده‌اند.

---

# 2. Calculation Responsibility

Pricing مسئول محاسبه مبلغ نهایی سفارش است.

اما مسئول محاسبه مقادیر مهندسی نیست.

نمونه

Weight

Length

Area

Volume

این مقادیر توسط Calculation Module تولید می‌شوند.

Pricing فقط از خروجی آن استفاده می‌کند.

---

# 3. Calculation Inputs

برای محاسبه مبلغ نهایی، Pricing به اطلاعات زیر نیاز دارد.

## Product

محصول انتخاب‌شده توسط مشتری.

این اطلاعات از Catalog دریافت می‌شود.

---

## Registration Unit

واحد ثبت قیمت محصول.

همیشه متعلق به Catalog است.

Base Price همیشه بر اساس این واحد تعریف شده است.

---

## Order Unit

واحد انتخاب‌شده توسط مشتری.

نمونه

Kilogram

Branch

Bundle

Capacity

---

## Conversion Information

اطلاعات تبدیل واحدها.

نمونه

1 Branch = 7 Kilograms

1 Bundle = 1000 Kilograms

1 Capacity = 25 Bundles

این اطلاعات متعلق به Catalog هستند.

---

## Calculated Quantity

مقداری که باید در Base Price ضرب شود.

این مقدار ممکن است مستقیماً توسط مشتری وارد شده باشد.

یا توسط Calculation Module محاسبه شده باشد.

---

## Base Price

قیمت پایه فعال Product.

همیشه بر اساس Registration Unit ثبت شده است.

---

## Quantity Threshold Rules

قوانین مربوط به Price Tier.

در صورت وجود

Base Price مناسب را انتخاب می‌کنند.

---

## VAT Configuration

تنظیمات فعال بودن یا نبودن VAT.

---

# 4. General Calculation Pipeline

تمام محاسبات قیمت باید مطابق ترتیب زیر انجام شوند.

Step 1

Receive Product

↓

Step 2

Determine Order Unit

↓

Step 3

Determine Quantity

↓

Step 4

If Engineering Calculation Is Required

↓

Receive Calculated Quantity

↓

Else

Use Ordered Quantity

↓

Step 5

Resolve Applicable Price Tier

↓

Step 6

Resolve Active Base Price

↓

Step 7

Calculate Subtotal

↓

Step 8

Apply VAT (If Enabled)

↓

Step 9

Return Final Price

این ترتیب نباید تغییر کند.
---

# 5. Determining the Calculation Quantity

Before any pricing calculation begins,

Pricing must determine the quantity that will be multiplied by the Base Price.

The quantity depends on the Product type.

---

## Case 1

Direct Quantity Products

These Products do not require engineering calculations.

Examples

- Nail
- Bolt
- Nut

Customer Input

↓

Quantity

↓

Pricing

↓

Uses Quantity Directly

Example

Product

Nail

Registration Unit

Piece

Customer Order

10 Pieces

Calculated Quantity

10 Pieces

No Calculation Module is involved.

---

## Case 2

Engineering Calculation Products

These Products require engineering calculations before pricing.

Examples

- Profile
- Box Profile
- Steel Sections
- Any future Product requiring engineering formulas

Customer enters Product dimensions.

The Calculation Module calculates the payable quantity.

Pricing never performs these calculations.

Pricing only receives the calculated result.

Example

Customer enters

Width

Height

Thickness

Length

Quantity

↓

Calculation Module

↓

Calculated Weight

↓

Pricing

↓

Uses Calculated Weight

---

# 6. Registration Unit Principle

This is the most important pricing rule.

Every Product has exactly one Registration Unit.

Examples

Rebar

↓

Kilogram

---

Nail

↓

Piece

---

Steel Pipe

↓

Kilogram

---

Every Base Price is defined using this Registration Unit.

No exceptions exist.

Changing the Order Unit never changes the Registration Unit.

Changing the Order Unit never changes the Base Price.

---

Example

Industrial Box Profile

Registration Unit

Kilogram

Base Price

115,000 Toman

Customer purchases

Bundle

↓

The payable quantity is converted to Kilograms.

↓

Pricing still uses

115,000 Toman per Kilogram.

---

# 7. Quantity Conversion

If the selected Order Unit differs from the Registration Unit,

the payable quantity must first be converted.

Pricing never owns conversion rules.

Pricing consumes conversion information from Catalog.

---

Example

Registration Unit

Kilogram

Order Unit

Branch

Conversion

1 Branch = 7 Kilograms

Customer Order

20 Branches

↓

Converted Quantity

140 Kilograms

↓

Pricing continues using

140 Kilograms

---

Example

Registration Unit

Kilogram

Order Unit

Bundle

Conversion

1 Bundle = 1000 Kilograms

Customer Order

3 Bundles

↓

Converted Quantity

3000 Kilograms

---

The converted quantity becomes the payable quantity.

---

# 8. Price Tier Resolution

After the payable quantity has been determined,

Pricing evaluates all configured Threshold rules.

The purpose is to determine which Base Price should be used.

No calculation is performed at this stage.

Only Tier selection.

---

Example

Tier A

Less than 143 Branches

↓

115,000 Toman

Tier B

143 Branches or More

↓

114,000 Toman

Customer

143 Branches

↓

Tier B selected

↓

Base Price

114,000 Toman

---

No part of the order uses Tier A.

The selected Tier applies to the entire order.

---

# 9. Subtotal Calculation

Once the applicable Base Price has been selected,

Pricing calculates the Subtotal.

General Formula

Subtotal

=

Payable Quantity

×

Resolved Base Price

---

Example

Calculated Weight

1000 Kilograms

Resolved Base Price

114,000

Subtotal

1000 × 114,000

---

Example

Pieces

15

Price Per Piece

12,000

Subtotal

15 × 12,000

---

Pricing performs only multiplication.

Engineering calculations have already finished.

---

# 10. VAT Calculation

VAT is optional.

Pricing behavior depends on system configuration.

---

## Mode A

VAT Enabled

Administrator stores

Base Price

Without VAT

↓

Subtotal calculated

↓

VAT calculated

↓

Final Price returned

---

## Mode B

VAT Disabled

Administrator stores

Base Price

Already Including VAT

↓

Subtotal calculated

↓

Returned directly

No VAT calculation occurs.

---

Pricing never applies VAT twice.
---

# 11. Final Price Calculation

After the Subtotal has been calculated,

Pricing determines the Final Price.

The Final Price is the value returned to the Shopping Cart, Order module, and Product Detail page.

General Flow

Resolved Base Price

↓

Determine Payable Quantity

↓

Calculate Subtotal

↓

Apply VAT (If Enabled)

↓

Return Final Price

The Final Price is never stored permanently.

It is always calculated at runtime.

---

# 12. Contact Us Behavior

Pricing is responsible for determining whether an active sellable price exists.

If no valid Active Price exists,

the Product remains visible.

The Product remains searchable.

The Product remains accessible.

Instead of returning a sellable price,

Pricing returns

Sales Status

↓

Contact Us

The Product is not removed from Catalog.

The Product is not hidden.

Only the ability to calculate a sellable price is unavailable.

---

# 13. Price History Behavior

Every Base Price that has ever become Active must remain preserved.

Price History is immutable.

Historical records are never edited to represent new Prices.

Whenever a new Base Price becomes Active,

the previous Active Price becomes Historical.

Historical Prices are used for

- Administrative review
- Customer price history
- Price trend charts

No historical Price is physically removed.

---

# 14. Obsolete Price Behavior

An Obsolete Price represents incorrect business data.

Example

Administrator intended to enter

150,000 Toman

but entered

15,000 Toman

instead.

After discovering the mistake,

the Price becomes Obsolete.

The Price remains stored.

The Price does not participate in

- Customer History
- Reports
- Charts

The purpose is preserving auditability while protecting analytical correctness.

---

# 15. Active Price Replacement

Only one Active Price may exist for a Product.

When a new Base Price is registered,

the system performs the following sequence.

Current Active Price

↓

Historical

↓

New Price

↓

Active

This transition is automatic.

The Administrator never manually deactivates the previous Active Price.

---

# 16. Calculation Examples

## Example 1

Simple Product

Product

Bolt

Registration Unit

Piece

Base Price

8 Toman

Customer Order

50 Pieces

Subtotal

50 × 8

↓

400 Toman

If VAT is enabled,

VAT is applied.

Otherwise,

400 Toman becomes the Final Price.

---

## Example 2

Weight-Based Product

Product

Rebar

Registration Unit

Kilogram

Base Price

105,910 Toman

Customer Order

850 Kilograms

Subtotal

850 × 105,910

VAT behavior depends on system configuration.

---

## Example 3

Engineering Calculation

Product

Industrial Profile

Registration Unit

Kilogram

Customer enters

Width

Height

Thickness

Length

Quantity

↓

Calculation Module

↓

Calculated Weight

↓

1,248 Kilograms

↓

Pricing

↓

Base Price

114,000 Toman

↓

Subtotal

1,248 × 114,000

↓

VAT (Optional)

↓

Final Price

Pricing never executes the engineering formula.

---

## Example 4

Threshold-Based Pricing

Product

Industrial Box Profile

Registration Unit

Kilogram

Tier A

Less than 143 Branches

↓

115,000 Toman

Tier B

143 Branches or More

↓

114,000 Toman

Customer Order

143 Branches

↓

Applicable Tier

Tier B

↓

Converted Quantity

1001 Kilograms

↓

Subtotal

1001 × 114,000

Tier A is never used.

The selected Tier applies to the entire order.

---

## Example 5

Bundle Purchase

Registration Unit

Kilogram

Order Unit

Bundle

Conversion

1 Bundle = 1000 Kilograms

Customer Order

2 Bundles

↓

Converted Quantity

2000 Kilograms

↓

Resolved Base Price

↓

Subtotal

2000 × Base Price

---

# 17. Calculation Invariants

The following rules must always remain true.

---

## INV-CAL-001

Pricing never executes engineering formulas.

---

## INV-CAL-002

Pricing never owns engineering formulas.

---

## INV-CAL-003

Every Base Price belongs to the Product Registration Unit.

---

## INV-CAL-004

Every calculation uses exactly one Active Base Price.

---

## INV-CAL-005

Threshold selection occurs before price calculation.

---

## INV-CAL-006

The selected Price Tier applies to the entire order.

---

## INV-CAL-007

The order is never divided between multiple Price Tiers.

---

## INV-CAL-008

VAT is applied only when the system configuration enables it.

---

## INV-CAL-009

Final Price is never stored.

It is always calculated when requested.

---

## INV-CAL-010

Conversion to the Registration Unit must be completed before applying the Base Price.

---

# 18. Consistency Validation

This document contains only the calculation behavior of the Pricing Bounded Context.

No engineering formulas are defined here.

No Catalog behavior is defined here.

No Order behavior is defined here.

No Inventory behavior is defined here.

The document depends entirely on the confirmed Business Facts and Pricing Domain Model.

It serves as the implementation specification for the Pricing calculation engine.

---

# Document Status

Version

1.0

Status

Completed

Next Document

Pricing Application Use Cases v1.0