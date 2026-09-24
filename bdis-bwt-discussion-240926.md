Absolutely. Based on the **entire BDIS-BWT discussion you shared**, including the questionnaire screenshots, I would give Copilot a structured project-context document rather than a meeting summary. That will help it understand the **scope, architecture, source ownership, consumption pattern, MVP, personas, retention, hierarchy, and open questions** before generating code.

You can paste the following directly into Copilot/project documentation.

---

# BDIS → BWT / Branch Health Integration: Complete Discussion & Technical Context

## 1. Purpose

Branch Health/BWT is building a new application that will provide branch/device health information across multiple personas and organizational levels.

BDIS is one of the trusted data sources for device information and telemetry.

The immediate objective is **not to implement every possible Branch Health use case**. The immediate objective is to prove that BWT/Branch Health can successfully consume BDIS data from the Data Lake in UAT, query the required information, and use it to power at least one end-to-end dashboard use case.

Once that integration path is proven, additional device types, personas, hierarchy levels, and use cases can be added incrementally.

---

# 2. Current BDIS scope

BDIS stands for a device integration, health, maintenance, and administration platform.

BDIS is **not the transactional system**.

Teller Express is responsible for transactional activities and transaction-related functionality.

BDIS focuses on device-related capabilities such as:

* Device inventory
* Device metadata
* Device health
* Device status
* Device telemetry
* Device integration
* Device administration
* Remote reboot
* Log retrieval
* Firmware upgrades
* Device management campaigns
* Device action/orders

### Current device coverage

The currently confirmed BDIS device type is:

**TCR - Teller Cash Recycler**

TCRs are already onboarded and in production.

BDIS captures broad TCR information including available:

* Device information
* Device metadata
* Device health
* Device status
* Telemetry
* Administrative information

### Future BDIS device coverage

Potential future devices include:

* Pinpads
* Check scanners
* Receipt printers
* Advanced/new TCRs
* Other physical devices associated with teller environments

Advanced TCRs may provide more frequent telemetry/heartbeat information, potentially approximately every 15 minutes, to indicate that the device is alive.

### Devices currently outside BDIS scope

The following should **not** be assumed to be BDIS-owned:

* ATM
* Network devices
* Teller workstations
* Other workstation lifecycle information

ATM remains within the ATM ecosystem/team.

Network remains within the network ecosystem.

Workstation ownership and lifecycle are associated with a separate area/Eagle replacement discussions.

Therefore, Branch Health must not assume that BDIS is the universal source for every physical device in a branch.

---

# 3. Important source-of-truth principle

"Device" is not a single universal source.

Different device categories may have different authoritative systems.

Current conceptual source landscape:

```text
TCR / BDIS-covered devices → BDIS
ATM                       → ATM ecosystem / ARM
Network                   → Cortex / network ecosystem
Tablets                   → Pollr
Channel availability      → Channel Management
Workstations              → TBD / separate workstation ecosystem
Other                     → TBD
```

These sources will need to be integrated into Branch Health over time.

BDIS is therefore one component of the broader Branch Health trusted-data architecture.

---

# 4. BDIS Data Lake availability

The BDIS team confirmed that the relevant data is already available in the **UAT Data Lake**.

Current state:

* Required tables/fields that have been finalized are available.
* Test data exists in UAT.
* The Data Lake is considered the trusted zone for this data.
* BWT can begin querying the UAT data.
* BDIS team is available to help with query construction and table relationships.

The BDIS team explicitly encouraged BWT to start with a high-level use case, write/run the required query, and prove that the data can be consumed successfully.

---

# 5. BDIS data publication architecture

BDIS has multiple potential consumption patterns.

### Kafka

BDIS has a Kafka producer.

A downstream application can potentially consume BDIS events through a Kafka consumer.

### Data Lake / Iceberg

BDIS also publishes/persists data into Data Lake Iceberg tables.

The BDIS team indicated that the data is available in near-real-time and that events become available in the Data Lake soon after the device state/data is generated.

### Snowflake

A Snowflake path is being developed.

However, Snowflake should **not be assumed to be the primary consumption mechanism for the operational Branch Health dashboard**.

The current direction for BWT/Branch Health is to investigate Data Lake/Iceberg consumption first.

---

# 6. Avro schemas and Data Lake tables

BDIS publishes **eight different Avro structures/schemas**.

Those eight Avro structures are persisted/transformed into approximately **eleven Data Lake tables**.

Therefore:

