PROJECT KNOWLEDGE BASE HyperAhan - Catalog and Pricing Modules Business Domain Analysis - Full Extraction Status: Phase 3 in progress, through Step 6 of 11. Steps 7-11 not yet executed. Scope lock: Only Catalog and Pricing modules are in active design scope. Order, Discount, Auth, Notification, Blog modules exist in the overall system but are deferred to later, separate decisions. System is a Modular Monolith, Clean Architecture.

===========================================================

1. SOURCE DOCUMENTS OVERVIEW ===========================================================

Document A (primary, authoritative): Internal business document from Foolad Shahyar Sepand (steel trading company). Version 1.0, dated 1405/05/01. This is the ONLY document that represents the actual business. It explicitly flags open ambiguities.

Documents B-F (competitor reference data): Extracted catalog/pricing/weight-calculator structures from AhanOnline, MarkazAhan, EsfahanAhan. These are NOT business rules for Foolad Shahyar Sepand by default. They only became authoritative facts when the business owner explicitly confirmed adoption of a specific point (see Section 3, item on product domain scope).

Rule applied throughout: competitor data = reference knowledge only, never auto-promoted to business rule without explicit confirmation.

# =========================================================== 2. TERMINOLOGY (Ubiquitous Language)

- Sale Mode (نحوه فروش): A property of a product (or product category) with three known values:
    
    1. Fully online - price shown, "add to cart" available, no phone contact needed.
    2. Semi-custom (e.g., I-beam) - default standard size is fully online/purchasable; a custom variation requires phone contact.
    3. Fully custom/contact-only (e.g., wall-post, bolts) - price is not predetermined; requires phone call. Can still appear in public catalog without a price and with a "contact expert" button. Open minor question (unresolved, low priority): whether Sale Mode is a closed 3-value enum or extensible in the future.
- Inventory Status (وضعیت موجودی): Independent of Sale Mode. Represents real-time stock availability. Confirmed to be a per-product configurable setting (see Section 5).
    
- Base Size (سایز پایه): For semi-custom products like I-beam, the standard size (e.g., 12-meter bar) on which default online pricing is based. Any deviation from Base Size is custom and requires phone contact.
    
- Call Path A / Call Path B (مسیر الف / مسیر ب): Two paths for phone-based orders.
    
    - Path A: Admin builds the order in the system and issues a payment link/notification to the customer.
    - Path B: Entire transaction is manual/offline (agreement and payment outside the system); admin later enters the order into the system only for reporting/statistics.
- Fabrik / Persi (فابریک / پرسی): Two fully independent products/variants, NOT a shared field on one product. Confirmed explicitly.
    
- Market slang terms - سبک/سنگین (light/heavy), نیمه (half), لقمه (cut piece): Currently stored only in the product's display name, not as structured fields, unless a direct, formulable effect on price is proven later. لقمه (Loghme) specifically is a derived/cut product from a raw product, not a new raw material.
    
- Tiered/Volume Pricing (قیمت‌گذاری پلکانی): Discount based on purchase volume (weight or quantity). Calculated per-item (not per-cart). Live/unlocked - recalculates automatically as cart quantity changes. Exact tier boundaries are unresolved and explicitly called out by the business owner as "part of our creativity and competitive advantage."
    
- Base Market Price: The confirmed-final term is: there is NO price lock anywhere in the system (see Section 7, this superseded an earlier reading of Document A). Price is always read live from backend up through checkout.
    
- Obsoleted Price Record (رکورد قیمت منسوخ): A price history record can be marked obsolete so it's excluded from reporting, but it is NEVER deleted or edited. Append-only history.
    
- Ancillary Cost (هزینه جانبی): E.g., freight/shipping cost. Recorded as an independent line item in the order/invoice, never folded into the product price. Currently manual entry by admin only (no automatic formula yet); backend may return a default fixed number.
    
- Registration Unit / Sale Unit / Display Unit / Pricing Unit (واحد ثبت / واحد فروش / واحد نمایش / واحد قیمت‌گذاری): Four independent unit concepts (see Section 6). A fixed numeric Conversion Factor relates them where needed.
    
