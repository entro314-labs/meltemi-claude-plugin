---
name: meeting-requests
description: Handle a meeting request in someone's Meltemi - find the invitation in a conversation, check their calendar, answer the invitation, or put the time in their calendar. Use when they ask whether they are free, to accept, decline or tentatively accept an invitation, to find a time with someone, or to add an event that came up in their mail.
---

# Meeting requests

## 1. Find the invitation

1. Find the conversation with `search_messages`, `list_messages` or `get_snapshot`.
2. `get_thread` on it. A calendar invitation that came with the conversation is returned under
   `invites`, already structured: `eventId`, `title`, `startMs`, `endMs`, `allDay`, `location`,
   `organizerEmail`, `attendees`, and `myPartstat`, the person's current answer.
3. Take the time from `invites`. Do not work it out from the wording of the message.
4. Read the rest of each entry: `status` says whether the event is confirmed, tentative or
   cancelled; a `sequence` above zero means the organizer has updated the invitation since it was
   first sent; `rrule` is set when the event repeats.

If there is no `invites` entry, the message only proposes a time in words. There is nothing for
`respond_to_invite` to answer. Check the calendar (step 2), then write back with the
`draft-a-reply` skill, or put the time in the calendar (step 6).

## 2. Check the person's calendar

`list_calendar_events` with `fromMs` and `toMs` returns every occurrence that overlaps the window,
with repeating events expanded. `toMs` is exclusive. A window with no overlapping occurrence is
free. It needs the `calendar` scope.

Look at the proposed window, and at the hours around it if the person asked how their day looks.

## 3. Other people's availability, where allowed

`get_availability` says when other people are busy. It needs the `people` scope, which is off
until the person turns it on in Meltemi → Settings → Agents.

- If it returns `FORBIDDEN`, say that other people's availability is switched off, and carry on
  with the person's own calendar.
- Pass up to 20 `addresses` with `fromMs` and `toMs`.
- Only people on a calendar server the person also uses can be checked.
- `busy: null` for someone means unknown. It never means free. Say you could not see their
  calendar; do not propose a time on their behalf.

## 4. Tell the person

Say what is proposed, by whom and when, whether they are free, and what it would clash with. If
they have not said how to answer, ask.

## 5. Answer the invitation

`respond_to_invite` with the `eventId` from `invites` and a `response` of `accepted`, `tentative`
or `declined`.

- Answer only with what the person told you. The organizer is notified in their name, and
  `revert_action` cannot take an answer back.
- An invitation on a synced calendar (`synced` true) is answered through the calendar server. One
  that came by mail is answered with a reply mailed to the organizer.
- A mailed answer asks the person first when the organizer is someone they have never written to,
  or when the hourly send limit is used up. They see the event's title, time and organizer.
- If the call returns `CONFIRMATION_REQUIRED`, this connection cannot ask them. Tell them to
  answer the invitation in Meltemi.
- `comment` is a short note for the organizer. It travels only with a mailed answer.
- This sends the accept or decline alone. A reply in words is a separate draft: follow the
  `draft-a-reply` skill.

## 6. Put time in the calendar

`create_calendar_event` makes a single event on the person's local calendar: `title`, `startMs`
and `endMs` (after the start), and optionally `allDay`, `location` and `notes`.

- Nothing is mailed and nobody is invited. If the person expects others to receive an
  invitation, say that this does not send one.
- Pass `sourceThreadId` when the event comes from a conversation, so Meltemi links the two.
- The person can delete the event in Meltemi.
- Use it to hold time for something agreed in words, or when they ask for an entry. Do not add
  one on your own initiative.

## 7. Report

One line: what you answered or added, and for when.
