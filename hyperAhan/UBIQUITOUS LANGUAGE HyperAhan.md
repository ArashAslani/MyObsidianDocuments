UBIQUITOUS LANGUAGE HyperAhan - Catalog and Pricing Modules Complete glossary of every business concept established through Phase 3, Step 6. Format per entry: Concept / Synonyms / Meaning / Relationships / Business Owner / Bounded Context. "Business Owner" below means the business-side role/actor who defines, controls, or is authoritative for this concept (Admin, Customer, System/Backend, or the business owner Arash/Foolad Shahyar Sepand as the source of the rule itself) - not a person's job title in the org chart. Bounded Context values used: Catalog, Pricing, Order (deferred/out of current design scope but referenced), Ancillary Cost (deferred), Invoice (deferred), Calculator. Where a Bounded Context has not yet been formally assigned (Step 10 not yet executed), this is marked "Not yet assigned (Step 10 pending)."

===========================================================

Concept: Product Synonyms: محصول Meaning: The single, generic aggregate representing any sellable item in the catalog, regardless of product family (steel or otherwise). Not modeled as one type per family. Relationships: Has one Specification. Has a Sale Mode. Has an Out-of-Stock Display Setting. Has a Registration Unit, a Sale Unit, and optionally Conversion Factors. Has one or more Price Entries. May be linked to a Fabrik/Persi counterpart as an independent variant. Business Owner: Admin (registers and configures the product); Business Owner/Arash (defines that the model must support broad, diverse product registration). Bounded Context: Catalog.

===========================================================

Concept: Specification Synonyms: مشخصات محصول, فیلد ساختاریافته, Specification VO Meaning: A typed, category-dependent structure attached to a Product, holding price-making and descriptive variables (size, thickness, length, grade, factory/brand, etc.). Fields are optional/lightweight by default so simple products are not burdened with unneeded structure, while complex products can use the full structured model. Relationships: Belongs to exactly one Product. Its values (in combination) form the key for a Price Entry. Feeds the Pricing Formula as input. Contains Price-Making Variables as a subset of its fields. Business Owner: Admin (defines which Specification fields apply per category); Business Owner/Arash (mandated that these fields be structured, not free text). Bounded Context: Catalog.

===========================================================

Concept: Price-Making Variable Synonyms: متغیر اصلی و قیمت‌ساز, متغیر قیمت‌ساز Meaning: The subset of Specification fields that directly determine price and are used for catalog filtering - explicitly: size, thickness, length, grade, manufacturing factory. Relationships: Subset of Specification. Together (in full combination) they form the key that identifies a specific Price Entry. Business Owner: Business Owner/Arash (Document A, Section 6-1). Bounded Context: Catalog / Pricing (spans both - structural definition lives in Catalog, but its purpose is entirely pricing-driven).

===========================================================

Concept: Sale Mode Synonyms: نحوه فروش, دسته یک/دو/سه (Category 1/2/3), Sale Category Meaning: A required attribute of every Product with exactly three defined values: (1) Fully Online - price shown, cart purchase available; (2) Semi-Custom - a Base Size is priced and purchasable online by default, any deviation requires a phone call; (3) Fully Custom/Contact-Only - price is never predetermined, always requires a phone call; still shown in the public catalog without price, with a contact-expert button. Relationships: Independent of Inventory/Out-of-Stock Display Setting (must never be merged with it, per explicit confirmation). Determines default catalog UI (add-to-cart vs. contact button vs. admin-only). Category 2 relates directly to Base Size. Business Owner: Business Owner/Arash (defines the three categories); Admin (assigns the value per product). Bounded Context: Catalog. Open item: whether this is a strictly closed 3-value enum or extensible in the future is unresolved (low priority, OPEN-4).

===========================================================

