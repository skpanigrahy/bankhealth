# 02 — Frontend changes

## Change 1 of 3: frontend/src/types/dashboard.ts — RequiredAction gets dueInDays

FILE: frontend/src/types/dashboard.ts

FIND:
```ts
export interface RequiredAction {
  actionId: string;
  actionType: ActionType;
  title: string;
  subtitle?: string;
  pendingCount: number;
  dueDescriptor: DueDescriptor;
  destinationUrl: string;
  priority: "HIGH" | "MEDIUM" | "LOW";
}
```

REPLACE WITH:
```ts
export interface RequiredAction {
  actionId: string;
  actionType: ActionType;
  title: string;
  subtitle?: string;
  pendingCount: number;
  dueDescriptor: DueDescriptor;
  /** Only meaningful for ICSC — countdown to the last business day of the month. */
  dueInDays?: number | null;
  destinationUrl: string;
  priority: "HIGH" | "MEDIUM" | "LOW";
}
```

---

## Change 2 of 3: frontend/src/types/dashboard.ts — NearbyLocation reshaped

FILE: frontend/src/types/dashboard.ts

FIND:
```ts
export type LocationType = "BRANCH_WITH_ATM" | "ATM_DRIVE_UP" | "FINANCIAL_CENTER";

export interface NearbyLocation {
  locationId: string;
  name: string;
  locationType: LocationType;
  distanceMiles: number;
  latitude: number;
  longitude: number;
  isOpen: boolean;
  hoursLabel?: string;
  atmSummary?: string | null;
  meetWithUsUrl?: string | null;
}
```

REPLACE WITH:
```ts
export type LocationType = "BRANCH_DRIVE_UP_ATM" | "BRANCH_ATM" | "ATM_ONLY";
export type LocationStatus = "OPEN" | "CLOSED_TODAY" | "TEMPORARILY_CLOSED";

export interface NearbyLocation {
  locationId: string;
  name: string;
  locationType: LocationType;
  accessType?: "DRIVE_UP" | "WALK_UP" | null;
  distanceMiles: number;
  latitude: number;
  longitude: number;
  /** True for the one pin representing the branch the banker is currently in. */
  isYourBranch?: boolean;
  status: LocationStatus;
  hoursLabel?: string;
  address?: string | null;
  phoneNumber?: string | null;
  atmSummary?: string | null;
  meetWithUsUrl?: string | null;
}
```

**Note:** if your compliance changes added fields to `NearbyLocation` or `RequiredAction`
(e.g. an audit/tracking field), re-add them after this REPLACE — this block only restores the
upstream shape, it doesn't know about your additions.

---

## Change 3 of 3: frontend/src/services/mockData.ts — two blocks

### 3a. requiredActions actions array

FILE: frontend/src/services/mockData.ts

FIND:
```ts
      {
        actionId: "a1",
        actionType: "CONTROL_EXCEPTION",
        title: "Control exceptions",
        subtitle: "2 items due today",
        pendingCount: 2,
        dueDescriptor: "DUE_TODAY",
        destinationUrl: "https://branch-dashboard.chase.com/action-items/controls-exception",
        priority: "HIGH",
      },
      {
        actionId: "a2",
        actionType: "CASH_MANAGEMENT",
        title: "Cash management",
        subtitle: "0 items due",
        pendingCount: 0,
        dueDescriptor: "NONE_DUE",
        destinationUrl: "https://mycashmanager.chase.com",
        priority: "LOW",
      },
      {
        actionId: "a3",
        actionType: "BRANCH_DASHBOARD",
        title: "Branch Dashboard",
        subtitle: "1 item due tomorrow",
        pendingCount: 1,
        dueDescriptor: "DUE_TOMORROW",
        destinationUrl: "https://branch-dashboard.chase.com",
        priority: "MEDIUM",
      },
      {
        actionId: "a4",
        actionType: "ICSC",
        title: "ICSC",
        subtitle: "4 items due in 2 days",
        pendingCount: 4,
        dueDescriptor: "DUE_IN_N_DAYS",
        destinationUrl: "https://branch-dashboard.chase.com/icsc",
        priority: "MEDIUM",
      },
      {
        actionId: "a5",
        actionType: "BANKER_NOTIFICATIONS",
        title: "Banker notifications",
        subtitle: "2 items to review",
        pendingCount: 2,
        dueDescriptor: "TO_REVIEW",
        destinationUrl: "https://banker-homepage.chase.com/notifications",
        priority: "LOW",
      },
```

