# 04 — New files and doc updates

## 1 of 4: api/examples/dashboard-response.json — full replace (this file has no "your" edits to preserve, it's a generated example)

FILE: api/examples/dashboard-response.json

Overwrite this file's entire contents (or, if your compliance version added extra fields for a
required masking/redaction pattern, tell me and I'll fold them in — this is just an example
payload, low risk either way) with:

```json
{
  "data": {
    "header": {
      "branch": {
        "branchId": "b3f1a2c0-1111-4a2b-9c3d-000000000001",
        "branchCode": "0421",
        "branchName": "Main Street",
        "market": "Mid-Atlantic",
        "region": "Northeast",
        "timezone": "America/New_York"
      },
      "greetingName": "Nick",
      "operationalStatus": "GREEN",
      "operationalStatusLabel": "Your branch is operational",
      "lastRefresh": "2026-08-09T17:45:00Z",
      "refreshInProgress": false,
      "availableBranches": []
    },
    "requiredActions": {
      "totalDueToday": 5,
      "actions": [
        { "actionId": "a1", "actionType": "CONTROL_EXCEPTION", "title": "Control exceptions", "subtitle": "2 items due today", "pendingCount": 2, "dueDescriptor": "DUE_TODAY", "dueInDays": null, "destinationUrl": "https://branch-dashboard.chase.com/action-items/controls-exception", "priority": "HIGH" },
        { "actionId": "a2", "actionType": "CASH_MANAGEMENT", "title": "Cash management", "subtitle": "0 items due", "pendingCount": 0, "dueDescriptor": "NONE_DUE", "dueInDays": null, "destinationUrl": "https://mycashmanager.chase.com", "priority": "LOW" },
        { "actionId": "a3", "actionType": "BRANCH_DASHBOARD", "title": "Branch Dashboard", "subtitle": "1 item due tomorrow", "pendingCount": 1, "dueDescriptor": "DUE_TOMORROW", "dueInDays": 1, "destinationUrl": "https://branch-dashboard.chase.com", "priority": "MEDIUM" },
        { "actionId": "a4", "actionType": "ICSC", "title": "ICSC", "subtitle": "4 items due in 2 days", "pendingCount": 4, "dueDescriptor": "DUE_IN_N_DAYS", "dueInDays": 2, "destinationUrl": "https://branch-dashboard.chase.com/icsc", "priority": "MEDIUM" },
        { "actionId": "a5", "actionType": "BANKER_NOTIFICATIONS", "title": "Banker notifications", "subtitle": "2 items to review", "pendingCount": 2, "dueDescriptor": "TO_REVIEW", "dueInDays": null, "destinationUrl": "https://banker-homepage.chase.com/notifications", "priority": "LOW" }
      ]
    },
    "meetings": {
      "date": "2026-08-09",
      "scheduledCount": 20,
      "prospectCount": 25,
      "bankers": [
        { "bankerId": "bk1", "bankerName": "Karen Jackson", "appointments": [] },
        { "bankerId": "bk2", "bankerName": "Nick Knapp", "appointments": [] },
        { "bankerId": "bk3", "bankerName": "Kristen Tucker", "appointments": [] },
        { "bankerId": "bk4", "bankerName": "Stan Musial", "appointments": [] },
        { "bankerId": "bk5", "bankerName": "Jay Baumgardner", "appointments": [] },
        { "bankerId": "bk6", "bankerName": "Dave Gilmore", "appointments": [] },
        { "bankerId": "bk7", "bankerName": "Frank Thomas", "appointments": [] }
      ]
    },
    "techReadiness": {
      "overallStatus": "AMBER",
      "tellerExpress": { "status": "STORE_MODE" },
      "connection": { "mode": "CELL_BACKUP" },
      "atm": { "operational": 3, "total": 4 },
      "tablets": { "online": 8, "total": 10 },
      "alerts": [
        { "component": "TELLER_EXPRESS", "severity": "CRITICAL", "title": "Teller Express is in store mode", "description": "Transactions will be processed when server is back online." },
        { "component": "CONNECTION", "severity": "WARNING", "title": "Connection is using cell backup", "description": "Internet may be slow." },
        { "component": "ATM", "severity": "WARNING", "title": "3 of 4 ATMs are operational", "description": "1 ATM may need attention." }
      ]
    },
    "performance": {
      "osat": { "metrics": [
        { "label": "Score", "value": "98%", "trendPercent": 1 },
        { "label": "Greeted", "value": "96%", "trendPercent": 1 },
        { "label": "Called by name", "value": "89%", "trendPercent": -2 },
        { "label": "Thanked", "value": "98%", "trendPercent": 0 }
      ]},
      "convenience": { "metrics": [
        { "label": "Wealth Plan", "value": "10", "trendPercent": 1 },
        { "label": "Credit Journey", "value": "17", "trendPercent": 1 },
        { "label": "Discover Needs", "value": "2", "trendPercent": -2 },
        { "label": "AAO", "value": "15", "trendPercent": -2 }
      ]},
      "oneChaseSales": { "metrics": [
        { "label": "Credit Card Open", "value": "10", "trendPercent": 3 },
        { "label": "First Time Investors", "value": "17", "trendPercent": 13 },
        { "label": "Banker Productivity", "value": "1,000", "trendPercent": -2 },
        { "label": "Portfolio Contact", "value": "100", "trendPercent": -22 }
      ]},
      "bankerAvailability": { "metrics": [
        { "label": "Weekly", "value": "97.8%", "trendPercent": -2 },
        { "label": "Monthly", "value": "98%", "trendPercent": 1 }
      ]}
    },
    "nearbyBranches": {
      "centerBranchId": "b3f1a2c0-1111-4a2b-9c3d-000000000001",
      "radiusMiles": 5,
      "locations": [
        { "locationId": "your-branch", "name": "Main Street", "locationType": "BRANCH_ATM", "accessType": null, "distanceMiles": 0, "latitude": 39.995, "longitude": -75.205, "isYourBranch": true, "status": "OPEN", "hoursLabel": "Open until 5 PM today", "address": "100 Main St, Riverdale, NJ 07457", "phoneNumber": "(973) 555-0100", "atmSummary": "4 of 4 ATMs open", "meetWithUsUrl": null },
        { "locationId": "n1", "name": "Mill Run", "locationType": "BRANCH_DRIVE_UP_ATM", "accessType": null, "distanceMiles": 1.10, "latitude": 39.99, "longitude": -75.2, "isYourBranch": false, "status": "OPEN", "hoursLabel": "Open until 5 PM today | ATM open 24 hours", "address": "142 Mill Run Rd, Riverdale, NJ 07457", "phoneNumber": "(973) 555-0142", "atmSummary": "5 of 6 ATMs open", "meetWithUsUrl": "https://kyb.chase.com/schedule/mill-run" },
        { "locationId": "n2", "name": "Lincoln Village", "locationType": "ATM_ONLY", "accessType": "DRIVE_UP", "distanceMiles": 3.16, "latitude": 40.01, "longitude": -75.18, "isYourBranch": false, "status": "TEMPORARILY_CLOSED", "hoursLabel": "Open 24 hours", "address": "8 Lincoln Village Plz, Riverdale, NJ 07457", "phoneNumber": null, "atmSummary": "Temporarily closed", "meetWithUsUrl": null },
        { "locationId": "n3", "name": "Livingston Ave", "locationType": "ATM_ONLY", "accessType": "DRIVE_UP", "distanceMiles": 3.16, "latitude": 39.98, "longitude": -75.22, "isYourBranch": false, "status": "OPEN", "hoursLabel": "Open 24 hours", "address": "220 Livingston Ave, Riverdale, NJ 07457", "phoneNumber": null, "atmSummary": null, "meetWithUsUrl": null }
      ]
    }
  },
  "metadata": {
    "requestId": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
    "generatedAt": "2026-08-09T17:45:03Z",
    "cacheAge": "PT3M"
  }
}
```