```text
BDIS
 |
 +-- 8 Avro schemas/events
 |
 +-- Data Lake / Iceberg
       |
       +-- 11 tables
```

There is not necessarily a 1:1 mapping between Avro structures and Data Lake tables.

### Required technical artifacts

BDIS should provide:

1. Eight Avro schemas.
2. Eleven Data Lake table structures/schema.
3. Avro-to-table mapping.
4. Table relationships.
5. Keys/business keys.
6. Recommended joins.
7. Recommended filtering/query patterns.
8. Timestamp semantics.
9. Current-state vs historical-event semantics.

The BDIS team indicated that if BWT consumes the Data Lake tables directly, the table structure and relationships are what matter most.

If BWT consumes the event stream, the Avro schemas become the primary contract.

Therefore the first architectural decision is:

> **For Branch Health, is the supported consumption contract Data Lake/Iceberg queries or Kafka/Avro events?**

The current preferred investigation is **Data Lake/Iceberg consumption** because Branch Health does not want to unnecessarily duplicate and permanently store the BDIS dataset.

---

# 7. Branch Health storage philosophy

The Branch Health team explicitly does **not** want to replicate the entire BDIS dataset unless there is a concrete requirement.

The preferred model is:

```text
BDIS
 ↓
Data Lake / Iceberg
 ↓
Branch Health queries required information
 ↓
Aggregate/filter
 ↓
BWT dashboard
```

Local Branch Health storage should only be introduced when there is a clear business or technical justification.

Possible reasons could include:

* Dashboard performance
* Short-lived operational snapshots
* Caching/reference data
* Required application-specific state
* Query optimization

But permanent replication of the full BDIS dataset is **not the current intention**.

The BDIS team also indicated that Kafka could be considered if BWT later determines that persistent/event-driven consumption is necessary.

---

# 8. Inventory vs health data

A major architectural distinction was established between **inventory/reference data** and **health/telemetry data**.

## Inventory/reference data

Inventory information changes relatively infrequently.

Examples:

* Device ID
* Device type
* Branch association
* Model
* Firmware
* Device metadata
* Device lifecycle information

BWT should avoid repeatedly downloading the entire device inventory.

Preferred conceptual pattern:

```text
Initial inventory
      ↓
Store/reference locally if required
      ↓
Identify changes
      ↓
Add new devices
Update changed devices
Remove/deactivate retired devices
```

The same principle was discussed using BLDS branch data.

For example, BWT should not repeatedly retrieve all 5,140 branches merely to display a branch hierarchy.

Instead, it should identify:

* New branches
* Closed branches
* Changed branches

---

# 9. Health/telemetry data

Health is different from inventory.

Health changes continuously.

BDIS publishes information such as:

* Device status
* Device health
* Heartbeat
* Telemetry
* Last activity/state

The Branch Health dashboard is primarily interested in:

> **What does the world look like now?**

Useful operational windows include:

* Current state
* Approximately 15 minutes
* Approximately 30 minutes
* Approximately one hour

The Branch Health team does not intend to permanently retain detailed health information indefinitely.

---

# 10. Health retention

The current Branch Health direction is:

**Health information should not be retained for more than approximately 24 hours in the operational Branch Health context.**

The reason is that health is highly time-sensitive.

Yesterday's detailed device state is generally not useful for the operational dashboard.

The 24-hour period may be used to support same-day replay/briefing scenarios such as:

> "Show me what the dashboard looked like one hour ago."

However, even that historical view may not require detailed device-level history.

An alternative is to retain only an aggregated snapshot:

```text
10:00 AM

Total devices: 5,000
Healthy: 4,850
Amber: 100
Red: 50
```

rather than retaining all 5,000 device records.

The exact historical snapshot requirement remains a design decision.

### Important distinction

The 24-hour retention applies to the **Branch Health operational use case**.

It does not mean that the BDIS Data Lake itself only retains 24 hours of data.

The Data Lake remains the trusted source and may have its own retention policy.

---

# 11. Long-term reporting

Long-term reporting is a separate use case.

Potential use cases include:

* Daily reporting
* Weekly reporting
* Monthly reporting
* Historical analytics
* Static reporting

These may use an analytics platform/Snowflake or another appropriate analytical store.

The operational BWT health dashboard should not be designed as the long-term historical reporting warehouse.

Therefore:

```text
Operational health
        ↓
BWT / Branch Health
        ↓
Current + short history

Historical analytics
        ↓
Analytics platform
        ↓
Daily / weekly / monthly
```

---

# 12. Branch ID / DND as the central association key