REPLACE WITH:
```ts
      {
        actionId: "a1",
        actionType: "CONTROL_EXCEPTION",
        title: "Control exceptions",
        subtitle: "2 items due today",
        pendingCount: 2,
        dueDescriptor: "DUE_TODAY",
        dueInDays: null, // Control exceptions have no due-date field — always "due today" (see decision-log.md)
        destinationUrl: "https://branch-dashboard.chase.com/action-items/controls-exception",
        priority: "HIGH",
      },
      {
        actionId: "a2",
        actionType: "CASH_MANAGEMENT",
        title: "Cash management",
        subtitle: "0 items due",
        pendingCount: 0,
        dueDescriptor: "NONE_DUE",
        dueInDays: null, // covers ATM, TCR, cash boxes, cash vaults — also no due-date field
        destinationUrl: "https://mycashmanager.chase.com",
        priority: "LOW",
      },
      {
        actionId: "a3",
        actionType: "BRANCH_DASHBOARD",
        title: "Branch Dashboard",
        subtitle: "1 item due tomorrow",
        pendingCount: 1,
        dueDescriptor: "DUE_TOMORROW",
        dueInDays: 1,
        destinationUrl: "https://branch-dashboard.chase.com",
        priority: "MEDIUM",
      },
      {
        actionId: "a4",
        actionType: "ICSC",
        title: "ICSC",
        subtitle: "4 items due in 2 days",
        pendingCount: 4,
        dueDescriptor: "DUE_IN_N_DAYS",
        dueInDays: 2, // countdown to the last business day of the current month
        destinationUrl: "https://branch-dashboard.chase.com/icsc",
        priority: "MEDIUM",
      },
      {
        actionId: "a5",
        actionType: "BANKER_NOTIFICATIONS",
        title: "Banker notifications",
        subtitle: "2 items to review",
        pendingCount: 2,
        dueDescriptor: "TO_REVIEW",
        dueInDays: null,
        destinationUrl: "https://banker-homepage.chase.com/notifications",
        priority: "LOW",
      },
```

### 3b. nearbyBranches object

FILE: frontend/src/services/mockData.ts

FIND:
```ts
  nearbyBranches: {
    centerBranchId: "b3f1a2c0-1111-4a2b-9c3d-000000000001",
    radiusMiles: 5,
    locations: [
      {
        locationId: "n1",
        name: "Mill Run",
        locationType: "BRANCH_WITH_ATM",
        distanceMiles: 1.1,
        latitude: 39.99,
        longitude: -75.2,
        isOpen: true,
        hoursLabel: "Open until 5 PM today | ATM open 24 hours",
        atmSummary: "5 of 6 ATMs open",
        meetWithUsUrl: "https://kyb.chase.com/schedule/mill-run",
      },
      {
        locationId: "n2",
        name: "Lincoln Village",
        locationType: "ATM_DRIVE_UP",
        distanceMiles: 3.16,
        latitude: 40.01,
        longitude: -75.18,
        isOpen: false,
        hoursLabel: "Open 24 hours",
        atmSummary: "Temporarily closed",
        meetWithUsUrl: null,
      },
      {
        locationId: "n3",
        name: "Livingston Ave",
        locationType: "ATM_DRIVE_UP",
        distanceMiles: 3.16,
        latitude: 39.98,
        longitude: -75.22,
        isOpen: true,
        hoursLabel: "Open 24 hours",
        atmSummary: null,
        meetWithUsUrl: null,
      },
    ],
  },
};
```