- Formula (فرمول قیمت‌گذاری اختصاصی): An admin-defined formula, per product, that computes the effective quantity/multiplier from the product's Specification values. This same formula engine also powers the public-facing weight calculator toolbox. Confirmed: the formula NEVER outputs final price directly - see Section 7.
    
- Price Entry (working name, not finalized): The pricing record tied to one specific full combination of a product's Specification values (including but not limited to factory/brand). Formerly assumed to be keyed only by factory; corrected in Step 6 (see Section 9).
    

# =========================================================== 3. CONFIRMED BUSINESS FACTS - CATALOG DOMAIN

3.1 Product classification by sale behavior (Document A, Section 1) Three categories of how steel products are sold:

- Category 1: Standard products, fully online (rebar, sheet, angle iron, channel, profile, etc.). Price shown directly, cart + online payment supported.
- Category 2: Semi-custom (e.g., I-beam). Standard base size has default online price and is purchasable online. Custom variation (different length, special cut) requires phone contact. CONFIRMED: base/standard size is priced and sold online; only deviations from standard size require a phone call. This pattern (base size online, custom variant phone-only) applies at minimum to I-beam and is treated as a general pattern for any product with a recognized standard/base size.
- Category 3: Fully custom (e.g., wall-post, bolts). Made from raw materials plus ancillary costs (welding, bending, labor). Price is not predetermined; fully phone/order dependent. These products DO appear in the public catalog, but without price and with a "contact expert" button (this directly overturned an earlier statement in the same document that said these products don't necessarily need public catalog display - the later statement supersedes it and is the confirmed rule).

Design implication (confirmed): Every product (or product category) must carry a Sale Mode attribute determining whether the UI shows "add to cart," "contact/request quote," or is admin-panel-only with no public catalog listing.

3.2 Product domain scope (CONFIRMED, upgraded from inference to fact)

- Current focus: iron/steel products.
- Future expansion (explicitly planned by business owner): construction materials, nails, screws, and similar - i.e., NOT limited to steel.
- Business owner explicitly stated twice: expects the system to register and price a wide variety of products.
- CONCLUSION (confirmed, high confidence): Product domain breadth = same breadth as competitors (AhanOnline/MarkazAhan/EsfahanAhan reference catalogs), PLUS future non-steel categories. This is the one place where competitor reference data was explicitly promoted to a confirmed business fact for Foolad Shahyar Sepand.

Competitor reference catalog breadth (context data, now relevant because scope was confirmed to match it):

- Long products: rebar (simple, ribbed/A2-A3-A4, coil), I-beam (I, H/INP, rail), profile/square-rectangular tube, angle iron, channel, wire products (wire, mesh, rabitz).
- Flat products: hot-rolled sheet (black, A516, A283, ST52, wear-resistant, ribbed, pickled, slab), cold-rolled (oiled), coated sheet (galvanized, colored, stainless), forming sheet (corrugated, shadoline, trapezoidal, sinusoidal, tile), punched sheet, strip, square bar, grating.
- Intermediate/raw: billet, slab, bloom, sponge iron, scrap.
- Pipe: seamless (manesman), seamed (black pipe, galvanized pipe, furniture pipe).

3.3 Fabrik/Persi and other market terms (Document A, Section 6)

- Fabrik and Persi MUST be two fully separate products/variants in the catalog, not one shared field.
- Other market terms (light/heavy, half, cut-piece) currently go in display name only; upgrade to structured field only if proven to have a direct, formulable price effect.
- Low-priority open item: complete list of all similar market terms is not yet known; business owner confirmed this is low priority and will be completed later.

3.4 Structured (price-making) variables (Document A, Section 6) Size, thickness, length, grade, and manufacturing factory MUST be structured fields (not free text), because they are the direct basis for pricing and filter/search.

3.5 Inventory/catalog display logic (RESOLVED CONTRADICTION - see Section 10)

- Original Document A statement (ambiguous/self-contradictory): "Site should only show what is truly purchasable online; out-of-stock products should not appear at all in the catalog" vs. "or at admin's discretion can be shown with a contact-expert action."
- FINAL RESOLUTION (confirmed): This is an admin-configurable, PER-PRODUCT setting, not a system-wide fixed policy. Three possible states for an out-of-stock product:
    1. Fully hidden from catalog.
    2. Shown with an "out of stock" tag, with NO action button at all (neither add-to-cart nor contact).
    3. Shown with a "contact expert" button.
- Business rationale for state 3 (confirmed): allows admin to consult the customer, offer a substitute product, or get a quote from another supplier and relay it to the customer.
- Business rationale for state 2: some out-of-stock products simply have no action at all.
- Sale Mode and Inventory Status/display-on-out-of-stock are CONFIRMED to be two fully independent attributes on the product. The final displayed behavior in the catalog is the combined result of both independent fields; they must not be collapsed into a single field. A "contact" action can originate from EITHER a fully-custom Sale Mode OR from the independent out-of-stock display setting - two different fields can produce the same visible UI action.

3.6 Aggregate/modeling decision for Product (CONFIRMED, Step 3)

- Product is modeled as a SINGLE, GENERIC aggregate type, not one aggregate type per product family (not one class for rebar, another for sheet, another for profile, etc.).
- Reasoning (confirmed by business owner): explicit, repeated statements that the system must support registering diverse products, plus explicit confirmation that future expansion goes beyond steel into construction materials/nails/screws. A per-family aggregate type approach would require code changes/migrations for every new product family, directly conflicting with this requirement.
- Each Product carries a structured, category-dependent Specification (see Section 6.6 for exact shape decision).
- Fabrik and Persi are modeled as two independent instances/variants of this same generic Product aggregate (exact variant-vs-independent-instance mechanics deferred, not yet finalized - open item, low priority, does not block progress).
- Every Product has (at minimum) these two independent top-level attributes, confirmed in Step 5: (a) Sale Mode, (b) Out-of-stock display behavior (one of the three states in 3.5). These are general Product-level fields, NOT part of the category-dependent Specification.

# =========================================================== 4. CONFIRMED BUSINESS FACTS - ORDER PROCESS (SUPPORTING DOMAIN, OUT OF CURRENT DESIGN SCOPE BUT DOCUMENTED)

Note: Order module is explicitly deferred from current design focus (business owner instruction), but the following facts were established before that scope-narrowing instruction and remain valid business knowledge for whenever that module is designed.

4.1 Phone order process (Document A, Section 2)

1. Customer calls and states request.
2. Admin determines final price (including any ancillary costs).
3. Two possible paths:
    - Path A: Admin builds the entire order in the system; customer receives a link/notification to log in, view order and price, and pay.
    - Path B: Entire transaction is phone-based and manual (agreement, payment outside system); admin enters the order into the system afterward only for reporting/statistics.
4. General principle: whatever CAN be automated should be done on the website. Phone path is used only after negative feedback or when the process is genuinely too complex. Online orders must ultimately be recorded in the same order-management system (not a separate, disconnected path).

Design implication (confirmed): Admin panel must be able to build an order FROM SCRATCH (not just manage customer-submitted orders) - including product selection, manual pricing (outside standard tiers if needed), adding ancillary costs, and issuing a payment link.

4.2 Minimum order quantity (Document A, Section 7-2) Some products have a minimum sellable quantity (by weight or count) - below a certain threshold, the product cannot be ordered at all.

# =========================================================== 5. CONFIRMED BUSINESS FACTS - PRICING DOMAIN (CORE)

5.1 VAT / Tax (Document A, Section 8) - CONFIRMED, no contradictions

- All customer-facing prices INCLUDE 10% VAT.
- Two prices (with/without VAT) are NEVER shown side by side.
- Backend stores the base price WITHOUT VAT; VAT is applied only at final calculation.
- A fixed disclaimer text must appear on all relevant pages: "All quoted prices include 10% VAT and there are no other additional costs."
- Confirmed: this matches competitor behavior (no contradiction, just corroboration).

5.2 Price history (Document A, Section 9)

- Every price change is a new record (append-only). Prior records are NEVER deleted or edited.
- System always uses the latest record for display/calculation.
- Admins can view historical price reports.
- Price records CAN be marked obsolete so they are excluded from reports (obsolete != deleted; these are two distinct states and must be modeled separately).

5.3 Tiered/volume pricing (Document A, Section 7) - mechanism confirmed, exact tier values NOT yet confirmed

- Unit price depends on purchase volume (weight or count): smaller purchase = higher unit price, larger = lower.
- Tiers CAN be defined separately per product family, but system must also have a global default tier that can be overridden per category by admin.
- Tier calculation is PER-ITEM (per order line), NOT per-cart-total. If a customer buys two different products each below the wholesale threshold individually, neither gets the wholesale discount even if their sum would exceed it.
- If the customer increases order quantity in the cart (e.g., crossing from retail tier to wholesale tier), unit price updates LIVE and automatically to the new tier rate.
- CONFIRMED (Step 6): Tier discount boundaries/rules are defined completely INDEPENDENTLY of the main pricing Formula. When an admin defines a discount and applies it to a product or product group, it is applied on top of the FINAL price (i.e., on the output of the main pricing Formula, not blended into it).
- Open, high priority per Document A: exact tier boundaries (weight/count thresholds and price change per tier, per product family) not yet finalized - explicitly stated to be "part of our creativity and competitive advantage," to be decided in a dedicated session. Until then, tiers must be admin-panel editable without code changes.

5.4 NO PRICE LOCK (CONFIRMED IN STEP 6 - SUPERSEDES EARLIER READING, see Section 10 for the correction record)

- There is NO price lock anywhere in the order lifecycle, from order creation through payment. Price is always read live from the backend at every stage.
- If a product's price changes during the order process, this is an ACCEPTED, INEVITABLE business event, not a defect.
- If a pre-invoice (پیش‌فاکتور) was issued and the underlying price changes, that pre-invoice becomes EXPIRED. This is stated in the terms/agreement that the customer accepts when placing an order.
- Exact expiration trigger mechanics (e.g., does expiration happen immediately on price change, or only checked at order finalization) are NOT yet resolved - deferred to Step 9 (Invoice/Pre-invoice modeling), still pending as of this knowledge base.

5.5 Base price is keyed by FULL SPECIFICATION COMBINATION, not just factory (CONFIRMED, CORRECTED IN STEP 6)

- Earlier assumption (now superseded): base price varies only by manufacturing factory.
- CORRECTED, CONFIRMED FACT: a base price record exists per full, specific combination of a product's Specification values - factory/brand is just ONE of these dimensions, not the only one. Size, thickness, etc. are equally price-determining dimensions of the SAME price record key.
- Concrete confirmed examples given by business owner:
    - Rebar, Zob-Ahan factory, size 14 -> 73,000 Toman/kg
    - Rebar, Zob-Ahan factory, size 12 -> 74,000 Toman/kg
    - Rebar, Abarkooh factory, size 14 -> 73,500 Toman/kg
    - Nail, size 3, Brand X -> 20 Toman/unit
    - Nail, size 3, Brand Alex -> 2,000,000 Toman/package
    - Nail, size 3, Ashtarkooh -> 25,000 Toman/unit
- CRITICAL CONFIRMED FACT: the PRICING UNIT itself (per kg, per unit/piece, per package, etc.) is part of the price record, NOT a single fixed setting for the whole product. Different combinations of the same product family can be priced in entirely different units (e.g., nails priced per-package for one brand, per-unit for another).
- Working concept name (not finalized, deferred to technical design): something like "Price Entry" or "Product Variant Price," associated with one unique Specification-value combination.

5.6 Pricing Formula mechanics (CONFIRMED, Step 6, no exceptions identified)

- Two-tier formula system:
    1. DEFAULT (system-wide fallback): Final price = base price x order quantity (count or weight) + N% tax.
    2. CUSTOM (admin-defined per product): An admin-defined formula based on that product's Specification values.
- CONFIRMED RULE (no exceptions found as of this knowledge base): the admin-defined Formula NEVER computes final price directly. It ALWAYS computes only the effective consumed QUANTITY or MULTIPLIER (e.g., calculated weight from sheet dimensions) from the Specification. That computed quantity is then multiplied by the Base Price Entry that matches the specific Specification combination (including factory) to get the pre-tax, pre-discount price.
- This same Formula engine is REUSED by the public-facing weight-calculator toolbox on the website (dual consumer: internal order pricing, and public calculator tool). Relationship confirmed as Upstream (Pricing Engine) / Downstream (public calculator) - to be formalized in Step 10 Context Map.
- Technical delivery detail (explicitly NOT part of domain modeling, recorded only as a pre-existing technical decision by the business owner, to be revisited in the technical design phase): frontend fetches calculator form metadata from backend (calculator name, input fields, field types, and the formula itself); results must be cacheable.

5.7 Order of operations for final price calculation (derived/confirmed synthesis from 5.3 + 5.5 + 5.6)

1. Determine the exact Specification combination of the ordered product (including factory/brand).
2. Look up the Base Price Entry for that exact combination (this entry also carries its own pricing unit).
3. Run the pricing Formula (default or custom) to get the effective quantity/multiplier from the Specification.
4. Multiply: quantity/multiplier x base price of that Specification combination = pre-discount, pre-tax price.
5. Apply tiered/volume discount (independent layer, live, per-item) if applicable, on top of the result of step 4.
6. Apply VAT (10%, or admin-configured N%) as the final layer.
7. Store the pre-VAT price separately in the backend at all times (per 5.1).

# =========================================================== 6. CONFIRMED BUSINESS FACTS - UNIT MODEL (Step 4)

6.1 Four independent unit concepts (all confirmed):

- Registration Unit: the unit the product's Specification is defined in (e.g., kg for rebar).
- Sale Unit: the unit the customer orders in (weight, count, or both - per Document A Section 4).
- Pricing Unit: the unit the base price is defined in (e.g., Toman per kg) - CONFIRMED in Step 6 to be a per-Price-Entry property, not fixed per product.
- Display Unit: the unit shown to the customer, independently configurable by admin in the CATALOG and in the INVOICE (these two display contexts are configured separately).

6.2 Conversion Factor (CONFIRMED, Step 4)

- Always a FIXED NUMBER stored on the product (e.g., weight of one rebar bar = fixed kg value), NEVER the output of the pricing Formula. This keeps Conversion Factor and pricing Formula fully independent concepts.
- Enables converting total order weight into a count of pieces (or vice versa) for products where this applies. Must be admin-configurable.

6.3 Invoice unit display rule (CONFIRMED)

- Maximum of TWO units may be shown simultaneously on an invoice.
- In most cases, only ONE unit is shown.
- Admin controls which unit(s) display on the invoice, independently from the catalog display unit setting.

6.4 Simple vs. complex products (CONFIRMED, Step 4 - resolves earlier open question)

- NOT all products require the full four-unit distinction. Some products can be SIMPLE and use just ONE unit throughout (registration = sale = display = pricing), with NO conversion factor needed.
- Explicit examples given by business owner: box, count/piece, carton, trailer-load, container.
- More complex products (e.g., rebar, sheet, profile) can use the full four-unit distinction with conversion factors.

6.5 System-wide diverse standard units (CONFIRMED, Document A response)

- The system supports a variety of standard measurement units (not just kg and bar-count).
- Admin chooses which unit a product is registered in, and which unit pricing is based on.

6.6 Specification structure decision (CONFIRMED, Step 4)

- Specification is modeled as a TYPED structure (not raw free-form key-value pairs), because price/filter-critical fields (size, thickness, grade, factory, etc.) need structured validation and filterability.
- BUT: fields must be OPTIONAL/lightweight-by-default so a SIMPLE product (Section 6.4) is not forced into unnecessary complexity, while a COMPLEX product can still use the full structured model. This resolves the tension between "must be structured" (Document A) and "must support simple products with one unit" (business owner, Step 4).

# =========================================================== 7. CORRECTIONS AND SUPERSEDED CONCLUSIONS (EXPLICIT REGRESSION LOG)

CORRECTION 1 (raised and resolved at Step 6):

- WHAT CHANGED: The previously confirmed fact "Base market price is locked to prevent momentary fluctuation, unlike tier discounts which are never locked" (originally drawn directly from Document A Section 7-1) is now INVALID.
- WHY IT CHANGED: Business owner clarified that the "lock" language in Document A referred only to the span from order placement through payment, and that this was a confusing/overcomplicated way to describe it. Business owner explicitly stated: "we don't have a price lock, disregard this."
- WHAT REPLACES IT: No price lock exists anywhere. Price is always read live from backend through checkout. Price changes mid-order are accepted as inevitable; pre-invoices expire if affected prices change (customer has agreed to this in the terms). See Section 5.4.
- STATUS: Fully resolved and confirmed. Not silently overwritten - explicitly logged here per instruction.

CORRECTION 2 (raised and resolved at Step 6):

- WHAT CHANGED: The previously confirmed/derived fact "Base price is a per-factory record" (a Step 3 conclusion, tentatively named ProductFactoryPrice) is now INVALID as stated (too narrow).
- WHY IT CHANGED: Business owner's rebar and nail examples showed that price varies independently by factory AND by size AND (for nails) by pricing unit type (per-unit vs per-package) simultaneously - i.e., price is keyed by the FULL Specification combination, not factory alone.
- WHAT REPLACES IT: Base price ("Price Entry") is keyed by the full, specific combination of Specification values, of which factory is only one dimension. See Section 5.5.
- STATUS: Fully resolved and confirmed.

RESOLVED CONTRADICTION (originally flagged inside Document A itself, unresolved from Phase 1 through Step 4, resolved at Step 5):

- ORIGINAL CONTRADICTION: Document A Section 3 said out-of-stock products "should not appear in the catalog at all," but also said they "can be shown at admin's discretion with a contact-expert action." Document A's own Section 10 summary table flagged this explicitly as needing final confirmation.
- RESOLUTION: Both behaviors are correct and coexist - the choice is a PER-PRODUCT admin setting, not a mutually exclusive global rule. A third state was also confirmed: out-of-stock with a tag and NO action at all. See Section 3.5 for full detail.
- STATUS: Fully resolved, confirmed, three-state model locked.

REJECTED/AVOIDED ASSUMPTIONS (things Claude explicitly declined to assume without confirmation, per DDD instructions):

- Did NOT assume Sale Mode and Inventory display behavior were a single combined field - explicitly asked and confirmed they are two independent fields (Section 3.5/3.6).
- Did NOT assume the admin-defined Formula could directly set final price without a Base Price Entry - explicitly asked; business owner confirmed no such exception exists (Section 5.6).
- Did NOT assume all products need the full four-unit model - explicitly asked; business owner confirmed simple single-unit products exist (Section 6.4).
- Did NOT assume the Conversion Factor could be formula-derived - explicitly asked; confirmed it is always a fixed stored number (Section 6.2).
- Did NOT infer tier discount boundary values from competitor data (retail/wholesale ~1 ton threshold seen in competitor docs) as a business rule for Foolad Shahyar Sepand - this remains explicitly UNRESOLVED/OPEN per business owner's own flag that it's a competitive-advantage decision pending a dedicated session.
- Did NOT treat any competitor-derived formula or category structure as a Foolad Shahyar Sepand rule unless explicitly confirmed (the one explicit confirmation being overall product domain breadth, Section 3.2).
- Technical/architectural details volunteered by the business owner (Stimulsoft invoice rendering pipeline; frontend fetching calculator metadata with caching) were explicitly logged as pre-existing technical decisions but NOT incorporated into domain modeling, per the working method (business before technology).

# =========================================================== 8. ARCHITECTURAL DECISIONS WITH SUPPORTING BUSINESS EVIDENCE

DECISION 1: Product is a single generic aggregate with category-dependent Specification, not one aggregate type per product family.

- Evidence: repeated explicit statements about needing to register diverse products; explicit future expansion beyond steel to construction materials/nails/screws; per-family aggregates would require code/schema changes for every new category, conflicting with stated goals.
- Confidence: High (explicitly confirmed twice).

DECISION 2: Catalog and Pricing are BOTH Core Domains (Bounded-Context-level implication: likely a close/partnership relationship rather than typical core-vs-supporting distance; to be formalized in Step 10).

- Evidence: business owner explicitly named tiered pricing as "part of our creativity and competitive advantage" (Document A); business owner explicitly confirmed both Catalog and Pricing are core when asked directly.
- Confidence: High (explicit confirmation).

DECISION 3: Order Management, Ancillary Cost handling, and Invoice/Pre-invoice generation are Supporting subdomains; the public Weight Calculator is a Generic subdomain but is a downstream CONSUMER of the core Pricing engine's Formula capability (not a fully isolated generic subdomain).

- Evidence: no competitive-advantage language applied to these; weight calculator formulas are stated to be standard/industry formulas, BUT the calculator engine is explicitly the same engine used for admin-defined product pricing formulas (business owner confirmed reuse).
- Confidence: Medium-high for Supporting classification (inferred, not directly stated); High for the Formula-engine-reuse relationship (explicitly confirmed).

DECISION 4: Specification is a typed-but-optional structure; not raw key-value, not a rigid mandatory schema.

- Evidence: Document A requires structured, filterable fields (size, thickness, grade, factory); business owner confirmed simple single-unit products (box, carton, container) must NOT be forced into the complex four-unit / heavy Specification model.
- Confidence: High (both sides of the tension explicitly confirmed).

DECISION 5: Conversion Factor and Pricing Formula are fully independent mechanisms; Conversion Factor is always a static number, Formula always computes a variable quantity/multiplier consumed by the Base Price Entry lookup.

- Evidence: explicit business owner confirmation on both points in direct response to direct questions.
- Confidence: High.

DECISION 6: No price-locking mechanism anywhere in the system; live price lookup at every stage; pre-invoice expiration is the mechanism that handles price-change-during-order-lifecycle, backed by a customer-facing terms agreement.

- Evidence: explicit business owner correction and confirmation (Correction 1, Section 7).
- Confidence: High.

# =========================================================== 9. WORKING DOMAIN CONCEPTS (NAMES ARE PROVISIONAL, NOT TECHNICAL DESIGN)

- Product (aggregate): generic, category-agnostic, holds Sale Mode, Out-of-stock display setting, Specification, Registration Unit, Sale Unit, Conversion Factor(s) if applicable, reference to one or more Price Entries.
- Specification (value object / typed structure): category-dependent, optional-by-default fields; includes price-making variables (size, thickness, length, grade, factory/brand) plus non-price-making descriptive/market-slang terms (currently display-name-only).
- Price Entry / "Base Price per Specification Combination" (entity, name not finalized): keyed by a full, specific combination of Specification values (including factory/brand); carries its own Pricing Unit; append-only history; can be marked obsolete (not deleted).
- Pricing Formula (per-product, admin-defined or default): computes effective quantity/multiplier from Specification values; feeds into Price Entry lookup; same engine powers the public weight-calculator tool.
- Tier Discount Policy (independent layer): per-category or global-default with override; per-item; live/unlocked; applied on top of Formula output x Price Entry result.
- VAT layer: final calculation step, 10% (or admin-configured N%), applied after tier discount; base (pre-VAT) price always stored separately.
- Out-of-stock Display Policy (per-product setting): one of {hidden entirely, shown with tag/no action, shown with contact-expert action}.
- Sale Mode (per-product/category setting): one of {fully online, semi-custom with base-size online + custom-via-phone, fully custom/contact-only}.
- Ancillary Cost line item (independent order/invoice line, currently manual entry, potential future default-fixed-value from backend).
- Pre-invoice expiration mechanism: tied to price changes on referenced Price Entries; exact trigger timing not yet resolved (deferred to Step 9).

# =========================================================== 10. UNRESOLVED / OPEN QUESTIONS (as of this knowledge base)

OPEN-1 (Document A, high priority, per business owner): Exact freight/shipping cost calculation formula/rule (basis: distance, total weight, destination zone?) is not yet decided; requires a dedicated session. Current interim behavior: manual admin entry per order, with a possible backend default fixed number.

OPEN-2 (Document A, high priority, per business owner): Exact tiered-discount boundaries (weight/count thresholds and price change per tier, per product family) not yet finalized; explicitly called out as competitive-advantage material requiring a dedicated session. Interim requirement: must be admin-panel-editable without code changes.

OPEN-3 (Document A, low priority): Complete list of all market-slang terms similar to Fabrik/Persi/Loghme/Nimeh/Sabok-Sangin is not yet compiled; explicitly deprioritized by business owner.

OPEN-4 (low priority, structural): Whether Sale Mode is a strictly closed 3-value enum or may be extended with additional values in the future - not yet asked/answered; does not block current modeling.

OPEN-5 (deferred to Step 9 by design): Exact trigger mechanics for pre-invoice expiration (e.g., immediate on any price change vs. checked only at order finalization/payment time).

OPEN-6 (deferred, minor): Exact mechanics of how Fabrik and Persi are represented as "independent variants" of the generic Product aggregate - i.e., fully separate Product records with no formal link, vs. some lightweight cross-reference. Not yet finalized; does not block progress per explicit note when originally raised in Step 3.

OPEN-7 (deferred to technical design phase, explicitly NOT a domain-modeling question per business owner's own volunteered technical decisions): Stimulsoft-based invoice/pre-invoice rendering pipeline (backend renders to image, converts to base64, frontend renders/downloads as image or PDF); frontend fetching of calculator metadata (name, fields, types, formula) with caching requirement. These are logged as pre-existing technical decisions by the business owner, to be revisited only after domain modeling is complete.

OPEN-8 (not yet reached in the workflow): Any question belonging to Steps 7 through 11 of the approved execution plan (phone-order/manual-order Business Process modeling with Actors and Domain Events; final Ancillary Cost modeling detail; Invoice/Pre-invoice full modeling; Bounded Context map and relationships; final full-model review pass) has not yet been asked or answered as of this knowledge base. These steps are PENDING, not completed.

# =========================================================== 11. PROCESS STATE / HOW TO CONTINUE

Working method in force for this project (must be preserved by any continuing agent):

- Reality over assumptions; never invent rules; distinguish Confirmed Facts / Derived Conclusions / Unknown / Open Questions at all times; flag contradictions and stop for clarification before proceeding; business modeling strictly before any technical/architecture/code decisions; no praise language; plain text output; phase discipline (documentation collection -> planning -> execution one small step at a time, each step confirmed before moving to the next); explicit regression checks against all previously confirmed facts before finalizing any step, with any change to a prior conclusion explicitly logged (what changed / why / which conclusion is now invalid) rather than silently overwritten.

Approved 11-step execution plan (Phase 2), current progress:

1. Ubiquitous language - DONE (Section 2 of this document, plus refinements added through later steps).
2. Subdomains / core vs supporting vs generic - DONE (Section 8, Decisions 2-3).
3. Product aggregate modeling - DONE (Section 3.6, Section 9).
4. Unit / conversion model - DONE (Section 6).
5. Sale-mode vs inventory (independent attributes + resolution of the out-of-stock contradiction) - DONE (Section 3.5, 3.6).
6. Pricing engine (base price keyed by full spec combination, no price lock, formula-then-price-entry order of operations, tier discount as independent layer) - DONE (Section 5).
7. Phone order process / admin manual order creation, as a formal Business Process with Actors and Domain Events - NOT YET STARTED.
8. Ancillary costs (final detailed modeling) - NOT YET STARTED (baseline facts exist in Section 3/4, formal step pending).
9. Invoice / Pre-invoice modeling (including pre-invoice expiration trigger mechanics, OPEN-5) - NOT YET STARTED.
10. Bounded Contexts and Context Map (including formalizing Catalog+Pricing core relationship, and Pricing-Upstream/Calculator-Downstream relationship) - NOT YET STARTED.
11. Final full-model review pass against all confirmed facts, plus final list of remaining low-priority open items - NOT YET STARTED.

Any agent continuing this project should resume at Step 7, using this document as complete grounding, and should continue applying the same working method (fact/inference/unknown separation, explicit regression checks, no assumption-filling, stop-and-ask on contradiction) exactly as practiced through Steps 1-6.

END OF KNOWLEDGE BASE.