Concept: Base Size Synonyms: سایز پایه Meaning: For Semi-Custom (Sale Mode category 2) products such as I-beam, the standard/default size (e.g., a 12-meter bar) on which the default online price is based and which is purchasable online without a phone call. Relationships: Belongs to a Product with Sale Mode = Semi-Custom. Any Specification value combination deviating from the Base Size triggers the Custom/Contact path instead of the online path. Business Owner: Business Owner/Arash (I-beam example, Document A); confirmed general pattern applies to any product with a recognized standard size. Bounded Context: Catalog.

===========================================================

Concept: Custom Size / Custom Order (in the Sale Mode sense) Synonyms: سایز سفارشی, برش خاص, طول متفاوت Meaning: Any deviation from a product's Base Size (for Semi-Custom products) or, more generally, any order that does not fit the standard online-purchasable configuration; requires phone contact rather than online checkout. Relationships: Opposite/complement of Base Size within Sale Mode category 2. Also generally describes the entirety of Sale Mode category 3 products. Business Owner: Business Owner/Arash. Bounded Context: Catalog / Order (the custom fulfillment itself belongs to Order, which is out of current design scope).

===========================================================

Concept: Out-of-Stock Display Setting (Inventory Display Policy) Synonyms: وضعیت موجودی, تگ ناموجود, ابهام "تماس بگیرید" برای ناموجودی Meaning: A per-Product, admin-configurable setting controlling how an out-of-stock product appears in the catalog. Exactly three possible states: (1) fully hidden from the catalog, (2) shown with an "out of stock" tag and no action button at all, (3) shown with a "contact expert" action button. Relationships: Fully independent from Sale Mode - must never be collapsed into it, even though state 3 and Sale Mode category 3 can both surface a visually similar "contact" button in the UI, from two different underlying fields. State 3's business rationale: lets the admin offer a substitute product or a quote from another supplier. Business Owner: Admin (sets the value per product); Business Owner/Arash (defined the three-state resolution after the original document's internal contradiction was clarified). Bounded Context: Catalog.

===========================================================

Concept: Call Path A Synonyms: مسیر الف Meaning: One of two phone-order fulfillment paths. Admin builds the entire order in the system on the customer's behalf and issues a payment link/notification; the customer logs in, reviews, and pays. Relationships: Alternative to Call Path B. Both occur after a phone conversation where Admin determines final price including any Ancillary Cost. Requires the "admin builds order from scratch" capability. Business Owner: Admin (executes this path); Business Owner/Arash (defined the two-path model, Document A Section 2). Bounded Context: Order (deferred from current design scope; documented for future modeling in Step 7).

===========================================================

Concept: Call Path B Synonyms: مسیر ب Meaning: The second phone-order fulfillment path. The entire transaction (agreement and payment) happens manually, outside the system; admin later enters the order into the system solely for reporting/statistics purposes. Relationships: Alternative to Call Path A. General principle governing when either path is used: whatever can be automated should happen on the website; phone paths are reserved for negative-feedback cases or genuinely complex orders. Business Owner: Admin; Business Owner/Arash. Bounded Context: Order (deferred).

===========================================================

Concept: Admin-Built Order / Order From Scratch Synonyms: ساخت سفارش از صفر, ثبت سفارش توسط ادمین Meaning: The capability for an admin to create a brand-new order directly in the system - not limited to managing customer-submitted orders - including product selection, manual pricing outside standard tiers if needed, adding Ancillary Costs, and issuing a Payment Link. Relationships: Required by both Call Path A and Call Path B. Distinguishes this admin panel from a typical order-management-only admin panel. Business Owner: Admin; Business Owner/Arash (explicit design implication in Document A Section 2). Bounded Context: Order (deferred).

===========================================================

Concept: Payment Link Synonyms: لینک پرداخت, نوتیفیکیشن پرداخت Meaning: A link or notification issued to the customer (in Call Path A) allowing them to log in, view the order and its price, and complete payment. Relationships: Output of the admin-built-order process under Call Path A. Its underlying payment mechanism/gateway is unspecified and out of scope for domain modeling as of this document. Business Owner: Admin (issues it); Business Owner/Arash (mandates its existence). Bounded Context: Order (deferred).