REPLACE WITH:
```ts
  nearbyBranches: {
    centerBranchId: "b3f1a2c0-1111-4a2b-9c3d-000000000001",
    radiusMiles: 5,
    locations: [
      {
        locationId: "your-branch",
        name: "Main Street",
        locationType: "BRANCH_ATM",
        distanceMiles: 0,
        latitude: 39.995,
        longitude: -75.205,
        isYourBranch: true,
        status: "OPEN",
        hoursLabel: "Open until 5 PM today",
        address: "100 Main St, Riverdale, NJ 07457",
        phoneNumber: "(973) 555-0100",
        atmSummary: "4 of 4 ATMs open",
        meetWithUsUrl: null,
      },
      {
        locationId: "n1",
        name: "Mill Run",
        locationType: "BRANCH_DRIVE_UP_ATM",
        distanceMiles: 1.1,
        latitude: 39.99,
        longitude: -75.2,
        status: "OPEN",
        hoursLabel: "Open until 5 PM today | ATM open 24 hours",
        address: "142 Mill Run Rd, Riverdale, NJ 07457",
        phoneNumber: "(973) 555-0142",
        atmSummary: "5 of 6 ATMs open",
        meetWithUsUrl: "https://kyb.chase.com/schedule/mill-run",
      },
      {
        locationId: "n2",
        name: "Lincoln Village",
        locationType: "ATM_ONLY",
        accessType: "DRIVE_UP",
        distanceMiles: 3.16,
        latitude: 40.01,
        longitude: -75.18,
        status: "TEMPORARILY_CLOSED",
        hoursLabel: "Open 24 hours",
        address: "8 Lincoln Village Plz, Riverdale, NJ 07457",
        phoneNumber: null,
        atmSummary: "Temporarily closed",
        meetWithUsUrl: null,
      },
      {
        locationId: "n3",
        name: "Livingston Ave",
        locationType: "ATM_ONLY",
        accessType: "DRIVE_UP",
        distanceMiles: 3.16,
        latitude: 39.98,
        longitude: -75.22,
        status: "OPEN",
        hoursLabel: "Open 24 hours",
        address: "220 Livingston Ave, Riverdale, NJ 07457",
        phoneNumber: null,
        atmSummary: null,
        meetWithUsUrl: null,
      },
    ],
  },
};
```

---

## Change 4 of 4 (full-file replace, look before you leap)

FILE: frontend/src/widgets/NearbyBranches/NearbyBranches.tsx

**Before replacing:** open this file and check whether it has any local edits beyond what a
plain download would have — e.g. company logging hooks, analytics calls, accessibility
additions, different copy for legal/compliance reasons. If you find any, list them for me before
proceeding rather than discarding them; I'll fold them into the block below by hand.

This is a full-file rewrite (added click-to-select interaction and a distinct "your branch"
pin), not a small patch, so there's no meaningful smaller FIND block — replace the entire file
with:

