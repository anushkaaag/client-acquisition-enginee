---
name: dm-sequence-writer
description: Writes personalised LinkedIn DM and connection-request sequences keyed off a prospect's own activity rather than merge tags. Use this skill ANY time someone wants to write outreach, a DM sequence, a connection request, a follow-up message, or asks why their reply rates are low or their messages sound templated. Also trigger when someone pastes a prospect's profile, post, or company info and wants a message written, or asks for a "sequence", "cadence", "touches", or "follow-ups" without naming the format. This is step 4 of the loop; its output is what the campaign launcher loads into SalesRobot.
---

# DM sequence writer

Writes outreach that reads like one person wrote it for one other person, because it
was.

## The distinction that decides everything

There are two kinds of outreach and they need completely different sequences.

**Warm:** the prospect has done something. Engaged with a post, viewed a profile,
attended an event, joined a group, replied to something, appeared in a comment thread.
There is a real, referenceable reason you are in their inbox.

**Cold:** nothing has happened. You found them in a database.

Ask which one this is before writing anything. If the answer is cold, say plainly that
the ceiling is lower and the sequence has to work harder, and write the cold version
rather than pretending a database row is a warm signal. Fake warmth ("I loved your
recent post!" about a post they did not write) is the single fastest way to get marked
as automation.

## What you need before writing

Ask for whatever is missing. Do not invent it.

Required:
- what the sender does, in one plain sentence
- who the prospect is: name, role, company
- warm or cold, and if warm, **the specific thing they did**
- the actual ask: a call, a reply, a resource, a referral

Strongly preferred:
- one or two lines of the sender's own writing, so the voice matches them rather than
  defaulting to LinkedIn-broetry (check `workspace/content/voice-notes.md` if the
  content writer already captured a voice)
- what the sender has seen work or fail before
- anything real about the prospect: a post they wrote, their bio, a company change, a
  public complaint

If someone gives you only a name and a title, say what is missing and what the message
will cost them without it. A message written off a title alone is a template with a
name in it, and it will perform like one.

## Sequence structure

Three touches is the default. More than five and you are pestering, fewer than three
and you are quitting before most replies happen.

**Touch 1, within 24 hours of the trigger if warm.**
Reference the specific thing. Not "I saw your post" but the actual argument they made
and one genuine reaction to it. No pitch. No ask beyond a reply. The goal of touch 1 is
a conversation, not a meeting.

**Touch 2, three to four days later.**
One qualifying question, tied to what they said or to their situation. This is where
you find out whether they are worth pursuing, so make the question one that a bad-fit
prospect answers differently than a good-fit one. Still no pitch.

**Touch 3, four to seven days later.**
The offer, and a soft call ask. Specific about what the call is for and how long.
"worth 15 minutes to walk through how X handled this?" beats "would love to hop on a
quick call to explore synergies."

**Optional touch 4, the honest close.**
Short. Names that they have not replied, without guilt-tripping. Leaves a door open.
This one gets more replies than people expect, because it is the only message in most
inboxes that is not trying.

## Length and format rules

- Touch 1 under 400 characters. Long first messages read as mass sends.
- One idea per message. Two questions in a message means neither gets answered.
- No links in touch 1, and often not until touch 3.
- No attachments in a first message.
- Line breaks between thoughts. A wall of text on mobile does not get read.
- Match the sender's actual register. If they write in lowercase, write in lowercase.

## Writing for the campaign launcher

The next step, the campaign launcher, loads these messages into SalesRobot. Two things
make that handoff clean:

**Use SalesRobot merge tags where a variable belongs, with double curly braces.**
`{{firstName}}`, `{{companyName}}`, `{{jobTitle}}`. Single braces do not resolve and go
out as literal text. Only use a merge tag where a real variable belongs; a message that
is nothing but merge tags is a template, which is the thing this skill exists to avoid.

**Match the touch types to the connection state.** A connection request with a note is
the first touch for a 2nd or 3rd-degree prospect. A direct message only works once
they are a 1st-degree connection. So a cold LinkedIn sequence is usually: connection
request with a note, then messages after they accept. Say which touch is a connection
request and which is a message, so the launcher maps them to the right SalesRobot step
types.

## Personalisation that actually is personalisation

The test: could this exact message be sent to a hundred other people with the name
swapped? If yes, it is not personalised, regardless of how many merge fields it has.

Real personalisation references something **only true of this person right now:**
→ the specific claim they made in a post, and where you disagree or want to push
→ a change at their company they would recognise as recent
→ something in their own bio in their own words
→ a public complaint, question, or gap they voiced

Not personalisation:
→ their name in the first line
→ their company name in the subject
→ "I see you're in [industry]"
→ congratulating them on a work anniversary
→ "hope this finds you well"

## Banned openers

These read as automation instantly, regardless of what follows:

- "Hope this finds you well"
- "I came across your profile"
- "I hope you don't mind me reaching out"
- "Quick question for you"
- "I'll keep this brief" (then does not)
- "We help companies like yours..."
- "Are you the right person to speak to about..."
- Any opener that is a compliment with no specific object

## The output format

Produce the sequence like this, every time:

```
SEQUENCE: [prospect name or segment], [warm/cold], trigger: [what they did, or "none, cold"]

TOUCH 1 - [connection request / message] - send [timing]
[message]
why this works: [one line]

TOUCH 2 - [message] - send [timing]
[message]
why this works: [one line]

TOUCH 3 - [message] - send [timing]
[message]
why this works: [one line]

IF THEY REPLY WITH:
- interest → [what to send]
- an objection → [what to send]
- "not now" → [what to send, and when to come back]

WHAT WOULD MAKE THIS SHARPER
[one honest line about what extra information would improve the sequence]
```

Save the sequence to `workspace/sequences/`. The campaign launcher reads from there.

## Failure modes to watch for in your own output

**Pitching in touch 1.** The most common failure. If touch 1 mentions what the sender
sells, rewrite it.

**A question the prospect has no reason to answer.** "What's your current process for
X?" is work for them and value for you. Make touch 2 a question they would enjoy
answering or have an opinion about.

**Sounding smarter than the sender.** If the message uses vocabulary the sender would
not use, it reads as ghostwritten and the call goes badly when the real person shows up.

**Sequences that ignore the reply.** If the prospect responded to touch 1, touch 2 must
respond to *them*, not continue the pre-written script. Say this out loud when handing
over the sequence, and note that the reply assistant takes over the moment someone
writes back.
