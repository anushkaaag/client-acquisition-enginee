# SalesRobot MCP — tool reference for the launcher

Everything the campaign launcher needs to construct correct calls. Tool names are
exact and snake_case. Guessing a parameter name produces a malformed request.

Package: `salesrobot-cli` (npm), built targeting Node 18. The MCP server name in this
project is `salesrobot`.

---

## The lifecycle, and the UUID chain

Every campaign follows the same order, and each step needs state from the one before:

```
linkedin_account_list   → accountUuid
campaign_create         → campaignUuid
prospect import         → prospects attached to the campaign
campaign_update_settings→ connectionLevels + enrichment (REQUIRED before sequence)
campaign_create_sequence_from_steps → the touches
campaign_start          → live
```

---

## Step 0 — account lookup

`linkedin_account_list` returns all connected accounts. Copy the `uuid`. If several
come back, identify by name/email with the operator, do not pick blindly.

---

## Step 1 — create campaign

`campaign_create`
- `linkedinAccountUuid`
- `campaignName` — name it after the signal, not "Campaign 1"

Returns `campaignUuid`. Save it.

---

## Step 2 — add prospects (pick one method)

All take `linkedinAccountUuid` and `campaignUuid` plus method-specific fields.

| Source | Tool | Key field |
|---|---|---|
| Bulk CSV (GetLeads export) | `prospect_upload_csv` | `filePath` |
| Single by profile URL | `prospect_add` | `profileUrl` |
| Single by details | `prospect_add` | `firstName`, `jobTitle`, `companyName` |
| LinkedIn search URL | `prospect_import_linkedin_search` | `searchUrl` |
| Sales Navigator URL | `prospect_sync_sales_nav` | `searchUrl` |
| Post commenters | `prospect_import_post_comments` | `searchUrl` (post URL) |
| Post reactors | `prospect_import_post_reactions` | `searchUrl` (post URL) |
| Group members | `prospect_import_linkedin_group` | `searchUrl` (group URL) |
| Event attendees | `prospect_import_linkedin_event` | `searchUrl` (event URL) |
| Recruiter Lite | `prospect_import_recruiter_lite` | `searchUrl` |

Optional on most imports: `collectContactInfo`, `enrichData`.

`searchUrl` and `searchType` persist automatically. Never pass them into
`campaign_update_settings`.

---

## Step 3 — campaign settings

`campaign_update_settings`

| Field | Type | Notes |
|---|---|---|
| `connectionLevels` | string[] | **REQUIRED. Omitting it throws HTTP 500.** Default `["2nd","3rd"]` for cold/search campaigns. Gates which sequence steps are legal. |
| `enrichData` | boolean | Auto-enrich before the sequence runs |
| `premiumOnly` | boolean | Restrict to LinkedIn Premium members |
| `dontAddIfInAnotherLinkedinAccountForMyUser` | boolean | De-dup across the operator's other accounts |
| `fetchAllAtOnce` | boolean | Fetch the whole list up front instead of streaming |
| `groupMessageUrl` / `eventMessageUrl` | string \| null | Only for group/event message steps |

Example:
```
campaign_update_settings
  linkedinAccountUuid: "..."
  campaignUuid: "..."
  connectionLevels: ["2nd", "3rd"]
  enrichData: true
  dontAddIfInAnotherLinkedinAccountForMyUser: true
```

---

## Step 4 — sequence

`campaign_create_sequence_from_steps`

**Step types and the connection level each needs:**

| sequenceStepType | Needs |
|---|---|
| SEND_CONNECTION_REQUEST | 2nd / 3rd |
| SEND_CONNECTION_REQUEST_BROWSER | 2nd / 3rd |
| SEND_MESSAGE | 1st only |
| SEND_MESSAGE_IN_MAIL | 2nd / 3rd (premium) |
| SEND_VOICE_MESSAGE | 1st only |
| SEND_VIDEO_MESSAGE | 1st only |
| SEND_MESSAGE_USING_GROUP | any |
| SEND_MESSAGE_USING_EVENT | any |
| VIEW_PROFILE | any (soft touch) |
| FOLLOW | any |
| LIKE_POST | any |
| LIKE_AND_COMMENT | any |
| ENDORSE | 1st only |
| INVITE_TO_EVENT | 1st only |

**Per-step inputs:** `stepOrdinal`, `day`, `hours`, `sequenceStepType`, `title`,
`messageBody` (with merge tags), plus optional media and A/B variants.

**Merge tags — always double curly braces:**
`{{firstName}}`, `{{lastName}}`, `{{companyName}}`, `{{jobTitle}}`,
`{{avatarFirstName}}` (sender's name), `{{aiVariableName}}` (from an AI variable).
Single braces do not resolve and go out as literal text.

**Worked example — cold sequence (2nd/3rd degree):**
```
campaign_create_sequence_from_steps
  linkedinAccountUuid: "..."
  campaignUuid: "..."
  campaignFamily: "LINKEDIN"
  sequenceSteps: [
    { stepOrdinal: 1, day: 0, sequenceStepType: "SEND_CONNECTION_REQUEST",
      title: "Connection request",
      messageBody: "Hi {{firstName}}, saw you lead {{jobTitle}} at {{companyName}}. [specific line]. Worth connecting." },
    { stepOrdinal: 2, day: 2, sequenceStepType: "SEND_MESSAGE",
      title: "First message after accept",
      messageBody: "Thanks for connecting {{firstName}}. [reference the trigger, no pitch]" },
    { stepOrdinal: 3, day: 5, sequenceStepType: "SEND_MESSAGE",
      title: "Qualifying question",
      messageBody: "[one question a bad-fit answers differently than a good-fit]" }
  ]
```

Note: `SEND_MESSAGE` steps come after the connection request, because they need a
1st-degree connection that only exists once the request is accepted.

---

## Step 5 — start

`campaign_start`
- `linkedinAccountUuid`
- `campaignUuid`
- `hasInviteMessage` — true if touch 1 is a connection request carrying a note

---

## Account-level settings (once per account, step 0b)

Separate scope from campaign settings. These protect the account across every campaign.

```
setting_quota          linkedinAccountUuid, dailyViewQuota, dailyConnectQuota, dailyMessageQuota, dailyFollowQuota
setting_add_schedule   linkedinAccountUuid, name, startTime, endTime, timeZone, maxProspects
setting_pending_invite linkedinAccountUuid, dailyConnectQuota, dailyMessageQuota   (optional)
setting_update_blacklist linkedinAccountUuid, companyNames: [...] or profileUrls: [...]   (optional)
```

Sensible starting quotas for a warmed account: view ~100, connect ~25, message ~50,
follow ~20 per day. Newer accounts should start lower.

---

## Reading results back (for the reply assistant and report)

```
inbox_get_messages    linkedinAccountUuid, isUnread: true
inbox_send_message    linkedinAccountUuid, prospectUuid, messageToSend   (reply assistant hands drafts over, does not auto-send)
inbox_tag_chat        linkedinAccountUuid, threadId, tags: ["hot-lead"]
dashboard_daily_stats linkedinAccountUuid, campaignUuids: ["..."], weeksBack: 4
campaign_stats        linkedinAccountUuid, campaignUuid
```

---

## Environment

`SR_API_KEY` is required and is set in `.mcp.json`. The MCP is built for Node 18; if
tools fail to load, a Node version mismatch is the most common cause. See `README.md`.
