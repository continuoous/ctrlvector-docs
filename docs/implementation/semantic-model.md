# Configure the semantic model

!!! warning "Page status: Draft"
    This sequence reflects current verified behavior and requires implementation-lead review before validation.

## Entry criteria

Foundation Dimensions, Periods, and Versions have passed display-mode verification.

The logical semantic design has also been approved before configuration begins. It should contain:

- independent expected Node totals and reporting bases;
- a reusable Node hierarchy and formula contract;
- source and derived Factor definitions;
- candidate Dimensions and Factor natural grains;
- explicit recognition timing, units, signs, and denominators;
- a decision log identifying confirmed assumptions and unresolved gaps.

Do not confuse logical design order with system-entry order. During design, outcomes and formulas identify the Factors and Dimensions that are needed. During configuration, Dimensions and policies are created first because Factors must reference them, and Factors are created before Nodes can reference the Factors.

## 0. Validate the formula contract

Before entering policies or Factors, review every Node formula for:

1. **Business meaning:** the formula represents the intended economic event.
2. **Accounting and timing:** production, shipment, recognition, sell-through, and period-end balances are not conflated.
3. **Units and signs:** quantities, rates, currency conventions, percentages, and scaling resolve to the Node Format.
4. **Reuse:** dimensional slices do not become duplicate Nodes or duplicate formulas.
5. **Executability:** source Factors, derived dependencies, operators, and denominators can be represented without a hidden spreadsheet step.
6. **Acceptance:** the outcome can reconcile to an independently established total or control.

Node formulas and derived-Factor formulas are different contracts. A Node defines the business outcome being explained. A derived Factor prepares a reusable input used by one or more Nodes.

### Distinguish Dimensions, Nodes, Factors, and Bridge results

Before creating an object, ask which question it answers:

| Object | Question it answers | Example | Common modelling error |
| --- | --- | --- | --- |
| Dimension | Which business slice or coordinate? | Segment, Country, Product | Creating `Commercial Revenue` and `Consumer Revenue` as separate Nodes when both use the same Revenue formula |
| Node | Which business outcome should reconcile and be explained? | Revenue, Gross Profit, Operating Profit | Duplicating one formula for every organizational or portfolio slice |
| Factor | Which reusable input or driver determines the outcome? | Units, Local Price, FX Rate | Creating `FX Impact` as an input even though impact is calculated from changing FX rates |
| Bridge result | How much of the baseline-to-current movement is attributable to each Factor and interaction? | Pure FX effect, interaction/mix, Shapley value | Creating impact Nodes or Factors that duplicate decomposition output |

Use this decision rule:

1. If only the member selection changes while the economic formula remains the same, use a Dimension filter on the same Node.
2. If the economic outcome or formula changes, consider a separate Node.
3. If a value is entered or derived once and reused in calculations, model it as a Factor.
4. If a value exists only because baseline and current results are compared, calculate it in the Bridge or presentation layer unless it has an independently governed accounting meaning.

For example, Segment Performance is normally a view of Revenue or Operating Profit filtered by Segment—not a second Node tree. Similarly, `FX_RATE` is a Factor, while FX impact is normally a BRRES attribution. This separation prevents duplicated formulas, inconsistent totals, and competing explanations of the same movement.

### Require a causal path from mix to outcome

A compositional-share Factor describes how a fixed total is distributed across members. Its members must sum to the governed total—normally `1.0`—within each parent group. The resulting additive quantity is the parent total multiplied by those shares, not a broadcast copy of the parent total.

A mix change affects a financial outcome only when downstream economics differ by the mix Dimension. For example, changing Route-to-Market Mix can affect Revenue or profit when routes carry different prices, discounts, rebates, recognition timing, commissions, channel spend, or fulfilment costs. If every downstream rate and cost is identical or lacks Route grain, the aggregate P&L impact of changing the route shares should be zero.

For fulfilment, distinguish physical activity from contractual cost responsibility. Route may change transport Mode Mix and Fill Rate, while commercial terms determine the share of freight borne by the reporting company. A governed freight-responsibility rate prevents the model from charging identical freight to every route regardless of Incoterms or channel arrangements. Carrier rates and FX may remain at a coarser billing-country grain and be reused through explicit policies.

For every proposed controllable mix Factor, document:

1. the parent total it allocates;
2. the members across which it sums to one;
3. the grain of the parent group;
4. the downstream Factors whose values differ by that Dimension;
5. the Nodes through which the economic effect appears.

