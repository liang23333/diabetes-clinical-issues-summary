# Endocrinology & Diabetes Clinical Tool Issues Analysis

This repository contains an investigation into open issues reported in major open-source diabetes clinical and decision-support systems (**Loop**, **xDrip+**, and **AndroidAPS**). It specifically focuses on critical clinical problems commonly reported by clinicians and patients: **insulin dosing / safety errors** and **data integration / synchronization failures**.

The detailed summary report is also available in [`summary.md`](summary.md).

---

## Summary of Categories

| Category / Theme | Issue Count | Primary Clinical Risk |
|---|:---:|---|
| **Dosing & Insulin Safety Errors** | 3 | Insulin stacking, automated over-delivery, severe iatrogenic hypoglycemia |
| **Data Integration & Interoperability Failures** | 3 | Inaccurate remote clinical audits, missing bolus telemetry, distorted basal-bolus profiles |
| **Total Sampled** | **6** | — |

---

## Categorized Issues Breakdown

### 1. Dosing & Insulin Safety Errors (3 issues)

#### [LoopKit/Loop #2385: Loop reporting 0 IOB following bolus](https://github.com/LoopKit/Loop/issues/2385)
- **Repository**: `LoopKit/Loop`
- **Subtheme**: Insulin on Board (IOB) Tracking Failure
- **Clinical Impact**: The automated closed-loop algorithm reported 0 IOB despite large boluses being administered. Because active insulin was zeroed out, the system continued issuing automatic boluses while prompting caregivers to stack manual doses (resulting in over 17 units of insulin delivered within 30 minutes and severe hypoglycemia).

#### [NightscoutFoundation/xDrip #1772: IOB Calculation Does Not Use Proper Insulin Duration (Humalog)](https://github.com/NightscoutFoundation/xDrip/issues/1772)
- **Repository**: `NightscoutFoundation/xDrip`
- **Subtheme**: Pharmacokinetic Decay Modeling Error
- **Clinical Impact**: xDrip hardcoded an insulin action duration curve of ~2.4 hours regardless of user/clinician settings (set to 3+ hours). By assuming insulin decays faster than it does in vivo, the calculator significantly underestimates IOB and recommends dangerously elevated boluses.

#### [LoopKit/Loop #2415: Loop UI update request: failed bolus after carbs are saved can lead to user entering carbs again](https://github.com/LoopKit/Loop/issues/2415)
- **Repository**: `LoopKit/Loop`
- **Subtheme**: Bolus Entry Workflow & Duplicate Delivery Risk
- **Clinical Impact**: Ambiguous UI state handling when boluses encounter transmission delays or failed confirmations leads caregivers/patients to repeatedly submit carb entries, resulting in multiple full-dose boluses (e.g., three identical carb entries delivering triple the intended dose).

---

### 2. Data Integration & Interoperability Failures (3 issues)

#### [NightscoutFoundation/xDrip #1158: Basal insulin dose syncing as Bolus to tidepool.org](https://github.com/NightscoutFoundation/xDrip/issues/1158)
- **Repository**: `NightscoutFoundation/xDrip`
- **Subtheme**: Cross-Platform Telemetry Mapping Error
- **Clinical Impact**: In multiple-insulin regimens, long-acting basal insulin (Lantus) is transmitted and logged as rapid boluses in Tidepool. This fundamentally corrupts remote clinical decision-making, as endocrinologists reviewing Tidepool reports cannot distinguish basal coverage from mealtime boluses.

#### [LoopKit/Loop #2496: Tidepool doesn't sync therapy settings](https://github.com/LoopKit/Loop/issues/2496)
- **Repository**: `LoopKit/Loop`
- **Subtheme**: Clinical EHR / Cloud Portal Configuration Sync Failure
- **Clinical Impact**: Active therapy profiles (basal rate schedules, insulin-to-carb ratios, and insulin sensitivity factors) do not synchronize to Tidepool despite blood glucose and insulin records syncing. Clinicians are unable to verify the active therapy parameters driving patient outcomes.

#### [nightscout/AndroidAPS #2048: AAPS v3.1.0.2 no longer syncs pump manual bolus history for IOB calculations](https://github.com/nightscout/AndroidAPS/issues/2048)
- **Repository**: `nightscout/AndroidAPS`
- **Subtheme**: Hardware-to-Controller Telemetry Desynchronization
- **Clinical Impact**: When boluses are administered directly on pump hardware (e.g., Roche Accu-Chek Combo), the history is omitted from the controller's IOB algorithm upon reconnection. Because the algorithm is unaware of the delivered bolus, it risks over-delivering automated basal and correction doses.

---

## Clinical Takeaways & Systemic Risks

1. **IOB Miscalculation as a Leading Threat to Patient Safety**: In automated insulin delivery (AID) and bolus calculator tools, any deviation in active insulin modeling (premature decay assumptions, zero-reset glitches) eliminates safety margins, creating acute risk for severe iatrogenic hypoglycemia.
2. **Interoperability & Data Translation Pitfalls**: Data handoffs between edge mobile controllers (e.g., xDrip, Loop) and clinical cloud platforms (e.g., Tidepool, Nightscout) frequently suffer semantic degradation. Mislabeling long-acting basal as bolus insulin misleads endocrinologists during telehealth reviews and therapy adjustments.
3. **Hardware-Controller Telemetry Gaps**: Clinical decision algorithms must implement robust bidirectional reconciliation for offline hardware events. Failing to capture manual pump interactions creates an incomplete picture of patient therapy.
