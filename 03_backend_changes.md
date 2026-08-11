# 03 — Backend changes (dashboard-service + actions-service)

## Change 1 of 6: dashboard-service DTOs — RequiredAction gets dueInDays

FILE: backend/dashboard-service/src/main/java/com/chase/branchhealth/dashboard/dto/DashboardDtos.java

FIND:
```java
    public record RequiredAction(
            String actionId,
            String actionType, // CONTROL_EXCEPTION | CASH_MANAGEMENT | BRANCH_DASHBOARD | ICSC | BANKER_NOTIFICATIONS
            String title,
            String subtitle,
            int pendingCount,
            String dueDescriptor, // DUE_TODAY | DUE_TOMORROW | DUE_IN_N_DAYS | NONE_DUE | TO_REVIEW
            String destinationUrl,
            String priority // HIGH | MEDIUM | LOW
    ) {
    }
```

REPLACE WITH:
```java
    public record RequiredAction(
            String actionId,
            String actionType, // CONTROL_EXCEPTION | CASH_MANAGEMENT | BRANCH_DASHBOARD | ICSC | BANKER_NOTIFICATIONS
            String title,
            String subtitle,
            int pendingCount,
            String dueDescriptor, // DUE_TODAY | DUE_TOMORROW | DUE_IN_N_DAYS | NONE_DUE | TO_REVIEW
            Integer dueInDays, // ICSC only — countdown to last business day of month; null otherwise
            String destinationUrl,
            String priority // HIGH | MEDIUM | LOW
    ) {
    }
```

---

## Change 2 of 6: dashboard-service DTOs — NearbyLocation reshaped

FILE: backend/dashboard-service/src/main/java/com/chase/branchhealth/dashboard/dto/DashboardDtos.java

FIND:
```java
    public record NearbyLocation(
            String locationId,
            String name,
            String locationType, // BRANCH_WITH_ATM | ATM_DRIVE_UP | FINANCIAL_CENTER
            double distanceMiles,
            double latitude,
            double longitude,
            boolean isOpen,
            String hoursLabel,
            String atmSummary,
            String meetWithUsUrl
    ) {
    }
```

REPLACE WITH:
```java
    public record NearbyLocation(
            String locationId,
            String name,
            String locationType, // BRANCH_DRIVE_UP_ATM | BRANCH_ATM | ATM_ONLY
            String accessType,   // DRIVE_UP | WALK_UP | null (ATM_ONLY only)
            double distanceMiles,
            double latitude,
            double longitude,
            boolean isYourBranch,
            String status,       // OPEN | CLOSED_TODAY | TEMPORARILY_CLOSED
            String hoursLabel,
            String address,
            String phoneNumber,
            String atmSummary,
            String meetWithUsUrl
    ) {
    }
```

**Note:** any other file that constructs a `new NearbyLocation(...)` (positional constructor,
since this is a Java record) needs its argument list updated to match the new field order/count.
In the unmodified repo, that's only `MockDashboardService.java` (change 3 below) — if your
compliance changes added another place that constructs this record, fix that call site too, in
the same order as the fields above.

---

## Change 3 of 6: dashboard-service mock data — requiredActions() method

FILE: backend/dashboard-service/src/main/java/com/chase/branchhealth/dashboard/service/MockDashboardService.java

FIND:
```java
    private RequiredActionsResponse requiredActions() {
        List<RequiredAction> actions = List.of(
                new RequiredAction("a1", "CONTROL_EXCEPTION", "Control exceptions", "2 items due today",
                        2, "DUE_TODAY", "https://branch-dashboard.chase.com/action-items/controls-exception", "HIGH"),
                new RequiredAction("a2", "CASH_MANAGEMENT", "Cash management", "0 items due",
                        0, "NONE_DUE", "https://mycashmanager.chase.com", "LOW"),
                new RequiredAction("a3", "BRANCH_DASHBOARD", "Branch Dashboard", "1 item due tomorrow",
                        1, "DUE_TOMORROW", "https://branch-dashboard.chase.com", "MEDIUM"),
                new RequiredAction("a4", "ICSC", "ICSC", "4 items due in 2 days",
                        4, "DUE_IN_N_DAYS", "https://branch-dashboard.chase.com/icsc", "MEDIUM"),
                new RequiredAction("a5", "BANKER_NOTIFICATIONS", "Banker notifications", "2 items to review",
                        2, "TO_REVIEW", "https://banker-homepage.chase.com/notifications", "LOW")
        );
        return new RequiredActionsResponse(5, actions);
    }
```

