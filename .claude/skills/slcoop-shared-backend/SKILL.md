---
name: slcoop-shared-backend
description: Backend/n8n knowledge shared by both SLCOOP websites (employee portal and member portal). Use this whenever working on anything that talks to the SLCOOP master webhook — sync logic, loan application flow, member data, announcements, or SMS notifications. Both repos depend on this same backend, so check here before assuming how a sync or API call should behave.
---

# SLCOOP Shared Backend

Both SLCOOP websites (employee portal and member portal) talk to **one shared n8n workflow**. This skill documents that workflow so behavior stays consistent across both repos.

## Naming gotcha — read this first

The n8n workflow is named **"TEST - SL Coop - Loan Application Handler"**. Despite the word "TEST" in the name, **this is the live production workflow** actually used by both websites. Don't assume it's a staging copy — verified directly with Rosh on 2026-06-23.

## The Master Webhook

There is **one webhook** ("SL Coop — Master API") that all requests go through. Internally, it routes based on a single field: `body.action`. A "Route by Action" Switch node (mode: Rules) reads `{{ $json.body.action }}` and sends the request down one of these paths:

| `action` value | Routes to (Output Name) | Purpose (best understanding so far — confirm with Rosh if unsure) |
|---|---|---|
| `loan-application` | Loan Application | Submitting a new loan application (member portal) |
| `update-status` | Update Status | Updating an application's status |
| `get-applications` | Get Applications | Retrieving loan applications |
| `sync-members` | Sync Members | Syncing member records |
| `member-portal` | Member Portal | Member portal-specific data/actions |
| `sync-transaction` | Sync Transaction | Syncing transaction data (likely employee portal collections) |
| `send-sms` | Send SMS | Sends SMS notifications via Semaphore |
| `sync-announcements` | Sync Announcements | Syncing announcements |
| `get-announcements` | Get Announcements | Retrieving announcements |
| `get-members` | Get Members | Retrieving member records — **not yet built, see note below** |

## Note: `get-members` branch is incomplete (not broken)

The `get-members` routing rule does NOT use the same `{{ $json.body.action }}` expression as every other rule — its left-hand field ("value1") appeared empty/unset when inspected on 2026-06-23. **This is not a bug.** Rosh confirmed this branch was added in advance (on Claude's earlier suggestion) but hasn't been mapped/connected to any nodes yet — it's a placeholder for future work, not something currently in use.

## Working with this webhook

- When debugging a sync issue from either website, first identify which `action` value is being sent, then check this table to know which n8n branch handles it.
- If a new feature needs a new backend action, it must be added as a new Routing Rule inside this same "Route by Action" node — both websites share this one node, so changes here can affect both repos.
- Per how-rosh-works rules: don't guess which branch handles something — verify directly in the n8n editor (Route by Action node → Parameters) rather than assuming from memory, since this list has been wrong before (earlier assumption was "4 routes" — actual count is 10).

## Still to confirm

- Exact purpose/payload shape for each action (marked "best understanding so far" above) — fill in as confirmed through real testing or stakeholder input.
- When `get-members` gets built out, update this skill with its actual mapped nodes and behavior.