This causal-path test prevents a Dimension from being added for presentation while the Factor model remains incapable of calculating its business impact.

Also distinguish numerical propagation from analytical attribution. A source Factor may correctly affect an outcome through a derived Factor while remaining only a supporting dependency in the configured Analysis. If the current decomposition contract attributes only the Factors directly present in a Node formula, an executive control vector hidden several derivation levels upstream will not appear as its own named impact.

Where a transitive driver must be visible, define an explicit analytical expression or governed decomposition mapping that exposes it. Do not create a second input Factor or silently duplicate the business definition. If an expanded Node expression repeats a governed intermediate calculation, add a reconciliation control proving that the expanded subexpression equals the reusable intermediate Factor or Node for every applicable coordinate.

## 1. Define dimension policies

Create reusable Dimension Policy Templates before Factors. For each Factor, classify every Analysis Dimension as:

- **Native/source dimension** when the Factor is entered at that dimension;
- **Missing/expansion target** when a governed method must make the Factor available at that dimension.

The source grain is the grain at which the Factor data input happens. Do not infer it from the full-grain report where a value may have been repeated for presentation.

Factors and Dimensions should already have been co-designed during planning. At this stage, create only the reusable policies required by the approved Factor–Dimension grain contract.

The workshop required these reusable patterns:

| Template pattern | Native dimensions | Governed expansion |
| --- | --- | --- |
| All dimensions native | Country + Product + Mode | None |
| Country and Product native | Country + Product | Broadcast to Mode |
| Product native | Product | Broadcast to Country and Mode |
| Country and Mode native | Country + Mode | Broadcast to Product |

See [Dimension Policy Templates](../configuration/dimension-policy-templates.md) for the compatibility matrix and runtime status of each method.

## 2. Define population policies

Separate uploaded leaf Factors from derived Factors. The workshop used one policy for contributor-uploaded Actual inputs and another for Factor derivation.

## 3. Create Factors in dependency order

Before entering FAC01 or FAC09, maintain a reviewable Factor contract that contains both source and derived layers. At minimum, record:

- stable code and business name;
- source, derived, allocated, or filtered-view classification;
- Format, behavior, and leaf role;
- derivation expression and every source object;
- candidate natural grain and Period semantics;
- financial classification, origin, controllability, and control relevance;
- evidence class and review status;
- whether the calculation is executable through a proven capability or requires an adapter or design decision.

Validate the contract mechanically before configuration: every direct Factor referenced by a Node must exist exactly once, every derivation source must resolve, and derivations must be acyclic. Do not mark a Factor ready merely because its business formula is understood. Eligible-base filtering, FIFO layer release, cross-grain mappings, governed allocations, and Dimension-filtered consumption may require resource-specific execution support beyond a simple Product or Ratio operator.

Filtered views deserve particular care. `People Cost where Function = Sales` or `Revenue where Segment = Commercial` may be formula-consumption context rather than new Factor master data. Keep such rows explicitly provisional until the Node/filter contract or governed adapter is defined. This preserves semantic completeness without multiplying Factors simply to represent Dimension members.

Create source Factors before the derived Factors that depend on them. Keep Factor behavior separate from derivation logic: behavior governs aggregation, while Product or Ratio defines how a Factor is calculated.

An illustrative chain is:

```text
HW KUnits × MoT Mix = XFactory
KG Per Unit ÷ Fill Rate = Weight
XFactory × Weight = Tonnage
Tonnage × Rate Per KG = Freight Spend
```

Assign the approved Format, leaf role, behavior, Dimension Policy Template, Population Policy Template, Data Collation Group, and Analysis Group. Use FAC03 to review the consolidated register.

For every Factor, preserve:

- its natural input grain;
- whether it is a flow, balance, rate, share, or derived value;
- the event that determines its Period;
- the eligible base for every percentage or rate;
- currency-rate convention and reporting basis;
- source evidence, owner, controllability, and approval relevance.

Also record the Factor's business-model applicability. A market-volume or market-share Factor that is valid for newly shipped hardware should not automatically drive consumables, installed-base monetization, subscriptions, or support services. Those revenue streams may require different operating bases—such as installed devices, usage, pages, subscribers, seats, or contracts—even when they ultimately reconcile to the same financial-statement Node. Keep the management-driver branches distinct, then reconcile them explicitly to the applicable external reporting classification.

