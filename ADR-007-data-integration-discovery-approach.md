# ADR-007: Data Integration Discovery Approach for Branch Health

**Status:** DRAFT — pending PO confirmation (Jay / Eric Prock) before promotion to a locked ADR
**Date:** 2026-08-24
**Owner:** Santosh (Branch Health MVP1 tech lead)
**Source:** Solution Architect working session, 2026-08-24

---

## 1. Context

The Solution Architect does not require full understanding of every Branch Health data
element to begin defining target topology. The immediate objective is establishing the
**technical landscape**, not final data mapping. This ADR captures the discovery
methodology agreed in the 8/24 session so it can be applied consistently across all
Branch Health data dependencies (and, per platform precedent, is reusable by other KYB
modules).

This ADR does **not** change any of ADR-001 through ADR-006. It defines *how* we discover
and classify dependencies before those decisions are extended or revisited.

## 2. Decision: Five-Question Discovery Framework

For every Branch Health data dependency, answer only these questions before attempting
deeper data mapping:

1. **Where is the data hosted?** (On-prem / AWS / Snowflake / data lake / other)
2. **Where is the API/service hosted?** (On-prem / AWS / other cloud / no API currently available)
3. **Is it behind a gateway or access layer?** (API Gateway / enterprise gateway / network boundary / other)
4. **What is the data freshness / update cadence?** (Real-time / daily / weekly / monthly)
5. **Is there an existing supported integration Branch Health can consume, or does a new
   API/data interface need to be created?**

Explicitly **out of scope** at this stage: "Is this exactly the right data for Branch
Health?" — data suitability is evaluated only after hosting/access/freshness/ownership
are known (see Workstream D below). This avoids designing architecture around
assumptions that later prove wrong (see the KYPB freshness case, §4).

## 3. Decision: Four-Bucket Dependency Classification

Every dependency is classified into one of:

| Bucket | Definition | Examples from current inventory |
|---|---|---|
| **1. Existing API/service, likely reusable** | Mature integration, documentation available | Digital BLDS (Swagger available), Client Central, Splunk, Cortex |
| **2. Existing data, interface/API needs confirmation** | Data exists but hosting/API/gateway unconfirmed | Branch Dashboard / Business Metric Dashboard, Banker Homepage, ATM Reporting & Monitoring, Performance Homepage, Branch Location Data Store, My Cash Manager |
| **3. Potential new integration/API required** | No existing API confirmed; owning team may need to expose one | Tony Roth's source area (no API confirmed as of 8/24 — **do not build against until confirmed**) |
| **4. Data-lake / analytical dataset** | Requires lineage/freshness validation before use | KYPB / Performance data (see §4) |

## 4. Key Finding: KYPB Freshness Cannot Be Assumed

KYPB is documented as having a two-month lag, but evidence reviewed 2026-08-24 (OSAT
report, "Data as of AUG 20", showing **Jul 2026** branch/market-level data) suggests the
lag may not apply uniformly, or the current dashboard may be drawing from a fresher
dataset than documented.

**Resolution:** Do not record "KYPB = 2-month lag" as a confirmed architecture fact.
Instead, trace full lineage before deciding integration approach:

```
Source system → ingestion → Snowflake/data lake → transformation → KYPB dataset → consuming app
```

Open sub-questions to resolve before committing to a KYPB-based integration:
- What is the source system, and when does source data become available?
- When is it ingested, and which dataset does KYPB actually consume?
- Is the 2-month lag inherent to the source, or an artifact of the current KYPB
  implementation?
- Could Branch Health consume a fresher upstream dataset directly instead of reusing the
  existing KYPB dataset?

This is tracked as an open item — see `data-dependency-matrix.md`, Performance section.

## 5. Decision: Four Parallel Workstreams

| Stream | Scope |
|---|---|
| **A. Environment / Engineering setup** | Repo structure, buckets, Jira, shared resources, personnel/access |
| **B. Architecture / Infrastructure** | Environment strategy (DEV/TEST/UAT/PROD), AWS/on-prem placement, network topology, gateway requirements, security boundaries, deployment model, NFRs — largely non-functional; Branch Health UX/design is not expected to significantly constrain this work |
| **C. Data & Integration Discovery** | Per-dependency: owner, hosting, API/interface, gateway, freshness, access/onboarding process (the 5-question framework in §2) |
| **D. Data Suitability** | Only *after* C is answered: reuse existing dataset/API vs. consume fresher dataset vs. direct integration vs. new API vs. store data vs. request/response-only vs. hybrid |

Architecture and infrastructure (Stream B) proceed **in parallel** with data discovery
(Stream C) rather than waiting for it — the Solution Architect can define baseline
topology and NFRs now and refine as dependency information lands.

## 6. Decision: Hybrid Integration Model (Working Assumption)

Cadence is not uniform across the 16 current Branch Health components (real-time /
daily / weekly / monthly — see `data-dependency-matrix.md`). Branch Health therefore
cannot use one generic retrieval strategy. Working assumption, to be confirmed as
dependencies are resolved:

- **Real-time** → API/request-response or event-driven integration
- **Daily** → scheduled ingestion / cache / data service
- **Weekly/Monthly** → analytical dataset / batch pipeline likely sufficient

This is consistent with, and does not contradict, ADR-002 (cached aggregation over live
calls, with per-source cadences) — this ADR provides the discovery method for
determining which per-source cadence applies to each of the 9 upstream systems.

## 7. Immediate Next Steps (as of 2026-08-24)

**This week:**
- Confirm the 5 discovery questions for each of the 16 components in
  `data-dependency-matrix.md`
- Continue dependency-owner discussions; engage data architects where direct owner
  access is difficult
- Investigate KYPB/Snowflake data freshness and lineage (§4)
- Confirm whether Tony Roth's source area truly has no existing API
- Continue BLDS/Digital BLDS onboarding using existing Swagger documentation
- Decide where environment/infrastructure definitions will live (repo/IaC vs.
  Confluence vs. combination) — feeds Stream B

**Target output:** a dependency map of Source → Hosting → API/Interface → Gateway →
Access → Data Freshness → Owner for all 16 components, enabling the Solution Architect
to define target topology with confidence even before every detail is finalized.

## 8. Open Questions Requiring PO Confirmation

Per existing convention, these are **not** to be built against until Jay/Eric Prock
confirm:
- Whether Tony Roth's source area has no existing API (new interface may be required)
- KYPB actual freshness/lineage and whether Branch Health should bypass it for a fresher
  source
- Whether the three stub-only Tech Readiness feeds (Teller Express/Splunk, Branch
  Network/Cortex, ATM Status/Kafka-ARM) should be re-scoped given the discovery
  questions above — pending Hila Morgan / Ravi Kota KT sessions per existing constraint

## 9. Relationship to Existing ADRs

- **ADR-002** (cached aggregation, per-source cadence): this ADR supplies the discovery
  method for populating those per-source cadences.
- **ADR-005** (service-per-capability backend): Bucket 3 dependencies (no existing API)
  are the most likely candidates for a new service-per-capability boundary once
  confirmed.
- No existing ADR is superseded.
