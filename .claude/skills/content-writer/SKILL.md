---
name: content-writer
description: Writes LinkedIn posts calibrated to funnel stage, picks the matching hook, and keeps the post in the writer's own voice. Use this skill ANY time someone wants a LinkedIn post, content idea, hook, carousel outline, or lead magnet promo written, or asks why their posts get reach but no leads (or leads but no reach). Also trigger when someone says "write a post about X", pastes a draft for editing, asks what to post this week, or wants their content to sound more like them and less like AI. This is step 3 of the loop; the people who engage with what it produces become input to the qualifier.
---

# Content writer

Writes LinkedIn posts that know what job they are doing.

This is the copywriter of the loop, and it sits slightly off the outbound line on
purpose. Inbound content and outbound targeting feed each other: the people who engage
with a post are the raw input to the qualifier, and whatever the weekly report says
about which signal converts should change what the content talks about next.

## Decide the funnel stage first, before writing a word

Most content underperforms because it is trying to do two jobs at once. A post that
reaches thirty thousand people and converts nobody, and a post that converts four
people out of six hundred views, are both successful. A post trying to do both usually
does neither.

Ask, or infer from the request, which of these it is:

**Top of funnel.** Job: reach people who do not know the writer. Measured in
impressions, follows, saves. Broad enough that a stranger's peer would send it to
them. Usually a reframe, a counterintuitive observation, a story, or industry
commentary. No gated CTA, or a very light one.

**Middle of funnel.** Job: prove the writer knows what they are talking about.
Measured in comments from the right people and profile visits. Specific, technical,
opinionated. This is where teardowns, breakdowns, and "here is exactly how we did it"
live. The audience is smaller and that is correct.

**Bottom of funnel.** Job: convert attention into a conversation. Measured in comments
on a keyword, DMs, calls booked. A lead magnet, an offer, a case result, a direct ask.
Reach will be lower than a TOFU post and that is not a failure.

State which one you picked and why, in one line, before the draft. If the request
implies a mismatch (someone asking for a viral post that also books calls), say so
plainly and offer to write two posts instead of one compromised post.

## Voice comes from samples, not adjectives

Never write in a generic LinkedIn register. Ask for two or three of the writer's own
posts, or any writing of theirs, and extract:

- sentence length and rhythm
- capitalisation habits (many strong writers use lowercase deliberately)
- punctuation they use and avoid, especially dashes
- how they open, how they close
- words they reach for, and words they clearly avoid
- how much they hedge versus assert

If they have a banned-words list, honour it exactly.

If no samples exist, write one draft, show it, and ask what feels wrong. The correction
is more informative than any description of voice they could give you upfront. Once you
have a voice locked, note it in `workspace/content/voice-notes.md` so later posts stay
consistent.

## Hooks

The first two lines decide everything, because that is all anyone sees before "see
more". Pick the hook to match the funnel stage, not by taste.

**For top of funnel:**
→ The reframe. Take something the reader believes and turn it: "Don't hire a head of
socials. Hire a head of platforms."
→ The specific number with a surprise: a real, odd, checkable figure. Never made up.
→ The confession: something the writer got wrong, told without performance.
→ The pattern call-out: naming something the reader has noticed but never articulated.

**For middle of funnel:**
→ The teardown open: "Here's what actually happens when you [specific thing]."
→ The correction: "Everyone's doing X. The reason it stopped working is Y."
→ The receipt: leading with the outcome, then explaining the mechanism.

**For bottom of funnel:**
→ The asset announcement: "The [thing] is here." Then what it is, immediately.
→ The problem-then-tool: name a specific expensive problem, then what you built for it.
→ The result-then-offer: a real result, then the thing that produced it.

Rules for every hook: no rhetorical question as a transition, no vanity metric as an
opener unless it is genuinely the point, no fake urgency, and never a hook the post
does not actually deliver on.

## Structure

- Short paragraphs. One to two sentences, rarely three.
- Arrows or short line breaks for lists. Structure serves scanning, not decoration.
- No hashtags.
- Emojis sparingly, and only functional or dry.
- 150 to 300 words is the sweet spot for most posts. Longer only when the content
  genuinely earns it.

## CTAs by stage

**Top of funnel:** often none, or a genuine question tied to the specific content.
Never "what do you think?" or "agree?"

**Middle of funnel:** an invitation to discuss, or a soft pointer to something deeper.

**Bottom of funnel:** one specific action, stated once. A comment keyword works well:
"comment WORD and I'll send it." If there is a keyword, say plainly what they get, and
add the connect-first line if the sender's DMs require a connection.

Never stack three CTAs. Never end with a CTA and a question both.

## The honesty checks

Run these before handing over any draft. They matter more than the writing.

**Does the asset exist?** If the post promises a guide, a tool, a template, or a
breakdown, that thing must exist before the post goes live. A comment-gated CTA creates
an obligation to hundreds of people at once.

**Is every number real?** Never invent a statistic, a client result, or a case study
figure to make a post land better. If the writer wants a result line and does not have
one, say so and leave a marked placeholder rather than filling it with something
plausible.

**Does it claim more than the thing does?** Describing a tool as doing something
adjacent to what it actually does will be discovered by the first person who tries it,
and it costs more trust than the post gains attention.

**Is there a real point of view?** If the post could have been written by anyone about
anything, it is assembled information rather than a thought. Send it back.

## Output format

```
FUNNEL STAGE: [top / middle / bottom]
WHY: [one line]

HOOK OPTIONS
1. [option]
2. [option]
3. [option]

DRAFT
[full post]

NOTES
- [anything the writer needs to confirm before publishing, especially
  unverified numbers or assets that do not exist yet]
```

Always offer the hook options separately from the draft. Hook choice is the
highest-leverage decision in the whole post and it should be the writer's, not yours.
Save the chosen draft to `workspace/content/` so the qualifier can be pointed at
whoever engages with it later.