For an installed-base revenue stream, choose a time basis that represents exposure during the calculation interval. Opening or closing devices alone may not represent a quarter in which installations, retirements, and usage occur continuously. A governed quarter-average active base can therefore be a legitimate supplied semi-additive Factor. Document how that average is produced; do not imply that averaging two endpoint snapshots reproduces the underlying daily exposure unless the business has accepted that approximation.

Do not group all recurring revenue under one convenient Node. A subscription may be classified externally as Product revenue while a support contract is classified as Services revenue, even when both use an `active base × revenue per active unit` management abstraction. Preserve separate active bases and yields where the reporting classification differs, then reconcile each branch to the correct statement line.

When modelling a price waterfall, do not apply every discount percentage silently to the same base. Define the eligible base for contractual discounts, promotions, rebates, returns, and credits. When modelling inventory cost, distinguish current production cost from cost released from governed inventory layers and from abnormal current-period losses.

Do not force Production Units to equal Units Shipped merely to close the model. Production is a supply and capacity flow; shipments are a market flow. Inventory buffers, production lead times, outsourced-manufacturer timing, and Product transitions can create legitimate differences. Maintain production at its observed supply grain, then reconcile both flows at an intentionally chosen common grain:

```text
Beginning Finished-Goods Inventory
+ Production
− Shipments
= Ending Finished-Goods Inventory
```

Treat beginning and ending inventory as snapshots and production and shipments as Period flows. Require a nonnegative ending balance. Add warehouse, in-transit, or shipment-origin detail only when a decision or control requires it; otherwise a Product-level reconciliation is often the safer first implementation. Where production and shipments originate at different natural grains, define the aggregation explicitly rather than broadcasting either additive flow.

Apply the same discipline to procurement price. Keep the contracted or standard benchmark distinct from supplier rebates, spot-buying premiums, and other signed purchase-price variance. Reconcile those components to an effective procurement price, then preserve the inventory policy that determines when the resulting variance is capitalized and when it reaches cost of revenue. A current procurement event and its P&L release may occur in different Periods.

Where that timing matters, preserve two governed views: operational purchase-price variance measured on current receipts, and released purchase-price variance carried by the inventory layers recognized in cost of revenue. Use the released view in the Gross Profit Bridge. Keep the receipt-based view as a leading Procurement indicator so current sourcing pressure is visible without being misrepresented as current earnings impact.

For fixed manufacturing overhead, use one governed cost pool and an explicit capacity denominator. Normal practical capacity is usually more defensible than theoretical maximum because ordinary maintenance and planned downtime should not automatically become an adverse idle-capacity event. Derive the absorption rate, absorbed overhead, and idle or unabsorbed cost from that common basis, and require the components to reconcile. Treat overtime, surge capacity, outsourcing, or exceptional downtime as explicit events instead of allowing negative idle cost or an unexplained plug.

Avoid combining engineered labor content, operating efficiency, base wages, and overtime into one labor-hours or labor-rate input. A useful separation is standard hours per unit, an actual-to-standard efficiency Factor, base labor rate, overtime share, and incremental overtime premium. This lets the resulting labor cost explain product complexity, productivity, wage inflation, and overtime independently. Define the efficiency convention and overtime bounds explicitly so favorable and unfavorable movements cannot be interpreted backwards.

Do not apply an owned-factory cost model automatically to outsourced production. Where the operating model is mixed, maintain governed applicability metadata on the manufacturing location or supplier relationship. Internally managed production may expose labor, machine efficiency, overhead absorption, and idle capacity; outsourced production may instead expose conversion charge per unit, capacity premium, and signed quality or rework settlement. Route each row through exactly one branch and reconcile both branches to the same Manufacturing and Conversion Cost Node. Use an applicability attribute rather than a new analytical Dimension when the field only chooses calculation logic and is not a requested business slice.

### Separate tariff incidence from economic burden

Do not calculate tariff expense by multiplying every shipped unit by one tariff rate. A shipment can be domestic, legally non-dutiable, or subject to duty that another party bears. Model those questions explicitly:

```text
Dutiable Import Units
= Shipped Units × Dutiable Import Share × Company Duty-Bearing Share

Gross Tariff and Duty
= Dutiable Import Units × Customs Value per Unit × Effective Tariff Rate

Net Tariff and Duty
= Gross Tariff and Duty − Tariff Refunds and Recoveries
```

