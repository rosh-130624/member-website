---
name: slcoop-employee-portal
description: Project knowledge specific to the SLCOOP employee staff portal (collector-website) — admin/manager/supervisor/officer-facing features, known risk areas, and pending work. Use this whenever working inside the employee-website repo. For backend/n8n knowledge shared with the member portal, see slcoop-shared-backend instead.
---

# SLCOOP Employee Portal (collector-website)

The employee-facing SLCOOP staff portal. Used by **admins, managers, supervisors, and officers** to log in, record, and check cooperative data. Single-file web app hosted on GitHub Pages, using localStorage with Google Sheets sync via the shared n8n webhook (see `slcoop-shared-backend` skill for backend details).

## What this portal does

- Collection route management
- Branch overview and month-end reports
- Barangay management
- Announcements (the ones the member portal auto-displays come from here — supervisor posts them)
- SMS templates
- Member approval flow
- Member conversion flow (APP- applicant → MEM- member)
- Sync indicators (showing localStorage ↔ Google Sheets sync status)

## Known risk areas — be careful here

- **`clearAllData` function** — this has had security hardening work done on it. Treat any change here as high-risk; confirm with Rosh before modifying (per the "confirm before risky actions" rule in how-rosh-works).
- **Audit trail** — known gaps have been identified in past sessions. Don't assume an action is logged just because similar actions are — check.
- **Conversion flow (APP- → MEM-)** — this flow has had bugs before and needs careful testing. Test thoroughly after any change, including edge cases around the approval step.
- **Dropdown filters** — there's a history of dropdown filter reset bugs in this codebase. Watch for this pattern recurring in new filter-related code.

## Pending / planned work (as of last sync with Rosh)

- Add per-notification delete capability
- Add a "Monthly Report" button
- Remove a dead/unused filter option

## Architecture notes

- Single HTML file, no build step — changes are direct edits to one file.
- Data flow: localStorage (offline-first) ↔ Google Sheets (via shared n8n webhook).
- Relevant action routes from the shared webhook: likely `sync-transaction`, `sync-members`, `sync-announcements`, `update-status`, `loan-application` (for approvals), `get-applications` — confirm exact usage per feature rather than assuming, since full payload shapes haven't been mapped yet (see `slcoop-shared-backend`).

## Still to confirm

- Exact list of which webhook actions this portal calls vs. which the member portal calls — not yet mapped feature-by-feature.
- Current status of the 3 pending features above (not started / in progress / blocked).