===========================================================

Concept: Fabrik Synonyms: فابریک Meaning: One of two fully independent products/variants (the other being Persi) - e.g., "ribbed sheet, Fabrik type." Not a shared field/value on a common product; a fully separate product identity. Relationships: Independent of, but conceptually paired with, Persi. Both are instances of the generic Product aggregate. Exact mechanics of "independent variant" representation (fully separate records vs. lightweight cross-reference) is unresolved (OPEN-6, low priority). Business Owner: Business Owner/Arash (Document A, Section 6-2). Bounded Context: Catalog.

===========================================================

Concept: Persi Synonyms: پرسی Meaning: The counterpart to Fabrik - a fully independent product/variant, never merged into Fabrik as a shared attribute. Relationships: Same as Fabrik (mirrored entry). Business Owner: Business Owner/Arash. Bounded Context: Catalog.

===========================================================

Concept: Market Slang Terms (general category) Synonyms: اصطلاحات بازاری Meaning: An umbrella term for market-usage naming conventions observed in real invoices that are not (yet) structured Specification fields. Includes Sabok/Sangin, Nimeh, and Loghme (each listed separately below). Stored only in the Product's display name unless a direct, formulable price effect is later proven, at which point the specific term is promoted to a structured field. Relationships: Subset relationship to Specification (currently NOT part of it; candidate for future promotion into it). The complete list of such terms beyond the four named ones is not yet known (OPEN-3, low priority, explicitly deprioritized by the business owner). Business Owner: Business Owner/Arash. Bounded Context: Catalog.

===========================================================

Concept: Sabok / Sangin (Light / Heavy) Synonyms: سبک / سنگین Meaning: A weight-class descriptor for a product (e.g., "ناودانی ۸ - سنگین"), currently recorded only in the display name, not as a structured field. Relationships: Instance of Market Slang Terms. Business Owner: Business Owner/Arash. Bounded Context: Catalog.

===========================================================

Concept: Nimeh (Half) Synonyms: نیمه Meaning: A version of a product with lower thickness/weight than the standard version (e.g., "نبشی نیمه ۵"), currently recorded only in the display name. Relationships: Instance of Market Slang Terms. Business Owner: Business Owner/Arash. Bounded Context: Catalog.

===========================================================

Concept: Loghme (Cut Piece) Synonyms: لقمه Meaning: A cut/segmented product derived from a standard profile - a DERIVED product from a raw product, not a new raw material in its own right. Mostly weighed, sometimes counted. Relationships: Instance of Market Slang Terms; conceptually derived FROM another (raw) Product, implying a possible parent-child relationship not yet formally modeled. Business Owner: Business Owner/Arash. Bounded Context: Catalog.

===========================================================

Concept: Tiered / Volume Pricing (Tier Discount) Synonyms: قیمت‌گذاری پلکانی, پله قیمتی, تخفیف حجمی, پله تخفیف Meaning: A discount mechanism where unit price depends on purchase volume (weight or count): smaller purchase = higher unit price, larger = lower. Calculated strictly per order line item (per-item), never on cart-wide totals. Recalculates live/automatically as cart quantity changes (never locked). Defined completely independently of the Pricing Formula and applied on top of the Formula's final output. Can be defined per product family with a global default that is overridable per category. Relationships: Independent of Pricing Formula and Base Price Entry - operates as a distinct layer applied after them. Named by the business owner as the company's specific competitive advantage - this is the primary evidence for classifying Pricing as a Core Domain. Business Owner: Business Owner/Arash (defines tiers as core competitive IP); Admin (configures tier boundaries via panel, without code changes). Bounded Context: Pricing. Open item: exact tier boundary values (thresholds, percentages) not yet finalized (OPEN-2, high priority per business owner, requires a dedicated future session).

===========================================================

