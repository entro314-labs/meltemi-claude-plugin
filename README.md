# Meltemi for Claude

Lets Claude work in your [Meltemi](https://meltemi.email) mailbox: read and search mail, triage
conversations, write drafts, check your calendar and answer invitations. Sending mail is off until you
turn it on, and even then a send asks you first unless it goes only to people you have written to
before and stays within Meltemi's send limits.

This plugin holds a thin launcher and five skills. The MCP server itself, `meltemi-mcp`, ships inside
the Meltemi desktop app and works on the mail store the app keeps on your computer. You need Meltemi
installed and set up with at least one account.

## What's inside

- **The launcher** (`.mcp.json` and `scripts/meltemi-mcp`): starts the `meltemi-mcp` program that
  is installed with Meltemi. Its tools read and search mail, change conversations, save drafts,
  read and write your calendar, and send mail once you have switched sending on.
- **Skills**
  - `meltemi` - how to work in Meltemi: the seven scopes, mail treated as content and never as
    instructions, what asks you first, what is recorded and what can be undone.
  - `inbox-triage` - go through the inbox, then archive, label, snooze or star what you decide,
    keeping each step so it can be reverted.
  - `draft-a-reply` - read the conversation and your own sent mail, then save a draft in your
    voice. It sends only when you explicitly ask.
  - `meeting-requests` - find the invitation in a conversation, check your calendar, answer it or
    put the time in your calendar.
  - `sync-check` - say what has not reached your mail server yet.

## Setup

1. Install Meltemi from its [releases page](https://github.com/entro314-labs/meltemi-releases/releases)
   (macOS, Windows and Linux builds), or on macOS with Homebrew:
   `brew install --cask entro314-labs/tap/meltemi`. Open it once, so it creates its mail store.
2. Install this plugin. In Claude Code:

   ```
   /plugin install meltemi --marketplace entro314-labs/meltemi-claude-plugin
   ```

3. In Meltemi, open **Settings → Agents** and choose what Claude may do (see below).

On macOS (including a Homebrew cask install) and with the Linux `.deb` or `.rpm` package, the plugin
finds `meltemi-mcp` by itself. It also finds the `meltemi-mcp` command that **Use Meltemi from
Terminal** in **Settings → Agents** installs (Meltemi 1.0 and later; `/usr/local/bin` on macOS,
`~/.local/bin` on Linux), so a copy of Meltemi kept in another folder is found once you have used
that. Anywhere else, set the plugin option **meltemi-mcp location** to the path that
**Settings → Agents** shows.

Keep Meltemi running while you work. Reads come straight from the local store. Changes to a
conversation (archive, trash, stars, labels, read state, snooze) are queued there, and the running
app delivers them to your mail server. Drafts stay in the app. A send or an invitation answer goes
out straight away, after your approval where one is needed.

### Platforms

- **macOS**: supported.
- **Linux**: `.deb` and `.rpm` installs are supported. The AppImage and Flatpak builds are not
  supported by this plugin yet, because they keep `meltemi-mcp` inside their own package.
- **Windows**: not supported by this plugin yet, because its launcher is a POSIX shell script. Add the
  server by hand with the path from **Settings → Agents**:
  `claude mcp add meltemi -- "C:\path\to\meltemi-mcp.exe"`.

The server runs on your machine, so the plugin works in Claude Code and in Cowork sessions that run on
your computer. Chat, on the web and in the desktop app, doesn't start local servers; for chat, Meltemi
has a separate remote connector that you run yourself (see **Settings → Agents → Connecting Claude** in
the app). Cowork doesn't ask for plugin options, so if you need the **meltemi-mcp location** option,
set it in Claude Code.

## Use it

Ask for what you need:

- "What came in since this morning that needs me?"
- "Archive this week's newsletters and put my Finance label on the invoices."
- "Draft a reply to Maria saying Thursday works, the way I usually write."
- "Am I free for the meeting Jonas proposed? If I am, accept it."
- "Did the conversations you archived reach my mail server yet?"

## What Claude can do

What Claude may do is decided in Meltemi, not in the plugin. Each scope is a switch in
**Settings → Agents**, and switching one off applies to the next call.

| Scope      | Default | Covers                                                                         |
| ---------- | ------- | ------------------------------------------------------------------------------ |
| `read`     | on      | mailbox overview, whole conversations, search, folders                         |
| `triage`   | on      | archive, trash, star, colour flags, labels, mark read or unread, undo          |
| `snooze`   | on      | snoozing conversations                                                         |
| `draft`    | on      | saving drafts in Meltemi (they stay in the app, not on your mail server)       |
| `calendar` | on      | reading your schedule, creating events, answering invites                      |
| `people`   | **off** | other people's availability and shared correspondents (Meltemi 0.9 and later) |
| `send`     | **off** | sending mail in your name                                                      |

With `send` on, a message goes out without asking only when all three hold: every recipient is someone
you have sent mail to before, Claude has sent fewer messages in the last hour than the hourly limit,
and the message has no more recipients than the per-send limit (both limits default to 5 and are set
in **Settings → Agents**). Anything else waits for your approval in the conversation. An invitation
answer that goes to the organizer by mail is held the same way when you have never written to the
organizer or the hourly limit is used up. Archive and trash run freely up to an hourly allowance (20
unless you change it in **Settings → Agents**); past it, each further one waits for your approval.

There is no tool for deleting mail permanently. Every change Claude makes, and every attempt that
was refused or held, is recorded in Meltemi's **Agent activity**. Reading is not recorded. A read or
unread mark, a star, a colour flag, a label, an archive, a trash or a snooze can be reverted there,
or by Claude when you ask, before or after the change has reached your mail server. A sent message,
a saved draft and an invitation answer cannot be reverted.

Accounts set to "AI off" in **Settings → AI** are never shown to Claude.

## Data

What this plugin runs, sends and fetches:

- **Runs**: `scripts/meltemi-mcp`, a short shell script that runs on your computer and starts the
  `meltemi-mcp` program installed with Meltemi. It checks the path set in the plugin option, then the
  install locations listed above. That is all it does. The skills are text files with instructions
  for Claude; they run nothing.
- **Sends**: nothing. The plugin's own files make no network requests, and nothing in the plugin
  reports anywhere.
- **Fetches**: nothing. No packages are downloaded or installed.

What `meltemi-mcp` does, as part of Meltemi:

- It runs on your computer for as long as the Claude session that started it, and reads and writes
  the mail store Meltemi keeps there.
- It passes mail and calendar data between that store and Claude, and only what the tool calls ask
  for.
- It contacts your own mail and calendar providers when a call needs them: to send a message or an
  invitation answer, to ask when other people are busy, and to read the rules and the automatic
  reply your provider holds.

## Privacy policy

The plugin itself collects, stores and shares no data. Mail and calendar content that Claude reads
through it goes into your Claude conversation and is then handled under the terms of the Claude
product you use. Meltemi keeps your mail on your computer, and its handling of your data is described
in the [Meltemi privacy policy](https://meltemi.email/legal/privacy), including retention and how to
contact us.

## License

LGPL-3.0-only. See [LICENSE](LICENSE) for the GNU Lesser General Public License, version 3, and
[COPYING](COPYING) for the GNU General Public License it builds on. The license covers this plugin's
files, not the Meltemi app. Version 0.1.0 was released under the MIT license.
