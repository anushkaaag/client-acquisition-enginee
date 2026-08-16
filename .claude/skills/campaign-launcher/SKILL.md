---
name: campaign-launcher
description: Takes a qualified prospect list and a written sequence and launches it on LinkedIn through the SalesRobot MCP. Use this skill ANY time someone wants to actually send outreach, start a campaign, load prospects into a sequence, set daily sending limits, or push a list live on LinkedIn. Also trigger for "launch this", "send the sequence", "start the campaign", "add these people to a campaign", or when the qualifier and DM writer have produced a list and a sequence that now need to go out. This is step 5 of the loop, the one that actually sends. It confirms before it starts anything.
---

# Campaign launcher

Turns a qualified list and a written sequence into a live LinkedIn campaign. This is
the step the rest of the loop was building toward, and the only skill here that
performs a real send. So it moves carefully: it confirms the account, the source, the
settings, and the sequence in plain language before it starts anything.

Everything here runs on the **SalesRobot MCP** (server name `salesrobot`). If those
tools are not loaded, stop and point the operator at `README.md`. Do not describe a
campaign as launched when it was only drafted. Full tool and parameter detail is in
`references/salesrobot-tools.md`; read it before constructing any call.

## What it needs first

- `workspace/qualified.md` — the scored list. Send to hot and warm. Never load
  disqualified leads into a campaign.
- a sequence from `workspace/sequences/` — the DM writer's output, with touch types
  already marked as connection request or message.
- the LinkedIn account this runs on. A LinkedIn account must already be connected
  inside SalesRobot itself; the MCP manages campaigns on accounts that exist, it does
  not create the connection.

If any of these is missing, say so and route back to the skill that produces it rather
than improvising.

## The flow, in order

Each step needs the UUID or state from the one before it. Run them in sequence,
confirming with the operator at the marked checkpoints.

**Step 0 — find the account.** `linkedin_account_list` returns every connected
account. If more than one comes back, do not guess. Show the operator the identifying
details (name, email) and let them pick. Copy the `uuid`. Every later call needs it.

**Step 0b — account settings, once per account (optional but recommended).** Daily
quotas and a sending schedule protect the account across every campaign that runs on
it. Sensible starting quotas: ~25 connection requests, ~50 messages, ~100 profile
views a day. Set a business-hours schedule in the account's timezone. These are
account-level (`setting_*` tools), separate from the per-campaign settings in step 3,
and only need doing once. Skip if the operator has already set them.

**Step 1 — create the campaign.** `campaign_create` with the account UUID and a
campaign name. Name it after the signal it was built on, not something generic, so the
report can attribute results later. Save the returned `campaignUuid`.

**Step 2 — add the prospects.** Pick the import method that matches where the list
came from:
- Qualified leads from a GetLeads pull usually arrive as a CSV or a set of LinkedIn
  URLs → `prospect_upload_csv` for a batch, or `prospect_add` with `profileUrl` for a
  small hot list.
- If the source was a LinkedIn search or Sales Navigator URL →
  `prospect_import_linkedin_search` or `prospect_sync_sales_nav`.
- People who engaged with a post → `prospect_import_post_comments` or
  `prospect_import_post_reactions`.

`searchUrl` and `searchType` are set automatically by whichever import tool runs. Do
not pass them into the settings call in step 3; they have no effect there and cause
confusion.

**Step 3 — campaign settings.** `campaign_update_settings`. This is where the most
common failure lives:

> **`connectionLevels` is mandatory.** Omit it and the backend throws an HTTP 500 with
> no graceful fallback. Default to `["2nd", "3rd"]` for search-based or cold
> campaigns even if the operator never mentioned connection levels.

`connectionLevels` also gates which sequence steps are legal (see step 4). Turn on
`enrichData` if the list needs enriching, and
`dontAddIfInAnotherLinkedinAccountForMyUser` to de-dup across the operator's other
connected accounts.

**Step 4 — build the sequence.** `campaign_create_sequence_from_steps`. Map the DM
writer's touches onto SalesRobot step types:
- A cold prospect is 2nd or 3rd degree, so the sequence opens with
  `SEND_CONNECTION_REQUEST` (a note under the connection request), then `SEND_MESSAGE`
  steps only *after* they accept, because `SEND_MESSAGE` requires a 1st-degree
  connection.
- Warm first-degree connections can start straight into `SEND_MESSAGE`.
- Space the touches with `day` and `hours` matching the DM writer's timing.

> **Merge tags need double curly braces.** `{{firstName}}` resolves. `{firstName}` goes
> out to the prospect as literal text. This only surfaces after a message has sent, so
> get it right before building the steps.

Optional, only when genuinely warranted: an AI personalization variable
(`ai_features_create_variable`) referenced as `{{variableName}}` in a message body, a
voice clone for `SEND_VOICE_MESSAGE` steps, or a video avatar. Do not add these because
they exist. Add them when the sequence actually calls for one.

**Step 5 — the confirmation checkpoint.** Before starting, show the operator, in plain
language: which account, how many prospects and from where, the connection levels, and
the full sequence with timing. Ask them to confirm. Do not skip this. A campaign nobody
looked at is exactly the thing this careful flow exists to prevent.

**Step 6 — start.** Only after confirmation. `campaign_start` with the account UUID,
the campaign UUID, and `hasInviteMessage` set to whether touch 1 is a connection
request with a note.

## After it starts

Write to `workspace/campaigns.md`, one entry per campaign:

```
CAMPAIGN: [name]
campaignUuid: [uuid]
linkedinAccountUuid: [uuid]
signal: [the targeting thesis this list was built on]
launched: [date]
list source: [where the prospects came from]
sequence: [file in workspace/sequences/]
```

The `signal` line is the one that matters most. The weekly report groups results by
signal, not by campaign name, and it can only do that if the launcher wrote the signal
down at launch. Without it, the loop never closes.

## What this skill will not do

- It will not start a campaign the operator has not seen. The checkpoint in step 5 is
  not optional.
- It will not load disqualified leads. Only hot and warm from `workspace/qualified.md`.
- It will not invent prospects, profile URLs, or company details to fill a list. If the
  list is thin, say so.
- It does not manage the LinkedIn login or connect the account. That happens inside
  SalesRobot before any of this runs.

## Verifying it worked

Confirm in the SalesRobot dashboard, not just by trusting a success response. Then the
reply assistant takes over the inbox and the weekly report reads the stats back out.
