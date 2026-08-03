# Calculation Engine Business Facts

Version: 1.0

Status: Confirmed

Bounded Context: Calculation Engine

Document Type: Business Facts

---

# 1. Purpose

Calculation Engine is responsible for executing engineering calculation formulas.

It is the only bounded context that owns engineering formulas.

The Calculation Engine calculates engineering values.

It does not calculate prices.

It does not own products.

It does not own orders.

It does not own inventory.

---

# 2. Business Responsibility

The Calculation Engine is responsible for

- Selecting the correct Formula Type.
- Executing engineering formulas.
- Validating calculation inputs.
- Returning calculation results.

The Calculation Engine is not responsible for

- Pricing.
- Product management.
- Unit management.
- Shopping cart.
- Orders.
- Inventory.

---

# 3. Business Goal

The purpose of the Calculation Engine is

To calculate engineering values required by the business.

These values may later be used by

- Pricing
- Public Calculation Tools

---

# 4. Consumers

The Calculation Engine is consumed by

Pricing

and

Public Website Tools.

No other consumer has been confirmed.

---

# 5. Formula Ownership

Every engineering formula belongs to the Calculation Engine.

No engineering formula belongs to

Catalog

Pricing

Order

Inventory

---

# 6. Formula Selection

Every Product may reference one Formula Type.

The Product itself does not contain the engineering formula.

The Product only identifies which Formula Type should be executed.

Formula execution belongs exclusively to the Calculation Engine.

---

# 7. Default Formula

The Calculation Engine provides one Default Formula Type.

If a Product does not reference any Formula Type,

the Default Formula Type shall be executed.

---

# 8. Default Formula Behavior

The Default Formula performs no engineering calculation.

The calculation result equals the quantity entered by the customer.

No weight calculation occurs.

No area calculation occurs.

No length calculation occurs.

No volume calculation occurs.

The output equals the input quantity.

---

# 9. Calculation Result

The Calculation Engine returns only the calculated engineering result.

The returned value may later be consumed by Pricing.

The Calculation Engine does not calculate monetary values.

---

# 10. Pricing Independence

The Calculation Engine has no knowledge of

Base Price

VAT

Price Tier

Price History

Subtotal

Final Price

Pricing remains completely independent.

---

# 11. Formula Types

The Calculation Engine organizes engineering calculations using Formula Types.

Formula Types identify the engineering calculation that must be executed.

Formula Types do not belong to Catalog.

Formula Types belong exclusively to the Calculation Engine.

Products reference Formula Types.

The Calculation Engine owns their behavior.

---

# 12. Product Relationship

A Product may reference one Formula Type.

The Product does not know

- the engineering formula,
- the calculation algorithm,
- the mathematical implementation.

The Product only identifies the required Formula Type.

---

# 13. Formula Execution

Formula execution always follows the same business workflow.

Receive Product.

↓

Determine Formula Type.

↓

If no Formula Type exists

↓

Use Default Formula.

↓

Validate Inputs.

↓

Execute Formula.

↓

Return Calculation Result.

The workflow is identical for every Product.

Only the Formula Type changes.

---

# 14. Engineering Inputs

Different Formula Types require different engineering inputs.

Examples include

- Width
- Height
- Thickness
- Diameter
- Length
- Quantity

The required inputs depend on the selected Formula Type.

The business has not defined a universal input set.

---

# 15. Formula Independence

Every Formula Type is independent.

Changing one Formula Type must not affect any other Formula Type.

Each Formula Type owns its own engineering calculation behavior.

---

# 16. Public Calculation Tools

The Calculation Engine supports independent engineering calculation tools.

Users may access these tools without creating an Order.

Users may calculate engineering values without purchasing Products.

The Calculation Engine behaves identically whether invoked by

- Pricing
- Public Website Tools

---

# 17. Reusability

A Formula Type may be reused by multiple Products.

Multiple Products may reference the same Formula Type.

The engineering calculation is implemented once.

Business reuse occurs through Formula Type references.

---

# 18. Business Consistency

The same engineering inputs must always produce the same engineering result.

Calculation behavior must be deterministic.

The Calculation Engine must not produce different engineering results for identical inputs.

---

# 19. Unit Responsibility

The Calculation Engine consumes engineering input values.

The definition of Product Units belongs to Catalog.

The Calculation Engine does not own Product Registration Units.

The Calculation Engine does not own Pricing Units.

The Calculation Engine only consumes the engineering values required by the selected Formula Type.

---

# 20. Extensibility

New engineering calculations may be introduced by defining new Formula Types.

Existing Products continue using their assigned Formula Types.

Introducing a new Formula Type must not require modification of existing Formula Types.

---

# 21. Business Invariants

The following rules must always remain true.

---

## INV-CALC-001

Engineering formulas exist only inside the Calculation Engine.

---

## INV-CALC-002

Pricing never executes engineering formulas.

---

## INV-CALC-003

Catalog never owns engineering formulas.

---

## INV-CALC-004

Every calculation executes exactly one Formula Type.

---

## INV-CALC-005

If no Formula Type is assigned,

the Default Formula Type must be executed.

---

## INV-CALC-006

The Default Formula performs no engineering calculation.

Its output equals the quantity entered by the customer.

---

## INV-CALC-007

Calculation results never contain pricing information.

---

## INV-CALC-008

Formula Types are reusable.

Multiple Products may reference the same Formula Type.

---

## INV-CALC-009

Formula execution must be deterministic.

Identical inputs always produce identical outputs.

---

## INV-CALC-010

The Calculation Engine is the single source of truth for engineering calculations.

---

# 22. Consistency Validation

This document defines only confirmed business facts.

No engineering formulas are defined here.

No implementation details are defined here.

No architecture is defined here.

Every statement represents confirmed business knowledge collected for the Calculation Engine bounded context.

---

# Document Status

Version

1.0

Status

Completed

Next Document

Calculation Engine Domain Model v1.0