Concept: VAT / Tax Synonyms: مالیات بر ارزش افزوده, ارزش افزوده Meaning: A 10% value-added tax embedded in all customer-facing prices. Two prices (with/without VAT) are never shown together. Backend always stores the pre-VAT base price separately; VAT is applied only at final calculation, as the last step after Tier Discount. Relationships: Final layer in the price-calculation order of operations (Formula output x Base Price -> Tier Discount -> VAT). Accompanied by a mandatory fixed disclaimer text shown on all relevant pages. Business Owner: Business Owner/Arash (Document A, Section 8). Bounded Context: Pricing.

===========================================================

Concept: Price History Record Synonyms: تاریخچه‌ی قیمت, رکورد قیمت Meaning: Every change to a price creates a new, append-only record. Prior records are never deleted or edited. The system always uses the latest (non-obsolete) record for display and calculation. Admins can view the full historical report. Relationships: Directly tied to Price Entry (each Price Entry has its own history over time). Interacts with the "no price lock" rule (D-29/Section 5.4) - the live-read price at any moment is whichever record is currently latest and non-obsolete. Business Owner: Business Owner/Arash (Document A, Section 9); Admin (views historical reports, marks records obsolete). Bounded Context: Pricing.

===========================================================

Concept: Obsolete Price Record Synonyms: رکورد قیمت منسوخ, منسوخ‌شدن Meaning: A distinct status a Price History Record can be given so that it is excluded from reports, WITHOUT being deleted. Explicitly a different state from deletion. Relationships: A status/flag on a Price History Record. Does not affect the append-only, never-edited nature of the underlying record. Business Owner: Admin (marks records obsolete); Business Owner/Arash (defined the distinction from deletion). Bounded Context: Pricing.

===========================================================

Concept: Ancillary Cost Synonyms: هزینه جانبی, کرایه حمل (its primary named example, freight/shipping cost) Meaning: Any cost, such as freight, that is recorded as an independent line item in the order/invoice rather than being folded into the product's unit price. Currently entered manually by the admin (no automatic formula yet); the backend may return a default fixed number. Relationships: Applied at the Order/Invoice level, separate from Product pricing. Part of the "business owner explicitly stated part of retail profit is embedded in this line" rationale for keeping it visible and separate. Business Owner: Admin (enters the value manually); Business Owner/Arash (mandated the independent-line-item rule; the exact calculation formula is explicitly still undecided). Bounded Context: Ancillary Cost (deferred subdomain, Supporting classification) / Order (deferred) / Invoice (deferred). Open item: exact calculation formula (distance/weight/zone-based) not yet finalized (OPEN-1, high priority per business owner, requires a dedicated future session).

===========================================================

Concept: Registration Unit Synonyms: واحد ثبت Meaning: The unit in which a Product's Specification is defined/registered (e.g., kilograms for rebar). Relationships: One of four independent unit concepts (with Sale Unit, Pricing Unit, Display Unit). Related to Sale/Pricing/Display units via a Conversion Factor when they differ. For Simple Products, this is the same as all other unit roles (no distinction needed). Business Owner: Admin (chooses the unit a product is registered in). Bounded Context: Catalog.

===========================================================

Concept: Sale Unit Synonyms: واحد فروش Meaning: The unit the customer orders in - weight, count/piece, or both simultaneously, depending on the product. Not a single global system-wide assumption; must be definable per product. Relationships: One of four independent unit concepts. Related to the tiered-pricing "per-item" quantity used in Tiered/Volume Pricing calculations. Business Owner: Business Owner/Arash (Document A, Section 4, rejecting the "everything in kg" default assumption); Admin (configures per product). Bounded Context: Catalog / Order (Order use of the sale unit is deferred).

===========================================================

