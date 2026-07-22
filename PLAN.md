# PLAN — water-quality-explainers (local drinking-water safety explainers from open data)

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated
>
> Risk tier: **medium** (with a hard escalation: any unit that carries a health *action* — e.g.
> "what to do about lead" — requires **credentialed expert sign-off**, the same bar as high-risk).

---

## Executive summary

**water-quality-explainers** turns open, authoritative drinking-water data into calm,
plain-language explainers that help an ordinary person understand **what the public record says
about their local water system** — which contaminants were measured, what the results mean, what
the limits are, and where to find the official, legally-binding advisory. It is two coupled lanes
of work: (1) a **reproducible data pipeline** that ingests open government datasets (US EPA SDWIS /
ECHO, the multi-agency Water Quality Portal, USGS, and state primacy-agency data), normalizes them
into a versioned schema, and snapshots them with full provenance; and (2) an **explainer engine +
accessible web app** that renders per-water-system, plain-language explanations sourced to named
authorities, in multiple languages.

The single most important design constraint is this: **the project never issues a safety verdict and
is never a substitute for an official advisory.** It does not tell anyone their water is "safe" or
"unsafe." It explains what the public data shows, what that data *cannot* tell you (home plumbing,
private wells, sampling gaps, detection limits, lag between sampling and publication), and routes the
user to the body with jurisdiction. Getting this wrong in either direction is harmful: false
reassurance can expose a family to lead or nitrate; false alarm causes panic, wasted money, and
erosion of trust in water systems that are in fact compliant. That asymmetry — two ways to do harm —
shapes every gate in this plan.

This is a **medium risk-tier** project because it touches public health. Data-accuracy review is
mandatory for the pipeline, and **drinking-water / environmental-health expert review is mandatory
for every contaminant explainer and any "what to do" content** before it ships. The project consumes
**open / public-domain / openly-licensed data only**, verifies each source's license before reuse,
and records provenance on every published figure.

**Honesty note on the partner & verified need.** No partner organization, MOU, or written
"verified need" exists yet. The public-interest need is well-documented in the abstract (drinking-
water information is fragmented, technical, and often inaccessible), so foundation work (M0–M2) can
proceed as a generic public good — but the **partner and `verifiedNeed` status are TO BE SECURED**,
and the project is not "shipped" by Hee-Lee Oss's *delivered, not merged* bar until a named partner
(public-health department, water-justice / environmental-justice nonprofit, or community
organization) confirms the need and the priority geography/languages. See *Problem & beneficiaries*
and *Open questions*.

---

## Problem & beneficiaries

**The problem.** People have a right to know what is in their tap water, and in many countries that
data is technically *public* — but it is effectively unreadable. It lives in regulatory databases
keyed by permit IDs, in PDF Consumer Confidence Reports written for compliance rather than
comprehension, and in units (ppb, mg/L, MCL vs. MCLG, "health-based" vs. "monitoring" violations)
that mean nothing to a non-specialist. The result is a vacuum that gets filled by **commercial
fear-marketing** (filter and bottled-water vendors with a financial interest in alarm) and by
**proprietary aggregators** whose methodology and licensing are opaque. Meanwhile the genuinely
important caveats — that a system-level result says little about the lead at *your* tap, that a
private well is unregulated and untested by anyone unless you test it, that a "violation" is often a
paperwork/monitoring lapse rather than a contamination event — are exactly the parts that get lost.

**Who is helped (beneficiaries).**
- **Residents** trying to understand their local public water system, especially renters, low-income
  households, caregivers of infants (nitrate risk), pregnant people, and immunocompromised people
  for whom the stakes are higher.
- **Private-well owners**, who are *not* covered by drinking-water regulations and frequently do not
  know that no agency tests their water — a distinct, underserved group needing different guidance.
- **Community health workers, librarians, teachers, and mutual-aid / environmental-justice
  organizers** who field "is my water OK?" questions and need a free, neutral, source-cited
  reference instead of a vendor pitch.
- **Local public-health departments and water-justice nonprofits** who want a free, brandable,
  multilingual explainer layer over the official data instead of building one.

**Verified need / partner org: TO BE SECURED.** No organization has confirmed the need in writing
and none is named. We treat the need as **plausible and publicly-documented but not partner-verified**
until at least one named partner confirms it (letter of support / lightweight MOU) and identifies the
priority geography, water systems, and languages. General foundation work proceeds; partner
endorsement is required before the *Definition of Shipped* is met. Every Task JSON therefore carries
`verifiedNeed: false` and `requestor: "TBD"` until that happens.

---

## Goals and non-goals

**Goals.**
- Render **per-water-system, plain-language explainers** that accurately reflect the open public
  record and are traceable, figure-by-figure, to a named authoritative source.
- Make the **limits of the data legible**: every explainer states what the data does and does *not*
  tell the reader (home plumbing/lead, private wells, sampling cadence, detection limits, reporting
  lag, monitoring-vs-health violations).
- **Always defer to official advisories**: prominent, persistent framing that this is an educational
  aid, not an advisory, with a direct route to the body that has jurisdiction.
- Ship a **reproducible, provenance-tracked pipeline**: anyone can re-run it and get the same
  normalized snapshot from the same source vintage.
- Be **genuinely free, neutral, and private**: MIT code / CC-BY content + open data; no ads, no
  telemetry, no accounts, **no logging of user searches** (a search here reveals location + a health
  concern and is treated as sensitive).