BDIS publishes device information **along with the Branch ID/DND association**.

This is an important design decision.

For example:

```text
DND 123
 |
 +-- TCR 001
 +-- TCR 002
 +-- Pinpad 001
 +-- Scanner 001
```

Branch Health can therefore query:

> "Give me all devices associated with DND 123."

This makes the Branch/DND identifier the central association point for Branch Health.

---

# 13. BLDS remains the organizational hierarchy source

The BDIS data does not currently contain the full organizational hierarchy.

BLDS remains responsible for branch organizational/reference information.

Conceptually:

```text
BLDS
 |
 +-- Division
      |
      +-- Market
           |
           +-- Region
                |
                +-- District
                     |
                     +-- DND / Branch
```

BDIS:

```text
BDIS
 |
 +-- DND / Branch ID
      |
      +-- Devices
           |
           +-- Health
           +-- Status
           +-- Telemetry
```

Branch Health combines these.

---

# 14. Other systems may use different identifiers

Not all source systems necessarily use DND/Branch ID.

For example, the network domain may use a **Building ID**.

Therefore Branch Health may need an identifier mapping:

```text
DND 123
   |
   +-- Building ID 456
          |
          +-- Network devices
```

The exact mapping must be identified and validated.

Do not assume that DND and Building ID are interchangeable.

---

# 15. Organizational hierarchy filtering

The Branch Health dashboard needs to support different levels:

```text
Fleet
 ↓
Division
 ↓
Market
 ↓
Region
 ↓
District
 ↓
Branch / DND
 ↓
Device Group
 ↓
Device
```

A potential performance concern was identified.

For example, a division could contain 1,000+ DNDs.

It may be inefficient to do:

```text
Get 1,000 DNDs
 ↓
Send 1,000 individual DND filters
 ↓
Get device data
```

Instead, the team should investigate whether Data Lake queries can efficiently filter at higher organizational levels.

Potential approaches:

### Option A

Join BLDS hierarchy with BDIS device data in the query.

### Option B

Provide a Data Lake view that exposes hierarchy + device information.

### Option C

Maintain a small hierarchy/reference mapping in BWT.

### Option D

Expose organizational metadata such as Market/Region/District/Division alongside the branch association.

This is an **open design/performance question** and should not be prematurely implemented as a large metadata framework.

---

# 16. Device grouping

Branch Health should support logical device groups.

Example:

```text
Teller devices
 ├── TCR
 ├── Pinpad
 ├── Check scanner
 └── Printer

ATM
 └── ATM devices

Network
 └── Network devices

Workstation
 └── Workstations
```

At branch level, users may want:

```text
TCRs
Total: 2
Up: 2
Down: 0
```

rather than immediately seeing every raw telemetry field.

Example:

```text
Branch DND 123

TCRs
2 total
2 up
0 down

Pinpads
6 total
5 up
1 down

Scanners
4 total
4 up
0 down
```

The exact device groups will evolve as new devices are onboarded.

MVP1 should not over-engineer a universal metadata-driven device taxonomy.

Start with currently confirmed device types and generalize when additional complexity actually requires it.

---

# 17. Branch Health personas

The same underlying data will support different personas.

## Executive view

The executive view is fleet-wide.

The executive wants an "elevator pitch" of branch/device health.

Typical information:

* Total population
* Healthy/up
* Unhealthy/down
* Percentage healthy
* Red/Amber/Green
* High-level trends
* Ability to drill into an area

Example:

```text
Fleet

TCRs       98% Green
Pinpads    97% Green
ATMs       96% Green
Network    98% Green
```

Exact status calculations are still being defined.

---

## Regional manager

The regional manager primarily wants their area.

Example:

```text
My Region
 ↓
Districts
 ↓
Branches
```

They may have access to broader information, but their default experience is focused on their region.

---

## Branch manager

The branch manager is focused on one branch.

Example:

```text
DND 123

TCRs
2 total
2 up
0 down

ATMs
4 total
3 up
1 down
```

The key questions are:

* What devices do I have?
* How many?
* What's up?
* What's down?

---

## Support / technology persona

Support users understand network/device topology and need more technical detail.

They may need:

* Exact device
* Status
* Last heartbeat
* Duration
* Connectivity details
* Device model
* Firmware
* Diagnostic information
* Relevant telemetry

Therefore the same underlying health state may be represented differently.

Example:

### Executive

```text
Network
RED
```

### Support

```text
Network
RED

Primary connectivity: DOWN
Backup connectivity: ACTIVE
Last heartbeat: ...
Duration: ...
Device: ...
```