Concept: Pricing Unit Synonyms: واحد قیمت‌گذاری Meaning: The unit the base price is expressed in for a specific Price Entry (e.g., Toman per kilogram, Toman per unit, Toman per package). Confirmed to be a property of each individual Price Entry, NOT a single fixed setting applied uniformly across the whole product - the same product/size can have different Pricing Units across different brands/Price Entries (e.g., nails priced per-unit for one brand, per-package for another). Relationships: One of four independent unit concepts. Lives on the Price Entry, not the Product itself. Directly used in the price-calculation order of operations (Formula output x Base Price, where Base Price is expressed per this unit). Business Owner: Admin (chooses pricing unit per Price Entry); Business Owner/Arash (nail/rebar examples establishing this granularity). Bounded Context: Pricing.

===========================================================

Concept: Display Unit Synonyms: واحد نمایش Meaning: The unit shown to the customer. Configured independently for two separate contexts: the Catalog view and the Invoice view. In the invoice specifically, a maximum of two units may be shown simultaneously, and in most cases only one unit is shown. Relationships: One of four independent unit concepts. Independent of Registration/Sale/Pricing Unit - what is shown does not have to equal what is stored, sold-in, or priced-in. Related to Conversion Factor, which enables translating an underlying quantity into the chosen Display Unit (e.g., showing a piece count computed from total weight). Business Owner: Admin (sets catalog display unit and invoice display unit(s) independently). Bounded Context: Catalog (catalog display) / Invoice (invoice display, deferred subdomain).

===========================================================

Concept: Conversion Factor Synonyms: ضریب تبدیل Meaning: A fixed, stored numeric value relating two units on the same product (e.g., the weight of one rebar bar in kilograms), enabling conversion of total order weight into a piece count, or vice versa. Always a static stored number - never the output of the Pricing Formula or any dynamic/computed mechanism. Relationships: Connects Registration/Sale/Pricing/Display Units when they differ for a given Product. Explicitly and deliberately kept independent of the Pricing Formula concept, even though both "compute a number from product data" superficially. Business Owner: Admin (defines/edits the fixed value); Business Owner/Arash (confirmed it must always be a fixed number, not formula-derived). Bounded Context: Catalog.

===========================================================

Concept: Simple Product Synonyms: محصول ساده Meaning: A product that uses a single unit throughout (Registration = Sale = Display = Pricing Unit are all the same), requiring no Conversion Factor and no multi-unit Specification complexity. Explicit examples: box, count/piece, carton, trailer-load, container. Relationships: Opposite/complement of Complex Product. Both are represented by the same generic Product aggregate and the same (optional-by-default) Specification model - the difference is in how many of the optional unit-related fields are actually populated. Business Owner: Business Owner/Arash (explicitly confirmed this category exists and must be supported without forcing full complexity). Bounded Context: Catalog.

===========================================================

Concept: Complex Product Synonyms: محصول پیچیده (implicit counterpart term, not separately named by the business owner but used in analysis to contrast with Simple Product) Meaning: A product requiring the full four-unit distinction and Conversion Factor(s) - e.g., rebar, sheet, profile - where Registration, Sale, Pricing, and Display Units may all differ from one another. Relationships: Opposite/complement of Simple Product. Business Owner: Business Owner/Arash (implicit, via rebar/sheet/profile examples used throughout). Bounded Context: Catalog.

===========================================================

Concept: Pricing Formula Synonyms: فرمول قیمت‌گذاری, فرمول اختصاصی, فرمول Meaning: A mechanism, either the system Default Formula or an Admin-Defined Custom Formula, that computes the effective consumed quantity or multiplier for a Product from its Specification values. Confirmed to NEVER compute final price directly - its output is always multiplied against the matching Base Price Entry to yield the pre-tax, pre-discount price. The same engine is reused to power the public Weight Calculator Tool. Relationships: Consumes Specification values as input. Produces a quantity/multiplier consumed by Price Entry lookup. Upstream of Tier Discount and VAT in the price-calculation order of operations. Shared/reused by the Calculator bounded context (Upstream/Downstream relationship, formal Context Map entry pending Step 10). Business Owner: Admin (defines custom formulas per product); Business Owner/Arash (requested this capability and confirmed no exceptions to the "never outputs final price" rule). Bounded Context: Pricing (primary owner) / Calculator (downstream consumer).

