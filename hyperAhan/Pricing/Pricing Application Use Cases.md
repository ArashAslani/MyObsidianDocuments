# Pricing Application Use Cases

Version: 1.0

Status: Confirmed

Bounded Context: Pricing

Document Type: Application Layer Specification

Depends On

- Pricing Business Facts v1.0
- Pricing Domain Model v1.0
- Pricing Calculation Rules v1.0

---

# 1. Purpose

This document defines every Application Use Case of the Pricing module.

The Application Layer coordinates business operations.

It does not contain business rules.

Business rules remain inside the Domain.

The Application Layer is responsible for

- Receiving requests
- Loading Aggregates
- Calling Domain behaviors
- Persisting changes
- Returning results

---

# 2. Responsibilities

The Application Layer is responsible for

- Creating Prices
- Updating Prices
- Activating Prices
- Managing Price Tiers
- Expiring Prices
- Marking Prices as Obsolete
- Calculating Final Prices
- Returning Price History
- Returning Current Active Prices

The Application Layer never calculates business rules itself.

---

# 3. Create Base Price

Purpose

Register a new Base Price for a Product.

---

Input

Product Id

Base Price

Registration Unit

Validity Period

Optional Tier Definitions

---

Business Flow

Load Product information from Catalog.

↓

Validate Registration Unit.

↓

Create Price Aggregate.

↓

Persist Aggregate.

↓

Return Success.

---

Result

A new Active Price exists.

If another Active Price already existed,

it becomes Historical automatically.

---

# 4. Replace Active Price

Purpose

Replace the currently Active Price.

---

Input

Product Id

New Base Price

Validity Period

---

Business Flow

Load Active Price.

↓

Register new Price.

↓

Deactivate previous Price.

↓

Persist changes.

↓

Return Success.

---

Result

Exactly one Active Price remains.

---

# 5. Add Price Tier

Purpose

Add a Threshold rule to an existing Price.

---

Input

Price Id

Threshold

Threshold Unit

Base Price

---

Business Flow

Load Aggregate.

↓

Add Tier.

↓

Persist Aggregate.

↓

Return Success.

---

Result

Price now contains an additional Tier.

---

# 6. Remove Price Tier

Purpose

Remove an existing Tier.

---

Input

Price Id

Tier Id

---

Business Flow

Load Aggregate.

↓

Remove Tier.

↓

Persist Aggregate.

↓

Return Success.

---

Result

Tier no longer participates in price selection.

Historical Price data remains preserved.

---

# 7. Mark Price As Obsolete

Purpose

Exclude an incorrect Price from business reporting.

---

Input

Price Id

Reason

---

Business Flow

Load Aggregate.

↓

Mark Price as Obsolete.

↓

Persist Aggregate.

↓

Return Success.

---

Result

Price remains stored.

Price disappears from

Customer History

Reports

Charts
---

# 8. Expire Price

Purpose

End the validity of an Active Price.

---

Trigger

- Expiration Date is reached.
- Future scheduled expiration.
- Administrative action (when supported by business workflow).

---

Business Flow

Load Active Price.

↓

Verify Price exists.

↓

Expire Price.

↓

Persist Aggregate.

↓

Return Success.

---

Result

The Product remains visible in Catalog.

Pricing no longer returns a sellable Price.

Sales Status becomes

Contact Us.

---

# 9. Get Active Price

Purpose

Retrieve the currently Active Price for a Product.

---

Input

Product Id

Optional Order Unit

Optional Quantity

---

Business Flow

Locate Active Price.

↓

If no Active Price exists

↓

Return Contact Us.

↓

Otherwise

↓

Return Active Price information.

---

Result

Current Price information required for price calculation.

---

# 10. Calculate Final Price

Purpose

Calculate the payable amount for a customer request.

---

Input

Product Id

Selected Order Unit

Requested Quantity

Optional Engineering Calculation Result

---

Business Flow

Load Active Price.

↓

Resolve applicable Price Tier.

↓

Determine payable quantity.

↓

Calculate Subtotal.

↓

Apply VAT if enabled.

↓

Return Final Price.

---

Result

The customer receives the calculated payable amount.

The Final Price is never persisted.

---

# 11. Get Price History

Purpose

Retrieve historical Prices of a Product.

---

Input

Product Id

---

Business Flow

Load historical Prices.

↓

Exclude Obsolete Prices.

↓

Sort by activation date.

↓

Return history.

---

Result

Historical Prices available for

- Customer History
- Administrative Review

---

# 12. Get Price Trend

Purpose

Return historical Prices for chart visualization.

---

Input

Product Id

Date Range (Optional)

---

Business Flow

Load Price History.

↓

Exclude Obsolete Prices.

↓

Return chronological Price sequence.

---

Result

Price Trend data.

---

# 13. Validate Active Price

Purpose

Determine whether a Product currently has a valid sellable Price.

---

Input

Product Id

---

Business Flow

Load Active Price.

↓

Check Expiration.

↓

If expired

Return Contact Us.

↓

Otherwise

Return Valid.

---

Result

Sellable

or

Contact Us.

---

# 14. Get Pricing Configuration

Purpose

Return configuration affecting price calculation.

---

Returned Information

VAT Mode

VAT Enabled / Disabled

Default Expiration Policy

---

Result

Configuration used during price calculation.

---

# 15. Application Layer Rules

The Application Layer must follow the rules below.

---

## APP-001

Business rules must never be implemented inside Use Cases.

---

## APP-002

All business decisions must be delegated to the Price Aggregate.

---

## APP-003

Application Services coordinate workflows only.

---

## APP-004

The Application Layer never performs engineering calculations.

---

## APP-005

The Application Layer never performs quantity conversions.

Conversions are resolved using Catalog information consumed by the Domain.

---

## APP-006

The Application Layer never determines Price Tier selection.

Tier resolution belongs to the Domain.

---

## APP-007

The Application Layer never calculates VAT.

VAT behavior belongs to the Domain according to system configuration.

---

## APP-008

The Application Layer never stores Final Prices.

Final Prices are calculated and returned only.

---

# 16. Application Dependencies

The Pricing Application Layer interacts with the following modules.

---

## Catalog

Used for

- Product existence
- Registration Unit
- Order Units
- Conversion information

---

## Calculation

Used for

Engineering calculation results.

Examples

- Weight
- Area
- Length
- Volume

---

## Configuration

Used for

VAT configuration.

Default expiration policy.

---

## Persistence

Used for

Loading and saving the Price Aggregate.

---

# 17. Consistency Validation

Every Application Use Case defined in this document maps directly to one or more behaviors of the Price Aggregate.

The Application Layer introduces no additional business rules.

No business decision is duplicated outside the Domain.

No responsibility belonging to Catalog, Calculation, Inventory, or Order is implemented here.

---

# Document Status

Version

1.0

Status

Completed

Next Document

Pricing Domain Implementation Guide v1.0