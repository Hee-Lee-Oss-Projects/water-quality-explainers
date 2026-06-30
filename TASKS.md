# TASKS — water-quality-explainers (local drinking-water safety explainers from open data)

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

## How these tasks map to Elyos

Each task below becomes an Elyos **Task JSON** validated against
`packages/schema/src/schemas.ts`. Field mapping:

- `id` — stable slug ID, e.g. `wqe-arch-001`.
- `title` — the task title from the table.
- `project` — `"water-quality-explainers"`.
- `type` — one of `code | research | writing | data | design-spec | maintenance` (the "Type" column).
- `lane` — `"donated"` for all tasks here (no escrow/API spend). A `funded` task would additionally
  require `fundedBudgetUsd`.
- `priority` — `high | medium | low`.
- `domain` — array, e.g. `["public-health","water","open-data","software","accessibility"]`.
- `riskTier` — `low | medium | high` (the "Risk" column). Most content is **medium**; any health-
  action content is reviewed at the **expert (high) bar** even when tagged medium (see review rule).
- `urgent` — boolean (default `false`; this is an explanatory reference, **not** a live advisory).
- `deliverable` — `pr | dataset | document | translation` (the "Deliverable" column).
- `tokenEstimate` — `small | medium | large` (the "Size" column).
- `status` — `open | in-progress | review | delivered | done` (all start `open`).
- `context`, `objective`, `acceptanceCriteria[]`, `output` — task narrative + checkable criteria.
- `resources[]` — source/reference URLs or repo paths.
- `requestor` — partner/requestor (**TO BE SECURED**; use `"TBD"` until a partner is named).
- `verifiedNeed` — **`false` until a named partner org confirms need in writing** (honest default).
- `outputLicense` — `"MIT"` for code, `"CC-BY-4.0"` for content/docs/translations, and `"CC0-1.0"` or
  `"CC-BY-4.0"` (or `"ODbL-1.0"` for OSM-derived geodata) for datasets per source compatibility.

**Reviewer column legend:** `maintainer` (code/data review + reconciliation gate), `expert`
(drinking-water / environmental-health / public-health professional or toxicologist — signs off
contaminant + health-action content), `a11y` (accessibility + readability reviewer),
`translation+expert` (competent speaker paired with technical-accuracy re-check), `steward`
(deployment + partner).

**Content review rule (all `expert` tasks):** the unit's **author may not approve their own work** —
`reviewedBy` (author) and `approvedBy` (expert) must be distinct people, and the approving expert
must meet the credential bar (drinking-water / environmental-health / public-health professional or
toxicologist, or equivalent recognized practice). Each approval writes a **PR-tied, append-only
review-log entry** (PR #, commit SHA, content/snapshot version, reviewer + approver + credential,
sources checked, any source-divergence decision). This is the auditable record the Definition of
Shipped checks against.

**No-verdict rule (all content + engine tasks):** output must **never** state or imply a safety
verdict, grade, ranking, or product recommendation, and must carry the limitations + "not a
substitute for official advisories" framing. This is checked in review and tested in the engine.

**Sequencing rule (M0):** the snapshot-format ADR and source-registry/license-gate ADR (**arch-001**)
are decided **before** the normalized schema (**data-003**) and any non-pilot ingestion are
finalized; until then the schema is provisional. data-003 and pipe-004 therefore depend on arch-001.

**Honesty defaults:** every task ships with `verifiedNeed: false` and `requestor: "TBD"` because **no
partner is secured**. `wqe-partner-016` flips these to `true` / the named org on success.

---

## Milestone M0 — Foundation & cold-start

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| wqe-arch-001 | ADRs: snapshot/data format, source registry + **license gate**, web stack + client-side lookup, geocoding (open data), freshness/corrections model | design-spec | small | low | document | — | maintainer |
| wqe-data-002 | Source registry + CI license/provenance gate (open/PD/CC only; reject unverified) | data | small | medium | dataset | arch-001 | maintainer |
| wqe-data-003 | Normalized water-data schema (PWS, ContaminantResult, Violation taxonomy, Citation, Snapshot, ExplainerUnit) | data | medium | medium | dataset | arch-001 | maintainer, expert |
| wqe-pipe-004 | Pilot ingestion: EPA ECHO/SDWIS for one small region → provenance-stamped, checksummed snapshot | code | medium | medium | pr | data-002, data-003 | maintainer |
| wqe-content-005 | Lead explainer template + lead contaminant entry, no-verdict + limitations, expert-signed-off | writing | medium | medium | document | data-003 | expert |
| wqe-web-006 | Static web skeleton + privacy CI gates (no telemetry, **no search logging**, CSP + runtime egress E2E) | code | medium | medium | pr | — | maintainer, a11y |

