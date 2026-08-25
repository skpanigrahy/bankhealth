# Branch Health — Data Dependency Discovery Matrix

**Status:** Discovery in progress — all Hosting / API / Gateway / Storage Model columns
are placeholders (`?` / TBD) by design. Do not build against a row until this ADR-007
process resolves its discovery questions.
**Source:** "Branch Health Observability – Branch Manager View" tracker (screenshots,
2026-08-24) + architecture discussion same date
**Related:** `ADR-007-data-integration-discovery-approach.md`

> Note: source screenshots show a minor discrepancy for row 11 (Tablets) between two
> revisions — "Splunk/Polar" vs. an app named that may read "Polly" in a later revision.
> Flagged as **CONFIRM** below; do not assume either until verified with Kola Oladejo.

## Legend
- **Cadence**: R = Real-time, D = Daily, W = Weekly, M = Monthly (✓ = confirmed cadence for that row)
- **Status**: Discovery / In Progress / On Track / Not Started (per existing tracker legend)
- **Bucket**: per ADR-007 §3 four-bucket classification (1–4), assigned provisionally — confirm during discovery

| # | Section | Component | App to View Data | App Product Owner (APO) | App Data Owner | Cadence | Hosting | API/Interface | Gateway | Storage Model | Bucket | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Required Actions | Control Exceptions | Branch Dashboard (Business Metric Dashboard – 84329) | Tony Roth | Kola Oladejo | R | ? | ? | ? | TBD | 2 | Discovery |
| 2 | Required Actions | Cash Management | My Cash Manager (Enterprise Cash Management System UI – 89650) | *pending Zane/Amber review* | Ravi Patel | D | ? | ? | ? | TBD | 2 | Discovery |
| 3 | Required Actions | Branch Dashboard (3 data points) | Branch Dashboard (Business Metric Dashboard – 84329) | Tony Roth | Kola Oladejo | R | ? | ? | ? | TBD | 2 | Discovery |
| 4 | Required Actions | CSC | Branch Dashboard (Business Metric Dashboard – 84329) | Tony Roth | Kola Oladejo | M | ? | ? | ? | TBD | 2 | Discovery |
| 5 | Required Actions | Banker Notifications | Banker Homepage (104294) | Tony Roth | Kola Oladejo | R | ? | ? | ? | TBD | 2 | Discovery |
| 6 | Today's Meetings | Branch Calendar | Client Central (102242) | Nick Brown | Sandy Clarke | R | ? | ? | ? | TBD | 1 | Discovery |
| 7 | Today's Meetings | Banker Calendar View | Client Central (102242) | Nick Brown | Sandy Clarke | R | ? | ? | ? | TBD | 1 | Discovery |
| 8 | Tech Readiness | Teller Express — Overall Status | Splunk | Matt Thiedt | Kola Oladejo | R | ? | ? | ? | TBD | 1 (stub-only — KT pending) | Discovery |
| 9 | Tech Readiness | Connection Status (Branch Network) | Cortex | Tim Freeman | Srinivas Kati | R | ? | ? | ? | TBD | 1 (stub-only — KT pending) | Discovery |
| 10 | Tech Readiness | ATM Status | ATM Reporting and Monitoring (110976) | Dan McGinnis | Nihari Paladugu | R | ? | ? | ? | TBD | 2 (stub-only — KT pending) | Discovery |
| 11 | Tech Readiness | Tablets | Splunk/Polar — **CONFIRM** (revision shows possible "Polly") | Joe Raquepaw | Kola Oladejo | R | ? | ? | ? | TBD | 2 | Discovery |
| 12 | Performance at a Glance | OSAT (4 data points) | Performance Homepage | Chad Laman *(confirm spelling)* | Yanina Jonesan | M | ? | ? | ? | TBD | 4 (KYPB lineage question — see ADR-007 §4) | Discovery |
| 13 | Performance at a Glance | Convenience (4 data points) | Performance Homepage | Chad Laman *(confirm)* | Yanina Jonesan | D | ? | ? | ? | TBD | 4 | Discovery |
| 14 | Performance at a Glance | One Chase Sales (4 data points) | Performance Homepage | Chad Laman *(confirm)* | Yanina Jonesan | D | ? | ? | ? | TBD | 4 | Discovery |
| 15 | Performance at a Glance | Banker Availability (2 data points) | Client Central (102242) | Nick Brown | Sandy Clarke | R | ? | ? | ? | TBD | 1 | Discovery |
| 16 | Nearby Branches | Branches/financial centers/ATMs within X miles | Branch Location Data Store (109913) | David Jordan | Kola Oladejo | R | ? | ? | ? | TBD | 2 | Discovery |

## Data point detail (from tracker footnotes)
- **OSAT data points:** OSAT Score, Greeted, Called by Name, Thanked
- **Convenience data points:** Wealth Plan, Credit Journey, Discover Needs, AAO
- **One Chase Sales data points:** Credit Card Open, First Time Investors, Banker Productivity, Portfolio Contact
- **Banker Availability data points:** Weekly and Percent Change, Monthly and Percent Change
- Refresh note from source tracker: data refreshes every 15 minutes for completed items; new items are received during the marked cadence.

## Evidence supporting the KYPB freshness question (ADR-007 §4)
OSAT report reviewed 2026-08-24 shows "Data as of AUG 20" with column header **Jul 2026**
at branch/market level (WC Chicago Far South Market) — i.e., ~3-4 week lag observed, not
the ~2-month lag documented elsewhere for KYPB. This needs lineage confirmation before
any integration decision is made for rows 12–14.

## Next actions per row
- Rows 1, 3, 4, 5 (Tony Roth as APO): confirm whether an API exists at all — flagged
  Bucket-3 candidate in the source discussion pending official confirmation.
- Row 2: awaiting Zane/Amber product-owner review before APO can be assigned.
- Rows 8–10: do not build — stub-only per existing constraint, blocked on KT with Hila
  Morgan / Ravi Kota.
- Row 11: confirm actual source app name (Splunk/Polar vs. possible alternate) with Kola
  Oladejo before documenting.
- Rows 12–14: resolve KYPB lineage/freshness question before committing to integration
  approach (ADR-007 §4).