---

# 18. Common data model, different presentation

The application should avoid creating separate data stores for each persona.

Preferred conceptual architecture:

```text
Trusted data
      ↓
Common Branch Health model
      ↓
Health aggregation
      ↓
+-----------+-----------+-----------+
|           |           |           |
Executive   Manager     Branch    Support
Summary     View        View      Technical
```

The data is common.

The presentation/detail level differs.

---

# 19. Dashboard query model

The Branch Health backend should think in terms of business capabilities rather than physical BDIS tables.

Avoid tightly coupling the UI to individual BDIS tables.

Conceptually:

```text
GET /fleet-health
GET /market-health
GET /region-health
GET /district-health
GET /branch-health/{branchId}
GET /device-health/{deviceId}
```

The backend determines the appropriate Data Lake queries, joins and filters.

The UI should not know that the underlying BDIS implementation happens to have eleven tables.

---

# 20. Example business query hierarchy

### Fleet

> Give me total devices and current health across the entire fleet.

### Market

> Give me current device health for the selected market.

### Region

> Give me current device health for the selected region.

### District

> Give me current device health for the selected district.

### Branch

> Give me all relevant devices for this branch and their current health.

### Device

> Give me detailed information for this specific device.

These should eventually be mapped to concrete Data Lake queries.

---

# 21. First MVP/UAT use case

The BDIS team explicitly stated that BWT does **not need to implement every use case initially**.

The objective is to prove one complete use case.

Recommended first use case:

> **Given a known DND/Branch ID, retrieve all BDIS-supported devices for that branch from the UAT Data Lake and display their current status/health in Branch Health.**

Example:

```text
DND 123

TCR
Total: 2
Up: 2
Down: 0

Devices:

TCR-001  UP
TCR-002  UP
```

This proves:

```text
Data Lake
 ↓
Authentication/access
 ↓
Query
 ↓
Filtering
 ↓
Joining
 ↓
Spring Boot
 ↓
Branch Health API
 ↓
UI
```

Once this works, expand progressively:

```text
One branch
 ↓
Multiple branches
 ↓
District
 ↓
Region
 ↓
Market
 ↓
Division
 ↓
Fleet
```

---

# 22. Required BDIS query support

The BDIS team offered to help BWT construct the queries.

For each use case, BDIS can help identify:

* Tables
* Joins
* Keys
* Filters
* Latest-record logic
* Device status logic
* Heartbeat logic
* Aggregations

The Branch Health team should provide the **business scenario**, and BDIS/data engineering can help translate that into the correct Data Lake query.

---

# 23. Critical health query questions

Before implementing production logic, confirm:

### Current-state question

Does the Data Lake contain:

* A current-state table/view?

or

* Historical device events where BWT must derive the latest state?

### Status question

What determines device health?

Is it:

* Explicit status?
* Heartbeat?
* Last-seen timestamp?
* Combination of status + heartbeat?

### Stale device question

How should BWT distinguish:

```text
Device is OFFLINE
```

from:

```text
Device has stopped reporting
```

### Freshness question

What is the expected end-to-end latency:

```text
Device change
 ↓
BDIS
 ↓
Kafka
 ↓
Data Lake
 ↓
BWT query
 ↓
Dashboard
```

---

# 24. Data freshness

BDIS indicated that the data is available in near-real-time.

Heartbeats and device information can be updated continuously.

The exact SLA/freshness should still be documented.

BWT should establish:

* Event generation time
* Data Lake ingestion time
* Table availability time
* Last heartbeat
* Expected freshness
* Maximum acceptable dashboard staleness

---

# 25. Query performance

Direct Data Lake consumption is currently preferred over unnecessary local replication.

However, performance must be validated.

Important queries include:

* Fleet aggregation
* Division aggregation
* Market aggregation
* Region aggregation
* District aggregation
* Branch aggregation
* Device detail

For large populations, determine whether the Data Lake supports:

* Appropriate partitioning
* Efficient filtering
* Recommended joins
* Materialized views
* Pre-aggregations
* Current-state tables
* Other query optimizations

The architecture should be based on measured performance rather than assuming that every dashboard request should scan the complete device history.

---

# 26. BDIS data contract

The integration should establish a formal technical contract covering:

### Schema

* Avro schema
* Data Lake tables
* Columns
* Data types
* Keys

### Semantics

* Device status
* Device health
* Heartbeat
* Last seen
* Offline
* Not reporting

### Relationships

