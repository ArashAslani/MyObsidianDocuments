# Pricing Domain Implementation Guide

Version: 1.0

Status: Confirmed

Bounded Context: Pricing

Document Type: Domain Implementation Guide

Depends On

- Pricing Business Facts v1.0
- Pricing Domain Model v1.0
- Pricing Calculation Rules v1.0
- Pricing Application Use Cases v1.0

---

# 1. Purpose

This document explains how the Pricing Domain should be implemented.

This is an implementation guide.

It is not a business document.

It is not an architectural proposal.

No additional business rules may be introduced during implementation.

The implementation must strictly follow the confirmed business documents.

---

# 2. Implementation Principles

The implementation must follow Domain Driven Design.

Business rules belong only to the Domain Layer.

Application Layer coordinates workflows.

Infrastructure persists data.

Presentation displays results.

No business rule may be duplicated.

---

# 3. Aggregate

The implementation contains exactly one Aggregate Root.

Aggregate Root

Price

All modifications to pricing data must go through this Aggregate.

No external component may directly modify internal pricing state.

---

# 4. Aggregate Responsibilities

The Aggregate is responsible for

- Maintaining Base Price
- Managing Price Lifecycle
- Managing Active Price
- Managing Price History
- Managing Price Tiers
- Selecting the applicable Tier
- Preventing multiple Active Prices
- Preserving business consistency

The Aggregate is not responsible for

- Product management
- Engineering calculations
- Unit conversion definitions
- Inventory
- Orders

---

# 5. Domain Layer Responsibilities

The Domain Layer owns

- Business Rules
- Invariants
- Validation
- State Transitions
- Aggregate Behaviors
- Value Object validation

Nothing else.

---

# 6. Application Layer Responsibilities

Application Layer

Loads Aggregate.

Calls Aggregate behaviors.

Persists Aggregate.

Returns Result.

Application Layer never performs business decisions.

---

# 7. Infrastructure Responsibilities

Infrastructure owns

Repositories

Persistence

EF Core Mapping

Transactions

Database configuration

Infrastructure never contains business rules.

---

# 8. Catalog Dependency

Pricing never owns Product information.

Pricing references Product.

Product remains inside Catalog.

Pricing consumes

Product Id

Registration Unit

Order Units

Conversion Information

Nothing else.

---

# 9. Calculation Dependency

Pricing never calculates

Weight

Area

Length

Volume

Engineering formulas

Pricing consumes only calculation results.

The Calculation module remains the single source of engineering calculations.
---

# 10. Aggregate Encapsulation

The internal state of the Price Aggregate must never be modified directly.

All state changes must occur through explicit Domain behaviors.

Examples

Correct

Register Price

Replace Active Price

Expire Price

Mark As Obsolete

Add Tier

Remove Tier

Resolve Tier

Resolve Base Price

Incorrect

Changing entity properties directly from

- Application Layer
- Repository
- Controller
- Infrastructure

The Aggregate must always protect its own invariants.

---

# 11. Value Object Implementation

Every Value Object must be immutable.

After creation,

its state cannot change.

If a business value changes,

a new Value Object must be created.

Examples include

- Base Price
- Threshold
- Validity Period
- VAT Configuration Snapshot

No Value Object has an identity.

Two Value Objects with identical values are considered equal.

---

# 12. Domain Validation

Every validation related to business rules belongs inside the Domain.

Examples

Exactly one Active Price.

Registration Unit compatibility.

Threshold consistency.

Price Lifecycle rules.

Historical preservation.

Expiration behavior.

Obsolete behavior.

The Application Layer must never duplicate these validations.

---

# 13. State Transitions

The implementation must explicitly support the business lifecycle of a Price.

Possible business states include

Active

Historical

Expired

Obsolete

Transitions between states must only occur through Aggregate behaviors.

State transitions must always preserve the business invariants defined in the Domain Model.

---

# 14. Price History

Historical Prices are immutable.

Once a Price leaves the Active state,

its business information must never be modified.

Historical records exist only for

- Audit
- Customer Price History
- Administrative Review
- Trend Analysis

A Historical Price never becomes Active again.

---

# 15. Tier Resolution

Tier selection is a Domain responsibility.

The implementation must guarantee

Exactly one applicable Tier.

The selected Tier applies to the entire order.

The implementation must never split a calculation across multiple Tiers.

Tier selection must occur before price calculation begins.

---

# 16. Final Price Calculation

The Domain calculates the Final Price.

The Domain does not persist the Final Price.

The Domain returns the calculated result.

Every calculation begins from

Resolved Base Price

×

Payable Quantity

VAT is applied only according to the active system configuration.

---

# 17. Repository Responsibilities

Repositories are responsible only for persistence.

Repositories must

Load Price Aggregate.

Persist Price Aggregate.

Load historical Prices.

Load Active Price.

Repositories must never

Calculate Prices.

Resolve Tier selection.

Apply VAT.

Perform engineering calculations.

Execute business decisions.

---

# 18. Transaction Boundary

Every command that modifies the Aggregate must execute inside a single transaction.

Examples

Register Price.

Replace Active Price.

Expire Price.

Mark Price As Obsolete.

Add Tier.

Remove Tier.

The transaction must guarantee that business invariants remain valid after completion.

---

# 19. Error Handling

Business rule violations must be reported as Domain failures.

Examples

Attempting to violate Aggregate invariants.

Attempting to register an invalid Price.

Attempting to create inconsistent Tier definitions.

Infrastructure failures

(Database, Network, Storage)

must not be represented as business failures.

Business failures and technical failures must remain separate.

---

# 20. Dependency Rules

Pricing may depend on abstractions.

Pricing must never depend on concrete implementations of

Catalog

Calculation

Infrastructure

Persistence

External services

Dependencies must always point inward toward the Domain.

---

# 21. Testing Strategy

Domain tests should focus on business behavior.

Examples

Registering a new Price replaces the previous Active Price.

Expired Prices produce "Contact Us".

Threshold selection chooses the correct Tier.

VAT behavior changes according to configuration.

Historical Prices remain preserved.

Obsolete Prices disappear from reports while remaining stored.

Engineering calculations are never executed by Pricing.

Tests should verify business outcomes rather than implementation details.

---

# 22. Implementation Constraints

During implementation

Do not introduce additional business concepts.

Do not merge Pricing responsibilities into Catalog.

Do not move engineering calculations into Pricing.

Do not store Final Prices.

Do not create multiple Active Prices.

Do not bypass Aggregate behaviors.

Do not duplicate business rules across layers.

The implementation must remain consistent with the previously approved business documents.

---

# 23. Implementation Readiness Checklist

Before implementation begins, verify the following.

✓ Pricing Business Facts completed.

✓ Pricing Domain Model completed.

✓ Pricing Calculation Rules completed.

✓ Pricing Application Use Cases completed.

✓ Aggregate boundaries defined.

✓ Domain invariants defined.

✓ Responsibilities separated.

✓ External dependencies identified.

✓ No unresolved business ambiguity remains.

If any business ambiguity exists,

implementation must stop until the business rule is clarified.

---

# 24. Consistency Validation

This document introduces no new business rules.

Every implementation instruction is derived from the approved Pricing documentation.

The purpose of this guide is to ensure that every implementation of the Pricing module follows the same Domain Model and preserves the same business behavior.

---

# Document Status

Version

1.0

Status

Completed

Next Document

Pricing Implementation Prompt v1.0