```tsx
import { useState } from "react";
import { Box, Typography, Stack, Button, Divider } from "@mui/material";
import PlaceIcon from "@mui/icons-material/Place";
import MyLocationIcon from "@mui/icons-material/MyLocation";
import AccountBalanceOutlinedIcon from "@mui/icons-material/AccountBalanceOutlined";
import LocalAtmOutlinedIcon from "@mui/icons-material/LocalAtmOutlined";
import ErrorOutlineIcon from "@mui/icons-material/ErrorOutline";
import type { LocationType, NearbyBranchesResponse, NearbyLocation } from "../../types/dashboard";
import { brand } from "../../theme/theme";

interface Props {
  data: NearbyBranchesResponse;
}

const LOCATION_ICON: Record<LocationType, React.ElementType> = {
  BRANCH_DRIVE_UP_ATM: AccountBalanceOutlinedIcon,
  BRANCH_ATM: AccountBalanceOutlinedIcon,
  ATM_ONLY: LocalAtmOutlinedIcon,
};

// Exact labels from docs/04_Engineering (the "Branch Services" spec) — the pipe-separated
// format is what engineering is building to, distinct from the friendlier mockup copy.
function typeLabel(loc: NearbyLocation): string {
  switch (loc.locationType) {
    case "BRANCH_DRIVE_UP_ATM":
      return "Chase branch | Drive-Up | ATM";
    case "BRANCH_ATM":
      return "Chase branch | ATM";
    case "ATM_ONLY":
      return loc.accessType === "DRIVE_UP" ? "Chase ATM | Drive-up" : "Chase ATM";
  }
}

// Pin color follows status directly (per the spec: open = blue, closed = red) —
// TEMPORARILY_CLOSED is the only red case; CLOSED_TODAY (outside operating hours but not
// reported closed) still renders blue since the location itself isn't down.
function pinColor(status: NearbyLocation["status"]): string {
  return status === "TEMPORARILY_CLOSED" ? brand.critical : brand.linkBlue;
}

// Deterministic placeholder positions for MVP1 — replace with a real map provider
// (see docs/14_Backlog and integrations/branch-locator) wired to lat/long.
const PIN_POSITIONS = [
  { top: "35%", left: "50%" }, // your branch, roughly centered
  { top: "62%", left: "22%" },
  { top: "35%", left: "78%" },
  { top: "20%", left: "20%" },
  { top: "78%", left: "48%" },
  { top: "50%", left: "72%" },
];

export function NearbyBranches({ data }: Props) {
  const [selectedId, setSelectedId] = useState<string | null>(
    data.locations.find((l) => !l.isYourBranch)?.locationId ?? null
  );

  const yourBranch = data.locations.find((l) => l.isYourBranch);
  const otherLocations = data.locations.filter((l) => !l.isYourBranch);

  return (
    <Box component="section" aria-labelledby="nearby-heading">
      <Typography id="nearby-heading" variant="h2">
        Nearby branches, financial centers and ATMs
      </Typography>
      <Typography color="text.secondary" variant="body2" sx={{ mb: 2 }}>
        See which locations are open or closed.
      </Typography>

      <Box
        sx={{
          display: "grid",
          gridTemplateColumns: { xs: "1fr", md: "1fr 380px" },
          border: "1px solid #E4E7EC",
          borderRadius: 2,
          overflow: "hidden",
        }}
      >
        {/* Map */}
        <Box
          sx={{
            position: "relative",
            minHeight: 320,
            bgcolor: "#E9EEF3",
            backgroundImage:
              "linear-gradient(#DCE4EC 1px, transparent 1px), linear-gradient(90deg, #DCE4EC 1px, transparent 1px)",
            backgroundSize: "32px 32px",
          }}
        >
          {yourBranch && (
            <Box
              sx={{
                position: "absolute",
                top: PIN_POSITIONS[0].top,
                left: PIN_POSITIONS[0].left,
                transform: "translate(-50%, -50%)",
              }}
              title={`${yourBranch.name} (your branch)`}
            >
              <MyLocationIcon sx={{ fontSize: 26, color: brand.headerBlue }} />
            </Box>
          )}
          {otherLocations.map((loc, i) => {
            const pos = PIN_POSITIONS[(i + 1) % PIN_POSITIONS.length];
            const selected = loc.locationId === selectedId;
            return (
              <Box
                key={loc.locationId}
                role="button"
                tabIndex={0}
                onClick={() => setSelectedId(loc.locationId)}
                onKeyDown={(e) => e.key === "Enter" && setSelectedId(loc.locationId)}
                sx={{
                  position: "absolute",
                  top: pos.top,
                  left: pos.left,
                  transform: "translate(-50%, -100%)",
                  display: "flex",
                  flexDirection: "column",
                  alignItems: "center",
                  cursor: "pointer",
                }}
                title={loc.name}
              >
                <PlaceIcon
                  sx={{
                    fontSize: selected ? 44 : 34, // selected pin renders larger, per spec
                    color: pinColor(loc.status),
                    filter: "drop-shadow(0 1px 2px rgba(0,0,0,0.25))",
                    transition: "font-size 120ms ease",
                  }}
                />
                <Typography
                  variant="caption"
                  sx={{
                    color: "#fff",
                    fontWeight: 700,
                    position: "relative",
                    top: selected ? -30 : -22,
                  }}
                >
                  {i + 1}
                </Typography>
              </Box>
            );
          })}
        </Box>

        {/* List */}
        <Stack divider={<Divider />} sx={{ bgcolor: "background.paper" }}>
          {otherLocations.map((loc, i) => {
            const selected = loc.locationId === selectedId;
            return (
              <Box
                key={loc.locationId}
                role="button"
                tabIndex={0}
                onClick={() => setSelectedId(loc.locationId)}
                onKeyDown={(e) => e.key === "Enter" && setSelectedId(loc.locationId)}
                sx={{
                  p: 2,
                  pl: 2.5,
                  display: "grid",
                  gridTemplateColumns: "28px 1fr",
                  columnGap: 1.5,
                  cursor: "pointer",
                  bgcolor: selected ? "#EEF4FC" : "transparent", // highlight selected card, per spec
                  borderLeft: selected ? `4px solid ${brand.linkBlue}` : "4px solid transparent",
                }}
              >
                <Typography sx={{ fontWeight: 700, fontSize: "1.1rem", color: "text.primary", lineHeight: 1.6 }}>
                  {i + 1}
                </Typography>
                <Box>
                  <Stack direction="row" justifyContent="space-between">
                    <Typography variant="caption" color="text.secondary">
                      {typeLabel(loc)}
                    </Typography>
                    <Typography variant="caption" color="text.secondary">
                      {loc.distanceMiles.toFixed(2)} mi.
                    </Typography>
                  </Stack>
                  <Stack direction="row" spacing={1} alignItems="center" sx={{ mt: 0.5 }}>
                    {(() => {
                      const LocIcon = LOCATION_ICON[loc.locationType];
                      return <LocIcon sx={{ fontSize: 20, color: "text.secondary" }} />;
                    })()}
                    <Typography sx={{ fontWeight: 600 }}>{loc.name}</Typography>
                  </Stack>

                  {loc.status !== "TEMPORARILY_CLOSED" && loc.hoursLabel && (
                    <Typography variant="body2" color="text.secondary" sx={{ mt: 0.5 }}>
                      {loc.hoursLabel}
                    </Typography>
                  )}
                  {loc.status !== "TEMPORARILY_CLOSED" && loc.atmSummary && (
                    <Typography variant="body2" color="text.secondary">
                      {loc.atmSummary}
                    </Typography>
                  )}
                  {loc.status === "TEMPORARILY_CLOSED" && (
                    <>
                      {loc.hoursLabel && (
                        <Typography variant="body2" color="text.secondary" sx={{ mt: 0.5 }}>
                          {loc.hoursLabel}
                        </Typography>
                      )}
                      <Stack direction="row" spacing={0.5} alignItems="center">
                        <ErrorOutlineIcon sx={{ fontSize: 16, color: "error.main" }} />
                        <Typography variant="body2" sx={{ color: "error.main", fontWeight: 600 }}>
                          Temporarily closed
                        </Typography>
                      </Stack>
                    </>
                  )}

                  {/* Address/phone: in the Branch Locator detail-overlay per the spec, not the
                      card face — shown here only when a card is selected, as a reasonable stand-in
                      until that overlay's design is finalized (see docs/14_Backlog/decision-log.md) */}
                  {selected && (loc.address || loc.phoneNumber) && (
                    <Box sx={{ mt: 0.75 }}>
                      {loc.address && (
                        <Typography variant="caption" color="text.secondary" sx={{ display: "block" }}>
                          {loc.address}
                        </Typography>
                      )}
                      {loc.phoneNumber && (
                        <Typography variant="caption" color="text.secondary" sx={{ display: "block" }}>
                          {loc.phoneNumber}
                        </Typography>
                      )}
                    </Box>
                  )}

                  {loc.meetWithUsUrl && (
                    <Button
                      size="small"
                      variant="outlined"
                      href={loc.meetWithUsUrl}
                      target="_blank"
                      rel="noopener noreferrer"
                      onClick={(e) => e.stopPropagation()}
                      sx={{ mt: 1 }}
                    >
                      Meet with us
                    </Button>
                  )}
                </Box>
              </Box>
            );
          })}
        </Stack>
      </Box>
    </Box>
  );
}
```

---

## Verify after all 4 changes

```bash
cd frontend
npm install
npm run build
```
Should complete with no TypeScript errors.
