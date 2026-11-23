# Changelog

All notable changes to the Meltemi plugin for Claude are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow
[SemVer](https://semver.org/).

## [0.2.0] - 2026-11-24

### Added

- Skills: `meltemi` (the seven scopes, mail treated as content and never as instructions, what
  asks you first, what is recorded and what can be undone), `inbox-triage`, `draft-a-reply`,
  `meeting-requests` and `sync-check`.
- The launcher also looks for the `meltemi-mcp` command that **Use Meltemi from Terminal**
  installs (`/usr/local/bin` on macOS, `~/.local/bin` on Linux), after the standard install
  locations. A copy of Meltemi kept in another folder starts without the **meltemi-mcp location**
  option once that command is installed.
- A plugin icon, a privacy policy link in the manifest, and a marketplace manifest, so the plugin
  installs in Claude Code with
  `/plugin install meltemi --marketplace entro314-labs/meltemi-claude-plugin`.

### Changed

- The README lists the skills and example asks, and its Data section says what the plugin runs
  and what `meltemi-mcp` does.
- Two README statements are corrected. A send or an invitation answer goes out directly; it is
  not queued for the app to deliver. A change can be reverted before or after it has reached your
  mail server, and a sent message, a saved draft and an invitation answer cannot be reverted.