**Acceptance criteria — key tasks**

- **wqe-data-002 (source registry + license gate):**
  - Declarative registry; each source records publisher, dataset/API URL, license/terms,
    license-verification note + date, allowed-use.
  - CI **fails** on ingestion from an unregistered source or one with an unverified/incompatible
    license; proprietary aggregators and CCR-PDF copying are rejected by policy + test.
  - WHO entries are marked NC (paraphrase+cite only); OSM/Nominatim entries carry ODbL share-alike +
    attribution flags.
- **wqe-data-003 (normalized schema):**
  - Captures `pwsId`, contaminant identity (CAS/EPA code), `measuredValue` + `detectionLimit` with
    **non-detect ≠ zero**, `unit` (normalized + original), `mcl`, `mclg`, **sample period (not
    publication date)**, and the **violation taxonomy** (health-based vs. monitoring/reporting vs.
    treatment-technique).
  - Every result/violation requires a `Citation` (publisher/dataset/url/retrievedDate/sourceVintage/
    license/checksum) tied to a `Snapshot`; build-time validation rejects a record missing required
    citation fields and an ExplainerUnit where `approvedBy == reviewedBy`.
  - Format follows the ADR (arch-001), which lands first; schema is provisional until then.
- **wqe-pipe-004 (pilot ingestion):**
  - Read-only adapter snapshots raw responses with retrieval metadata before transform; raw→normalized
    is **deterministic** (same source vintage + pipeline version ⇒ same snapshot checksum).
  - Output snapshot reconciles to source in CI; any API key is used only in CI/pipeline secrets and
    never written to logs/snapshots/commits.
- **wqe-content-005 (lead explainer + entry):**
  - Every figure/claim traceable to a cited authoritative source (EPA LCR/MCLG, WHO) with retrieval
    date + license; no verbatim copying of copyrighted text.
  - Contains explicit **"system data ≠ your tap" (home plumbing/service lines)** caveat, a
    limitations section, and "where to get the official advisory"; **no safety verdict**.
  - Reviewed and **signed off by an expert distinct from the author**; PR-tied review-log entry
    written; source-divergence precedence applied/recorded if EPA and WHO values differ.
- **wqe-web-006 (web skeleton + privacy CI):**
  - Static site loads; **no analytics/trackers**, **no server-side search log**, no PII fields.
  - Enforced by defense in depth: static audit **plus** CSP locked `connect-src` **plus** a runtime
    network-interception E2E that fails on any unexpected outbound request. A grep alone does not pass.
  - TypeScript/ESM; `pnpm build && pnpm test && pnpm lint` pass.

**Definition of Done (M0):** ADRs recorded; source registry + license gate live in CI; provisional
normalized schema defined; pilot snapshot reconciles to source with complete provenance; one
expert-signed-off lead explainer renders with no-verdict + limitations framing; web skeleton loads
with no-telemetry/no-search-logging verified at runtime.

---

## Milestone M1 — Explainer engine & data accuracy

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| wqe-pipe-007 | Core-contaminant normalization (lead, copper, nitrate, arsenic, coliform/E. coli, TTHM/HAA5, PFAS) + non-detect/detection-limit handling | code | large | medium | pr | pipe-004 | maintainer, expert |
| wqe-engine-008 | Deterministic explainer-generation engine (no-verdict; limitations + violation-context logic) | code | large | medium | pr | content-005, pipe-007 | maintainer, expert |
| wqe-data-009 | Data-accuracy reconciliation + provenance-completeness suite (staleness flags, unit/identity checks) | code | medium | medium | pr | pipe-007 | maintainer |
| wqe-content-010 | Contaminant library: ≥ 8 core entries, plain-language, sourced to EPA/WHO, expert-reviewed | writing | large | medium | document | content-005 | expert |
| wqe-a11y-011 | WCAG 2.2 AA + plain-language readability hardening (automated + manual AT audit) | code | medium | low | pr | web-006, engine-008 | a11y |

