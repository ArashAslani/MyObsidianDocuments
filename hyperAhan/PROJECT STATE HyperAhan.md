PROJECT STATE HyperAhan - Catalog and Pricing Modules, DDD Business Analysis Prepared as a resumption handoff. Any AI or analyst reading only this document, plus the accompanying Knowledge Base, Decision Log, and Ubiquitous Language documents, should be able to continue this project immediately without reading the original conversation transcript.

===========================================================

1. WORKING METHOD IN FORCE (must be preserved by whoever continues) ===========================================================

This project is run under a strict Senior DDD Architect / Business Analyst discipline, not a normal chat assistant mode. The following rules are currently active and must continue to be applied without exception:

- Reality over assumptions. Never invent business rules, workflows, entities, or technical decisions. Every conclusion must be traceable to explicit user statements or valid logical reasoning. Missing information must be stated as missing, never filled in from common industry practice unless explicitly requested.
- Every statement must be separated into: Confirmed Fact, Derived Conclusion, Unknown, Open Question. These are never merged.
- Contradictions, ambiguities, or duplicated concepts must stop the analysis and be raised as explicit questions before continuing.
- Business understanding must be complete before any architecture, database, API, or code decisions are made.
- No praise language, no filler, no markdown decoration in the analytical output style established so far (note: this current set of four deliverable documents uses structured headers for organization purposes, which is a reasonable exception for reference documents; the constraint against decorative/praise language in analytical prose remains in force).
- Phase discipline: Phase 1 (documentation collection) is CLOSED. Phase 2 (planning) is CLOSED and approved. Phase 3 (execution) is IN PROGRESS, one small step at a time, each step fully confirmed before moving to the next.
- Explicit regression checks are required before finalizing any step: every new fact must be checked against every previously confirmed fact; if a prior conclusion must change, this must be explicitly logged as "what changed / why it changed / which conclusion is now invalid" - never silently overwritten. Two such regressions have already occurred and are fully logged (see Section 4).
- Competitor-derived reference data (from the AhanOnline/MarkazAhan/EsfahanAhan documents) is NEVER auto-promoted to a business rule for Foolad Shahyar Sepand without explicit confirmation from the business owner. Exactly one such promotion has occurred so far (product domain scope breadth - see Section 4, Decision D-14 in the Decision Log).
- Current design scope is explicitly narrowed to the Catalog and Pricing modules only. Order, Discount, Authentication, Notification, and Blog modules exist in the overall system (a Modular Monolith with Clean Architecture) but are explicitly deferred to separate, later decisions. This narrowing was an explicit business owner instruction and remains in force for all subsequent steps.

# =========================================================== 2. DOCUMENT SET (what exists, and where authority lives)

Four reference documents together constitute the current state of this project:

1. This document (Project State) - where we are, what's next.
2. Project Knowledge Base (HyperAhan_Catalog_Pricing_KnowledgeBase.md) - full extraction of confirmed facts, business rules, invariants, terminology, unresolved questions, resolved contradictions, architectural decisions, and rejected assumptions, organized by domain.
3. Decision Log (HyperAhan_Decision_Log.md) - 31 individually logged decisions (D-01 through D-31), each with Decision ID, Decision, Reason, Business Evidence, Alternatives Considered, Confidence, Current Status. Includes two explicit regression/correction records (D-29 superseding an earlier price-lock conclusion, D-30 superseding an earlier factory-only price-keying conclusion).
4. Ubiquitous Language (HyperAhan_Ubiquitous_Language.md) - complete glossary of every business concept established so far, each with meaning, relationships, business owner (the business-side role authoritative for the concept), Bounded Context where already known, and synonyms. Includes one deliberately retained REJECTED concept (Price Lock) to prevent future re-introduction.

These four documents are the authoritative memory of this project. This Project State document does not repeat their full content; it summarizes structurally so a resuming agent knows where to look and what to do next.

# =========================================================== 3. SOURCE DOCUMENTS RECEIVED (Phase 1, CLOSED)

- Document A: Primary, authoritative internal business document from Foolad Shahyar Sepand (v1.0, 1405/05/01). The single source of truth for actual business rules. Contains its own explicitly flagged open ambiguities (freight formula, tier boundaries, an inventory-display contradiction later resolved, and a low-priority market-term list).
- Documents B, C, D, E, F, G (five separate documents in total, submitted across several messages): Competitor reference data extracted from AhanOnline, MarkazAhan, EsfahanAhan - covering category structures, weight-calculation formulas, unit tables, pricing display patterns, and calculator tool structures. Treated strictly as non-authoritative background knowledge UNLESS explicitly promoted by the business owner (this happened exactly once - product domain scope breadth).
- Business owner explicitly confirmed "documentation is complete" - this closed Phase 1 and authorized moving to Phase 2 (Planning).