===========================================================

Concept: Default Formula Synonyms: فرمول پیش‌فرض Meaning: The system-wide fallback pricing formula: Final price = Base Price x order quantity (count or weight) + N% tax. Relationships: One of the two forms of Pricing Formula (the other being Custom Formula). Used automatically for any product without a defined Custom Formula. Business Owner: Business Owner/Arash. Bounded Context: Pricing.

===========================================================

Concept: Custom Formula Synonyms: فرمول اختصاصی, فرمول تعریف‌شده توسط ادمین Meaning: An admin-authored formula, specific to one product, computing the effective quantity/multiplier from that product's Specification values (e.g., computing sheet weight from length x width x thickness x density-like logic). Relationships: Alternative to Default Formula. Reused by the Weight Calculator Tool. Exact authoring mechanism (expression syntax, technical implementation) is explicitly deferred to the technical design phase (OPEN-7) and not part of domain modeling. Business Owner: Admin (authors it); Business Owner/Arash (requested the capability). Bounded Context: Pricing.

===========================================================

Concept: Weight Calculator Tool Synonyms: ماشین‌حساب وزن, جعبه ابزار محاسبه‌گر, ابزار محاسبه وزن عمومی Meaning: A public-facing tool for site visitors to compute product weight from dimensional inputs. Its underlying formulas are standard, industry-wide (not business-specific), sourced identically from competitor reference documents. Explicitly reuses the exact same Formula engine used for internal product pricing. Its business purpose is to generate visitor traffic, not to express competitive pricing logic. Relationships: Downstream consumer of the Pricing Formula engine (Upstream = Pricing). Classified as a Generic Domain in terms of its formula content, despite its technical dependency on the Core Pricing subdomain. Business Owner: Business Owner/Arash (confirmed its purpose is traffic generation and confirmed formula-engine reuse). Bounded Context: Calculator (Generic Domain classification; formally as a downstream consumer of Pricing - Context Map entry pending Step 10).

===========================================================

Concept: Price Entry (provisional name, not finalized) Synonyms: قیمت پایه به‌ازای ترکیب مشخصات (no single fixed Persian term used by the business owner; "Base Price" and "قیمت واحد" used descriptively in examples), tentatively also referred to in earlier analysis as "ProductFactoryPrice" (SUPERSEDED naming, see Decision Log D-30) Meaning: The pricing record tied to one specific, full combination of a Product's Specification values (including but not limited to factory/brand, and including size, thickness, etc.). Carries its own Pricing Unit. Subject to the append-only Price History Record model (never deleted/edited, can be marked Obsolete). Relationships: Keyed by a full Specification-value combination. Multiplied by the Pricing Formula's output to yield pre-tax, pre-discount price. Has its own Pricing Unit (independent from other Price Entries of the same Product). Has its own Price History over time. Business Owner: Admin (creates/edits price entries per combination); Business Owner/Arash (established the combination-keyed model via the rebar and nail examples). Bounded Context: Pricing.

===========================================================

Concept: Factory / Brand Synonyms: کارخانه سازنده, برند, کارخانه Meaning: The manufacturer or brand of a specific product instance. Confirmed to be one dimension (among several, alongside size, thickness, etc.) of the full Specification combination that keys a Price Entry - NOT the sole determinant of price as originally assumed. Relationships: Part of Specification. One of the Price-Making Variables. Co-determines, together with other Specification values, which Price Entry applies. Business Owner: Business Owner/Arash (rebar example: Zob-Ahan vs. Abarkooh factories at different prices for the same size, and different prices for different sizes within the same factory). Bounded Context: Catalog (as a Specification field) / Pricing (as a price-key dimension).

===========================================================