**Acceptance criteria — key tasks**

- **wqe-pipe-007 (core-contaminant normalization):**
  - Each contaminant normalized to a stable identity (CAS/EPA code) with harmonized units and its MCL
    + MCLG; **non-detect is preserved as non-detect (not zero)** with the detection/reporting limit.
  - Violations classified by the taxonomy; resolved vs. open status preserved; sample periods retained
    distinct from publication.
  - 2024 PFAS MCLs and Lead & Copper Rule values are sourced and flagged for regulatory re-review.
- **wqe-engine-008 (explainer engine):**
  - Deterministic generation from snapshot + library; same inputs ⇒ identical output.
  - **Tested to emit no safety verdict / grade / ranking / product recommendation**; every generated
    explainer includes the limitations section and "not a substitute for official advisories" + the
    route to the jurisdiction's advisory.
  - Distinguishes health-based vs. monitoring/reporting violations in plain language; states what the
    data does and does not cover (home plumbing, private wells, sampling gaps, lag).
- **wqe-data-009 (reconciliation + provenance suite):**
  - CI fails if **any** published figure does not reconcile to its cited snapshot, or lacks a complete
    citation (source/retrieval date/license).
  - Unit and contaminant-identity sanity checks; staleness flags based on sample period + snapshot
    date.
- **wqe-content-010 (contaminant library):**
  - ≥ 8 entries (lead, copper, nitrate, arsenic, total coliform/E. coli, TTHM, HAA5, PFAS), each
    expert-signed-off by an approver distinct from the author, sourced to EPA/WHO with retrieval dates;
    nitrate entry notes acute infant risk neutrally and routes to authorities (no individual advice).

**Definition of Done (M1):** core contaminants normalized with non-detect/limit handling + violation
taxonomy; reconciliation + provenance gates green (100% reconcile); engine proven to emit no verdict;
≥ 8 expert-reviewed contaminant entries; WCAG 2.2 AA + readability pass.

---

## Milestone M2 — Coverage & privacy-preserving search

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| wqe-pipe-012 | Scale ingestion to a full pilot state/region + snapshot refresh | code | large | medium | pr | pipe-007, data-009 | maintainer |
| wqe-web-013 | Privacy-preserving lookup/search (client-side index; **no query logging**; no precise-geolocation storage) | code | medium | medium | pr | web-006, engine-008 | maintainer, a11y |
| wqe-data-015 | Service-area / geocoding using open data only (Census/TIGER PD; OSM ODbL attribution) | data | medium | medium | dataset | arch-001 | maintainer |
| wqe-content-014 | Private-well guidance module (unregulated; "no one tests this unless you do"; not advice) | writing | medium | medium | document | content-010 | expert |

**Acceptance criteria — key tasks**

- **wqe-pipe-012 (scale to pilot state):**
  - Full pilot state/region ingested into refreshed, checksummed snapshots; coverage report committed;
    upstream data gaps surfaced honestly (never silently "corrected").
- **wqe-web-013 (privacy-preserving search):**
  - Lookup works with **no server-side query log** and no third-party calls (verified by runtime
    egress E2E); precise user geolocation is never stored; client-side index used where feasible.
  - Accessible (keyboard, screen-reader labels, AA contrast); no critical axe violations.
- **wqe-content-014 (private-well module):**
  - States clearly that private wells are **not covered by drinking-water regulations** and are not
    tested by any agency by default; links to official testing/lab resources; **no lab-result
    interpretation or individual advice**; expert-signed-off; no verdict.
- **wqe-data-015 (open geocoding):**
  - Uses only open/PD/ODbL sources; ODbL attribution + share-alike honored; derived geodata licensed
    accordingly; maps to **system**, not to person.

**Definition of Done (M2):** ≥ 1 full pilot state/region covered with refreshed snapshots;
privacy-preserving lookup live with no query logging (verified); private-well module shipped
(expert-reviewed); geocoding uses open data only with correct attribution.

