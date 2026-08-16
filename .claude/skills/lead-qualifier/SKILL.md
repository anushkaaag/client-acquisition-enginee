---
name: lead-qualifier
description: Scores leads against a defined ICP, assigns a heat rating with reasoning, and routes qualified ones into a queue. Use this skill ANY time someone pastes a lead, a list of leads, a LinkedIn profile, a form fill, a GetLeads pull, or a list of people who engaged with their content and wants to know who is worth pursuing. Also trigger when someone asks "is this a good lead", wants leads sorted or prioritised, asks who to follow up with first, or needs a batch scored against their criteria. This is step 2 of the loop; it reuses the ICP strategist's criteria and feeds the campaign launcher.
---

# Lead qualifier

Decides who is worth time, and says why, so the answer can be argued with.

## Get the criteria before scoring anything

Scoring without stated criteria is just opinion with a number attached.

**First, check `workspace/icp-brief.md`.** If the ICP strategist has run, the fit
criteria, disqualifiers, and signal thesis are already there. Reuse them directly.
Do not make the operator define their ICP twice.

If no brief exists, establish the criteria before scoring a single lead:

**The fit criteria** (what makes someone right):
- role, seniority, or function
- company size band and industry
- geography, and specifically which kind: where the person is, where the office is,
  or where the company is headquartered
- anything about their situation that predicts need

**The disqualifiers** (what rules someone out regardless of fit):
- competitors
- existing customers and current pipeline
- company sizes that cannot afford or do not need the product
- regions that cannot be serviced
- roles with no budget and no influence

**The intent signals** (what makes someone timely rather than merely suitable):
- what they did, and how recently
- whether they initiated or merely appeared

If criteria do not exist yet, do not invent them. Say what is missing, propose a
starting set from whatever they have described, and get confirmation before scoring.

## Scoring

Three bands worth actioning, plus a disqualified pile. Resist adding more, because a
five-band system produces a pile in the middle that nobody actions.

**Hot.** Meets fit criteria, has a recent and specific intent signal, no
disqualifiers. Contact today, by a human, referencing the signal.

**Warm.** Meets fit criteria, no strong recent signal, no disqualifiers. Worth a
sequence, not worth dropping everything.

**Cold.** Fits loosely, or fits but with a weak or stale signal. Goes on a nurture
list. Not a rejection, a timing call.

**Disqualified.** Hits a disqualifier. State which one. Never bury a disqualification
in a low score, because someone will work the lead anyway.

Weight intent above fit when they conflict. A slightly-off-profile person who just did
something specific is usually a better use of an hour than a perfect-profile person who
has never heard of you.

## Output format

For a single lead:

```
[Name] - [Role] at [Company]
RATING: [hot / warm / cold / disqualified]

FIT: [what matches, what does not]
SIGNAL: [what they did and when, or "none"]
DISQUALIFIERS: [any, or "none"]

WHY THIS RATING: [one or two lines]
NEXT ACTION: [specific, with timing]
WHAT I'M UNSURE ABOUT: [anything that would change the rating]
```

For a batch, produce a table sorted hot to cold, then list the disqualified separately
with reasons, then give a short read on the batch as a whole: how many of each, what
the pattern was, and what that suggests about the source of the list. That last part is
often the most valuable output, because a batch that comes back 80% cold usually means
the targeting was wrong rather than the leads were bad.

## What to save

Write the scored queue to `workspace/qualified.md`, hot to cold, disqualified kept
separate. The campaign launcher reads this to decide who goes into a campaign. Never
let disqualified leads flow into the launcher's import.

## Routing to the campaign

The hot and warm leads are what the campaign launcher sends to. Hand the launcher a
clean list. Never route disqualified leads into a campaign, and keep them in a separate
list so the same person does not get re-qualified next month.

If the operator wants qualified leads written into a CRM (Notion, HubSpot, Airtable, a
Google Sheet) and a connector for it is available in the session, offer to write them
in, and confirm the destination and field mapping first. If no connector is available,
say so plainly and output a clean table or CSV. Do not describe routing as automatic
when it is not.

## The judgement calls that matter

**Do not treat seniority as a proxy for authority.** In small companies a Manager
often decides. In large ones a VP often cannot. Weight company size against title.

**A stale signal is not a signal.** Someone who liked a post four months ago is cold,
not warm, and treating them as warm produces outreach that references something they
have forgotten.

**Engagement is not intent.** Someone who comments on everything comments on
everything. Look for whether the engagement was specific to the topic that matters.

**Say when the criteria themselves look wrong.** If most leads in a batch fail on the
same criterion, name it. Sometimes the list is bad. Sometimes the ICP is. When it is
the ICP, that is a note for the next run of the strategist, so put it in the batch read.