Concept: Minimum Order Quantity Synonyms: حداقل مقدار قابل‌فروش, محدودیت حداقل سفارش Meaning: A per-product threshold (by weight or count) below which the product cannot be ordered at all. Relationships: A constraint associated with a Product's Sale Unit / order quantity; enforced presumably at Order time (deferred subdomain). Business Owner: Business Owner/Arash (Document A, Section 7-2). Bounded Context: Catalog (as a product-level constraint definition) / Order (as the enforcement point, deferred).

===========================================================

Concept: Pre-invoice Synonyms: پیش‌فاکتور Meaning: A preliminary invoice document issued to a customer, referencing then-current prices. Can become EXPIRED if the referenced product price(s) change before the order is finalized, per terms the customer has agreed to. Relationships: Related to Invoice (a distinct but related document). Its expiration is triggered by Price History Record changes on referenced Price Entries. Exact trigger timing (immediate vs. checked only at finalization) is unresolved (deferred to Step 9). Business Owner: Admin (issues it); Business Owner/Arash (confirmed the expiration mechanism and its link to the "no price lock" rule). Bounded Context: Invoice (deferred Supporting subdomain, Step 9 pending).

===========================================================

Concept: Invoice Synonyms: فاکتور Meaning: The final billing document for a completed order. Displays prices inclusive of VAT, subject to the max-two-units display rule, and reflects Ancillary Cost as an independent line item. Relationships: Downstream of Order, Pricing (final calculated price), Ancillary Cost, and Display Unit configuration. Business Owner: Business Owner/Arash; Admin (configures invoice display unit(s)). Bounded Context: Invoice (deferred Supporting subdomain, Step 9 pending).

===========================================================

Concept: Terms Agreement (Customer Agreement) Synonyms: توافق‌نامه Meaning: The agreement a customer accepts when placing an order, which includes acknowledgment that a Pre-invoice may expire if prices change, and that price changes mid-order are expected/accepted. Relationships: Legal/procedural backing for the "no price lock" rule and Pre-invoice expiration behavior. Business Owner: Business Owner/Arash; Customer (accepts it). Bounded Context: Order (deferred) / Invoice (deferred).

===========================================================

Concept: Price Lock (REJECTED CONCEPT) Synonyms: قفل قیمت Meaning: A previously assumed mechanism (drawn from an ambiguous reading of Document A) whereby the base market price would be held constant to prevent momentary fluctuation. EXPLICITLY REJECTED AND CONFIRMED NOT TO EXIST by the business owner at Step 6. Retained in this glossary specifically as a rejected concept, per the instruction not to omit anything, and to prevent any future re-introduction of this assumption without cause. Relationships: Directly superseded by the "always live price, pre-invoice expiration" model. See Decision Log D-29 for the full regression record. Business Owner: Business Owner/Arash (explicitly rejected it: "قفل قیمت نداریم. این رو در نظر نگیر."). Bounded Context: Pricing (as a rejected concept within this context).

===========================================================

Concept: Core Domain Synonyms: زیردامنه هسته, هسته کسب‌وکار Meaning: A DDD classification for a subdomain that carries the business's primary competitive differentiation. Confirmed to apply to both Catalog/Product-Variant and Pricing. Relationships: Contrasted with Supporting Domain and Generic Domain. Business Owner: Business Owner/Arash (explicitly confirmed both Catalog and Pricing are core). Bounded Context: Applies at the subdomain-classification level, spanning Catalog and Pricing bounded contexts.

===========================================================

Concept: Supporting Domain Synonyms: زیردامنه پشتیبانی Meaning: A DDD classification for subdomains necessary for business operation but not a source of competitive differentiation. Confirmed (with medium-to-high, not directly re-verified, confidence) to apply to Order Management, Ancillary Cost Management, and Invoice/Pre-invoice Generation. Relationships: Contrasted with Core Domain and Generic Domain. Business Owner: Inferred by absence of competitive-advantage language; not yet independently re-confirmed word-for-word by the business owner. Bounded Context: Applies to Order, Ancillary Cost, and Invoice bounded contexts (all deferred from current design scope).