---

## Milestone M3 — i18n, partner adoption & ship

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| wqe-partner-016 | Secure named partner (public-health dept / water-justice org): need, geography, languages, endorsement | research | medium | medium | document | — | maintainer, steward |
| wqe-i18n-017 | i18n framework + completeness check + first non-English locale (UI + core explainers), accuracy-reviewed | code | large | medium | translation | engine-008, content-010 | maintainer, translation+expert |
| wqe-deploy-018 | Production deploy + freshness automation + corrections/erratum process + disclaimers/branding | code | medium | medium | pr | a11y-011, web-013, pipe-012 | maintainer, steward |
| wqe-ops-019 | Outcomes tracking (partner self-report) + re-review/freshness cadence + regulatory-update process | maintenance | small | low | document | partner-016, deploy-018 | maintainer, steward |

**Acceptance criteria — key tasks**

- **wqe-partner-016 (secure partner):**
  - A named partner confirms the need in writing (letter of support / MOU) and identifies priority
    geography, water systems, and languages.
  - On success, `verifiedNeed` flips to `true` and `requestor` is set to the named org across tasks.
- **wqe-i18n-017 (i18n + first locale):**
  - Strings externalized; build-time completeness check; safe fallback to English (never blank/broken
    safety text); ≥ 1 non-English locale for UI + core explainers.
  - Translations accuracy-reviewed for **health/regulatory meaning**, not only fluency, with expert
    escalation for health content; derivative content licensed CC-BY-4.0.
- **wqe-deploy-018 (production deploy):**
  - Static production build over HTTPS; freshness automation refreshes snapshots with snapshot dates +
    staleness flags; **corrections/erratum workflow** purges/replaces a wrong figure and logs it.
  - Prominent persistent "not a substitute for official advisories" + "not an official agency product
    unless endorsed" disclaimers; no agency logos reused; neutral, non-partisan framing.
- **wqe-ops-019 (outcomes + freshness + regulatory):**
  - Privacy-preserving outcomes process (partner self-report; distinct users, de-dup, flagged
    estimates; no in-app telemetry); re-review cadence + **regulatory-update trigger** (PFAS/LCR) with
    a named owner.

**Definition of Done (M3):** ≥ 1 non-English locale (UI + core explainers) accuracy-reviewed;
**named partner endorsement/adoption on file** (verifiedNeed=true); production deploy with freshness
+ corrections process; outcomes tracking + regulatory re-review operational. Satisfies the
project-level *Definition of Shipped (partner-adopted)*.