The tariff key normally needs destination Country, origin or manufacturing location, and Product classification. Dutiable Import Share may total less than one across origins because the remainder can be domestic or otherwise non-dutiable. Keep the company duty-bearing share separate so commercial responsibility is not confused with legal incidence. Record refunds and recoveries as positive benefits subtracted from gross duty rather than silently netting them into the tariff rate.

This structure lets decomposition distinguish shipment volume, sourcing mix, dutiable exposure, burden sharing, customs value, policy rate, and recovery actions. Validate both shares within zero and one, cap the dutiable origin shares at one for each destination-Product group, and preserve gross duty and recoveries as separately auditable evidence.

Apply an evidence-proportionality test before adding exceptional cost pools. Expedites, demurrage, premium freight, or disruption costs can be valid Factors when the implementation has a credible event baseline and can preserve the event category, affected lanes, evidence, owner, mitigation, and due date. If those foundations are absent, defer the exception Factor rather than inventing synthetic precision or creating a balancing plug. A narrower model built from standard freight and tariff drivers is more trustworthy than a richer model whose exception story cannot be independently supported.

### Keep workforce cost separate from Product allocation

Maintain headcount, compensation, and People Cost at the grain where workforce ownership is real—commonly Function and employee or cost-center Country. Do not add Product merely because a later profitability report needs a Product column. Doing so can duplicate shared employees and present allocated cost as directly observed cost.

Use one reusable People Cost relationship:

```text
People Cost = Average FTE × Compensation per FTE × applicable Country FX Rate
```

Function-specific costs should be filtered uses of that relationship rather than duplicate formulas. If Product profitability requires attribution, assign dedicated resources directly and allocate shared costs through a separate governed driver whose shares reconcile to one. Preserve the distinction in decomposition: headcount, compensation, Country mix, and FX change the underlying workforce cost; allocation mix changes only its Product presentation.

For variable sales compensation, define both the timing basis and eligible revenue base explicitly. A proportionate executive model can use:

```text
Variable Sales Compensation
= Eligible Recognized Net Revenue × Effective Commission Rate
```

Derive eligible recognized net revenue after the recognition and contra-revenue waterfall, then apply contractual eligibility rules. Do not add discounts, rebates, channel incentives, returns, credits, or market-development funding back into the commissionable base, and do not count those items again as compensation expense. Bookings, quotas, attainment tiers, and accelerators require separate semantics when they are genuinely needed; they should not be implied by an effective commission rate.

Keep controllable investment and its intended business outcome separate unless a causal response model is genuinely governed. For example, Marketing Spend and Market Share can both be submitted and reviewed, and approval evidence can record the expected outcome, owner, milestone, and review date. That does not justify deriving Market Share mechanically from spend. Decomposition may present both movements together, but the narrative must distinguish an intended relationship from calculated causation. Add an elasticity, response curve, or lag model only when its evidence source, eligible population, time basis, and validation method are explicit.

Treat restructuring charges and productivity benefits as different objects. A restructuring charge can be a separately governed one-time GAAP expense. Realized productivity benefits should appear through the operating Factors that actually changed—such as headcount, compensation, facilities, program spend, labor hours, or efficiency—not through an additional favorable savings line in the P&L formula.

Keep the program's gross run-rate commitment, realized-to-date assessment, affected Factors, accountable owner, milestone, due date, and delivery risk as governed control evidence. This lets approval and narrative distinguish promised savings from benefits visible in the forecast. Introduce a residual savings Factor only when it has a distinct economic definition and reconciliation proving it is not already captured elsewhere.

### Persist the decision rationale with every Factor

A Factor note is part of the semantic contract. It should explain:

- which executive or operational decision the Factor supports;
- why it is maintained separately from adjacent drivers;
- its critical grain, timing, sign, denominator, eligible base, or applicability boundary;
- how a material movement should be interpreted in approval and decomposition.

Carry that rationale from the design contract into the maintained Factor record. Do not leave it only in meeting notes or implementation prose. This helps reviewers understand why the input exists and gives an agent durable semantic evidence for choosing the right capability and Factor without inventing a duplicate.

For interest-bearing balances, also state the rate convention. When rates are annualized, calculate Period interest as average balance multiplied by annualized rate and the exact calendar day fraction. Source the day counts from governed calendar metadata, not manually entered Factors, and apply the fraction exactly once.