===========================================================

Concept: Generic Domain Synonyms: زیردامنه عمومی Meaning: A DDD classification for a subdomain with standard, non-business-specific logic. Confirmed to apply to the Weight Calculator Tool, though its technical implementation is a downstream consumer of the Core Pricing subdomain's Formula engine. Relationships: Contrasted with Core Domain and Supporting Domain. Business Owner: Business Owner/Arash (implicitly, via confirming the tool's formulas are standard and its purpose is traffic generation). Bounded Context: Calculator.

===========================================================

Concept: Modular Monolith Synonyms: سیستم ماژولار مونولیتیک Meaning: The overall system architecture style within which all modules (Catalog, Pricing, Order, Discount, Auth, Notification, Blog, etc.) are built as distinct modules inside a single deployable system, following Clean Architecture principles. Relationships: The architectural container for all Bounded Contexts in this project. Business Owner: Business Owner/Arash (explicit architectural instruction, Step 3 scope-narrowing message). Bounded Context: N/A (this is the architectural style, not a bounded context itself); noted here as a technical/architectural fact per the instruction not to omit anything, even though it is technically a Technology-layer decision rather than a business/domain concept.

===========================================================

Concept: Clean Architecture Synonyms: معماری کلین Meaning: The internal layering/dependency-direction style mandated for each module within the Modular Monolith. Relationships: Governs internal structure of Catalog and Pricing bounded contexts (and all others, once addressed). Business Owner: Business Owner/Arash. Bounded Context: N/A (architectural style).

===========================================================

Concept: Admin (Actor) Synonyms: ادمین, پنل ادمین Meaning: The internal staff role responsible for configuring products, Specifications, Sale Mode, Out-of-Stock Display Setting, Price Entries, Custom Formulas, Tier Discounts, Ancillary Costs, and for building phone-based orders from scratch. Relationships: Primary actor across nearly every concept in Catalog and Pricing. Distinguished from Customer and System/Backend. Business Owner: N/A (Admin IS a business owner role in the domain sense, acting on behalf of Foolad Shahyar Sepand). Bounded Context: Spans Catalog, Pricing, and (deferred) Order, Ancillary Cost, Invoice.

===========================================================

Concept: Customer (Actor) Synonyms: مشتری, کاربر Meaning: The external party purchasing products, either online (Sale Mode: Fully Online, or the online portion of Semi-Custom) or via phone (Call Path A/B), subject to the Terms Agreement. Relationships: Interacts with Product display (Sale Mode, Out-of-Stock Display), receives Payment Links, accepts Terms Agreement, receives Pre-invoices/Invoices. Business Owner: N/A (Customer is an external actor, not a source of business rules in this conversation). Bounded Context: Spans Catalog (as a viewer) and (deferred) Order, Invoice.

===========================================================

Concept: System / Backend (Actor) Synonyms: بکند, سیستم Meaning: The technical system itself, which always serves live price data (per the "no price lock" rule), computes Formula outputs, applies Tier Discounts and VAT, and can return a default fixed Ancillary Cost number. Relationships: Executes the price-calculation order of operations across Pricing; serves Formula metadata to the frontend for the Calculator Tool (explicitly noted as a technical, not domain, detail). Business Owner: N/A (technical actor). Bounded Context: Spans Pricing and Calculator; underlies all bounded contexts technically.

===========================================================

END OF UBIQUITOUS LANGUAGE DOCUMENT. Prepared through Phase 3, Step 6 of the approved 11-step execution plan. Terms belonging strictly to Steps 7-11 (formal Business Process actors/events for phone orders beyond what is captured here, detailed Ancillary Cost formula terms, formal Invoice/Pre-invoice field-level terms, and the full formal Context Map) will be added once those steps are executed; they are not yet part of this glossary because those steps have not yet occurred.