**Decision point (so a finished site isn't stranded):** if no partner is secured by **6 months after
the M3 production build is ready**, the steward + Elyos governance may declare **"Publicly Shipped
(generic public good)"** — criteria (1)–(5) met, deployed/distributed directly or via community
channels, outcomes by best-effort self-report. A later partner endorsement upgrades the status.

---

## Milestone M4 — Scale & sustainability (post-launch)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| wqe-pipe-020 | Extend coverage to ≥ 2 additional regions + scheduled re-ingest | code | large | medium | pr | pipe-012, ops-019 | maintainer |
| wqe-content-021 | Expand contaminant library (radionuclides, chromium-6, DBP subspecies), expert-reviewed | writing | large | medium | document | content-010 | expert |
| wqe-i18n-022 | Second non-English locale (UI + core explainers), accuracy-reviewed | writing | medium | medium | translation | i18n-017 | translation+expert |

**Acceptance criteria — key tasks**

- **wqe-pipe-020 (more regions + scheduled re-ingest):**
  - ≥ 2 additional regions covered; scheduled re-ingest with staleness flags operational; per-state
    license re-verified before ingestion; a real regulatory change re-review exercised end-to-end.
- **wqe-content-021 (expanded library):**
  - New entries sourced to EPA/WHO, expert-signed-off, no verdict; regulatory-evolution items flagged
    for re-review.

**Definition of Done (M4):** ≥ 2 additional regions with scheduled freshness; expanded
expert-reviewed contaminant coverage; ≥ 2 non-English locales; a regulatory-update re-review
demonstrated on a real rule change.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| wqe-feat-023 | Historical trend view per system (sample periods over time) | code | medium | medium | pr | No prediction; past public record only |
| wqe-feat-024 | Offline/PWA mode for low-connectivity users | code | medium | low | pr | Reuse static artifacts; no tracking |
| wqe-data-025 | Open public dataset export of normalized snapshots (with provenance) | data | medium | medium | dataset | CC0/CC-BY/ODbL per source |
| wqe-feat-026 | Read-aloud / low-literacy "easy-read" explainer variants | writing | medium | medium | document | a11y + expert review |
| wqe-research-027 | Feasibility of international sources (UK DWI, EU) with license verification | research | medium | medium | document | License gate per source |
| wqe-ops-028 | Dependency/supply-chain audit + SRI hardening | maintenance | small | low | pr | Recurring |
| wqe-ops-029 | Annual content re-validation vs. updated EPA/WHO guidance | maintenance | medium | medium | document | Recurring; regulatory-drift owner |

---

## Generated task index

Decomposed by Elyos task-decomposition agent on 2026-06-29. 29 total task files; 28 generated + 1 seed (wqe-arch-001). All validated against `packages/schema/src/schemas.ts` (draft-07). Fan-out: none (tasks enumerated individually in backlog). All tasks: `lane: "donated"`, `status: "open"`, `verifiedNeed: false`, `requestor: "TBD"` (no partner secured; honest default).

| File | Title | Type | Size | Risk | Deliverable | Priority | License |
| --- | --- | --- | --- | --- | --- | --- | --- |
| wqe-arch-001.json | ADRs: snapshot/data format, source registry + license gate, web stack, geocoding, freshness/corrections model | design-spec | small | low | document | high | CC-BY-4.0 |
| wqe-data-002.json | Source registry + CI license/provenance gate | data | small | medium | dataset | high | CC-BY-4.0 |
| wqe-data-003.json | Normalized water-data schema | data | medium | medium | dataset | high | MIT |
| wqe-pipe-004.json | Pilot ingestion: EPA ECHO/SDWIS | code | medium | medium | pr | high | MIT |
| wqe-content-005.json | Lead explainer template + lead contaminant entry | writing | medium | medium | document | high | CC-BY-4.0 |
| wqe-web-006.json | Static web skeleton + privacy CI gates | code | medium | medium | pr | high | MIT |
| wqe-pipe-007.json | Core-contaminant normalization + non-detect handling | code | large | medium | pr | high | MIT |
| wqe-engine-008.json | Deterministic explainer-generation engine | code | large | medium | pr | high | MIT |
| wqe-data-009.json | Data-accuracy reconciliation + provenance-completeness suite | code | medium | medium | pr | medium | MIT |
| wqe-content-010.json | Contaminant library: >= 8 core entries | writing | large | medium | document | high | CC-BY-4.0 |
| wqe-a11y-011.json | WCAG 2.2 AA + plain-language readability hardening | code | medium | low | pr | medium | MIT |
| wqe-pipe-012.json | Scale ingestion to a full pilot state/region | code | large | medium | pr | medium | MIT |
| wqe-web-013.json | Privacy-preserving lookup/search | code | medium | medium | pr | medium | MIT |
| wqe-content-014.json | Private-well guidance module | writing | medium | medium | document | medium | CC-BY-4.0 |
| wqe-data-015.json | Service-area / geocoding using open data only | data | medium | medium | dataset | medium | ODbL-1.0 |
| wqe-partner-016.json | Secure named partner | research | medium | medium | document | high | CC-BY-4.0 |
| wqe-i18n-017.json | i18n framework + first non-English locale | code | large | medium | translation | medium | CC-BY-4.0 |
| wqe-deploy-018.json | Production deploy + freshness automation + corrections/erratum | code | medium | medium | pr | medium | MIT |
| wqe-ops-019.json | Outcomes tracking + re-review/freshness cadence + regulatory-update process | maintenance | small | low | document | low | CC-BY-4.0 |
| wqe-pipe-020.json | Extend coverage to >= 2 additional regions + scheduled re-ingest | code | large | medium | pr | low | MIT |
| wqe-content-021.json | Expand contaminant library (radionuclides, chromium-6, DBP subspecies) | writing | large | medium | document | medium | CC-BY-4.0 |
| wqe-i18n-022.json | Second non-English locale | writing | medium | medium | translation | low | CC-BY-4.0 |
| wqe-feat-023.json | Historical trend view per system | code | medium | medium | pr | low | MIT |
| wqe-feat-024.json | Offline/PWA mode for low-connectivity users | code | medium | low | pr | low | MIT |
| wqe-data-025.json | Open public dataset export of normalized snapshots | data | medium | medium | dataset | low | CC-BY-4.0 |
| wqe-feat-026.json | Read-aloud / low-literacy easy-read explainer variants | writing | medium | medium | document | low | CC-BY-4.0 |
| wqe-research-027.json | Feasibility of international sources (UK DWI, EU) | research | medium | medium | document | low | CC-BY-4.0 |
| wqe-ops-028.json | Dependency/supply-chain audit + SRI hardening | maintenance | small | low | pr | low | MIT |
| wqe-ops-029.json | Annual content re-validation vs. updated EPA/WHO guidance | maintenance | medium | medium | document | medium | CC-BY-4.0 |

---

## Example task JSON

Complete, schema-valid Task JSON for the first M0 task. `verifiedNeed` is `false` and `requestor` is
`"TBD"` because **no partner org is secured yet** (honest default per the plan).

```json
{
  "id": "wqe-arch-001",
  "title": "ADRs: snapshot/data format, source registry + license gate, web stack + client-side lookup, geocoding, freshness/corrections model",
  "project": "water-quality-explainers",
  "type": "design-spec",
  "lane": "donated",
  "priority": "high",
  "domain": ["public-health", "water", "open-data", "software"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "document",
  "tokenEstimate": "small",
  "status": "open",
  "context": "water-quality-explainers turns open, authoritative drinking-water data (EPA SDWIS/ECHO, Water Quality Portal, USGS, state primacy agencies) into calm, plain-language, per-water-system explainers that never issue a safety verdict and always defer to official advisories. Before any non-pilot ingestion or schema finalization, the foundational architecture decisions must be recorded so that 'open/PD/CC only' licensing, reproducible provenance, and no-user-search-logging privacy are enforceable rather than aspirational.",
  "objective": "Record Architecture Decision Records (ADRs) for: (1) the snapshot/data format and the deterministic raw-to-normalized reproducibility contract; (2) the source registry format and the CI license gate that rejects unregistered or unverified sources; (3) the static web stack and a client-side lookup approach that makes no-query-logging feasible; (4) open-data-only geocoding with attribution; and (5) the freshness/snapshot/versioning and corrections/erratum model.",
  "acceptanceCriteria": [
    "ADR for snapshot/data format specifies a deterministic raw-to-normalized contract (same source vintage + pipeline version yields the same checksummed snapshot)",
    "ADR for the source registry + license gate defines how CI blocks ingestion from unregistered or unverified-license sources, and explicitly excludes proprietary aggregators and CCR-PDF copying",
    "ADR for the web stack selects a static-first, a11y-friendly framework and a lookup approach (client-side index or pre-rendered) that requires no server-side query logging",
    "ADR for geocoding uses open data only (US Census/TIGER public domain; OSM/Nominatim ODbL) with attribution and share-alike obligations recorded",
    "ADR for freshness records the snapshot/versioning model and a corrections/erratum workflow that can purge and replace a wrong published figure",
    "ADRs are committed as documents; the normalized schema (wqe-data-003) and pipeline (wqe-pipe-004) are sequenced to depend on these decisions"
  ],
  "resources": [
    "C:\\code\\elyos\\planning\\projects\\water-quality-explainers\\PLAN.md",
    "C:\\code\\elyos\\planning\\ROADMAP.md",
    "https://echo.epa.gov/",
    "https://www.waterqualitydata.us/",
    "https://www.epa.gov/ground-water-and-drinking-water"
  ],
  "output": "A document (set of ADRs) recording the snapshot/data-format + reproducibility contract, source registry + license-gate mechanism, web stack + client-side lookup, open-data geocoding, and freshness/corrections model, with downstream schema and pipeline tasks sequenced to depend on them.",
  "requestor": "TBD",
  "verifiedNeed": false,
  "outputLicense": "CC-BY-4.0"
}
```
