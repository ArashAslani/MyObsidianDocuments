# Pricing Implementation Prompt

Version: 1.0

Status: Final

Target Audience

AI Software Engineering Agents

Examples

- Grok
- Claude Code
- Cursor
- Codex
- Gemini CLI

---

# Objective

Implement the Pricing Bounded Context exactly according to the provided documentation.

The implementation must strictly follow the approved business documents.

Do not redesign the business.

Do not simplify the domain.

Do not introduce additional assumptions.

If any required information is missing,

implementation must stop and request clarification.

Never invent business rules.

---

# Source Documents

Implementation order

1.

Pricing Business Facts

↓

2.

Pricing Domain Model

↓

3.

Pricing Calculation Rules

↓

4.

Pricing Application Use Cases

↓

5.

Pricing Domain Implementation Guide

Only these documents define the Pricing domain.

No additional interpretation is allowed.

---

# Implementation Goal

Implement a production-ready Pricing module.

The implementation must be

DDD compliant

Testable

Maintainable

Framework independent

Business driven

Infrastructure independent

---

# Required Architecture

Use Domain Driven Design.

Separate layers clearly.

Domain

↓

Application

↓

Infrastructure

↓

Presentation

Business rules belong only to Domain.

---

# Domain Requirements

Implement

Aggregate Root

Price

Child Entity

Price Tier

Value Objects

As defined in the Domain Model.

Protect Aggregate consistency.

Never expose mutable Aggregate state.

---

# Business Rules

Implement every Business Fact.

Implement every Invariant.

Implement every Domain Behavior.

No business rule may be omitted.

No business rule may be replaced.

---

# Aggregate Rules

Guarantee

Only one Active Price.

Automatic replacement of previous Active Price.

Historical preservation.

Obsolete handling.

Expiration handling.

Threshold-based Tier selection.

Registration Unit pricing.

---

# Calculation Rules

Implement the pricing pipeline exactly.

Receive input.

↓

Determine payable quantity.

↓

Resolve Tier.

↓

Resolve Base Price.

↓

Calculate Subtotal.

↓

Apply VAT (optional).

↓

Return Final Price.

Never change this sequence.

---

# Engineering Calculations

Do not implement engineering formulas.

Pricing consumes engineering calculation results.

Weight calculations belong to the Calculation module.

Area calculations belong to the Calculation module.

Length calculations belong to the Calculation module.

Volume calculations belong to the Calculation module.

Pricing must never execute engineering formulas.

---

# Catalog Dependency

Do not duplicate Product information.

Consume only

Product Id

Registration Unit

Order Units

Conversion information

Catalog remains the owner of Product data.

---

# Application Layer

Application Layer coordinates workflows only.

Do not place business logic inside

Commands

Queries

Handlers

Controllers

Repositories

---

# Infrastructure Layer

Infrastructure owns

Persistence

Repositories

EF Core Mapping

Transactions

Nothing else.

No business rules.

---

# Repository Rules

Repositories may

Load Aggregate.

Save Aggregate.

Load Active Price.

Load Historical Prices.

Repositories may never

Calculate Prices.

Apply VAT.

Resolve Tier.

Execute engineering calculations.

Make business decisions.

---

# Database

The database schema must reflect the Domain Model.

Do not redesign the Aggregate because of relational database limitations.

Persistence follows the Domain.

The Domain never follows persistence.

---

# Validation

Business validation belongs only to Domain.

Infrastructure validation

Database connectivity

Storage failures

Network failures

must remain outside Domain.

---

# Error Handling

Separate

Business failures

from

Technical failures.

Never mix them.

---

# Testing Requirements

Produce comprehensive automated tests.

At minimum verify

Exactly one Active Price.

Historical preservation.

Tier selection.

Threshold behavior.

VAT behavior.

Expiration.

Obsolete handling.

Contact Us behavior.

Registration Unit pricing.

Final Price calculation.

Engineering calculation isolation.

---

# Forbidden Actions

Never

Invent business rules.

Move Product ownership into Pricing.

Move engineering calculations into Pricing.

Duplicate Catalog data.

Store Final Price.

Create multiple Active Prices.

Bypass Aggregate behaviors.

Split orders across multiple Price Tiers.

Ignore Price History.

Delete historical Prices.

Delete obsolete Prices.

Assume missing business behavior.

---

# Implementation Order

Step 1

Implement Value Objects.

↓

Step 2

Implement Child Entities.

↓

Step 3

Implement Price Aggregate.

↓

Step 4

Implement Domain Behaviors.

↓

Step 5

Implement Repositories.

↓

Step 6

Implement Application Layer.

↓

Step 7

Implement Persistence.

↓

Step 8

Implement Unit Tests.

↓

Step 9

Implement Integration Tests.

↓

Step 10

Perform Business Validation.

---

# Quality Requirements

The implementation must

Compile successfully.

Follow Clean Architecture.

Follow Domain Driven Design.

Preserve Aggregate consistency.

Protect business invariants.

Remain framework independent inside Domain.

Be production ready.

---

# Completion Criteria

Implementation is complete only if

Every Business Fact is implemented.

Every Invariant is enforced.

Every Domain Behavior exists.

Every Application Use Case is implemented.

All tests pass.

No forbidden action exists.

No undocumented business behavior exists.

No business assumptions exist.

The implemented module is fully consistent with the approved Pricing documentation.

---

# Final Instruction

When uncertainty exists,

do not guess.

Stop implementation.

Identify the missing business rule.

Request clarification.

Business correctness has higher priority than implementation speed.