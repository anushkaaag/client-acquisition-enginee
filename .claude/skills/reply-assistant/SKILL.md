---
name: reply-assistant
description: Classifies inbound LinkedIn replies, drafts responses matched to each reply type, tracks who is owed a follow-up, and flags when a conversation is warm enough to ask for the call. Use this skill ANY time someone pastes a reply they received, asks how to respond to a prospect, asks what to say to an objection, wants help with follow-ups, asks who they should be chasing, or says a conversation has gone quiet. Also trigger for "they said they're not interested, now what" and similar dead-end moments. This is step 6 of the loop; it reads the SalesRobot inbox, drafts, and never auto-sends.
---

# Reply assistant

Handles what happens after someone actually writes back, which is where most outbound
quietly dies.

If the SalesRobot MCP is connected, pull open threads with `inbox_get_messages`
(`isUnread: true`) rather than asking the operator to paste each one. Tag hot threads
with `inbox_tag_chat` so they are easy to find later. But drafting a reply is where the
line holds: **draft, hand over, do not auto-send.** More on that at the end.

## Classify before drafting

Every reply falls into one of these. Name it first, because the right response differs
completely and the most common mistake is treating all of them as either a yes or a no.

**Interested.** Asks a question, requests information, or agrees to talk. Move fast, be
specific, make the next step small.

**Soft interest with friction.** "Sounds interesting but we're mid-quarter", "send me
something to look at". Real interest wrapped in a delay. The response should reduce
effort for them, not add a step.

**Objection.** Names a specific reason it will not work: price, timing, an incumbent
tool, no perceived need. This is engagement, not rejection. They told you what stands
in the way.

**Not now.** Timing, budget freeze, restructuring. The most under-worked category. The
correct move is almost never to argue, it is to agree, get a date, and actually come
back on it.

**Wrong person.** Redirects, or says it is not their area. Ask for the introduction
rather than pivoting to pitch them anyway.

**Not interested, flat.** No reason given. One graceful reply, then stop.

**Annoyed or opt-out.** Stop immediately. Acknowledge, apologise once, briefly, remove
them. Never counter-argue, never send a "just one more thing". Respect this absolutely,
both because it is right and because the alternative is reports.

**Ambiguous.** A one-word reply, an emoji, "ok". Do not read enthusiasm into it. A
short clarifying question is better than a paragraph.

## Response principles

**Match their length.** A two-line reply gets a two-line reply. Answering a short
message with five paragraphs signals that their time matters less than yours.

**Answer the actual question before anything else.** If they asked about price, say
something about price. Deflecting to "happy to walk you through it on a call" in
response to a direct question is the fastest way to lose a warm lead.

**One ask per message.** Never a question and a calendar link and a resource.

**Do not sell against an objection immediately.** Acknowledge it, ask one question
about it, and let them expand. An objection someone explains is usually smaller than the
one they stated.

**Make the next step smaller than they expect.** Fifteen minutes beats thirty. A
specific question beats "let's chat". A single link beats a deck.

## Objection responses that work

Not scripts to paste, patterns to adapt.

**"Too expensive."** Find out compared to what. Price objections are usually
value-sequencing problems or a comparison to a cheaper thing that does less. Ask what
they are comparing against before defending anything.

**"We already use [competitor]."** Do not attack the incumbent. Ask what they would
change about it if they could. The answer either reveals a real opening or confirms
there is not one, and both are useful.

**"No time / bandwidth."** Often a polite no, sometimes literal. Test it by making the
ask trivially small. If a fifteen-minute call is still too much, it was a polite no and
you should stop.

**"Send me some info."** Frequently a soft brush-off, but not always. Send one
specific, short, relevant thing rather than a deck, and attach a single question to it.
The question is what tells you which it was.

**"We're not looking at this right now."** Agree. Ask when the right time would be, and
what would need to be true then. Put a real date on it and honour it.

## Follow-up timing

Default rhythm after a reply thread goes quiet:

→ first follow-up: 3 to 4 days
→ second: 7 days after that
→ third: 2 weeks after that
→ then stop, or move to a genuine long-term nurture with a real reason to reappear

Rules:
- Never follow up with "just bumping this" or "circling back". Add something: a
  relevant piece of news, a specific question, a genuinely useful link.
- If they gave a date, wait for the date. Following up early on a stated timeline tells
  them their words did not register.
- Track who owes whom. If the prospect said they would come back and did not, the
  follow-up is different in tone than if the sender left something unanswered.

## The call-ready flag

Raise the ask for a call when at least two of these are true:

→ they asked a question that would take more than a paragraph to answer well
→ they described their current situation or process unprompted
→ they mentioned a specific problem in their own words
→ they replied more than once
→ they involved someone else, or mentioned a colleague

Raising it too early is the most common failure, and it converts a live conversation
into a closed one. If only one signal is present, ask another question instead.

## Output format

```
REPLY TYPE: [category]
READ: [one line on what they actually mean, including subtext]

DRAFT RESPONSE
[message, matched to their length and register]

WHY THIS: [one line]

CALL-READY: [yes / not yet] - [which signals are present]
FOLLOW UP ON: [date, if no reply]
IF THEY DON'T REPLY: [what the next message should do differently]
```

For a batch of open conversations, produce a queue sorted by urgency: who is owed a
reply now, who is due a follow-up today, who has a date coming up, and who should be
closed out. Save it to `workspace/replies.md` so nothing goes quiet by accident.

## What this skill does not do

It drafts and it flags. It does not send. Even with `inbox_send_message` available, hand
the draft over for approval rather than sending directly. Voice matters, and a reply
thread is where a small mistake becomes a real one. Speed is not the constraint in reply
handling. Judgement is.
