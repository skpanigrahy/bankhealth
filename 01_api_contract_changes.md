# 01 — api/openapi.yaml changes

## Change 1 of 2: RequiredAction schema — add dueInDays + due-date semantics note

FILE: api/openapi.yaml

FIND:
```yaml
    RequiredAction:
      type: object
      required: [actionId, actionType, title, pendingCount, destinationUrl, priority]
      properties:
        actionId: { type: string, format: uuid }
        actionType:
          type: string
          enum: [CONTROL_EXCEPTION, CASH_MANAGEMENT, BRANCH_DASHBOARD, ICSC, BANKER_NOTIFICATIONS]
        title: { type: string, example: "Control exceptions" }
        subtitle: { type: string, example: "2 items due today" }
        pendingCount: { type: integer, minimum: 0 }
        dueDescriptor:
          type: string
          enum: [DUE_TODAY, DUE_TOMORROW, DUE_IN_N_DAYS, NONE_DUE, TO_REVIEW]
        destinationUrl: { type: string, format: uri, description: "Deep link to the owning application (ADR-001)" }
        priority: { type: string, enum: [HIGH, MEDIUM, LOW] }
```

REPLACE WITH:
```yaml
    RequiredAction:
      type: object
      description: >
        Due-date semantics vary by actionType (see docs/14_Backlog/decision-log.md,
        7/31/26 "CTR/MIPL Work Returns"): CONTROL_EXCEPTION and CASH_MANAGEMENT have no
        underlying due-date field — pendingCount is always "due today" by definition, there is
        no future-dated bucket for these two. BRANCH_DASHBOARD and ICSC do carry a real due date
        (ICSC's countdown is to the last business day of the current month). Whether CTR/MIPL
        Work Returns needs its own separate action card (rather than rolling into
        BRANCH_DASHBOARD) is an open decision — do not build a 6th card until that's resolved.
      required: [actionId, actionType, title, pendingCount, destinationUrl, priority]
      properties:
        actionId: { type: string, format: uuid }
        actionType:
          type: string
          enum: [CONTROL_EXCEPTION, CASH_MANAGEMENT, BRANCH_DASHBOARD, ICSC, BANKER_NOTIFICATIONS]
        title: { type: string, example: "Control exceptions" }
        subtitle: { type: string, example: "2 items due today" }
        pendingCount: { type: integer, minimum: 0 }
        dueDescriptor:
          type: string
          enum: [DUE_TODAY, DUE_TOMORROW, DUE_IN_N_DAYS, NONE_DUE, TO_REVIEW]
        dueInDays:
          type: integer
          nullable: true
          description: >
            Only meaningful for ICSC — day countdown to the last business day of the current
            month. Null for actionTypes with no due-date concept (see description above).
        destinationUrl: { type: string, format: uri, description: "Deep link to the owning application (ADR-001)" }
        priority: { type: string, enum: [HIGH, MEDIUM, LOW] }
```

---

## Change 2 of 2: NearbyLocation schema — tri-state status, address, phone, isYourBranch, accessType

FILE: api/openapi.yaml

FIND:
```yaml
    NearbyLocation:
      type: object
      required: [locationId, name, locationType, distanceMiles, latitude, longitude, isOpen]
      properties:
        locationId: { type: string, format: uuid }
        name: { type: string, example: "Mill Run" }
        locationType: { type: string, enum: [BRANCH_WITH_ATM, ATM_DRIVE_UP, FINANCIAL_CENTER] }
        distanceMiles: { type: number, format: float }
        latitude: { type: number, format: double }
        longitude: { type: number, format: double }
        isOpen: { type: boolean }
        hoursLabel: { type: string, example: "Open until 5 PM today" }
        atmSummary: { type: string, example: "5 of 6 ATMs open" }
        meetWithUsUrl: { type: string, format: uri, nullable: true }
```

REPLACE WITH:
```yaml
    NearbyLocation:
      type: object
      description: >
        See docs/14_Backlog/decision-log.md — filter options (location type / services / ATM
        access) and the detail-overlay UI for a location are still open decisions, not built yet.
      required: [locationId, name, locationType, distanceMiles, latitude, longitude, status]
      properties:
        locationId: { type: string, format: uuid }
        name: { type: string, example: "Mill Run" }
        locationType:
          type: string
          enum: [BRANCH_DRIVE_UP_ATM, BRANCH_ATM, ATM_ONLY]
          description: >
            Display label per docs/04_Engineering: BRANCH_DRIVE_UP_ATM → "Chase branch |
            Drive-Up | ATM", BRANCH_ATM → "Chase branch | ATM", ATM_ONLY → "Chase ATM"
            (optionally suffixed "| Drive-up" via accessType for a standalone drive-up ATM).
        accessType:
          type: string
          nullable: true
          enum: [DRIVE_UP, WALK_UP]
          description: "Only set for ATM_ONLY locations; appends to the locationType label."
        distanceMiles: { type: number, format: float }
        latitude: { type: number, format: double }
        longitude: { type: number, format: double }
        isYourBranch:
          type: boolean
          default: false
          description: "True for the one pin representing the branch the banker is currently in."
        status:
          type: string
          enum: [OPEN, CLOSED_TODAY, TEMPORARILY_CLOSED]
          description: >
            Pin color follows this directly: OPEN/CLOSED_TODAY render blue (scheduled hours,
            just outside vs. inside them), TEMPORARILY_CLOSED renders red (reported closed via
            BLDS — Branch Location Data Store). isOpen (boolean) was the earlier, less precise
            shape; status replaces it.
        hoursLabel:
          type: string
          description: "'Open until <X> PM today' | 'Closed until <X> AM today', per status"
          example: "Open until 5 PM today"
        address: { type: string, nullable: true, example: "142 Main St, Riverdale, NJ 07457" }
        phoneNumber: { type: string, nullable: true, example: "(973) 555-0142" }
        atmSummary: { type: string, example: "5 of 6 ATMs open" }
        meetWithUsUrl: { type: string, format: uri, nullable: true }
```

---

## Verify after both changes

```bash
python3 -c "import yaml; yaml.safe_load(open('api/openapi.yaml')); print('OK')"
```
(or any OpenAPI linter you have — `npx @redocly/cli lint api/openapi.yaml` works if you have
npm/internet access, same as the CI workflow in `.github/workflows/ci.yml`)