---

## 2 of 4: docs/14_Backlog/decision-log.md — brand new file

FILE: docs/14_Backlog/decision-log.md

If this file doesn't exist yet in your working copy, create it. If it already exists (maybe you
already ported the decision log yourself for compliance reasons), stop and show me a diff
instead of overwriting — don't lose any decisions you've already logged.

CREATE WITH CONTENT:
```markdown
# Decision log

Ported from the team's live Decision Log (owner: Eric Prock) as of the 7/31–8/5/26 working
sessions. **Open items are recorded as open, not resolved by assumption** — do not build
against an open item's most-likely-guessed answer; flag it back to product/design instead.

| Date | Title | Owner | Status | Type | Notes |
| --- | --- | --- | --- | --- | --- |
| 7/31/26 | CTR/MIPL Work Returns | Eric Prock | **Open** | Design | The only item within the Branch Dashboard required-action card that has a due date — must be completed in 2 days. Open question: create a separate action card for this, or leave it rolled into the general Branch Dashboard card? **Do not build a 6th required-action card until this is decided** (`RequiredAction.actionType` in `api/openapi.yaml` stays at 5 values). |
| 7/31/26 | Banker Notifications | Eric Prock | **Open** | Functional | How does the pending count decrement? Open question: does clicking into the notifications tab clear the counter for that person, treating everything as read? Not implemented — `BANKER_NOTIFICATIONS.pendingCount` in the mock data is static. |
| 8/4/26 | Branch Locator — Number of Branches | Eric Prock | **Closed** (8/5/26) | Functional | chase.com/locator shows the 10 closest branches to a zip code; the UI mockup shows 5 closest to the branch in session. **Decision: show closest 5.** Already reflected in `api/openapi.yaml` (`NearbyBranchesResponse`) and `database/schema/schema.sql`. |
| 8/4/26 | Branch Locator — Filter Options | Eric Prock | **Open** | Functional | Should Nearby Branches offer a filter like chase.com/locator? Candidate options under discussion: Location Type, Branch Services, ATM Access. **Not built** — `frontend/src/widgets/NearbyBranches` has no filter UI yet; don't add one speculatively. |
| 8/5/26 | Branch Locator — Detail overlay UI design | Eric Prock | **Open** | Design | Need a design for a detail overlay showing additional info about a selected branch (address, phone, etc. beyond what fits on the list card). **Stand-in in place today:** `frontend/src/widgets/NearbyBranches` shows address/phone inline on the selected card rather than in an overlay — replace once the overlay design lands. |

## How to use this file

- When a decision closes, move its row's Status to **Closed (date)** and add a one-line pointer
  to where the resolution landed in code (ADR, schema change, or component).
- When a new open question surfaces in a working session, add it here in the same session —
  don't let it live only in a meeting transcript or chat history.
- `api/openapi.yaml` and code comments cross-reference specific rows here (e.g.
  `RequiredAction`'s description references the CTR/MIPL row) — keep titles stable so those
  references don't go stale.
```