REPLACE WITH:
```java
    private RequiredActionsResponse requiredActions() {
        List<RequiredAction> actions = List.of(
                new RequiredAction("a1", "CONTROL_EXCEPTION", "Control exceptions", "2 items due today",
                        2, "DUE_TODAY", null, "https://branch-dashboard.chase.com/action-items/controls-exception", "HIGH"),
                new RequiredAction("a2", "CASH_MANAGEMENT", "Cash management", "0 items due",
                        0, "NONE_DUE", null, "https://mycashmanager.chase.com", "LOW"),
                new RequiredAction("a3", "BRANCH_DASHBOARD", "Branch Dashboard", "1 item due tomorrow",
                        1, "DUE_TOMORROW", 1, "https://branch-dashboard.chase.com", "MEDIUM"),
                new RequiredAction("a4", "ICSC", "ICSC", "4 items due in 2 days",
                        4, "DUE_IN_N_DAYS", 2, "https://branch-dashboard.chase.com/icsc", "MEDIUM"),
                new RequiredAction("a5", "BANKER_NOTIFICATIONS", "Banker notifications", "2 items to review",
                        2, "TO_REVIEW", null, "https://banker-homepage.chase.com/notifications", "LOW")
        );
        return new RequiredActionsResponse(5, actions);
    }
```

---

## Change 4 of 6: dashboard-service mock data — nearbyBranches() method

FILE: backend/dashboard-service/src/main/java/com/chase/branchhealth/dashboard/service/MockDashboardService.java

FIND:
```java
    private NearbyBranchesResponse nearbyBranches() {
        List<NearbyLocation> locations = List.of(
                new NearbyLocation("n1", "Mill Run", "BRANCH_WITH_ATM", 1.10, 39.99, -75.20,
                        true, "Open until 5 PM today | ATM open 24 hours", "5 of 6 ATMs open",
                        "https://kyb.chase.com/schedule/mill-run"),
                new NearbyLocation("n2", "Lincoln Village", "ATM_DRIVE_UP", 3.16, 40.01, -75.18,
                        false, "Open 24 hours", "Temporarily closed", null),
                new NearbyLocation("n3", "Livingston Ave", "ATM_DRIVE_UP", 3.16, 39.98, -75.22,
                        true, "Open 24 hours", null, null)
        );
        return new NearbyBranchesResponse(
                "b3f1a2c0-1111-4a2b-9c3d-000000000001", 5.0, locations
        );
    }
```

REPLACE WITH:
```java
    private NearbyBranchesResponse nearbyBranches() {
        List<NearbyLocation> locations = List.of(
                new NearbyLocation("your-branch", "Main Street", "BRANCH_ATM", null,
                        0, 39.995, -75.205, true, "OPEN",
                        "Open until 5 PM today", "100 Main St, Riverdale, NJ 07457", "(973) 555-0100",
                        "4 of 4 ATMs open", null),
                new NearbyLocation("n1", "Mill Run", "BRANCH_DRIVE_UP_ATM", null,
                        1.10, 39.99, -75.20, false, "OPEN",
                        "Open until 5 PM today | ATM open 24 hours", "142 Mill Run Rd, Riverdale, NJ 07457",
                        "(973) 555-0142", "5 of 6 ATMs open", "https://kyb.chase.com/schedule/mill-run"),
                new NearbyLocation("n2", "Lincoln Village", "ATM_ONLY", "DRIVE_UP",
                        3.16, 40.01, -75.18, false, "TEMPORARILY_CLOSED",
                        "Open 24 hours", "8 Lincoln Village Plz, Riverdale, NJ 07457", null,
                        "Temporarily closed", null),
                new NearbyLocation("n3", "Livingston Ave", "ATM_ONLY", "DRIVE_UP",
                        3.16, 39.98, -75.22, false, "OPEN",
                        "Open 24 hours", "220 Livingston Ave, Riverdale, NJ 07457", null, null, null)
        );
        return new NearbyBranchesResponse(
                "b3f1a2c0-1111-4a2b-9c3d-000000000001", 5.0, locations
        );
    }
```

---

## Change 5 of 6: actions-service DTOs — RequiredAction gets dueInDays

FILE: backend/actions-service/src/main/java/com/chase/branchhealth/actions/dto/ActionsDtos.java