- Meet **WCAG 2.2 AA** and a **plain-language readability bar** as hard gates.
- Be **multilingual** with at least one non-English locale, accuracy-reviewed.
- Be **adoptable** by a named partner in their community's languages and geography.

**Non-goals.**
- **No safety verdicts.** The project will never label water "safe," "unsafe," "clean," or "toxic,"
  never rank or "grade" systems, and never tell a user whether to drink, filter, or avoid their
  water. It explains and routes to authorities.
- **Not an advisory or alerting service.** Not a real-time boil-water-notice feed, not an emergency
  alert system, not a substitute for the legally-binding notices a utility/agency must issue.
- **No product recommendations.** No filters, no bottled water, no testing kits, no vendors — not
  even "neutral" comparisons that could be read as endorsements.
- **No individual health advice / diagnosis.** It does not interpret anyone's symptoms or tell an
  individual what their personal risk is.
- **No private/identifiable data.** No individual household water results, no controlled-access data,
  no scraping of proprietary aggregators (e.g. subscription/closed tap-water databases).
- **No partisan or blame framing.** Civic-neutral: facts and official accountability channels, never
  attribution of blame to named political actors or parties.
- **Not a primary-source lab.** It does not generate new measurements; it explains existing ones.

---

## Success metrics (outcomes)

Outcome-centric and beneficiary-first. Baselines are zero at project start unless noted.

| Outcome | Baseline | Target (first 12 months post-launch) | How measured (privacy-preserving) |
| --- | --- | --- | --- |
| Partner orgs endorsing/adopting | 0 | ≥ 1 named partner adopts; goal 3 | Signed letters of support / MOUs (manual record) |
| Public water systems with a reviewed explainer | 0 | ≥ 1 full pilot state/region covered | Pipeline coverage report in repo |
| Contaminant explainers expert-reviewed & source-cited | 0 | ≥ 8 core contaminants, each signed off | Review log in repo |
| Comprehension (do readers grasp the key caveats?) | none | ≥ 80% of moderated testers correctly state "this is not a safety verdict / I should check the official advisory" after reading | Small moderated comprehension test (no PII retained) |
| Data accuracy vs. source | none | 100% of published figures reconcile to the cited source snapshot; 0 unreconciled figures shipped | Automated reconciliation test in CI |
| Provenance completeness | none | 100% of published figures carry source + retrieval date + license | Provenance-completeness gate in CI |
| Accessibility & readability | none | WCAG 2.2 AA verified (automated + manual AT); plain-language readability target met | axe/pa11y + manual AT audit + readability check |
| Languages fully localized (UI + core explainers) | 1 (en) | ≥ 2 languages, accuracy-reviewed | Translation completeness check in CI |
| Privacy posture | n/a | 0 search logs, 0 trackers, 0 PII fields, 0 unexpected egress | Static audit + runtime network-interception E2E |
| People reached (partner-reported, opt-in) | 0 | ≥ 10,000 distinct users reached (denominator: distinct users, not page views) | Partner self-report via standard template (no in-app tracking) |

We deliberately avoid vanity metrics (page views, stars). Because we collect **no telemetry**, reach
is partner-reported, not measured in-app — an honest privacy/measurement trade-off. **Comprehension
is a first-class metric** precisely because a beautiful explainer that leaves the reader with a false
sense of certainty is a *failure*, not a success.

**Reach measurement — windows & anti-double-counting.** "People reached" counts **distinct users**
over a rolling 12-month window from production launch. Each partner counts a person once per
reporting period across touchpoints; cross-partner overlap is de-duplicated best-effort at roll-up by
the steward; estimates are recorded separately from confirmed counts. (Standard self-report template:
partner, period, channels, distinct users (confirmed), flagged estimates, region/languages, de-dup
notes, outcomes note.)

---

## Scope

**In scope.**
- A **source registry + license gate**: an explicit, machine-checkable list of approved open
  datasets, each with its verified license/terms; non-approved or unverified sources are rejected by
  CI.
- A **reproducible ingestion pipeline**: pull approved open datasets, normalize to a versioned
  schema, snapshot with provenance (source vintage, retrieval date, license, checksum).
- An **explainer engine**: deterministic generation of per-water-system, plain-language explainers
  from the normalized snapshot, with built-in limitations/caveats and **no verdict logic**.
- An **expert-reviewed contaminant library**: plain-language entries for core regulated contaminants
  (lead, copper, nitrate, arsenic, total coliform / E. coli, disinfection byproducts (TTHM/HAA5),
  PFAS), each sourced to EPA/WHO and reviewed by a qualified expert.