# =========================================================== 4. PHASE 2: APPROVED EXECUTION PLAN (CLOSED, APPROVED, IN EXECUTION)

An 11-step plan was presented and explicitly approved by the business owner. The steps, and their current completion status, are:

1. Ubiquitous Language - COMPLETE. (Locked at Step 1; refined further at Steps 4-6 as new terms emerged - e.g., Price Entry, Conversion Factor, Simple/Complex Product were added during later steps and back-integrated into the glossary.)
2. Subdomains / Core vs. Supporting vs. Generic classification - COMPLETE. Catalog and Pricing = Core (explicitly confirmed by business owner). Order Management, Ancillary Cost, Invoice/Pre-invoice = Supporting (inferred, medium-high confidence, not separately re-verified word-for-word). Weight Calculator = Generic but downstream of Core Pricing engine.
3. Product aggregate modeling - COMPLETE. Product is a single generic aggregate (not one type per product family), carrying a category-dependent Specification. Confirmed directly by business owner given confirmed future expansion beyond steel (construction materials, nails, screws).
4. Unit / conversion model - COMPLETE. Four independent unit concepts (Registration, Sale, Pricing, Display), fixed-number-only Conversion Factor, explicit Simple-Product (single-unit) vs. Complex-Product (full model) distinction, Specification as typed-but-optional structure, invoice max-two-units display rule.
5. Sale-Mode vs. Inventory independence - COMPLETE. Resolved the original document's internal contradiction about out-of-stock display into a three-state, per-product admin setting, fully independent from the three-value Sale Mode attribute.
6. Pricing engine - COMPLETE. No price lock anywhere (live price through checkout, pre-invoice expiration on price change - this SUPERSEDED an earlier confirmed "price is locked" reading). Base price keyed by FULL Specification combination including but not limited to factory (this SUPERSEDED an earlier "per-factory-only" reading). Pricing Unit is itself a property of each individual Price Entry, not fixed per product. Two-tier formula system (Default vs. Custom), with the Custom Formula confirmed to NEVER compute final price directly - it always computes a quantity/multiplier that is then multiplied against the matching Price Entry. Tier Discount confirmed to be a fully independent layer applied on top of the Formula's final output.
7. Phone order process / admin manual order creation, modeled formally as a Business Process with Actors and Domain Events - NOT STARTED.
8. Ancillary costs, final detailed modeling - NOT STARTED (baseline facts already captured in Knowledge Base Section 3/4 and Ubiquitous Language, but no formal Step 8 pass has been executed).
9. Invoice / Pre-invoice modeling, including resolving the exact pre-invoice expiration trigger mechanics (immediate vs. checked-at-finalization) - NOT STARTED.
10. Bounded Contexts and Context Map - NOT STARTED. This step must formally resolve: (a) the relationship type between the two Core bounded contexts, Catalog and Pricing (expected to be a close/Partnership-style relationship rather than typical Core-to-Supporting distance, per the reasoning already logged in Decision D-16, but not yet formally drawn); (b) the Upstream(Pricing)/Downstream(Calculator) relationship for the shared Formula engine (per Decision D-18/D-20); (c) formal context boundaries for Order, Ancillary Cost, and Invoice even though their detailed internal modeling is deferred.
11. Final full-model review pass against all confirmed facts, plus a final list of remaining low-priority open items - NOT STARTED.

# =========================================================== 5. EXACTLY WHERE WE STOPPED

Steps 1 through 6 of the approved 11-step plan are COMPLETE, LOCKED, and have passed explicit regression checks (requested and performed twice during this project, after Step 4 and after Step 5, both confirming no unresolved conflicts among Steps 1-4 and Steps 1-5 respectively; Step 6 itself introduced two explicit, fully-logged regressions against earlier steps, not a regression failure - see Decision Log D-29 and D-30).

Immediately after Step 6 was locked, the business owner (via an instruction framed as a "Behavior Reset" / consistency-check request, and separately via explicit regression-check requests) did NOT provide new business content but instead requested four deliverable documents be produced from everything established so far:

