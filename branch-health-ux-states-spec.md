# Branch Health Dashboard — Error & Popover States Spec
Source: UX team screenshots in `docs/02_UX/`. Transcribed to text so Copilot can implement without image input.

**Out of scope for this spec** (same folder, happy-path tile screenshots, not error/popover states): `branchhealth-dashboard.png`, `HeaderOrWelcomeBar.png`, `NearbyBranches-BranchDetails.png`, `NearbyBranchesMap.png`, `PerformanceAtAGlance.png`, `RequiredActions.png`, `TechReadiness.png`, `TodaysMeetings.png`.

---

## 1. Page-level error states (full page replaced, not a tile)

Shared layout: centered icon (orange/amber outline stroke) → bold title → gray subtitle line → smaller italic "Error code: …" footer. This looks like ONE reusable `<PageError icon title subtitle errorCode linkText? />` component with three variants.

| Scenario | Source file (docs/02_UX/) | Icon | Title | Subtitle | Error code | Extra |
|---|---|---|---|---|---|---|
| No permission | `page-access-403-err.png` | padlock (outline) | "You don't have access to this page" | "You need permission to view this site. Please request access from your team admin." | 403 forbidden | — |
| Not found | `page-404-err.png` | broken pin/flag (outline) | "This page is unavailable" | "The content you're looking for is either missing or no longer exists." | 404 | Inline link "Report broken link" (blue, clickable) appended to subtitle |
| Server error | `page-502-err.png` | circular refresh arrow (outline) | "We're having trouble loading this page" | "Please try refreshing the page or visiting later." | 502 bad gateway | — |

**Implementation note for Copilot:** build `PageError` once, drive the three states via props/config, don't duplicate markup per error code.

---

## 2. Tile-level error states (a single widget fails to load, rest of dashboard renders normally)

Two DIFFERENT copy patterns appear across tiles using the same orange info-diamond icon. This is a discrepancy — flag it, don't silently pick one (see Open Question 1 below).

**Pattern A — generic "trouble loading" copy:**

| Tile | Source file (docs/02_UX/) |
|---|---|
| Customer appointments | `customer-appointments-browser-err.png` |
| Branch and ATM availability map | `branch-atm-availability-map-browser-err.png` |
| OSAT | `OSAT-browser-err.png` |

> [icon: orange info diamond]
> **{Tile Name}**
> We're having trouble loading this tile. Please try refreshing the page.

**Pattern B — short "data couldn't load" copy:**

| Tile | Source file (docs/02_UX/) |
|---|---|
| Cash management | `cashmanament-comp-err.png` |

> [icon: orange info diamond]
> **Cash management**
> Data couldn't load.

No retry button or refresh CTA appears in either pattern in the screenshots — text says "refresh the page" but there's no button, implying the browser refresh is the expected action (or a button was scoped but not shown in this mock).

**Implementation note for Copilot:** build one `TileError` component taking `{ title, variant: 'generic' | 'short' }`, do not hardcode two separate components unless UX confirms these are permanently distinct.

---

## 3. Popover states ("Required actions" tile detail views)

Shared structure across all four popovers — build ONE `RequiredActionPopover` component:

- Header row: icon (varies by source tile) + title (tile name) + close "×" button (top right)
- Meta row: `{date/time} | {count} items due today` / `{count} items to review` / `{count} item due tomorrow` / `0 items due`
- Body: either
  - **Empty state**: green checkmark + "All caught up — no items are due right now."
  - **List state**: numbered list, each row = `{n}. {item label}` + priority pill (`High priority` = red/magenta, `Medium priority` = amber/orange, `Low priority` = gray)
- Footer: single primary blue CTA button, right-aligned: `Go to {Target} →`

### Observed instances

| Source tile | Source file (docs/02_UX/) | Meta count | Items | CTA label |
|---|---|---|---|---|
| Cash management | `cash-management-popup.png` | 0 items due | *(empty state — all caught up)* | Go to Cash Manager → |
| Banker notifications | `banker-notification-popup.png` | 2 items to review | 1. Dual control override — **High priority**  2. Segregation of duties exception in teller line — **Low priority** | Go to Branch Dashboard → |
| Control exceptions | `control-exception-popup.png` | 2 items due today | 1. Dual control override — **High priority**  2. Segregation of duties exception in teller line — **Medium priority** | Go to Branch Dashboard → |
| Branch dashboard | `branch-dashboard-popup.png` | 1 item due tomorrow | 1. Dual control override — **Low priority** | Go to Branch Dashboard → |

**Implementation note for Copilot:** each popover pulls items from a shared "required actions" data source and renders count/framing per its own lens (review queue vs. compliance due-today vs. forecast-due-tomorrow) — don't hardcode item lists per tile file; wire to whatever the actual data source is once confirmed.

---

## 4. Open questions to confirm with UX before Copilot finalizes anything (do not guess)

1. **Tile error copy split (Pattern A vs B above).** Is "Cash management" intentionally terser, or is that a copy inconsistency that should match the other three tiles?
2. **Priority badge conflict on the same item.** "Dual control override" is **High priority** in two popovers but **Low priority** in the "Branch dashboard" popover. "Segregation of duties exception in teller line" is **Low priority** in one popover and **Medium priority** in another. Same item, different badge, depending which tile surfaces it. Confirm whether priority is:
   - a fixed property of the item (same everywhere), or
   - contextual to the tile/lens showing it (e.g. escalates as due date approaches)
   Building it wrong means the badge will visibly disagree between two tiles that both link to the same action.
3. **No retry/refresh button in tile errors** — confirm whether a button was scoped for this sprint or whether "refresh the page" literally means browser refresh only.


Reference: /02_UX/branch-health-ux-states-spec.md (exact copy + component
structure for error and popover states — screenshots in this folder are
for human reference only, do not attempt to read them)

1. Implement section 1 as one reusable PageError component with the 3 
   variants listed
2. Implement section 2 as one reusable TileError component; use the 
   'variant' prop, do not fork two components
3. Implement section 3 as one reusable RequiredActionPopover component 
   driven by the shared data source, not per-tile hardcoded lists
4. Do NOT resolve open questions 1–3 in section 4 — leave a // TODO: 
   confirm with UX comment at each affected line instead of guessing
