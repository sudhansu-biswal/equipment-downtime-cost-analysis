# Equipment Downtime & Maintenance Cost Analysis
### Where 18 months of breakdowns actually cost money — Excel 365 and Power Query

> **The workshop's log recorded hours, not money.** Converting 686 downtime events into a full cost model — parts, labour and production loss — put a figure of **₹2,99,37,396** on 18 months of downtime, and showed that **two of twelve assets carry 49% of it**. Unplanned cost is rising **₹55,600 per month**, and a six-month forecast fitted on that trend backtests at **22.5% MAPE**.

*Synthetic dataset, modelled on real fabrication workshop maintenance log structure. Asset codes, areas and rates are invented.*

---

## The problem

A fabrication workshop was logging every breakdown properly — date, asset, failure type, shift, restoration hours, parts cost. What it had no way to do was answer the question the log was collected for.

Management could see that March had 42 hours of downtime. They could not see what that cost, because the log recorded **hours** and the money sat in three places: the parts figure in the log, a labour rate held separately, and production loss that was never written down anywhere.

The consequence is the one you find in most maintenance functions. Downtime gets treated as a fixed cost of running a workshop rather than as something concentrated in specific machines, specific failure types and specific shifts — and preventive maintenance gets allocated by habit instead of by exposure.

---

## The data

- **686 downtime events**, 01-Feb-2025 to 31-Jul-2026 (18 months)
- **12 assets** across **6 equipment types** and **4 areas**
- **3,845.0 total downtime hours** — 3,579.2 unplanned, 265.8 planned
- Fields: event date, asset, equipment type, area, criticality, maintenance type, failure type, shift, restoration hours, parts cost
- A separate rate table supplying labour rate per hour and production-loss rate per hour by equipment type

**The critical design point: cost was not in the file.** `Parts_Cost_INR` covered spares only. Labour and production loss had to be constructed from the rate table, and how you construct them determines every number downstream.

---

## What I built

**A three-component cost model.** Total event cost = parts + (restoration hours × labour rate) + (restoration hours × production-loss rate for that equipment type). Rates are held as **editable Power Query parameters**, not hard-coded — the client changes one cell and the entire 18-month model recosts.

**One explicit assumption, stated rather than buried:** planned maintenance carries no production loss, because it is scheduled into windows when the line is not running. Unplanned downtime carries the full loss. This is the assumption that produces the 98.2% unplanned share of cost, and it is the first thing a client should be asked to confirm.

**Verified against eight control checks** before any analysis was built — event count, hours split, parts total, and per-area and per-type subtotals reconciled to the source.

**Five analysis blocks** on the data model: cost summary, asset ranking with Pareto cumulative, failure-type breakdown, preventive-versus-breakdown comparison, and monthly restoration-time trend with a shift sub-analysis.

**An interactive dashboard** — five KPI cards, five charts, four slicers and a timeline, all driven off a hidden engine sheet. Filtering to Fabrication Bay 1 returns ₹1,05,32,156 across 205 events at 98.9% unplanned, so the model holds under subsetting.

**A forecast tab** — three-month moving average, linear trend fitted on 18 months, six-month projection with ±25% bands, backtested against three held-out months.

---

## What it found

**Cost concentrates in two machines.** CMP-02 and CRN-01 — two of twelve assets — account for **49% of total downtime cost**. Anything spent spreading maintenance attention evenly across twelve assets is being spent in the wrong place.

**A matched pair makes the preventive maintenance case better than any average could.** CMP-01 and CMP-02 are the same equipment type in the same workshop. CMP-01 received **6 preventive jobs and cost ₹21 lakh**. CMP-02 received **3 and cost ₹78 lakh** — 3.7 times the cost on half the preventive work.

**Frequency is not cost.** The failure types that occur most often are not the ones that cost most. Ranking by event count, which is how most maintenance registers are read, points at the wrong problem.

**Restoration is getting slower.** Mean time to restore has worsened **19%** across the 18 months, and night-shift events take longest — a staffing and spares-access finding, not an equipment one.

**The trend is upward.** Unplanned cost is rising ₹55,600 per month on a linear fit. At that rate the next six months cost more than the last six, before any new failure mode appears.

---

## What this analysis does not prove

Two limits are worth stating, because a client will find them anyway and it is better that they hear them from me.

**The CMP-01 / CMP-02 comparison is an association, not a causal result.** Two assets is not a sample. The preventive-maintenance gap is the most plausible explanation and the pair is unusually well matched, but a difference in duty cycle, age or operator could produce the same picture. It is strong enough to justify a trial on CMP-02 — not strong enough to publish as a proven return.

**The headline figure is sensitive to the production-loss rate.** Loss rate is the largest single component of cost and it was supplied, not measured. If the real rate is materially different, the ₹2.99 crore total moves with it. The rankings and concentration findings are far more robust than the absolute number, because they depend on relative cost, not on the rate's level.

**The July 2026 spike is treated as an outlier.** It came in 40% above trend. The model holds the trend line rather than re-levelling to it. If August also exceeds ₹25 lakh, the trend should be refitted — that is a decision rule the client can apply themselves without reopening the model.

---

## Tools

Excel 365 · Power Query (M) · Data Model · Pivot Tables · Slicers & Timeline

## Files

| File | What it is |
|---|---|
| `Equipment_Downtime_Analysis.xlsx` | Full workbook — Read Me, Data, Analysis, Dashboard, Forecast |
| `scope_note.pdf` | The scope document written before the data was opened |
| `data/equipment_downtime_log_18m.xlsx` | Source log and rate table |
| `images/` | Dashboard, filtered dashboard and forecast exports |

---

*I spent 12 years in QA/QC on refinery, steel plant and fabrication projects — IOCL Paradip, Jindal Steel Angul, Tata Steel HSM. The assumption in this cost model about planned maintenance and production loss comes from having scheduled that work on live plants, not from a tutorial.*
