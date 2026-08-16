---
name: weekly-report
description: Turns a week of raw outbound and content numbers into a short plain-language report that says what changed, what caused it, and what to do differently next week. Use this skill ANY time someone wants a weekly or monthly GTM, outbound, pipeline, or content report, pastes campaign stats or a CSV export and asks what it means, asks how last week went, wants to know which list or signal performed best, or asks what to double down on. Also trigger for "make sense of these numbers" and end-of-week reviews. This is step 7 of the loop; it pulls SalesRobot stats, attributes results to the signal each campaign was built on, and feeds next week's targeting.
---

# Weekly GTM report

Turns numbers into a decision. A report nobody acts on is a chore, not a report. This
one is built to end in three specific changes for next week.

## What to gather

If the SalesRobot MCP is connected, pull the numbers directly rather than asking the
operator to summarise their own data:
- `dashboard_daily_stats` (accountUuid, campaignUuids, weeksBack) for activity
- `campaign_stats` (accountUuid, campaignUuid) per campaign

Read `workspace/campaigns.md` to know which signal each campaign was built on. That
mapping is what makes attribution possible.

Gather whatever exists. Do not stall on a complete dataset, because a partial report
delivered Friday beats a perfect one delivered never.

**Outbound, per campaign or list:** sent, accepted or delivered, replied, positive
replies, meetings booked; which list or signal each campaign came from; when it started.

**Content:** posts published, impressions, comments, profile views, followers gained,
and any comment-keyword CTA and how many actioned it.

**Pipeline:** new conversations opened, calls booked, calls held, opportunities
created, closed.

**Context that explains anomalies:** holidays, outages, account restrictions, a post
that unexpectedly took off, a list smaller than usual.

## Rates, not just counts

Raw counts hide everything. Always compute and lead with rates:

- reply rate = replies / delivered
- positive reply rate = positive replies / replies
- meeting rate = meetings / positive replies
- and the one most people skip: **meetings per hundred sent**, the only number that
  lets two differently-sized campaigns be compared honestly

A campaign that sent 80 messages and booked 3 meetings beat one that sent 900 and
booked 9. Say so explicitly, because volume is what people instinctively reward.

## Attribution by list and signal

This is the part that makes the report worth writing. Group results by **which list or
signal the campaign came from**, using the `signal` line in `workspace/campaigns.md`,
not just by campaign name.

The question being answered: is a particular targeting thesis working, or was one
campaign lucky? Report it as: this signal produced this reply rate across this many
sends. Then say whether the sample is big enough to mean anything. Under about 50 sends,
differences are usually noise, and saying so protects the operator from over-correcting
on a fluke.

## Statistical honesty

The most common failure in weekly reporting is treating a small swing as a trend.

- Name the sample size next to every rate. "12% reply rate" and "12% reply rate on 25
  sends" are different claims.
- Do not call a change a trend on one week of data. Two consecutive weeks in the same
  direction is a signal. One week is weather.
- If a number moved because the denominator moved, say that instead of implying the
  campaign improved.
- If something cannot be explained from the data, say it is unexplained rather than
  inventing a cause. A confident wrong explanation is worse than an open question.

## Output format

Keep the whole thing under a page. Nobody reads a long weekly report twice. Save it to
`workspace/reports/` dated, so week-over-week comparisons are possible.

```
WEEK OF [dates]

THE HEADLINE
[one sentence. what actually mattered this week.]

THE NUMBERS
[small table: metric, this week, last week, change]

WHAT WORKED
→ [specific thing, with the number that supports it, and sample size]

WHAT DIDN'T
→ [specific thing, with the number, stated plainly and without hedging]

BY SIGNAL / LIST
[which targeting thesis produced what, with sample sizes]

WHAT I'D CHANGE NEXT WEEK
1. [specific, actionable, tied to something above]
2. [specific, actionable]
3. [specific, actionable]

WHAT I CAN'T TELL FROM THIS DATA
[what is missing, and what to start tracking to answer it next week]
```

That last section is not filler. It is what makes the next report better, and it stops
the report from implying more certainty than the numbers support.

## Closing the loop

The "what I'd change next week" list is the input back into step 1. If a signal is
outperforming, the strategist should build more lists around it. If a signal is dead,
retire it. If the content that pulled the best-converting engagers was a particular
angle, the content writer should do more of that angle. Say this explicitly, because the
report is only worth writing if next week is different because of it.

## Tone

Plain. No congratulation, no doom. A bad week reported clearly is more useful than a bad
week softened, and the operator reading it already knows how it went. The value is in
the causes and the next move, not the framing.