Model only the tax detail required by the implementation objective. A business-finance implementation may use one Corporate Effective Tax Rate to bridge Pretax Income to Tax Provision and EPS. Do not introduce a Tax Jurisdiction Dimension, legal-entity profit allocation, permanent differences, credits, and discrete tax-event logic merely because a comprehensive tax engine could exist. Those belong in a separately scoped Corporate Finance or tax-planning capability when the customer needs them. Keep external GAAP-to-non-GAAP tax adjustments as reporting reconciliation evidence rather than confusing them with operational forecast drivers.

Apply the same proportionality to EPS. When the implementation needs EPS as an executive endpoint but not capital-allocation analysis, use the governed diluted weighted-average share count from the applicable reporting basis as one Corporate denominator. Do not decompose it into opening shares, repurchase timing, option dilution, and equity awards unless those are decisions the implementation is intended to support. The simpler model still reconciles `Net Earnings ÷ Diluted Weighted-Average Shares` while leaving detailed share-capital mechanics to a separately scoped Corporate Finance capability.

Some statement lines may be required only for reconciliation. When an implementation is deliberately focused on one business, load an out-of-scope business's published Revenue and Cost as governed reconciliation inputs instead of inventing unsupported operational drivers. The statement remains complete, but decomposition must not claim detail beneath that boundary. Record the limitation explicitly and design the omitted business as a separate module only when its decisions become part of the implementation objective.

Use the same rule for external reporting adjustments. Preserve acquisition charges, amortization, litigation, retirement-related items, and tax adjustments when they are required for an exact GAAP-to-non-GAAP bridge, but do not manufacture Product, Country, owner, or mitigation detail beneath a published total. Keep reconciliation-only items collapsed in the operating story. An adjustment such as restructuring may remain analytically visible when it has a separately governed connection to transformation commitments and realized operating changes.

When a detailed operating model does not yet explain an independently sourced statement total, expose the remaining difference as a calculated reconciliation residual. Do not ask users to forecast it as though it were a business driver, and do not generate a causal narrative for it. Display both its value and its percentage of the anchored total, establish explicit warning and readiness thresholds, and reduce it by improving the model. A transparent residual is safer than a plausible-sounding “Other” Factor that conceals model incompleteness.

Audit every Factor or Node containing “Other,” “residual,” “adjustment,” or “reconciliation.” A legitimate “Other” category can remain when it has an explicit eligible base, rate, source evidence, and decision meaning. A residual should normally be calculated from an independently observed outcome and named components rather than entered by a user. Material operational residuals require cause evidence and ownership; statement-only residuals carry no generated operating narrative. The exit condition is that no editable unexplained balancing plug remains.

### Reuse exchange rates without losing business context

When one governed P&L exchange-rate basis applies, define one reusable `FX_RATE` Factor at Country grain rather than separate Revenue FX, Procurement FX, Manufacturing FX, Freight FX, and Opex FX Factors. Baseline and current rates are Period values of that Factor, not separate Factor definitions.

Each currency-bearing calculation must still identify the Country role that selects the rate:

- selling Country for revenue;
- sourcing Country for procurement;
- manufacturing Country for conversion cost;
- carrier billing or transaction Country for freight;
- employee or cost-center Country for people cost.

These roles may resolve against the same governed Country master data. A new FX Factor is justified only when the rate basis genuinely differs—for example, a hedge rate, budget rate, transaction-date rate, or period-end balance-sheet rate—not merely because a different formula consumes it.

Keep constant-currency reporting distinct from decomposition. For `R = L × F`, a current-at-baseline constant-currency comparison uses current local value with the baseline FX rate. A Bridge can instead calculate the pure FX effect from the baseline local value, retain the local-value/FX interaction, and allocate that interaction through Shapley values. Constant currency may therefore be a useful presentation lens, but it should not automatically become a duplicate primary Node or FX Factor.

#### Example decision and pivot record

| Decision field | Recorded learning |
| --- | --- |
| Initial approach | Define separate current and baseline Revenue FX Factors, purpose-specific cost FX Factors, a Constant-Currency Revenue Node, and an FX-impact Node. This initially appeared to make executive reporting explicit. |
| Change trigger | Formula review showed that Period already distinguishes baseline from current, purpose-specific Factors repeated the same economic rate, and dedicated constant-currency Nodes overlapped with Bridge decomposition. |
| Confirmed design | Maintain one Country-grain `FX_RATE` per Period and identify the applicable Country role in each consuming calculation. Keep constant currency as an optional presentation lens. |
| Why it is better | One governed value is entered once and reused; semantic context remains explicit; the primary Node graph is smaller; and pure, interaction, and Shapley effects remain the responsibility of the decomposition layer. |
| Important exception | Create another rate Factor when its basis is economically different, such as a hedge, budget, transaction-date, or period-end translation rate. |
| Required follow-through | Update formula, Factor, grain, Dimension-policy, seed-data, acceptance, and narrative contracts together; verify that no removed Factor or Node remains referenced. |