* Device → Branch/DND
* Device → Device Type
* Branch/DND → BLDS hierarchy

### Freshness

* Update frequency
* Expected latency

### Evolution

* Schema changes
* New device types
* New telemetry fields
* Backward compatibility

---

# 27. New device onboarding

The architecture must remain extensible.

When BDIS introduces a new device:

```text
New device
 ↓
BDIS onboarding
 ↓
Avro/data contract
 ↓
Data Lake
 ↓
BWT informed
 ↓
Evaluate attributes/health
 ↓
Determine Branch Health use case
 ↓
Enhance dashboard/API if required
```

Example:

```text
Today:
TCR

Future:
TCR
Pinpad
Check Scanner
Printer
Advanced TCR
Other devices
```

BWT should not assume today's device universe is permanent.

---

# 28. Ongoing BDIS-BWT collaboration

The teams proposed an approximately **monthly touchpoint**, with flexibility to skip a month if there are no meaningful changes.

Purpose:

### BDIS shares

* New devices
* New telemetry
* New fields
* New tables
* Schema changes
* New capabilities
* Data freshness changes

### BWT shares

* New use cases
* New personas
* Dashboard requirements
* Data gaps
* Query/performance issues
* New aggregation requirements

This creates an ongoing feedback loop between the source-data team and the consuming product.

---

# 29. Answers to BDIS-BWT questionnaire

## A. Scope

**Question: What device types does BDIS currently cover?**

Answer:

Current confirmed coverage is TCRs. Future planned coverage includes pinpads, check scanners, receipt printers, advanced TCRs and potentially other physical teller devices.

ATM, network and teller workstation ownership should not be assumed to be BDIS.

---

## B. Is BDIS authoritative?

For BDIS-covered device information and health/administration data, BDIS is the trusted source being evaluated for Branch Health consumption.

However, BDIS is not the authoritative source for every branch technology/device.

Each device category must be mapped to its owning system.

---

## C. Where is the data?

The data is available in the UAT Data Lake using Iceberg tables.

UAT contains finalized tables/fields and test data.

---

## D. How is the data published?

BDIS publishes eight Avro structures/events.

Those are persisted/transformed into approximately eleven Data Lake tables.

BDIS also has a Kafka producer.

A Snowflake path is being developed.

---

## E. What is the preferred Branch Health consumption mechanism?

Current direction:

**Investigate Data Lake/Iceberg querying first.**

BWT does not currently want to permanently replicate the entire BDIS dataset.

Kafka remains an alternative if a persistent/event-driven consumption requirement emerges.

---

## F. What schemas are available?

Avro schemas are available and should be shared with BWT.

Data Lake table definitions should also be provided.

The Avro-to-table mapping is required to understand how eight Avros become eleven tables.

---

## G. How does BWT access the data?

Exact production access mechanism still needs to be confirmed, including:

* Authentication
* IAM/service role
* Application identity
* Catalog permissions
* Query engine
* JDBC/ODBC/API mechanism
* Network connectivity
* Data Lake registration requirements

This is an open technical item.

---

## H. Does BWT need to store the data?

Current answer:

**Not by default.**

The preferred model is to query the trusted Data Lake and use the results for Branch Health.

Short-term storage/caching may be introduced only where justified.

---

## I. How long does BWT need health data?

Current operational requirement:

**Approximately 24 hours maximum.**

The primary use is current/recent health.

A historical "one hour ago" view may be implemented using either:

* Short-term detailed data, or
* Aggregated snapshots

This remains a design decision.

---

## J. Does BWT need long-term health history?

Not for the operational dashboard.

Daily/weekly/monthly historical reporting is a separate analytics use case.

---

## K. How is branch association established?

BDIS publishes device information with Branch ID/DND.

Branch ID/DND is the central association key for Branch Health.

---

## L. Where does organizational hierarchy come from?

BLDS.

BDIS does not currently own the complete organizational hierarchy.

BLDS provides the mapping:

```text
Division
 → Market
 → Region
 → District
 → DND/Branch
```

---

## M. How will other systems be associated?

Different systems may use different identifiers.

Example:

```text
BDIS → DND
Network → Building ID
```

A mapping between identifiers will be required.

---

## N. How will fleet/market/region queries work?

This is an open optimization/design area.

The team needs to determine whether to:

* Join BLDS hierarchy in the Data Lake query
* Use a Data Lake view
* Add hierarchy metadata
* Maintain a lightweight BWT reference mapping

The objective is to avoid sending hundreds/thousands of individual DND filters for large organizational queries.