1. Project Knowledge Base - DELIVERED.
2. Decision Log - DELIVERED.
3. Ubiquitous Language - DELIVERED.
4. This Project State document - IN PROGRESS (being delivered now).

No new business facts, rules, or decisions have been introduced since the close of Step 6. All four deliverables are extractions/reorganizations of Steps 1-6 content; they do not represent new analysis.

# =========================================================== 6. LOCKED DECISIONS (quick-reference index; full detail lives in the Decision Log)

All of the following are LOCKED (confirmed, not subject to re-litigation unless new business evidence emerges):

- D-01: Sale Mode, three values, required per product.
- D-02: Category 3 products appear in public catalog without price, with contact button.
- D-03: Admin can build orders from scratch (Path A/B support).
- D-04: Sale Unit is per-product, not globally fixed to kilograms.
- D-05: Ancillary cost (freight) as independent line item, manual entry currently, formula still open (OPEN-1).
- D-06: Size/thickness/length/grade/factory must be structured fields.
- D-07: Fabrik and Persi are independent products/variants, never a shared field.
- D-08: Other market slang terms stay in display name only, unless proven price-relevant.
- D-09: Tier discount is per-item, live/unlocked, never per-cart-total.
- D-10: VAT included in displayed price, no dual display, disclaimer text required.
- D-11: Price history is append-only; obsolete is a distinct state from deleted.
- D-12: Out-of-stock display is a per-product, three-state admin setting (hidden / tag-no-action / contact-button).
- D-13 / D-28: Sale Mode and Out-of-Stock Display are two independent Product-level fields, never merged.
- D-14: Product domain scope = competitor breadth + future non-steel expansion (explicitly confirmed, not inferred).
- D-15: Product is a single generic aggregate, not one type per product family.
- D-16: Catalog AND Pricing are both Core Domains (explicitly confirmed).
- D-17: Order, Ancillary Cost, Invoice are Supporting Domains (inferred, not separately re-verified).
- D-18: Weight Calculator is Generic but downstream of the Core Pricing Formula engine.
- D-19: Current design scope narrowed to Catalog + Pricing only; Modular Monolith, Clean Architecture confirmed as the overall system style.
- D-20: Admin-defined Custom Formula exists, reused by the public Calculator tool.
- D-21: Formula never computes final price directly, no exceptions identified; always feeds into a Price Entry lookup.
- D-22: Tier Discount is independent of Formula, applied on the Formula's final output.
- D-23: Four independent unit concepts confirmed (Registration/Sale/Pricing/Display).
- D-24: Conversion Factor is always a fixed stored number, never formula-derived.
- D-25: Simple, single-unit products are explicitly supported (box, count, carton, trailer, container).
- D-26: Specification is typed but optional-by-default.
- D-27: Invoice display allows max two units, usually one; catalog and invoice display units configured independently.
- D-29 (SUPERSEDES prior conclusion): NO price lock anywhere; live price through checkout; pre-invoice expires on price change per Terms Agreement.
- D-30 (SUPERSEDES prior conclusion): Base price ("Price Entry") is keyed by the FULL Specification combination, not factory alone.
- D-31: Pricing Unit is a property of each individual Price Entry, not fixed per product.

