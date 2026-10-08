# Meltemi for Claude

Lets Claude work in your [Meltemi](https://meltemi.email) mailbox: read and search mail, triage
conversations, write drafts, check your calendar and answer invitations. Sending mail is off until you
turn it on, and even then a send asks you first unless it goes only to people you have written to
before and stays within Meltemi's send limits.

This plugin is a thin launcher. The MCP server itself, `meltemi-mcp`, ships inside the Meltemi desktop
app and works on the mail store the app keeps on your computer. You need Meltemi installed and set up
with at least one account.

## Setup

1. Install Meltemi from [meltemi.email](https://meltemi.email) and open it once, so it creates its
   mail store.
2. Install this plugin.
3. In Meltemi, open **Settings → Agents** and choose what Claude may do (see below).

On macOS (including a Homebrew cask install) and with the Linux `.deb` or `.rpm` package, the plugin
finds `meltemi-mcp` by itself. Anywhere else, set the plugin option **meltemi-mcp location** to the path
that **Settings → Agents** shows.

Keep Meltemi running while you work. Reads come straight from the local store; changes (archive,
drafts, sends, invitation answers) are queued there, and the running app delivers them to your mail
server.

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

## What Claude can do

What Claude may do is decided in Meltemi, not in the plugin. Each scope is a switch in
**Settings → Agents**, and switching one off applies to the next call.

| Scope      | Default | Covers                                                   |
| ---------- | ------- | -------------------------------------------------------- |
| `read`     | on      | mailbox overview, whole conversations, search, folders   |
| `triage`   | on      | archive, trash, star, colour flags, mark read or unread  |
| `snooze`   | on      | snoozing conversations                                   |
| `draft`    | on      | writing into your Drafts folder                          |
| `calendar` | on      | reading your schedule, creating events, answering invites |
| `send`     | **off** | sending mail in your name                                |

With `send` on, a message goes out without asking only when all three hold: every recipient is someone
you have sent mail to before, Claude has sent fewer messages in the last hour than the hourly limit,
and the message has no more recipients than the per-send limit (both limits default to 5 and are set
in **Settings → Agents**). Anything else waits for your approval in the conversation. There is no tool for deleting mail permanently. Every change Claude makes is recorded in
Meltemi's activity tray, and a change can be undone there until the app has synced it to your mail
server.

Accounts set to "AI off" in **Settings → AI** are never shown to Claude.

## What this plugin runs, sends and fetches

- **Runs**: `scripts/meltemi-mcp`, a short shell script that starts the `meltemi-mcp` program installed
  with Meltemi. It checks the path set in the plugin option, then the standard install locations listed
  above. That is all it does.
- **Sends**: nothing of its own. The plugin makes no network requests. `meltemi-mcp` passes mail and
  calendar data between Meltemi's local store and Claude, and only what the tool calls ask for.
- **Fetches**: nothing. No packages are downloaded or installed.

## Privacy policy

The plugin itself collects, stores and shares no data. Mail and calendar content that Claude reads
through it goes into your Claude conversation and is then handled under the terms of the Claude
product you use. Meltemi keeps your mail on your computer, and its handling of your data is described
in the [Meltemi privacy policy](https://meltemi.email/legal/privacy), including retention and how to
contact us.

## License

MIT. See [LICENSE](LICENSE). The license covers this plugin's files, not the Meltemi app.