---

## O. What is the first UAT test?

Recommended:

> Retrieve all BDIS-supported devices and their current health for one known DND/branch.

This validates the entire consumption path.

---

# 30. Target conceptual architecture

```text
                         ┌─────────────────┐
                         │      BLDS       │
                         │ Branch hierarchy│
                         └────────┬────────┘
                                  │
                                  │ Branch/DND
                                  │
┌──────────────┐                  │
│    BDIS      │                  │
│ Device data  │                  │
└──────┬───────┘                  │
       │                          │
       │ Avro / events            │
       ▼                          │
┌──────────────────────────────────────────┐
│          Data Lake / Iceberg             │
│                                          │
│ BDIS device + health + telemetry data    │
└────────────────────┬─────────────────────┘
                     │
                     │ Query/filter/aggregate
                     ▼
          ┌──────────────────────┐
          │ Branch Health / BWT  │
          │ Backend              │
          └──────────┬───────────┘
                     │
              Common health model
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
   Executive     Regional/      Branch
     View         Market         View
       │             │             │
       └─────────────┼─────────────┘
                     ▼
              Support / Tech
                 Details
```

---

# 31. Target data flow

```text
Device
  ↓
BDIS
  ↓
Avro
  ↓
Data Lake / Iceberg
  ↓
Branch Health query
  ↓
Filter by Branch/DND or hierarchy
  ↓
Aggregate device health
  ↓
BWT API
  ↓
Dashboard
```

---

# 32. Core design principles for Copilot

Copilot should follow these principles when implementing Branch Health:

1. **Do not assume BDIS owns every device type.**
2. **Do not hard-code only TCRs as the permanent device universe.**
3. **Use Branch/DND as the primary device-to-branch association key.**
4. **Use BLDS for organizational hierarchy.**
5. **Do not duplicate the complete BDIS dataset unnecessarily.**
6. **Prefer Data Lake/Iceberg consumption for the initial implementation.**
7. **Keep Kafka as a future/alternative event-driven integration option.**
8. **Separate inventory/reference data from transient health data.**
9. **Do not retain detailed health history beyond the operational requirement without a specific use case.**
10. **Design APIs around business use cases rather than physical BDIS tables.**
11. **Support Fleet → Division → Market → Region → District → Branch → Device Group → Device.**
12. **Support logical device groups.**
13. **Keep persona presentation separate from the underlying health model.**
14. **Optimize queries before introducing local persistence.**
15. **Do not build a large metadata framework unless future device/use-case complexity requires it.**
16. **Treat new device types as extensible additions to the model.**
17. **Do not assume DND and Building ID are the same.**
18. **Validate current-state versus historical-event semantics before implementing health calculations.**
19. **Validate heartbeat freshness and offline/not-reporting semantics.**
20. **First prove one end-to-end UAT use case before implementing the entire dashboard.**

---

# 33. Immediate implementation plan

### Phase 1: Data discovery

Obtain:

* 8 Avro schemas
* 11 table schemas
* Avro → table mapping
* Table relationships
* Keys
* Sample UAT data
* Access mechanism

### Phase 2: First UAT query

Implement:

```text
Input:
DND/Branch ID

Output:
Device type
Device ID
Current status
Health
Last heartbeat/last update
```

### Phase 3: First dashboard

Display:

```text
Branch
 ├── Total devices
 ├── Up
 ├── Down
 └── Device groups
```

### Phase 4: Validate performance

Test:

* One branch
* Multiple branches
* District
* Region
* Market
* Fleet

### Phase 5: Add persona views

Start with:

1. Executive
2. Branch

Then add:

3. Regional/field
4. Support/technology

### Phase 6: Add future devices

When BDIS introduces:

* Pinpads
* Scanners
* Printers
* Advanced TCRs
* Other devices

evaluate their available data and determine how they should appear in Branch Health.

---

# 34. Final MVP objective

The immediate MVP should prove:

> **BWT can securely access the BDIS trusted data in the UAT Data Lake, execute the required query, associate devices to a branch using DND/Branch ID, retrieve current device health/status, and display the result in Branch Health.**

Once that works, the "railroad" is established.

The remaining work becomes incremental:

```text
One branch
    ↓
More branches
    ↓
District
    ↓
Region
    ↓
Market
    ↓
Division
    ↓
Fleet
    ↓
More device types
    ↓
More personas
    ↓
More use cases
```

**Do not implement all of these upfront. Build the integration foundation first, prove one use case, then expand based on validated business requirements and available data.**
