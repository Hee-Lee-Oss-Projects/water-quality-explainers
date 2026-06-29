# water-quality-explainers

> **water-quality-explainers** turns open, authoritative drinking-water data into calm, plain-language explainers that help an ordinary person understand **what the public record says about their local   ·  **Risk tier:** med  ·  **Status:** planning

**water-quality-explainers** turns open, authoritative drinking-water data into calm, plain-language explainers that help an ordinary person understand **what the public record says about their local water system** — which contaminants were measured, what the results mean, what the limits are, and where to find the official, legally-binding advisory. It is two coupled lanes of work: (1) a **reproducible data pipeline** that ingests open government datasets (US EPA SDWIS / ECHO, the multi-agency Water Quality Portal, USGS, and state primacy-agency data), normalizes them into a versioned schema, and snapshots them with full provenance; and (2) an **explainer engine + accessible web app** that renders per-water-system, plain-language explanations sourced to named authorities, in multiple languages.

**Definition of shipped:** `verifiedNeed: false` and `requestor: "TBD"` until that happens.

This is an **Elyos** good-deed project. Contributors pull a task, do it with their own coding agent, and open a PR. Platform: https://github.com/jdev1977/elyos

## Plan
- [PLAN.md](./PLAN.md) — robust enterprise plan (vision, architecture, roadmap, risks; includes an applied-improvements appendix + review sign-off)
- [TASKS.md](./TASKS.md) — schema-mapped task backlog
- [tasks/](./tasks/) — ready-to-pull task JSON(s)

## Contribute
```bash
elyos browse
elyos next --repo Elyos-Projects/water-quality-explainers --no-fork
```

## Licensing & review
- Open license (see PLAN.md).
- Risk tier **med** — deeds are *delivered, not merged*; a domain reviewer (and expert sign-off for any high-stakes content) must approve before merge.

> Planning stage; no adopting partner secured yet (`verifiedNeed: false` on delivery-dependent tasks).