Two decisions carry lower confidence and are explicitly flagged as such (see Decision Log for full detail, not repeated here): D-17 (Supporting Domain classification, inferred) and the specific synthesis language of D-26 (typed-but-optional Specification, the analysis team's own reconciliation framing).

# =========================================================== 7. OPEN QUESTIONS NOT YET RESOLVED (do not assume answers; ask if needed)

- OPEN-1 (high priority per business owner): exact freight/ancillary-cost calculation formula (distance/weight/zone basis) - requires a dedicated future session with the business owner.
- OPEN-2 (high priority per business owner): exact tiered-discount boundary values (thresholds, percentages, per product family) - explicitly called out as competitive-advantage material requiring a dedicated session.
- OPEN-3 (low priority, explicitly deprioritized): complete list of market-slang terms beyond Fabrik/Persi/Loghme/Nimeh/Sabok-Sangin.
- OPEN-4 (low priority): whether Sale Mode is a strictly closed 3-value enum or extensible in the future.
- OPEN-5 (deferred to Step 9 by design): exact pre-invoice expiration trigger mechanics (immediate on price change vs. checked only at order finalization).
- OPEN-6 (deferred, low priority): exact mechanics of how Fabrik/Persi are represented as "independent variants" of the same generic Product aggregate (fully separate records vs. lightweight cross-reference).
- OPEN-7 (explicitly technical, deferred to technical design phase, NOT a domain-modeling question): Stimulsoft-based invoice/pre-invoice rendering pipeline (backend renders to image/base64, frontend renders/downloads); frontend fetching of calculator metadata (name, fields, types, formula) with a caching requirement. These were volunteered by the business owner as pre-existing technical decisions and explicitly logged rather than incorporated into domain modeling, per the "business before technology" rule.
- OPEN-8: Any question belonging to Steps 7-11 that has not yet been asked. These are NOT YET KNOWN because those steps have not been executed - a resuming agent must generate and ask these questions when reaching each step, not assume they are already answered.

# =========================================================== 8. CURRENT STEP

There is no active, in-progress analytical step right now. Step 6 is fully closed and locked. The most recent activity (production of the four reference documents) was a documentation/handoff task, not a new Phase 3 step, and is now also complete.

The project is in a PAUSED-BETWEEN-STEPS state, ready to resume at Step 7.

# =========================================================== 9. NEXT STEP

Step 7: Model the phone order process and admin-built manual order creation as a formal Business Process, with explicit Actors (Admin, Customer, System) and Domain Events.

Before beginning Step 7, the resuming agent should:

1. Re-state the working method (Section 1 of this document) to the business owner if there is any doubt it is still in force, OR simply continue applying it silently if context is already established (consistent with how Steps 2-6 proceeded without needing to re-explain the method every time).
2. Ground itself fully in Section 4 (Document A, Sections 2 and 7-2) and the corresponding Knowledge Base/Ubiquitous Language entries for Call Path A, Call Path B, Admin-Built Order, Payment Link, and Minimum Order Quantity before asking any new questions.
3. Identify what is genuinely still unknown about this process (e.g., exact Domain Events such as "OrderCreatedByAdmin," "PaymentLinkIssued," "OrderRecordedForReporting" are NOT yet confirmed by the business owner in this exact form - these are analytical hypotheses to propose and confirm, not facts to assume) before proposing a model.
4. Apply the same rigor as Steps 1-6: propose, cite evidence, list alternatives considered, flag anything uncertain, and stop to ask before locking anything that depends on an unconfirmed assumption.

# =========================================================== 10. REMAINING ROADMAP (Steps 7-11, in order)

Step 7 (next): Phone order process and admin manual order creation - Business Process, Actors, Domain Events. Must integrate Call Path A, Call Path B, Payment Link, and the "admin builds order from scratch" capability already confirmed in Steps 1-2.

Step 8: Ancillary cost, final detailed modeling. Must integrate the already-confirmed rule (independent line item, manual entry, possible fixed backend default) and explicitly flag that the calculation formula itself remains OPEN-1, not to be invented at this step.

Step 9: Invoice / Pre-invoice modeling. Must resolve OPEN-5 (exact pre-invoice expiration trigger timing) and formally model the Invoice's relationship to Price Entry, Ancillary Cost, VAT, and the max-two-units Display Unit rule (D-27).

Step 10: Bounded Contexts and Context Map. Must formally decide and diagram: Catalog-Pricing relationship type (expected Partnership given both are Core, per D-16 reasoning); Pricing-Calculator Upstream/Downstream relationship (per D-18/D-20); placement and relationship of Order, Ancillary Cost, and Invoice contexts relative to the two Core contexts, even though their internal detail is deferred; and explicit confirmation of which team/module owns which context if relevant to the business.

Step 11: Final full-model review pass. Must re-verify the entire model (all of Steps 1-10) against every confirmed fact for internal consistency (as was done via the two regression checks already performed after Steps 4 and 5, but now across the complete model), and produce a final, explicit list of remaining open items - distinguishing which are safe to leave open indefinitely (e.g., OPEN-3, OPEN-4, OPEN-6) versus which genuinely block moving into technical/architectural design (e.g., OPEN-1 and OPEN-2, both explicitly marked high priority by the business owner and both requiring a dedicated session before final commercial launch, even if they do not block further domain modeling in the meantime).

After Step 11 is complete and confirmed, Phase 3 (Execution) of the DDD/BA engagement, as originally scoped to the Catalog and Pricing modules, will be finished. Any move into technical architecture, database schema, or API design must not begin until Step 11 is explicitly confirmed complete by the business owner, per the working method's "business first, technology second" rule that has governed this entire engagement.

# =========================================================== END OF PROJECT STATE.