---

## 3 of 4: docs/14_Backlog/README.md — one link added at the top

FILE: docs/14_Backlog/README.md

FIND:
```markdown
# Backlog — explicitly out of MVP1

Captured so nobody re-derives these debates mid-sprint. Pull into a milestone only via a new/updated ADR.
```

REPLACE WITH:
```markdown
# Backlog — explicitly out of MVP1

See also [`decision-log.md`](decision-log.md) for open questions from working sessions — those
are unresolved items being tracked, not yet a firm "out of MVP1" call either way.

Captured so nobody re-derives these debates mid-sprint. Pull into a milestone only via a new/updated ADR.
```

(Everything below this in the file — the bullet list — is unchanged, leave it exactly as-is.)

---

## 4 of 4: ARCHITECTURE.md — widget→source table gets an App Product Owner column

FILE: ARCHITECTURE.md

FIND (the whole "Widget → data source map" section, table through closing paragraph):
```markdown
## Widget → data source map

| Widget | Primary source app | Data owner (from discovery) | Cadence |
| --- | --- | --- | --- |
| Control Exceptions | Branch Dashboard (Business Metric Dashboard – 84329) | Kola Oladejo | Real-time (15 min refresh) |
| Cash Management | My Cash Manager (Enterprise Cash Mgmt System UI – 89850) | Ravi Patel | Daily |
| Branch Dashboard (3 data points) | Branch Dashboard (84329) | Kola Oladejo | Real-time (15 min refresh) |
| ICSC | Branch Dashboard (84329) | Kola Oladejo | Monthly |
| Banker Notifications | Banker Homepage (104294) | Kola Oladejo | Real-time (15 min refresh) |
| Today's Meetings / Calendar | Client Central (102242) | Sandy Clarke | Real-time (15 min refresh) |
| Teller Express status | Splunk | Hila Morgan / Mike Allen | Real-time (15 min refresh) |
| Connection status | Cortex | Srinivas Kati / Hila Morgan | Real-time (15 min refresh) |
| ATM status | ATM Reporting and Monitoring (110976) | Nihari Paladugu | Real-time (15 min refresh) |
| Tablets | Splunk | Joe Raquepaw | Real-time (15 min refresh) |
| OSAT / Convenience / One Chase Sales | Performance Homepage | — | Weekly / Monthly per metric |
| Banker Availability | Client Central (102242) | Sandy Clarke | Real-time (15 min refresh) |
| Nearby branches, financial centers, ATMs | Branch Location Data Store (109913) | Kola Oladejo | Real-time (15 min refresh) |

This mapping is normative for `integrations/*` and `database/schema/schema.sql`; if a document
elsewhere disagrees, this table and `api/openapi.yaml` win (see `docs/00_INDEX.md`).
```

REPLACE WITH:
```markdown
## Widget → data source map

| Widget | Primary source app | App Product Owner | Data owner | Cadence |
| --- | --- | --- | --- | --- |
| Control Exceptions | Branch Dashboard (Business Metric Dashboard – 84329) | Tony Roth | Kola Oladejo | Real-time (15 min refresh) |
| Cash Management | My Cash Manager (Enterprise Cash Mgmt System UI – 89850) — covers ATM, TCR, cash boxes, cash vaults | *TBD — ask Don Butler* | Ravi Patel | Daily |
| Branch Dashboard (3 data points) | Branch Dashboard (84329) | Tony Roth | Kola Oladejo | Real-time (15 min refresh) |
| ICSC | Branch Dashboard (84329), launched from it | Tony Roth | Kola Oladejo | Monthly |
| Banker Notifications | Banker Homepage (104294) | Tony Roth | Kola Oladejo | Real-time (15 min refresh) |
| Today's Meetings / Calendar | Client Central (102242) | Nick Brown | Sandy Clarke | Real-time (15 min refresh) |
| Teller Express status | Splunk | Matt Thiedt | Kola Oladejo | Real-time (15 min refresh) |
| Connection status | Cortex | Tim Freeman | Srinivas Kati | Real-time (15 min refresh) |
| ATM status | ATM Reporting and Monitoring (110976) | Dan McGinnis | Nihari Paladugu | Real-time (15 min refresh) |
| Tablets | Splunk/Polar | Joe Raquepaw | Kola Oladejo | Real-time (15 min refresh) |
| OSAT / Convenience / One Chase Sales | Performance Homepage | — | — | Weekly / Monthly per metric |
| Banker Availability | Client Central (102242) | Nick Brown | Sandy Clarke | Real-time (15 min refresh) |
| Nearby branches, financial centers, ATMs | Branch Location Data Store (109913) | David Jordan | Kola Oladejo | Real-time (15 min refresh) |

"App Product Owner" and "Data owner" are two distinct roles per the platform's ownership
tracking (PAT) — Product Owner owns the source app's roadmap/access; Data owner is who to ask
about the specific data contained. The Cash Management App Product Owner is unresolved as of
this table's last update (7/31/26) — see `docs/14_Backlog/decision-log.md`.

This mapping is normative for `integrations/*` and `database/schema/schema.sql`; if a document
elsewhere disagrees, this table and `api/openapi.yaml` win (see `docs/00_INDEX.md`).
```

---

## Optional (not required for anything to compile/run): reference screenshots

If you want the actual spec screenshots archived alongside the code (recommended, but skip if
your compliance rules restrict storing meeting screenshots in the repo — check with your team
first): create `docs/02_UX/engineering-spec-details/` and drop the relevant images in, with a
short `README.md` noting which code changes each one drove. This is documentation only, nothing
references these files programmatically.