FIND:
```java
    public record RequiredAction(
            String actionId,
            String actionType, // CONTROL_EXCEPTION | CASH_MANAGEMENT | BRANCH_DASHBOARD | ICSC | BANKER_NOTIFICATIONS
            String title,
            String subtitle,
            int pendingCount,
            String dueDescriptor, // DUE_TODAY | DUE_TOMORROW | DUE_IN_N_DAYS | NONE_DUE | TO_REVIEW
            String destinationUrl,
            String priority // HIGH | MEDIUM | LOW
    ) {
    }
```

REPLACE WITH:
```java
    public record RequiredAction(
            String actionId,
            String actionType, // CONTROL_EXCEPTION | CASH_MANAGEMENT | BRANCH_DASHBOARD | ICSC | BANKER_NOTIFICATIONS
            String title,
            String subtitle,
            int pendingCount,
            String dueDescriptor, // DUE_TODAY | DUE_TOMORROW | DUE_IN_N_DAYS | NONE_DUE | TO_REVIEW
            Integer dueInDays, // ICSC only — countdown to last business day of month; null otherwise
            String destinationUrl,
            String priority // HIGH | MEDIUM | LOW
    ) {
    }
```

---

## Change 6 of 6: actions-service mock data — getRequiredActions() method

FILE: backend/actions-service/src/main/java/com/chase/branchhealth/actions/service/MockActionsService.java

FIND:
```java
    public RequiredActionsResponse getRequiredActions() {
        List<RequiredAction> actions = List.of(
                new RequiredAction("a1", "CONTROL_EXCEPTION", "Control exceptions", "2 items due today",
                        2, "DUE_TODAY", "https://branch-dashboard.chase.com/action-items/controls-exception", "HIGH"),
                new RequiredAction("a2", "CASH_MANAGEMENT", "Cash management", "0 items due",
                        0, "NONE_DUE", "https://mycashmanager.chase.com", "LOW"),
                new RequiredAction("a3", "BRANCH_DASHBOARD", "Branch Dashboard", "1 item due tomorrow",
                        1, "DUE_TOMORROW", "https://branch-dashboard.chase.com", "MEDIUM"),
                new RequiredAction("a4", "ICSC", "ICSC", "4 items due in 2 days",
                        4, "DUE_IN_N_DAYS", "https://branch-dashboard.chase.com/icsc", "MEDIUM"),
                new RequiredAction("a5", "BANKER_NOTIFICATIONS", "Banker notifications", "2 items to review",
                        2, "TO_REVIEW", "https://banker-homepage.chase.com/notifications", "LOW")
        );
        return new RequiredActionsResponse(5, actions);
    }
```

REPLACE WITH:
```java
    public RequiredActionsResponse getRequiredActions() {
        List<RequiredAction> actions = List.of(
                new RequiredAction("a1", "CONTROL_EXCEPTION", "Control exceptions", "2 items due today",
                        2, "DUE_TODAY", null, "https://branch-dashboard.chase.com/action-items/controls-exception", "HIGH"),
                new RequiredAction("a2", "CASH_MANAGEMENT", "Cash management", "0 items due",
                        0, "NONE_DUE", null, "https://mycashmanager.chase.com", "LOW"),
                new RequiredAction("a3", "BRANCH_DASHBOARD", "Branch Dashboard", "1 item due tomorrow",
                        1, "DUE_TOMORROW", 1, "https://branch-dashboard.chase.com", "MEDIUM"),
                new RequiredAction("a4", "ICSC", "ICSC", "4 items due in 2 days",
                        4, "DUE_IN_N_DAYS", 2, "https://branch-dashboard.chase.com/icsc", "MEDIUM"),
                new RequiredAction("a5", "BANKER_NOTIFICATIONS", "Banker notifications", "2 items to review",
                        2, "TO_REVIEW", null, "https://banker-homepage.chase.com/notifications", "LOW")
        );
        return new RequiredActionsResponse(5, actions);
    }
```

---

## Verify after all 6 changes

No `javac`/Maven available in the sandbox this guide was written in, so these weren't
compiled — I hand-checked brace/paren balance instead. Run this yourself first:

```bash
cd backend/dashboard-service && mvn -q compile
cd ../actions-service && mvn -q compile
```

If either fails, it's a real bug — likely a missed call site constructing `NearbyLocation` or
`RequiredAction` positionally elsewhere in your compliance-modified code that also needs its
argument list updated (Java records break compilation loudly on arity mismatches, so the
compiler error will point you straight at it).
