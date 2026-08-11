# 00 — Paste this into Copilot Chat first

Open your **current, compliance-modified** `branch-health` project in IntelliJ (not a fresh
download — your existing working copy). Open Copilot Chat and paste this:

---

I'm syncing a set of upstream changes into this repo without losing my own local edits. I have
4 files in this chat: `01_api_contract_changes.md`, `02_frontend_changes.md`,
`03_backend_changes.md`, `04_new_files.md`. Each contains one or more changes shaped like:

```
FILE: <path>
FIND:
<exact text to locate>
REPLACE WITH:
<exact text to substitute>
```

or, for brand-new files:

```
FILE: <path>
CREATE WITH CONTENT:
<full file content>
```

For each change, in order:

1. Open the file at that path.
2. Search for the FIND block. Match it exactly, including whitespace/indentation.
3. **If found**: replace it with the REPLACE WITH block. Don't touch anything else in the file.
4. **If NOT found**: stop on that change, don't guess or fuzzy-match. Show me the section of the
   file where you expected it, and the FIND block that didn't match, so I can tell you how my
   local version differs (this means one of my compliance edits touched that exact spot).
5. For CREATE WITH CONTENT blocks: if the file already exists, stop and show me a diff instead
   of overwriting it.

After each file's changes are applied, run:
- `cd frontend && npm run build` for any frontend change
- Nothing needs to compile-check backend changes automatically — just apply them; I'll build
  in IntelliJ myself since Maven isn't available in your sandbox either.

Go through the 4 files one at a time — don't start the next until I confirm the current one's
changes applied cleanly (or we've resolved the mismatches).

---

Then paste the contents of `01_api_contract_changes.md`, wait for it to finish and report back,
then `02_frontend_changes.md`, then `03_backend_changes.md`, then `04_new_files.md`.

## Why this approach instead of just re-downloading the zip

A fresh zip would overwrite any file you've changed for compliance, even in places unrelated to
what changed here. This guide only touches the specific blocks that actually changed — anything
else in those files, including your compliance edits, is left alone. If a FIND block doesn't
match, that's Copilot correctly telling you "your version differs here — decide how to merge"
instead of silently clobbering it.

## What these 4 files cover

All of it comes from one working session: closing the gap between the dashboard and the detailed
engineering-spec screenshots (Required Actions data elements, Nearby Branches spec, data
ownership table, decision log) that were reviewed on a call.

| File | What changes |
| --- | --- |
| `01_api_contract_changes.md` | `api/openapi.yaml` — `RequiredAction.dueInDays`, `NearbyLocation` reshaped (status tri-state, address, phone, isYourBranch, accessType) |
| `02_frontend_changes.md` | `frontend/src/types/dashboard.ts`, `frontend/src/services/mockData.ts`, full rewrite of `frontend/src/widgets/NearbyBranches/NearbyBranches.tsx` (click-to-select, your-branch pin) |
| `03_backend_changes.md` | Same shape changes mirrored into `backend/dashboard-service` and `backend/actions-service` Java DTOs + mock services |
| `04_new_files.md` | New `docs/14_Backlog/decision-log.md`, updated `api/examples/dashboard-response.json`, updated `ARCHITECTURE.md` ownership table, one-line addition to `docs/14_Backlog/README.md` |

## Not included here — separate conversation needed

Your senior's ADLC (vs. SDLC) methodology point came in after this batch of changes and got cut
off before you finished explaining it. I don't have enough to act on yet — is ADLC your team's
name for the Synthesis → Grooming → Execution pipeline from the "Agentic SDLC" screenshots you
shared earlier (Confidence Gate, QUAD Review, RACI charts, toll-gates), or something else? And is
the ask to document it in `CONTRIBUTING.md`/`docs/00_INDEX.md`, or to actually restructure how
work moves through this repo? Once you spell that out, that's its own patch file the same shape
as these four.
