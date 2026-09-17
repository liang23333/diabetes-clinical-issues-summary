# Endocrinology & Diabetes Clinical Tool Issues Analysis

## Overview
A curated sample of open issues from leading open-source diabetes clinical and decision-support systems (Loop, xDrip, AndroidAPS) was analyzed. The investigation specifically focuses on high-risk problems frequently encountered in digital endocrinology workflows: **dosing calculation/delivery safety errors** and **data integration/synchronization failures**.

## Summary of Categories

| Category / Theme | Issue Count | Primary Clinical Risk |
|---|:---:|---|
| **Dosing & Insulin Safety Errors** | 3 | Insulin stacking, automated over-delivery, severe iatrogenic hypoglycemia |
| **Data Integration & Interoperability Failures** | 3 | Inaccurate remote clinical audits, missing bolus telemetry, distorted basal-bolus profiles |
| **Total Sampled** | **6** | — |

## Categorized Issues Breakdown

### Dosing & Insulin Safety Errors (3 issues)

#### [LoopKit/Loop #2385: Loop reporting 0 IOB following bolus](https://github.com/LoopKit/Loop/issues/2385)
- **Repository**: `LoopKit/Loop`
- **Subtheme**: Insulin on Board (IOB) Tracking Failure
- **Clinical Impact**: Loop algorithm falsely reports 0 IOB after insulin administration, causing severe stacking of automated and manual boluses (patient received >17U excess insulin within 30 min).

#### [NightscoutFoundation/xDrip #1772: IOB Calculation Does Not Use Proper Insulin Duration (Humalog)](https://github.com/NightscoutFoundation/xDrip/issues/1772)
- **Repository**: `NightscoutFoundation/xDrip`
- **Subtheme**: Pharmacokinetic Decay Modeling Error
- **Clinical Impact**: Hardcoded ~2.4h duration overrides clinician settings (3+ hours), underestimating active insulin and prompting excessively high suggested boluses.

#### [LoopKit/Loop #2415: Loop UI update request: failed bolus after carbs are saved can lead to user entering carbs again](https://github.com/LoopKit/Loop/issues/2415)
- **Repository**: `LoopKit/Loop`
- **Subtheme**: Bolus Entry Workflow & Duplicate Delivery Risk
- **Clinical Impact**: Interface ambiguity and unhandled failed bolus states allow duplicate carbohydrate submissions and repeated bolusing (e.g., 3 identical carb entries delivering triple dose).

### Data Integration & Interoperability Failures (3 issues)

#### [NightscoutFoundation/xDrip #1158: Basal insulin dose syncing as Bolus to tidepool.org](https://github.com/NightscoutFoundation/xDrip/issues/1158)
- **Repository**: `NightscoutFoundation/xDrip`
- **Subtheme**: Cross-Platform Telemetry Mapping Error
- **Clinical Impact**: Long-acting basal doses (Lantus) are transmitted to Tidepool as rapid boluses, distorting retrospective EHR/clinical review and masking true basal-bolus distribution.

#### [LoopKit/Loop #2496: Tidepool doesn't sync therapy settings](https://github.com/LoopKit/Loop/issues/2496)
- **Repository**: `LoopKit/Loop`
- **Subtheme**: Clinical EHR / Cloud Portal Configuration Sync Failure
- **Clinical Impact**: Active therapy settings (basal rates, ISF, carbohydrate ratios) fail to sync with Tidepool, leaving clinicians without verified therapy parameters when reviewing glycemic control.

#### [nightscout/AndroidAPS #2048: AAPS v3.1.0.2 no longer syncs pump manual bolus history for IOB calculations](https://github.com/nightscout/AndroidAPS/issues/2048)
- **Repository**: `nightscout/AndroidAPS`
- **Subtheme**: Hardware-to-Controller Telemetry Desynchronization
- **Clinical Impact**: Manual boluses given on pump hardware fail to sync into the closed-loop controller's IOB model upon reconnect, leading the system to over-deliver automated insulin.

## Clinical Takeaways & Systemic Risks

1. **IOB Miscalculation as a Leading Threat to Safety**: In automated insulin delivery (AID) and bolus calculators, any discrepancy in active insulin tracking (either software zero-reset or unmodeled decay curves) directly degrades the safety buffer, frequently precipitating hyper-dosing events.
2. **Interoperability & Data Translation Pitfalls**: Data handoffs between edge mobile controllers (e.g., xDrip, Loop) and clinical aggregates (e.g., Tidepool, Nightscout) often corrupt semantic insulin metadata (such as labelling basal insulin as boluses). Clinicians making adjustments based on aggregated portals risk titrating medications against erroneous delivery records.
3. **Offline/Hardware Reconciliation Gaps**: When patient pump hardware delivers insulin independently of the smartphone app during Bluetooth disconnects, failure to backfill pump logs into the algorithmic state upon reconnect poses an immediate risk of insulin stacking.