This example illustrates why the implementation journal must preserve both the plausible initial design and the evidence-led pivot. Recording only the final `FX_RATE` definition would lose the reusable lesson about avoiding Factor and Node duplication.

See [Factors](../configuration/factors.md) for Product, Ratio, weighted-rate, and Format guidance.

## 4. Create the analytical Node

Choose Node Factors that expose the controllable drivers users need to explain. A Freight Spend PR Node can use:

```text
HW KUnits × MoT Mix × Weight × Rate Per KG
```

This retains four analytical drivers while KG Per Unit and Fill Rate remain supporting dependencies inside Weight. When Factors have different native grains, every missing Node dimension must be explicitly and compatibly governed; otherwise Bridge preparation should stop rather than guess.

Verify the saved formula and operators in NOD03. See [Nodes and calculation structures](../configuration/nodes.md).

Reuse one Node across its governed dimensional slices. For example, Commercial and Consumer Revenue should normally be views of one Revenue Node filtered by the appropriate Business Unit or Customer member. Create separate Nodes only when the economic formula, reporting basis, or decision meaning genuinely differs.

## 5. Create the Analysis

Scope the Analysis to the required Dimensions and Node. Selecting the Node should identify:

- analytical Factors used directly by the Node;
- supporting derivation dependencies;
- uploaded leaf Factors required from contributors.

Do not add every registered Factor merely because it is available. Review the effective scope in the Analysis display transaction.

## Exit evidence

- [ ] Every uploaded Factor has the correct native-grain policy
- [ ] Every Node formula has approved business meaning, timing, units, signs, and denominator
- [ ] Every cross-grain expansion is explicit and compatible
- [ ] Every derived Factor resolves its sources in dependency order
- [ ] Every Node-referenced Factor exists exactly once in the Factor contract
- [ ] Adapter-required derivations are identified rather than treated as currently executable
- [ ] Filtered Dimension views have not been duplicated as Factors without a distinct economic meaning
- [ ] The Node formula exposes the intended business drivers
- [ ] Executive control vectors are direct analytical Factors or have an explicit governed decomposition mapping
- [ ] Expanded analytical expressions reconcile to any repeated intermediate calculation
- [ ] Dimensional slices reuse common Nodes instead of duplicating calculations
- [ ] Flow, balance, recognition, and inventory-release timing are explicit
- [ ] Production and shipment flows remain independent and reconcile through governed finished-goods inventory at a deliberate common grain
- [ ] Tariff models separate legal incidence, economic burden, customs value, policy rate, and recoveries
- [ ] Workforce inputs use their observed organizational grain and keep Product attribution as a separate governed allocation
- [ ] Variable compensation declares its timing basis, eligible revenue base, exclusions, and effective-rate meaning
- [ ] Investment Factors do not imply causal outcomes without a governed and validated response model
- [ ] Restructuring charges, savings commitments, realized operating benefits, and remaining delivery gaps are separated without double counting
- [ ] Every Factor note records its decision rationale and critical semantic boundary
- [ ] Annualized financing rates use governed Period day counts and apply the time fraction exactly once
- [ ] Tax detail is proportionate to the implementation objective and does not crowd out the intended business-finance decisions
- [ ] EPS denominator detail is proportionate to the intended operating or capital-allocation story
- [ ] Reconciliation-only businesses are explicit and are not given synthetic driver stories merely to fill the model
- [ ] Reconciliation-only reporting adjustments preserve the external bridge without unsupported operating detail
- [ ] Any residual is calculated, visibly labelled, threshold-controlled, and excluded from causal narrative
- [ ] Every “Other,” residual, adjustment, and reconciliation item has been classified and no editable unexplained plug remains
- [ ] Reused FX Factors declare the applicable Country role and rate convention
- [ ] Analysis scope separates analytical Factors, supporting dependencies, and required uploads
- [ ] Data Collation and Analysis grouping prerequisites are complete

Next: [Load and govern data](data-workflow.md).
