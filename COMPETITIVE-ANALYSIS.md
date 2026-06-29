# Competitive + Improvement Analysis — water-quality-explainers

Scope reviewed: `PLAN.md` v0.1.0 (2026-06-28) and `TASKS.md` (M0–M1 detail). Project: open,
plain-language, source-cited explainers of local drinking-water quality, US-first (EPA/state data)
with WHO as a health-context source. Risk tier: medium with a hard expert-sign-off escalation for any
health-action content. All competitor claims below are grounded in cited web sources (accessed
2026-06-29).

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually strong on governance and the two-sided-harm framing (false reassurance vs.
false alarm). It is structurally sound: MCL vs. MCLG, the violation taxonomy (health-based vs.
monitoring/reporting vs. treatment-technique), non-detect ≠ zero, sample-period vs. publication-date,
the "system data ≠ your tap" lead caveat, the private-well module, and the no-verdict rule are all
correct and well-placed. Findings, ordered by importance:

**A. Contaminant/health accuracy — the values the plan names are correct, but the PFAS standard is
mid-reversal and the plan treats it as settled.** The plan repeatedly anchors on "the 2024 PFAS
MCLs" and the "evolving Lead & Copper Rule" as the two regulatory-drift items. The 2024 numbers it
implies are right: PFOA and PFOS at **4.0 ppt (ng/L)** MCLs; PFHxS, PFNA, HFPO-DA (GenX) at **10
ppt**; a **Hazard Index of 1** for mixtures; rule effective **June 25, 2024**
([Federal Register, 2024-07773](https://www.federalregister.gov/documents/2024/04/26/2024-07773/pfas-national-primary-drinking-water-regulation);
[EPA technical overview PDF](https://www.epa.gov/system/files/documents/2024-04/drinking-water-utilities-and-professionals-technical-overview-of-pfas-npdwr.pdf)).
**But the standard is actively being unwound.** In May 2025 EPA announced it would *keep* PFOA/PFOS
at 4 ppt but extend the compliance deadline to **2031**, and **rescind** the limits for PFHxS, PFNA,
HFPO-DA and the Hazard Index; the two proposed rules were published **May 18, 2026** with comment
periods closing **July 20, 2026**
([EPA, "EPA Announces It Will Keep MCLs for PFOA, PFOS"](https://www.epa.gov/newsreleases/epa-announces-it-will-keep-maximum-contaminant-levels-pfoa-pfos);
[EPA Proposed PFAS Rescission Rule](https://www.epa.gov/sdwa/proposed-pfas-rescission-rule);
[Harvard EELP PFAS tracker](https://eelp.law.harvard.edu/tracker/pfas-in-drinking-water/)).
**Implication:** a PFAS contaminant-library entry written today against the 2024 rule risks being
wrong within the project's own M1 window. This is not a hypothetical "regulatory drift" risk — it is a
live, in-flight reversal. The plan's mitigation (re-review on rule change, `lastReviewed`) is correct
in principle but under-specified for a standard that is *currently in proposed-rescission*. The
explainer engine should be able to render "MCL set in 2024, compliance extended/under reconsideration"
state, not just a single number.

**B. Lead is described as "action level" but the plan never states the number, and the number just
changed.** The plan leans hard (correctly) on "system data ≠ your tap," but to be accurate the lead
explainer must encode that lead has **no MCL — it has a treatment-technique action level**, and that
the **Lead and Copper Rule Improvements (LCRI, final 2024-10-30, compliance 2027-11-01) lowered the
action level from 15 ppb to 10 ppb**
([EPA LCRI general-public fact sheet PDF](https://www.epa.gov/system/files/documents/2024-10/final_lcri_fact-sheet_general_public.pdf);
[Federal Register LCRI](https://www.federalregister.gov/documents/2024/10/30/2024-23549/national-primary-drinking-water-regulations-for-lead-and-copper-improvements-lcri)).
The plan's schema has an `mcl` field but lead/copper are *action levels*, not MCLs — the schema must
not coerce an action level into the `mcl` slot or it will mislabel the most important contaminant.
This is a concrete schema-correctness gap (the violation/limit model needs an action-level/
treatment-technique limit type distinct from MCL).

**C. WHO vs. EPA divergence is handled in policy but the lead case is a trap.** The source-divergence
precedence rule (jurisdiction wins; state both; never blend) is correct. The sharpest real instance:
**WHO treats lead as having no safe threshold** and sets a provisional guideline of **0.01 mg/L (10
µg/L)**, while EPA's MCLG for lead is **zero** and there is no MCL — only an action level
([WHO Guidelines for Drinking-water Quality, 4th ed.](https://www.who.int/publications/i/item/9789241549950)).
An explainer that shows "EPA action level 15/10 ppb" next to a measured value risks *false
reassurance* (action level is a system-wide 90th-percentile trigger, not a personal safety line — a
point even EPA's own framing stresses). The plan's "system data ≠ your tap" caveat covers part of
this, but the no-verdict gate should explicitly forbid presenting an action level as a safety
threshold. Recommend an explicit "action level ≠ safe level" rule in the engine's no-verdict tests.

**D. WHO nitrate units are an error magnet.** WHO nitrate guideline is **50 mg/L as nitrate**, which
equals roughly **10 mg/L as nitrogen** — and EPA's MCL is **10 mg/L as nitrogen (NO3-N)**
([WHO 4th ed.](https://www.who.int/publications/i/item/9789241549950)). These look like a 5× discrepancy
but are the same value in different reporting conventions. The plan's unit-normalization and "as N vs.
as NO3" handling is implied by the schema but not called out; for infant-methemoglobinemia (the plan's
named nitrate-risk population) a unit mislabel is exactly the "undue alarm" failure mode. Recommend an
explicit as-N/as-NO3 normalization test alongside the non-detect handling.

**E. No-medical-advice boundary is well-drawn but has one soft edge.** Non-goals correctly exclude
symptom interpretation and individual risk. However, the plan names higher-stakes subgroups (infants/
nitrate, pregnant people, immunocompromised) in the *beneficiaries* and *contaminant* context. Stating
"nitrate is a particular concern for bottle-fed infants" is sourced public-health fact, not advice —
but it sits one sentence away from advice. Recommend the expert-review checklist include an explicit
"general population-level health information vs. individual advice" line so reviewers police that edge.

**F. Region/system-specificity is correct (CCRs are per-utility, keyed by PWSID) and well-modeled.**
The `WaterSystem.pwsId` + `primacyAgency` design correctly captures that advisories come from the body
with jurisdiction. One gap: **~1 in 8 Americans rely on private wells that no agency regulates or
tests** ([CDC, Private Drinking Water & Public Health](https://www.cdc.gov/environmental-health-services/php/water/private-water-public-health.html)),
and the plan's private-well module is M2 — meaning the *most* underserved, highest-relative-risk group
is deferred to the third milestone. Consider surfacing the "is this a regulated system or a private
well?" fork earlier (even as a stub) so the app never implies well users are covered.

**G. Currency / staleness — good design, but "snapshot date" is necessary not sufficient.** The plan
shows sample period + snapshot date and links to the live source. Given (A), add an explicit
*regulatory-vintage* field separate from the data snapshot: the standard a value is compared against
can change independently of the measurement. SDWIS itself is acknowledged to have known gaps/errors
(plan risk row), which is correct and honest.

**H. Equity / accessibility — strong.** WCAG 2.2 AA as a hard gate, plain-language readability gate,
i18n with accuracy-reviewed non-English locales, no-search-logging (a query = location + health
concern), and the private-well/environmental-justice framing are all present and above the bar for the
sector. Minor: "plain-language readability target" is unquantified — name the metric (e.g. target
grade level) so the CI gate is objective. EPA's own 2024 CCR revisions explicitly moved to a
plain-language summary and translation support
([EPA CCR Rule Revisions](https://www.epa.gov/ccr/consumer-confidence-report-rule-revisions)), so a
concrete readability bar is also a competitive necessity.

**Completeness:** all 17 sections present; metadata, outcome-based metrics, conservative licensing,
honest "partner TO BE SECURED" status. The plan does not overclaim. The principal correctness risk is
not omission — it is that **health/regulatory facts are a moving target and the plan slightly
under-engineers for "the standard itself changed,"** most acutely for PFAS and lead.

---

## 2. Competitive landscape

**EWG Tap Water Database** — the 800-lb gorilla; consumer-facing, ZIP/utility lookup, the default
"what's in my water" destination. **Strength:** enormous coverage, slick UX, aggregates utility data
nationally, names contaminants and health effects. **Weakness (and our wedge):** it is **advocacy-
framed** — it compares results to **EWG's own health guidelines** (largely California OEHHA one-in-a-
million cancer-risk goals), which are far stricter than enforceable limits (its nitrate guideline is
~100× stricter than the EPA MCL; its PFOA/PFOS guidelines are 0.09/0.3 ppt vs. EPA's 4 ppt), and it
explicitly characterizes EPA limits as politically negotiated and "too weak"
([EWG methodology](https://www.ewg.org/tapwater/methodology.php);
[EWG standards](https://www.ewg.org/tapwater/ewg-standards.php)). That framing reliably produces
*alarm* (most tap water shows "above health guideline" flags) and is the exact failure mode this
project is designed to avoid. It is also proprietary/closed — correctly excluded by the plan's license
gate.

**EPA — official data + tools (CCR, SDWIS, ECHO, the 2024 CCR revisions).** **Strength:** the
authoritative, public-domain source of truth (compliance, violations, MCLs); the 2024 CCR Rule
Revisions mandate plain-language summaries, biannual reports for systems >10k, and translation support
starting 2027 ([EPA CCR Rule Revisions](https://www.epa.gov/ccr/consumer-confidence-report-rule-revisions)).
**Weakness:** SDWIS/ECHO are regulator-facing databases keyed by permit IDs; CCRs are per-utility PDFs
written for compliance, not comprehension; nothing ties it together into a calm per-person explainer.
This is the raw material, not a finished good — and the plan correctly builds *on top* of it.

**WHO Guidelines for Drinking-water Quality (4th ed.).** **Strength:** globally authoritative
health-based guideline values (arsenic 0.01 mg/L; nitrate 50 mg/L as NO3; lead provisional 0.01 mg/L,
treated as no-threshold) ([WHO 4th ed.](https://www.who.int/publications/i/item/9789241549950)).
**Weakness:** a 600-page technical reference, copyrighted (CC BY-NC-SA), not a consumer tool, and not
US-jurisdictional. Useful as health context, not as the enforceable-limit source.

**CDC drinking-water pages.** "How to Read Drinking Water Quality Reports," "Guidelines for Testing
Well Water," private-well/public-health pages. **Strength:** authoritative, plain, public-domain,
national, covers the private-well gap EWG ignores. **Weakness:** generic and thin — the "how to read"
page defines *contaminant/source water/violation* but **does not even define MCL, MCLG, or action
level**, and is not system-specific
([CDC how-to-read CCR](https://www.cdc.gov/drinking-water/about/how-to-read-drinking-water-quality-reports.html);
[CDC well testing](https://www.cdc.gov/drinking-water/safety/guidelines-for-testing-well-water.html)).

**NRDC.** Advocacy reports ("What's on Tap? Grading Drinking Water in U.S. Cities," Flint coverage,
infrastructure equity report cards) ([NRDC What's on Tap PDF](https://www.nrdc.org/sites/default/files/whatsontap.pdf)).
**Strength:** credible, equity-focused, good on the LCRI ([NRDC LCRI overview](https://www.nrdc.org/bio/erik-d-olson/welcoming-epas-new-lead-tap-water-rule-overview)).
**Weakness:** report-and-grade format (explicitly the "rank/grade" approach this project refuses),
episodic not per-system, advocacy-framed.

**USGS.** Water-quality benchmarks, NAWQA groundwater assessments, the multi-agency Water Quality
Portal ([USGS benchmarks](https://www.usgs.gov/mission-areas/water-resources/science/water-quality-benchmarks-contaminants)).
**Strength:** authoritative, public-domain, strong on groundwater/wells. **Weakness:** scientist-
facing; benchmarks ≠ regulatory limits; no consumer explainer layer.

**University extension / state-agency well guides.** State private-well owner guides (e.g. NC DHHS,
ND DEQ) ([NC DHHS well FAQs](https://epi.dph.ncdhhs.gov/oee/wellwater/faqs.html)). **Strength:**
practical, trusted locally, fill the well gap. **Weakness:** fragmented per-state, inconsistent,
PDF-heavy, no normalized data layer, variable currency.

**Commercial filter/test vendors (Hydroviv, etc.).** Publish surprisingly decent explainers
([Hydroviv on LCR action levels](https://www.hydroviv.com/blogs/water-smarts/lead-and-copper-rule)) —
but exist to sell filters: structurally incentivized toward alarm. The plan's no-product-recommendation
and no-vendor rules are the right defense.

---

## 3. Gaps we can fill

1. **A calm, *non-advocacy* per-system explainer that uses enforceable limits as the frame** — the
   space between EWG's alarm framing and EPA's unreadable raw data. Nobody owns "accurate, sourced,
   and deliberately un-alarming."
2. **Violation literacy.** No consumer tool clearly distinguishes a *health-based* violation from a
   *monitoring/paperwork* violation. The plan's violation taxonomy directly fills the "paperwork =
   poison" misread.
3. **Non-detect ≠ zero and detection-limit transparency** — routinely lost everywhere; a genuine
   accuracy differentiator.
4. **The private-well "no one tests this unless you do" message**, integrated with regulated-system
   lookup rather than buried in scattered state PDFs.
5. **Provenance you can re-run** — figure-by-figure citation + reproducible snapshots. No consumer
   competitor offers auditable provenance; EWG's methodology is opaque by comparison.
6. **Multilingual, WCAG 2.2 AA, no-tracking** delivery of authoritative water info — EPA's own
   translations don't arrive until 2027 and aren't a per-system app.
7. **Regulatory-vintage transparency** — showing *which* standard (and which year's version) a value
   is compared against, exactly the thing the live PFAS reversal makes essential.

---

## 4. Differentiators to win (vs. EWG)

1. **Enforceable-limit-first, advocacy-free framing.** We state the MCL/MCLG/action level *and* the
   WHO health-context value, label which is enforceable in the user's jurisdiction, and never declare
   "above safe level." EWG's product *is* the alarm; ours is the calm. This is the single strongest
   wedge.
2. **No verdict, ever — structurally enforced.** Schema + deterministic engine + CI no-verdict test +
   expert sign-off. EWG, NRDC, and vendors all grade/rank/flag; we route to the authority.
3. **Auditable provenance + reproducibility.** Every figure reconciles to a cited snapshot in CI;
   opaque-methodology aggregators can't match this.
4. **Violation taxonomy + non-detect honesty** — prevents both false alarm and false reassurance,
   the two-sided harm EWG's framing ignores.
5. **Privacy as a feature.** No search logging, no accounts, no trackers — a water search reveals
   location + a health worry. EWG and vendors monetize attention.
6. **Equity built in:** private-well coverage, multilingual, accessible, free/CC-BY/MIT, partner-
   adoptable and brandable by local health departments.

---

## 5. Claude API leverage (and the hard limits)

**Where Claude helps (donated lane: human runs the agent; funded lane only with budget cap):**

1. **Drafting plain-language contaminant explainers from EPA/WHO source text** — converting MCL/MCLG
   tables and WHO guideline chapters into calm, readability-targeted prose with the mandatory
   limitations + "not-a-substitute" framing as a fixed template. Highest-value, repeated across ≥8
   contaminants and every locale.
2. **CCR / data interpretation assistance** — turning a per-system normalized record (values, limits,
   violation category/status, sample period) into the four explainer sections, and *flagging* when a
   result is a non-detect, a monitoring vs. health violation, or an action-level (not MCL) case.
3. **Structuring the contaminant library + source-divergence drafting** — proposing the EPA-vs-WHO
   "state both, label the enforceable one" copy for expert review; normalizing units (as-N vs.
   as-NO3) into human-readable explanations.
4. **Translation drafting + readability linting** — first-pass localization and grade-level checks,
   always paired with a competent-speaker + technical-accuracy re-review.
5. **Provenance/QA assistant** — drafting citations, detecting figures missing a source, summarizing
   what a snapshot does/doesn't cover.

**Where Claude must NOT decide (hard gates — these are human/expert calls):**

- **No contaminant/health *fact* ships unsourced or unverified.** Every MCL/MCLG/action-level/
  guideline value must trace to the cited authoritative source; **no model-recalled or fabricated
  numbers** (the PFAS-in-reversal and lead-action-level cases prove how fast recalled facts go stale).
- **No safety verdict, grade, ranking, or "is my water safe" judgment.** The model may explain; it
  may not conclude.
- **No medical advice / individual risk interpretation.**
- **No tuning of tone into either undue alarm or false reassurance** — the model drafts, the expert
  signs off; an action level may never be rendered as a personal safety threshold.
- **No region/jurisdiction guess.** The body with jurisdiction and the enforceable limit are data, not
  inference.
- **Final approval is a credentialed drinking-water/environmental-health expert**, distinct from the
  author (`reviewedBy` ≠ `approvedBy`), with a PR-tied review-log entry.

---

## 6. Ten concrete optimizations

1. **Add a regulatory-vintage / standard-version field** to the schema distinct from the data
   snapshot date, and render "standard set in YYYY; status: in force / compliance extended / under
   reconsideration." Directly addresses the live PFAS reversal.
2. **Add a limit-type enum** (`mcl | mclg | action-level | treatment-technique | secondary | who-
   guideline`) so lead/copper action levels are never mislabeled as MCLs. Fixes finding B.
3. **Make "action level ≠ safe level" an explicit no-verdict engine test**, not just prose. Fixes the
   lead false-reassurance trap (finding C).
4. **Add an as-N / as-NO3 nitrate unit-normalization test** with the infant-risk framing, alongside
   the existing non-detect test. Fixes finding D.
5. **Quantify the readability gate** (name the target grade level / metric) so CI is objective and so
   we credibly out-read EPA's own 2024 plain-language CCR push.
6. **Surface the regulated-system vs. private-well fork earlier** (M0/M1 stub) so the app never implies
   well users are covered; pull the well "no one tests this unless you do" message forward.
7. **Build a PFAS "moving standard" content pattern now** — an explainer that can represent
   "MCL = 4 ppt (2024), compliance to 2031, other PFAS limits under proposed rescission" — and wire
   the regulatory-update trigger to the EPA SDWA/PFAS pages and Federal Register.
8. **Ship a one-line, persistent "this is not your tap / not an advisory / check your utility" banner
   spec** as a reusable component with its own a11y + i18n tests, so the framing can't be dropped per
   page.
9. **Comprehension test as a real artifact, not a metric line** — pre-register the ≥80% "this is not a
   safety verdict" question set in the repo so it's reproducible and partner-portable.
10. **Pre-stage state-primacy-agency license verification** as a structured checklist in the source
    registry (the plan flags per-state licensing as an open question) so pilot-geography selection
    isn't blocked late.

---

## 7. Parallel & perpendicular spin-offs

- **Reusable sourced-health-info engine (perpendicular, highest leverage).** The pattern here —
  ingest authoritative open data → normalize with provenance → expert-reviewed plain-language entries →
  no-verdict explainer → CI reconciliation + readability + a11y gates — generalizes to any
  health/safety domain. Extract it as a shared library powering **wash-guides** (WASH/sanitation),
  **incidence-explorers** (disease-incidence plain-language), and **open-data-explainers**.
- **community-resource-maps (parallel).** Service-area + open-geocoding work (Census/TIGER, OSM/ODbL)
  and the privacy-preserving lookup transfer directly to mapping local resources (testing sites,
  health departments, filter-assistance programs) without product recommendations.
- **open-data-explainers (parallel).** The source-registry + license-gate + provenance-reconciliation
  CI machinery is domain-agnostic — a reusable "turn a government dataset into a sourced, reproducible
  plain-language explainer" toolkit.
- **incidence-explorers / wash-guides (parallel).** Same two-sided-harm discipline (no undue alarm /
  no false reassurance), same expert-sign-off ladder, same no-verdict rule — these are siblings, not
  forks; share the review-log and no-verdict test harness.
- **MCP server (perpendicular).** Expose the normalized, provenance-stamped water-data snapshot +
  contaminant library as a **read-only, no-verdict MCP server**: tools like `get_system_by_pwsid`,
  `explain_contaminant`, `list_violations` returning *sourced facts with citations and limitations
  baked in* and no judgment. Lets other agents query authoritative water data without re-scraping or
  hallucinating limits — and the citation-always/no-verdict contract is enforced at the tool boundary.

---

## 8. Open questions

1. **PFAS content strategy under active reversal:** do we ship a PFAS entry in M1 at all, or gate it
   behind a "moving standard" template until the May 2026 proposed rules resolve (comments close
   2026-07-20)? Shipping a static 2024 number risks being wrong within the milestone.
2. **Action-level framing:** how do we present lead's 15→10 ppb action-level change (compliance
   2027-11-01) without implying "below the line = safe"? Needs expert-approved standard copy.
3. **Private-well prioritization:** given wells are the most underserved/highest-relative-risk group,
   should the well module move earlier than M2?
4. **Readability metric:** which specific readability standard/grade target becomes the CI gate?
5. **State licensing + pilot geography:** which pilot state's primacy-agency data is both
   open-licensed and high-need (partner-driven)?
6. **WHO NC terms:** is paraphrase-and-cite sufficient, or is a one-time license review warranted
   before publishing WHO-derived guideline values at scale?
7. **Regulatory-watch ownership:** who owns monitoring EPA SDWA/Federal Register for the next PFAS/LCR
   move, and what is the SLA from rule change → content re-review?
8. **Engine representation of "no MCL, only action level / treatment technique"** — is the schema's
   limit model expressive enough (see optimization #2) before M1 normalization is finalized?

---

### Sources
- EPA PFAS NPDWR (2024): https://www.federalregister.gov/documents/2024/04/26/2024-07773/pfas-national-primary-drinking-water-regulation · https://www.epa.gov/system/files/documents/2024-04/drinking-water-utilities-and-professionals-technical-overview-of-pfas-npdwr.pdf
- EPA PFAS 2025–26 reconsideration: https://www.epa.gov/newsreleases/epa-announces-it-will-keep-maximum-contaminant-levels-pfoa-pfos · https://www.epa.gov/sdwa/proposed-pfas-rescission-rule · https://eelp.law.harvard.edu/tracker/pfas-in-drinking-water/
- EPA LCRI (2024): https://www.epa.gov/system/files/documents/2024-10/final_lcri_fact-sheet_general_public.pdf · https://www.federalregister.gov/documents/2024/10/30/2024-23549/national-primary-drinking-water-regulations-for-lead-and-copper-improvements-lcri
- WHO Guidelines for Drinking-water Quality, 4th ed.: https://www.who.int/publications/i/item/9789241549950
- EPA CCR Rule Revisions (2024): https://www.epa.gov/ccr/consumer-confidence-report-rule-revisions
- EWG Tap Water Database methodology/standards: https://www.ewg.org/tapwater/methodology.php · https://www.ewg.org/tapwater/ewg-standards.php
- CDC: https://www.cdc.gov/drinking-water/about/how-to-read-drinking-water-quality-reports.html · https://www.cdc.gov/environmental-health-services/php/water/private-water-public-health.html · https://www.cdc.gov/drinking-water/safety/guidelines-for-testing-well-water.html
- NRDC: https://www.nrdc.org/sites/default/files/whatsontap.pdf · https://www.nrdc.org/bio/erik-d-olson/welcoming-epas-new-lead-tap-water-rule-overview
- USGS benchmarks: https://www.usgs.gov/mission-areas/water-resources/science/water-quality-benchmarks-contaminants
- NC DHHS private wells: https://epi.dph.ncdhhs.gov/oee/wellwater/faqs.html
