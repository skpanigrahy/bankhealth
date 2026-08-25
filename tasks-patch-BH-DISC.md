# Patch: Add Data Integration Discovery Tasks to tasks.md

**Task ID prefix:** BH-DISC (new — Data Integration Discovery workstream, per ADR-007)
**Applies to:** `tasks.md`
**Type:** Additive only — no existing task IDs (BH-EP1-01 → BH-EP6-06) are modified.
**Apply via:** GitHub Copilot Chat in IntelliJ (Claude model). Do not regenerate the
whole file — apply as an anchor-based insertion.

---

## Copilot instructions

> Open `tasks.md`. Find the last task in the existing task list (the final BH-EP6-xx
> entry, or the end-of-file marker if present). Insert the new **BH-DISC** section
> below, as a new top-level section titled `## Data Integration Discovery (ADR-007)`,
> immediately after the last existing epic section. Do not alter, renumber, or reformat
> any existing BH-EP1 through BH-EP6 task entries. If a section titled
> `## Data Integration Discovery` already exists, do not duplicate it — report back
> instead of inserting.

---

## New section to insert

```markdown
## Data Integration Discovery (ADR-007)

> Tracks per-dependency discovery for the 16 Branch Health Manager View components.
> See `docs/architecture/ADR-007-data-integration-discovery-approach.md` and
> `docs/architecture/data-dependency-matrix.md`. No implementation task under BH-EP1–EP6
> may consume a given data source until its BH-DISC task is marked resolved.

### BH-DISC-01 — Confirm Tony Roth source area API existence
**Blocks:** BH-EP2 (Required Actions: Control Exceptions, Branch Dashboard data points, CSC, Banker Notifications)
**Owner:** Santosh (coordination) / Tony Roth (confirmation)
**Acceptance criteria:** Written confirmation of whether an existing API exists for
Branch Dashboard / Business Metric Dashboard (84329) and Banker Homepage (104294)
sources. If none exists, capture whether owning team can expose a new interface and
target timeline.
**Status:** Not Started

### BH-DISC-02 — Cash Management APO assignment + discovery
**Blocks:** BH-EP2 (Cash Management widget)
**Owner:** Santosh (coordination) — pending Zane/Amber product review
**Acceptance criteria:** APO confirmed for My Cash Manager (Enterprise Cash Management
System UI – 89650); 5-question discovery (ADR-007 §2) answered with Ravi Patel as data
owner.
**Status:** Not Started

### BH-DISC-03 — Client Central discovery (Today's Meetings + Banker Availability)
**Blocks:** BH-EP4 (Today's Meetings), portion of BH-EP5 (Performance at a Glance —
Banker Availability)
**Owner:** Nick Brown / Sandy Clarke (Client Central) — coordination: Santosh
**Acceptance criteria:** 5-question discovery answered for Client Central (102242)
covering Branch Calendar, Banker Calendar View, and Banker Availability.
**Status:** Not Started

### BH-DISC-04 — Tech Readiness stub sources KT + discovery
**Blocks:** BH-EP3 (Tech Readiness) — currently stub-only, do not build against
**Owner:** Hila Morgan / Ravi Kota (KT) — coordination: Santosh
**Acceptance criteria:** KT sessions complete for Teller Express (Splunk), Connection
Status (Cortex), ATM Status (Kafka/ARM team); 5-question discovery answered for each;
Tablets source app name confirmed (Splunk/Polar vs. alternate) with Kola Oladejo.
**Status:** Blocked — awaiting KT sessions (existing constraint, unchanged)

### BH-DISC-05 — Performance Homepage / KYPB freshness & lineage investigation
**Blocks:** BH-EP5 (Performance at a Glance: OSAT, Convenience, One Chase Sales)
**Owner:** Chad Laman (APO, spelling to confirm) / Yanina Jonesan (data owner) —
coordination: Santosh
**Acceptance criteria:** Full lineage traced (source system → ingestion →
Snowflake/data lake → transformation → KYPB dataset → consuming app); actual freshness
vs. documented 2-month lag reconciled; decision recorded on whether Branch Health
consumes existing KYPB dataset or a fresher upstream source. See ADR-007 §4.
**Status:** Not Started

### BH-DISC-06 — Branch Location Data Store discovery
**Blocks:** BH-EP6 (Nearby Branches)
**Owner:** David Jordan — coordination: Santosh
**Acceptance criteria:** 5-question discovery answered for Branch Location Data Store
(109913).
**Status:** Not Started

### BH-DISC-07 — Environment/infrastructure decision record location
**Blocks:** Stream B (Architecture/Infrastructure) kickoff
**Owner:** Solution Architect / Santosh
**Acceptance criteria:** Decision made and recorded on where environment/infra
definitions live (repo/IaC, Confluence, or hybrid) so Stream B can begin in parallel
with Stream C.
**Status:** Not Started

### BH-DISC-08 — Consolidated dependency map for Solution Architect
**Blocks:** Target topology sign-off
**Owner:** Santosh
**Acceptance criteria:** `data-dependency-matrix.md` fully populated (no remaining `?`)
across Source → Hosting → API/Interface → Gateway → Access → Data Freshness → Owner for
all 16 components. Handed to Solution Architect.
**Status:** Not Started — depends on BH-DISC-01 through BH-DISC-06
```
