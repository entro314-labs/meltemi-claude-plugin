---
name: sync-check
description: Say whether changes made in Meltemi have reached the mail server yet. Use when the person asks if something went through, why an archived or labelled conversation still looks unchanged on their phone or in webmail, whether Meltemi is in sync, or after a batch of changes made while the app may have been closed.
---

# Checking what has reached the server

Changes to a conversation (read, star, marker, label, archive, trash, snooze) are written on the
person's computer first. The running Meltemi app then delivers them to the mail server. While
the app is closed they wait in a queue; they are not lost, and they are not on the server yet.

`pending_ops_summary` reads that queue. It takes no arguments and needs the `read` scope.

## 1. Call it

`pending_ops_summary` returns four numbers:

- `pending`: changes waiting to be delivered.
- `executing`: changes being delivered now.
- `failed`: changes whose delivery failed.
- `oldestPendingMs`: how long the oldest waiting change has waited, in milliseconds; `null` when
  nothing is waiting.

## 2. Say what it means

1. All three counts are zero: nothing is waiting and nothing has failed.
2. `pending` above zero: that many changes are on this computer and not yet on the server. Give
   the age of the oldest in minutes or hours. Ask whether Meltemi is open, because only the
   running app delivers them.
3. `executing` above zero: delivery is under way. Checking again shortly will show it.
4. `failed` above zero: those changes are not applied on the server. Say so plainly, and ask the
   person to look in Meltemi.

## 3. What it cannot tell you

- It gives counts. It does not say which conversations or which changes are waiting.
- The queue holds every change waiting on this computer, including ones the person made by hand
  in the app, so a number above zero may come from their own changes.
- A draft saved with `save_draft` stays in Meltemi and is never in this queue.
- A message sent with `send_message` goes out during that call. Its own result said whether it
  was delivered.

## 4. What to do, and what to leave

- Do not repeat a change to push it through. It is already queued, and calling again does not
  deliver it sooner.
- Do not call `pending_ops_summary` in a loop. Check once, report, and check again when the
  person asks.
- If the person wants a waiting change taken back, `revert_action` with its `actionId` works
  whether or not the change has reached the server.
