# The GTM Claude Playbook

One Claude Code agent that runs an entire outbound channel. Research → target →
qualify → write → send → reply → report, and the report feeds back into next week's
targeting.

Built by [Anushka Gupta](https://www.linkedin.com/in/anushka-gupta-linkedin/) in
collaboration with GetLeads.

---

## What this is

Most people run outbound as a pile of disconnected AI chats. One for the ICP, one for
the messages, one for the report, and every single time you have to re-paste the
context so each chat knows what the others already figured out.

This is the opposite. It is a single project you open in Claude Code once. Inside it
are seven skills and one brain (`CLAUDE.md`) that holds them together. The ICP you
agree on in step one is the fit criteria the qualifier uses in step three and the
targeting thesis the report attributes results to in step seven. You never explain
your business to it twice.

Two data layers are wired in:

- **GetLeads** — where the contacts come from. 402M B2B contacts, 179 fields each.
  The agent writes the search, GetLeads runs it. 5,000 contacts free, no card.
- **SalesRobot** — where the LinkedIn sending happens. The agent builds the campaign,
  loads the qualified list, sets the sequence, and starts it.

## The seven skills

| # | Skill | What it does |
|---|---|---|
| 1 | `icp-strategist` | Interviews you about your business, then writes a GetLeads filter set with a reason on every field. |
| 2 | `lead-qualifier` | Scores the list hot / warm / cold against your criteria, shows the reasoning. |
| 3 | `content-writer` | Posts that pull the right people in. Decides the funnel job before writing a word. |
| 4 | `dm-sequence-writer` | A three-touch sequence keyed off what a prospect actually did, not a merge tag. |
| 5 | `campaign-launcher` | Loads the qualified list and the sequence into SalesRobot and launches it. |
| 6 | `reply-assistant` | Classifies replies, drafts responses, flags when a conversation is call-ready. |
| 7 | `weekly-report` | Turns a week of stats into three changes for next week, attributed by signal. |

The output of one is the input to the next. They work standalone too, but the point is
the loop.

---

## Quickstart

You need [Claude Code](https://claude.com/claude-code) installed. If you have never
used it, the step-by-step PDF guide walks you through everything from zero, including
installing Node and cloning this repo.

```bash
# 1. clone this repo
git clone https://github.com/YOUR-USERNAME/gtm-claude-playbook
cd gtm-claude-playbook

# 2. copy the MCP config template and add your keys
cp .mcp.json.example .mcp.json
#    open .mcp.json and paste in your GetLeads and SalesRobot keys

# 3. open Claude Code in this folder
claude
```

That is it. Claude Code reads `CLAUDE.md` and `.claude/skills/` automatically, and
connects both MCP servers from `.mcp.json`. Ask it *"who should I be targeting?"* and
the ICP strategist takes it from there.

---

## Getting your two keys

**GetLeads.** Sign up at [getleads.io](https://getleads.io) (5,000 contacts free, no
card). Generate an API key at `app.getleads.io/api-keys`. The `glb_live_…` secret
shows only once, so copy it straight away.

**SalesRobot.** In SalesRobot, go to Settings → API Key. A LinkedIn account has to be
connected inside SalesRobot before any campaign can run on it.

Paste both into `.mcp.json`. That file is gitignored, so your keys never get committed.

---

## The one setup detail that costs people an hour

Claude Code does **not** read environment variables inside HTTP headers. It fails
silently, with no useful error. So in `.mcp.json`, paste the **literal** GetLeads key
into the `Authorization` header. Do not use `$GETLEADS_KEY` or any variable. This is
the single most common setup mistake.

SalesRobot's key goes in the `env` block, where variables do work normally.

SalesRobot's MCP is built for **Node 18**. If its tools do not load, a Node version
mismatch is almost always why. The PDF guide has the fix.

---

## Confirming it worked

Open Claude Code in the folder and ask:

> Using the GetLeads MCP, how many credits do I have remaining?

A number back means GetLeads is live. Then:

> List my connected LinkedIn accounts

An account back means SalesRobot is live.

---

## How the loop connects

```
   ┌──────────────────────────────────────────────────┐
   │                                                    │
   ▼                                                    │
1. ICP STRATEGIST      ── writes the GetLeads search    │
   ↓ the list                                           │
2. LEAD QUALIFIER      ── scores hot / warm / cold      │
   ↓ the queue                                          │
3. CONTENT WRITER      ── pulls the right people in ────┤ engagers
   ↓ posts                                              │ feed the
4. DM SEQUENCE WRITER  ── writes the touches            │ qualifier
   ↓ the sequence                                       │
5. CAMPAIGN LAUNCHER   ── sends it via SalesRobot       │
   ↓ live campaign                                      │
6. REPLY ASSISTANT     ── drafts replies, flags calls   │
   ↓ conversations                                      │
7. WEEKLY REPORT       ── which signal actually worked ─┘
        feeds back into next week's targeting
```

Everything the agent produces is written to `workspace/` as it goes, so context
persists across steps without any copy-paste. That folder is gitignored: it is your
live data, not part of the shipped playbook.

---

## Where it sends, and where it only drafts

This version sends, because the sending step is where the loop was breaking. But the
line still matters:

- The **campaign launcher** starts a campaign only after you have seen the list, the
  settings, and the sequence and said go. It does not launch anything you have not
  looked at.
- The **reply assistant** drafts and flags. It never auto-sends a reply. A reply thread
  is where a small mistake becomes a real one.

---

## Make it yours

Edit the skills, rename them, adapt them to your own vocabulary and ICP. The two most
worth editing are the fit criteria the qualifier uses and the voice section in the
content writer, because both of those are yours and nobody else's.

If you build something better on top of one, say so publicly. That is the whole point
of shipping these.

---

Built in collaboration with GetLeads. 5,000 contacts free at
[getleads.io](https://getleads.io), no card required.