- A **private-well guidance module** (distinct from regulated systems; "no one tests this unless you
  do"; links to testing resources; not advice).
- An **accessible, privacy-preserving web app**: search/lookup with **no query logging**, static-
  first, WCAG 2.2 AA, plain-language.
- **i18n** + at least one non-English locale, accuracy-reviewed.
- A **corrections/erratum process** and a **freshness model** (snapshot dates, staleness flags,
  re-ingest cadence).

**Out of scope (explicit).**
- Safety verdicts, grades, rankings, "best/worst water" lists, or drink/filter/avoid recommendations.
- Real-time advisory/alert feeds, boil-water-notice notifications, or anything implying it replaces
  official notices.
- Product/vendor recommendations or comparisons (filters, bottled water, test kits).
- Individual or symptom-based health advice; private-well *lab interpretation* for a specific
  household.
- Any individual-level, identifiable, or controlled-access data; scraping proprietary aggregators.
- New measurement/sampling; predictive modeling of "future" contamination.
- Geographies/languages we cannot source-verify and expert-review.
- Partisan framing or attribution of blame to named individuals/parties.

---

## Solution approach & architecture

**Overview.** A data-pipeline-plus-static-site architecture. The pipeline runs **offline / in CI**
(not in the user's browser), producing a versioned, provenance-stamped snapshot and a set of
pre-generated explainers. The web app is **static-first** and serves those pre-built artifacts, so
runtime needs no backend and — critically — **no server sees or logs what a user looks up.**

**Components.**
1. **Source registry + license gate** (`data/sources/`): a declarative registry; each entry records
   source name, publisher, dataset URL/API, license/terms, license-verification note + date, and
   allowed-use. CI rejects ingestion from any source not in the registry or whose license is
   unverified/incompatible. This is the enforcement point for "open/PD/CC only."
2. **Ingestion adapters** (`pipeline/adapters/`): one adapter per source (EPA ECHO/SDWIS, Water
   Quality Portal, USGS, per-state primacy agency). Adapters are **read-only**, rate-limit-aware,
   and snapshot raw responses with retrieval metadata before any transform (raw → normalized is
   reproducible and auditable).
3. **Normalizer** (`pipeline/normalize/`): maps heterogeneous source records into the **normalized
   water-data schema** (below), harmonizing units, contaminant identities (CAS / EPA contaminant
   codes), detection-limit / non-detect handling, and the **violation taxonomy** (health-based vs.
   monitoring/reporting vs. treatment-technique).
4. **Explainer engine** (`pipeline/explain/`): deterministic, template-driven generation of
   per-system explainers from the snapshot + the expert-reviewed contaminant library. **No verdict
   logic** — it states measured values, the relevant regulatory limit (MCL) and goal (MCLG), the
   violation type and status, and the standard caveats; it never computes "safe/unsafe."
5. **Contaminant library** (`content/contaminants/`): expert-reviewed plain-language entries, each
   with sources, retrieval dates, reviewer/approver, and review-log reference.
6. **Web app** (`app/`): static site rendering pre-built explainers; privacy-preserving lookup
   (client-side index where feasible; no query logging server-side); WCAG 2.2 AA; i18n.
7. **CI gates** (`.github/`/`ci/`): license gate, schema validation, **data-accuracy reconciliation**,
   provenance-completeness, a11y, readability, no-telemetry/no-PII (CSP + runtime interception).

**Tech stack.** TypeScript, ESM, pnpm workspaces. Pipeline: Node/TS (data tooling TBD via ADR).
Web: static-first PWA-capable site (lightweight, a11y-friendly framework — Preact/Svelte/Lit class —
chosen by ADR). Hosting: static (GitHub Pages / Netlify / Cloudflare Pages — no app server).
Testing: Vitest (unit), Playwright (E2E incl. no-egress), axe-core/pa11y (a11y). Geocoding: open
data only (US Census/TIGER, OSM/Nominatim with ODbL attribution). License: **MIT** (code) /
**CC-BY-4.0** (content) / **CC0 or CC-BY** for derived datasets (per source compatibility).

**Normalized water-data schema (per measurement/observation).**
```
WaterSystem {
  pwsId: string                 // public water system ID (e.g. EPA PWSID) — system-level, not a person
  name: localized string
  type: "community" | "non-transient-non-community" | "transient" | ...
  serviceArea: region tags[]    // advisory; from open boundary data where available (attributed)
  population: integer | null    // served population (public, system-level)
  primacyAgency: string         // the body with jurisdiction (state/EPA) — where advisories come from
}
ContaminantResult {
  pwsId: string
  contaminantId: string         // CAS / EPA contaminant code (normalized identity)
  measuredValue: number | null  // null when non-detect
  detectionLimit: number | null // reporting/detection limit (critical: non-detect != zero)
  unit: string                  // normalized (e.g. mg/L) with original recorded too
  mcl: number | null            // enforceable Maximum Contaminant Level
  mclg: number | null           // Maximum Contaminant Level Goal (health goal; may be 0)
  samplePeriod: { start, end }  // when sampled (NOT when published)
  resultStatus: "detect" | "non-detect" | "not-monitored"
  source: Citation
}
Violation {
  pwsId: string
  contaminantId: string | null
  category: "health-based" | "monitoring-reporting" | "treatment-technique" | "other"
  status: "open" | "resolved" | "archived"
  period: { start, end }
  source: Citation
}
Citation { publisher, dataset, url, retrievedDate, sourceVintage, sourceLicense, checksum }
Snapshot { id, builtAt, sources: Citation[], pipelineVersion, checksum }
ExplainerUnit {
  pwsId, lang, snapshotId,
  sections: { whatThisIs, whatTheDataShows, limitations, whereToGetTheOfficialAdvisory },
  contaminantRefs: contaminantId[],   // links into the expert-reviewed library
  reviewStatus: "draft" | "in-review" | "approved",
  reviewedBy, approvedBy,             // MUST differ (no self-approval); approver = qualified expert
  reviewLogRef, lastReviewed, contentLicense: "CC-BY-4.0"
}
```

**Key decisions (recorded as ADRs in M0).**
1. Data tooling + storage format for snapshots (columnar/Parquet vs. JSON/SQLite) and the
   reproducibility contract (deterministic raw→normalized).
2. Source registry format + the **license gate** mechanism (how CI blocks unapproved/unverified
   sources).
3. Web framework (a11y + bundle size + static-first weighted) and lookup approach (client-side index
   vs. pre-rendered pages) — chosen to keep **no query logging** feasible.
4. Geocoding / service-area approach using **open data only**, with attribution.
5. Freshness/snapshot/versioning model and the **corrections/erratum** workflow.

**Decision ordering.** The snapshot-format ADR (#1) and source-registry/license-gate ADR (#2) are
decided **before** the normalized schema is finalized and before any non-pilot ingestion. Until then
the schema above is **provisional**. TASKS.md sequences this (schema and pipeline tasks depend on the
ADR task).

---

## Data, licensing & compliance

**This section is load-bearing for a medium-risk public-health project. Be conservative.**

**Approved source classes (open / public-domain / openly-licensed ONLY).**
- **US EPA SDWIS / ECHO** (Safe Drinking Water Act compliance, violations, system inventory) —
  US federal government work, **public domain**.
- **Water Quality Portal (WQP)** (EPA + USGS + NWQMC) and **USGS water data** — US federal,
  **public domain**.
- **EPA regulatory reference** (MCL/MCLG tables, the Lead & Copper Rule, the 2024 PFAS National
  Primary Drinking Water Regulation, health advisories) — **public domain**.
- **State primacy-agency data** — license/terms **verified per state** before use; many are open but
  this is **not assumed**.
- **WHO Guidelines for Drinking-water Quality** — **copyrighted (typically CC BY-NC-SA)**; used by
  **paraphrase + citation**, never copied verbatim, and NC terms respected (our content is
  non-commercial public good, but we still cite and do not relicense WHO text as CC-BY).
- **Open geocoding** — US Census/TIGER (public domain); OSM/Nominatim (**ODbL**, share-alike +
  attribution obligations tracked).

**Explicitly excluded.** EWG Tap Water Database and other **proprietary/closed aggregators**;
subscription datasets; any **controlled-access or individual/household-identifiable** water results;
any source whose license we cannot verify; **Consumer Confidence Report PDFs** are used only for the
**underlying public monitoring facts** (which are reportable/public), **not** by copying report
layout/text/branding.

**License gate (critical).** Every source must be in the source registry with a **verified license**
and verification date before ingestion. CI **fails** on ingestion from an unregistered or
unverified-license source. Our outputs: **MIT** (code), **CC-BY-4.0** (content), **CC0/CC-BY** for
derived datasets where source terms allow (ODbL-derived geodata stays **ODbL/share-alike** with
attribution).

**Source-divergence precedence (federal vs. state vs. WHO).** When sources disagree on a limit or a
result, the explainer does not silently pick one: **(1) the agency with jurisdiction over the
user's system wins** for regulatory limits/advisories (state primacy agency → EPA); **(2)** where a
health-context number differs (e.g. WHO guideline vs. EPA MCL), **state both and label which is the
enforceable limit in the user's jurisdiction**; **(3)** never average or "blend" limits. The chosen
precedence is recorded in the unit's review-log entry.

**Provenance model.** Every published figure carries a `Citation` (publisher, dataset, URL, retrieval
date, **source vintage**, license, checksum) and ties to a `Snapshot` (built-at, pipeline version,
checksum). The pipeline is reproducible: same source vintage + same pipeline version ⇒ same snapshot.
A provenance-completeness CI gate blocks any published figure lacking a citation.

**Privacy / PII stance.** **Zero personal PII.** The *data* is **system-level** (water systems,
not people); we publish nothing that identifies an individual household's water. **User searches are
treated as sensitive** (a query reveals a person's location + a health concern): **no search
logging**, no accounts, no analytics, no third-party scripts, client-side lookup where feasible, and
**no storage of precise user geolocation**. Geocoding is best-effort to system, not to person.

**Attribution.** Source agencies are credited per explainer and on a credits page; ODbL geodata
attribution is honored. We **do not imply endorsement** by EPA, WHO, USGS, or any agency unless that
body has explicitly endorsed the project.

**Currency / non-staleness.** Drinking-water status changes (violations open and resolve; new rules
like the 2024 PFAS MCLs phase in). Every explainer shows the **sample period** and **snapshot date**,
warns that the data is a lagging public record (not live), and links to the live official source. The
**2024 PFAS regulation and the evolving Lead & Copper Rule** are explicitly tracked as content that
must be re-reviewed on each regulatory update.

---

## Quality, review & risk gates

**Risk tier: medium**, with a **hard escalation to expert sign-off** for any health-action content.

**Required reviews before a deed is "done":**
- **Pipeline / code tasks:** maintainer code review + CI green (lint, typecheck, unit, **license
  gate**, **schema validation**, **data-accuracy reconciliation**, **provenance completeness**,
  a11y, readability, no-telemetry/no-PII). A figure that does not reconcile to its cited source
  **blocks merge**.
- **Contaminant / explainer content (medium → expert-gated):** every contaminant entry and any "what
  to do / where to get help" content is reviewed against the cited authoritative sources **and signed
  off by a qualified expert** — a **drinking-water / environmental-health / public-health
  professional or toxicologist** (recognized credential or equivalent practice). The **author may
  not approve their own unit** (`reviewedBy` ≠ `approvedBy`). Each approval writes a **PR-tied,
  append-only review-log entry** (PR #, commit SHA, `snapshotId`/content version, reviewer + approver
  identities + credential, sources checked, any source-divergence decision).
- **No-verdict check:** content and engine output are reviewed/tested to confirm **no safety verdict,
  grade, ranking, or product recommendation** is produced, and that the **limitations + "not a
  substitute for official advisories"** framing is present and prominent on every explainer.
- **Translations:** reviewed by a competent speaker **and** re-checked for **technical accuracy of
  health/regulatory meaning** (not just fluency), with the same expert escalation for health content.
- **Accessibility & readability:** automated checks **plus** a manual assistive-tech audit on a
  defined matrix (NVDA+Firefox, JAWS+Chrome, VoiceOver+Safari, TalkBack+Chrome, plus keyboard-only),
  before each milestone exit and each release; **plain-language readability** target enforced.

**Definition of Shipped (project-level).** A deployed, accessible, privacy-preserving explainer site
that: (1) renders per-system explainers reconciling 100% to cited open-source snapshots with complete
provenance; (2) carries prominent **no-verdict / not-an-advisory** framing and data-limitation
caveats on every explainer; (3) ships only **expert-reviewed** contaminant/health content from
**license-verified open sources**; (4) logs **no user searches** and collects no PII; (5) meets WCAG
2.2 AA + the readability bar; and (6) is **adopted/endorsed by a named partner** in their community's
geography and languages. Until a partner is secured, criterion (6) is **outstanding**: the site is
"publicly usable" but not "shipped" by Hee-Lee Oss's bar.

**"Publicly shipped (no partner)" success state.** Criteria (1)–(5) can be met without a partner. If,
by a decision point **6 months after the M3 build is production-ready**, no partner is secured, the
steward + Hee-Lee Oss governance may declare **"Publicly Shipped (generic public good)"** — deployed and
distributed directly / via community channels, outcomes by best-effort self-report. A later partner
endorsement upgrades the status rather than gating launch.

---

## Roadmap & milestones

Phased; each phase has measurable exit criteria. M0 is a thin cold-start foundation.

- **M0 — Foundation & cold-start (thin slice).**
  Goal: the source-registry/license gate, the provisional normalized schema, a pilot ingestion for
  **one small region**, one **expert-reviewed** sample explainer (lead) end-to-end, and a static web
  skeleton with privacy/no-telemetry gates in CI.
  Exit criteria: source registry + **license gate** rejects unapproved/unverified sources in CI;
  snapshot-format ADR (#1) and registry/license-gate ADR (#2) recorded **before** the schema is
  finalized; pilot ingestion produces a provenance-stamped snapshot that **reconciles to source** in
  CI; one lead explainer renders from the snapshot + library with no-verdict + limitations framing,
  **expert-signed-off** by an approver distinct from the author; web skeleton loads; no-telemetry/no-
  search-logging verified by CSP + runtime network-interception E2E (not a grep alone).

- **M1 — Explainer engine & data accuracy.**
  Goal: robust normalization of core contaminants, the deterministic no-verdict explainer engine, the
  data-accuracy validation suite, the expert-reviewed contaminant library, and a11y/readability
  hardening.
  Exit criteria: core contaminants (lead, copper, nitrate, arsenic, total coliform/E. coli,
  TTHM/HAA5, PFAS) normalized with **non-detect/detection-limit handling** and the **violation
  taxonomy** (health-based vs. monitoring/reporting vs. treatment-technique); reconciliation suite
  proves 100% of published figures match the cited snapshot; engine emits **no verdict** (tested);
  ≥ 8 contaminant entries expert-reviewed; WCAG 2.2 AA + readability pass.

- **M2 — Coverage & privacy-preserving search.**
  Goal: scale ingestion to a full pilot state/region, ship the privacy-preserving lookup, add the
  private-well guidance module, and open service-area geocoding.
  Exit criteria: ≥ 1 full pilot state/region covered with refreshed snapshots; search/lookup works
  with **no query logging** (verified no egress); **private-well module** (expert-reviewed; "no one
  tests this unless you do"; not advice) shipped; geocoding uses **open data only** with attribution.

- **M3 — i18n, partner adoption & ship.**
  Goal: internationalization + first non-English locale, secure a named partner, production deploy
  with freshness automation + corrections process, and meet the full Definition of Shipped.
  Exit criteria: i18n framework + build-time completeness check; ≥ 1 non-English locale (UI + core
  explainers) accuracy-reviewed; **named partner endorsement/adoption on file** (verifiedNeed→true);
  production deploy with freshness/snapshot refresh + **corrections/erratum** process; outcomes-
  tracking (partner self-report) in place.

- **M4 — Scale & sustainability (post-launch).**
  Goal: broaden geographic + contaminant coverage, automate freshness, harden against regulatory
  drift (PFAS/LCR updates), add locales.
  Exit criteria: ≥ 2 additional regions; scheduled re-ingest + staleness flags operational;
  regulatory-update re-review process exercised on a real rule change; ≥ 2 non-English locales.

Dependencies: M1 → M0 (schema + pilot snapshot). M2 → M1 (engine + validation). M3 → M2 (coverage +
search) and the **partner being secured** (can start in parallel from M0). M4 → M3.

---

## Work breakdown

The itemized, schema-mapped backlog lives in **TASKS.md**, organized by the M0–M4 milestones above.
Each task maps to a Hee-Lee Oss Task JSON (see schema), is sized (small/medium/large), risk-tagged, and
names a reviewer. TASKS.md includes acceptance criteria for the most important tasks per milestone,
milestone Definitions of Done, a backlog, and a complete, schema-valid example Task JSON.

---

## Governance, roles & stakeholders

- **Maintainer (Owner): TBD.** Owns repo, roadmap, releases, review standards, the license gate.
- **Code/data reviewers:** rotation of TS/data-competent contributors; ≥ 1 approval + CI green to
  merge; owns the data-accuracy reconciliation gate.
- **Domain expert reviewer(s):** **drinking-water / environmental-health / public-health
  professional or toxicologist** who signs off contaminant + health-action content (the credentialed
  escalation). Distinct from the content author.
- **Accessibility reviewer:** contributor competent with assistive tech; manual audits + readability.
- **Translation reviewers:** competent speakers per locale, paired with technical-accuracy re-check.
- **Steward (last-mile owner): TBD** — owns deployment, the partner relationship, and getting
  explainers in front of beneficiaries.
- **Partner / requestor: TO BE SECURED** — a named public-health dept / water-justice nonprofit /
  community org confirming need, geography, and languages, and ultimately endorsing/adopting.
- **Hee-Lee Oss governance/board:** arbitrates edge cases, risk-tier escalations, and the no-verdict /
  not-advice guardrails per the good-deed definition.

---

## Dependencies & integrations

- **External data sources (read-only, license-checked):** EPA SDWIS/ECHO, Water Quality Portal,
  USGS, state primacy agencies, EPA regulatory tables, WHO guidelines (cite/paraphrase), open
  geocoding (Census/TIGER, OSM/Nominatim).
- **Tooling/libraries:** Node/TS data tooling (ADR), web framework (ADR), test stack (Vitest,
  Playwright, axe-core/pa11y), readability checker.
- **Hosting:** static host (no app server), so no server-side query logs are possible.
- **Hee-Lee Oss pieces:** Task schema (`packages/schema`), CLI workspace prep / PR flow (donated lane),
  good-deed definition & risk-tier governance, review/sign-off process.
- **Human/expert dependency:** drinking-water/environmental-health expert reviewers and a partner org
  — the gating non-software dependencies.

---

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| **False reassurance** — reader infers "safe" from data that doesn't cover their tap (lead/home plumbing, private wells, sampling gaps) | Medium | **High** | No-verdict design rule; mandatory limitations section on every explainer; explicit "system data ≠ your tap"; private-well module; expert sign-off; comprehension metric gates quality | Domain expert + Maintainer |
| **False alarm** — a monitoring/paperwork violation or detect-below-limit is read as danger | Medium | High | Violation taxonomy (health-based vs. monitoring/reporting); non-detect ≠ zero handling; plain-language framing of what a violation is/isn't; expert review | Domain expert |
| Inaccurate figure published (unit error, mis-mapped contaminant, stale value) | Medium | High | Data-accuracy reconciliation gate (100% reconcile to source snapshot); unit/contaminant-identity normalization tests; snapshot dates + staleness flags | Code/data reviewer |
| License/provenance violation (proprietary aggregator, unverified state data, WHO NC terms, ODbL attribution) | Medium | High | Source registry + CI license gate (open/PD/CC only); paraphrase+cite never copy; ODbL attribution tracked; per-state verification | Maintainer |
| No partner secured (verifiedNeed unconfirmed) | Medium | High | Pursue partner from M0 in parallel; 6-month-post-M3 decision point to declare "Publicly Shipped (generic public good)"; later endorsement upgrades status | Steward |
| User-search privacy leak (query reveals location + health concern) | Low | High | No query logging by design; client-side lookup; no precise-geolocation storage; CSP `connect-src` locked; runtime network-interception E2E | Maintainer |
| Project mistaken for / misused as an official advisory | Medium | High | Prominent persistent "not a substitute for official advisories" framing; direct link to jurisdiction; no-endorsement disclaimer; not-an-alert-system non-goal | Maintainer + Steward |
| Regulatory drift (2024 PFAS MCLs, evolving Lead & Copper Rule) makes content stale | High | Medium | Regulatory-update tracking; re-review on rule change; `lastReviewed` + freshness automation | Domain expert |
| Civic content perceived as partisan / blame-assigning | Low | Medium | Non-partisan editorial rule; facts + neutral official accountability channels only; governance review | Maintainer + Board |
| Source data quality issues upstream (SDWIS known gaps/errors) | High | Medium | Surface data caveats honestly; do not "correct" upstream silently; link to source for disputes; staleness/coverage reporting | Code/data reviewer |
| Maintainer bandwidth / bus factor | Medium | Medium | Reviewer rotation, documented processes, MIT/CC-BY low lock-in, reproducible pipeline | Maintainer |

---

## Security & privacy

**Threat surface.** A static, no-backend, no-PII site has a small surface. Principal concerns:
(1) supply-chain risk in dependencies; (2) third-party script/tracker creep; (3) any pipeline
secret/token (API keys for data sources) leaking into logs/commits; (4) hostile forks implying false
agency endorsement; (5) user-search privacy.

**Controls.**
- **No secrets in client or commits.** Source APIs that need keys are used **only in the pipeline /
  CI** via secret stores; **never** written to logs, snapshots, explainers, or committed files (per
  Hee-Lee Oss rule). Static hosting has nothing to leak at runtime.
- **No telemetry / no PII / no search logging — defense in depth.** (1) Strict CSP with locked
  `connect-src` / `script-src` / `img-src` / `font-src 'self'`; (2) **runtime network-interception
  E2E** exercising every flow, failing the build on any unexpected outbound request; (3) static CI
  audit (no analytics/trackers/PII fields, **no server-side search log**). Lookup is client-side
  where feasible; precise user geolocation is never stored.
- **Supply chain:** pinned/locked deps (pnpm lockfile), dependency review, minimal deps, SRI where
  applicable, CI dependency audit.
- **Pipeline integrity:** snapshots are checksummed; raw responses are retained for audit;
  raw→normalized is deterministic and reproducible; reconciliation gate prevents drift between
  published figures and source.
- **Content integrity:** all figures source-cited and reconciliation-gated; all health content
  expert-reviewed; corrections/erratum workflow purges/replaces corrected content.
- **Abuse/misuse:** prominent "not an official agency product unless endorsed" + "not a substitute
  for official advisories" disclaimers; trademark/attribution terms; no agency logos reused.

---

## Sustainability & maintenance

- **Ownership after delivery:** maintainer owns code/pipeline/releases; steward owns deployment + the
  partner relationship. Both **TBD**, named before M3.
- **Data freshness:** scheduled re-ingest with snapshot dates + staleness flags; explainers show
  sample period + snapshot date and link to the live official source; regulatory-update tracking
  (PFAS/LCR) triggers content re-review.
- **Corrections / erratum:** a documented workflow to correct a published figure or claim, purge the
  stale version, and record the correction in the review log — important because a wrong water-safety
  figure can cause real-world action.
- **Outcomes tracking:** no telemetry, so adoption/reach/comprehension are tracked via partner
  self-report + a manual record of endorsements/MOUs.
- **Low lock-in:** MIT/CC-BY, open data, static hosting, reproducible pipeline — forkable and cheap
  to run.

---

## Open questions

1. **Partner org:** Who is the named partner (public-health dept / water-justice nonprofit /
   community org)? Needed before *Definition of Shipped*. (Human decision.)
2. **Pilot geography:** Which region/state first? Should be partner-driven; affects which state
   primacy-agency data and which languages.
3. **Priority languages:** First non-English locale (provisionally Spanish (es)); partner may
   override.
4. **State-data licensing:** Per-state verification needed; some states' terms may block reuse — open
   question per pilot region.
5. **Service-area boundaries:** EPA/state PWS service-area boundary data coverage and license vary;
   how precisely can we map a person to a system using open data only?
6. **WHO NC terms:** confirm our paraphrase-and-cite stance for WHO guideline values is sufficient,
   and whether any one-time license review is warranted.
7. **Comprehension testing logistics:** how to run a small, no-PII moderated comprehension test.
8. **Regulatory cadence:** process + owner for re-reviewing on PFAS/LCR and future rule changes.
9. **ADR decisions:** data tooling/snapshot format, web framework + client-side lookup, geocoding
   (M0).

---

## References

- Hee-Lee Oss work rules — `C:\code\hee-lee-oss\CLAUDE.md`
- Good-deed definition & risk tiers — `C:\code\hee-lee-oss\docs\good-deed-definition.md`
- Task schema — `C:\code\hee-lee-oss\packages\schema\src\schemas.ts`
- Portfolio roadmap (this project: Track 10) — `C:\code\hee-lee-oss\planning\ROADMAP.md`
- Sibling exemplar (medium-risk safety) — `C:\code\hee-lee-oss\planning\projects\proper-prepper\PLAN.md`
- Authoritative data/reference sources (license-checked per use): EPA SDWIS/ECHO, EPA Safe Drinking
  Water Act regulations (MCL/MCLG, Lead & Copper Rule, 2024 PFAS NPDWR), Water Quality Portal,
  USGS water data, state primacy agencies, WHO Guidelines for Drinking-water Quality.
- Open geocoding: US Census/TIGER (public domain), OpenStreetMap/Nominatim (ODbL).
- WCAG 2.2 AA (W3C Web Content Accessibility Guidelines).

---

## Appendix A — Improvements applied

Twenty-five specific improvements made to the first draft of this plan, each **applied** in the text
above (not merely proposed):

1. **No-verdict rule elevated to project identity** — moved "never label water safe/unsafe" from a
   buried non-goal to the executive summary and the headline constraint; added a CI/no-verdict review
   gate (Quality gates) and an engine test (M1) so it's enforced, not aspirational.
2. **Two-sided harm framed explicitly** — added the false-reassurance *and* false-alarm asymmetry to
   the summary and the risk table as the two top risks, each with distinct mitigations.
3. **Comprehension made a success metric** — added an ≥80% moderated-comprehension target so an
   explainer that leaves a false sense of certainty counts as failure.
4. **License gate made machine-enforced** — turned "open/PD/CC only" into a concrete source registry
   + CI gate that rejects unregistered/unverified sources, rather than a verbal promise.
5. **Proprietary aggregators explicitly excluded** — named EWG-class closed databases and CCR-PDF
   copying as out of scope to prevent license contamination.
6. **WHO NC terms handled specifically** — paraphrase+cite, no verbatim, no relicensing WHO text as
   CC-BY; added as an open question for a possible one-time license check.
7. **ODbL share-alike/attribution tracked** — geocoding via OSM/Nominatim now carries explicit
   share-alike + attribution obligations and keeps derived geodata under ODbL.
8. **Non-detect ≠ zero** — added detection-limit / non-detect handling to the schema and made it an
   M1 exit criterion, a common source of misleading water figures.
9. **Violation taxonomy added** — distinguished health-based vs. monitoring/reporting vs.
   treatment-technique violations in the schema, engine, and risk table to prevent "paperwork = poison"
   misreading.
10. **Sample period vs. publication date separated** — schema records when water was *sampled*, not
    when published, and the explainer surfaces lag — avoiding implying the data is live.
11. **Lead/home-plumbing caveat made central** — explicit "system data ≠ your tap" because lead is
    dominated by service lines/home plumbing absent from system data.
12. **Private-well module added** — a distinct M2 deliverable for the unregulated, untested-by-default
    well population, with its own expert review.
13. **User-search privacy hardened** — searches reclassified as sensitive (location + health concern);
    added no-query-logging, client-side lookup, no precise-geolocation storage, CSP + runtime egress
    test.
14. **Reproducibility contract** — snapshots are checksummed and raw→normalized is deterministic, so
    anyone can reproduce a figure from a source vintage (provenance you can re-run).
15. **Data-accuracy reconciliation gate** — 100% of published figures must reconcile to the cited
    snapshot in CI; an unreconciled figure blocks merge.
16. **Provenance-completeness gate** — every published figure must carry source + retrieval date +
    license, enforced in CI.
17. **Expert escalation despite "medium" tier** — added a hard rule that any health-action content
    gets credentialed drinking-water/environmental-health/toxicology sign-off (high-risk bar) with
    no self-approval and a PR-tied review log.
18. **Source-divergence precedence rule** — federal vs. state vs. WHO conflicts resolved by
    jurisdiction, with both stated and never blended; decision recorded in the review log.
19. **Regulatory-drift tracking** — 2024 PFAS MCLs and the evolving Lead & Copper Rule named as
    re-review triggers, with an owner and an M4 exercise on a real rule change.
20. **Corrections/erratum workflow** — explicit process to purge/replace a wrong published figure and
    log it, recognizing that water figures drive real-world action.
21. **No-product-recommendation rule** — filters/bottled water/test kits excluded, including "neutral"
    comparisons, to avoid commercial-benefit and endorsement risks.
22. **Non-partisan editorial rule** — civic-neutral framing; facts + neutral official accountability
    channels only; no blame to named actors/parties (Flint-type politicization risk).
23. **"Publicly Shipped (no partner)" success state** — a 6-month-post-M3 decision point so a finished
    site isn't stranded waiting on a partner, mirroring the sibling exemplar.
24. **Pipeline-secret hygiene** — data-source API keys live only in CI/pipeline secret stores, never
    in client, logs, snapshots, or commits.
25. **Schema mirrors authoritative concepts** — MCL vs. MCLG, primacy agency, PWSID, service-area as
    advisory-only, and "where to get the official advisory" are first-class schema fields so the
    not-an-advisory routing is structural, not cosmetic.

---

## Review sign-off

**Reviewer:** senior staff engineer + TPM (self-review pass against PLAN_SPEC, CLAUDE.md, the
good-deed definition, and the Task schema).

**Completeness check.**
- All 17 required PLAN sections present and in spec order. ✅
- Metadata header present (Status/Version/Last updated/Owner/Lane) + explicit risk tier. ✅
- Outcome-based metrics with baselines + targets (not vanity metrics). ✅
- License/provenance section conservative, source-by-source, with a CI-enforced gate. ✅
- Privacy/PII covers both data (system-level) and the sensitive user-search vector. ✅
- Risk table uses Risk | Likelihood | Impact | Mitigation | Owner. ✅
- Roadmap M0–M4 each has measurable exit criteria; M0 is a thin cold-start. ✅
- Partner + verified need honestly marked **TO BE SECURED**; `verifiedNeed:false` mandated in TASKS. ✅

**Correctness check (domain).**
- MCL vs. MCLG, violation categories, non-detect handling, lead/home-plumbing and private-well
  caveats, and the 2024 PFAS / Lead & Copper Rule references reflect the authoritative framework. ✅
- "Open/PD/CC only" is enforced; WHO (NC) and ODbL (share-alike) handled correctly; proprietary
  aggregators excluded. ✅
- The no-verdict / not-an-advisory stance is structural (schema + engine + gate), not just copy. ✅

**Fixes applied during review.**
- Clarified that the explainer engine emits **no verdict logic** in both the architecture and M1 exit
  criteria (was implicit).
- Made the expert-sign-off escalation explicit in the metadata header so the "medium" tier is not
  mistaken for "domain-reviewer-only" on health-action content.
- Added "source data quality issues upstream (SDWIS gaps)" to the risk table — we surface, never
  silently "correct," upstream data.

**Residual items requiring a human decision** (see *Open questions*): named partner, pilot geography
+ languages, per-state data licensing, service-area boundary feasibility, and the WHO license check.

**Sign-off:** Plan is internally consistent and ready for maintainer/governance review. It is
**not** approved for "shipped" until a partner is secured (or the "Publicly Shipped" decision point
is invoked) and ADRs are recorded in M0.
