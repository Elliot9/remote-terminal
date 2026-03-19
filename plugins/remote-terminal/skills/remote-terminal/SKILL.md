---
name: remote-terminal
description: This skill should be used when the user asks to "start remote terminal", "access terminal from phone", "expose Claude Code to mobile", "stop remote terminal", "check remote session", "remote terminal status", or discusses managing a mobile-accessible terminal session via ttyd, Caddy, or ngrok.
version: 1.0.0
---

# Remote Terminal

Control your Claude Code terminal from any mobile browser — no app required.

## Overview

This skill manages a mobile-accessible terminal session using:
- **ttyd** — serves a terminal over HTTP
- **Caddy** — adds basic auth and reverse proxies ttyd
- **ngrok** — exposes the local Caddy server to the internet with a public HTTPS URL

## When This Skill Applies

Activate when the user wants to:
- Start a remote terminal session accessible from a phone or tablet
- Stop an active remote terminal session
- Check the URL or credentials of a running session

## Available Commands

| Command | Description |
|---|---|
| `/start-remote` | Start the full stack (tmux + ttyd + Caddy + ngrok) and display the URL |
| `/stop-remote` | Kill all services and clean up credentials |
| `/remote-status` | Show the active session URL and password (with QR code) |

## Dependencies

Required tools (install via `brew` if missing):

```
ttyd   tmux   caddy   ngrok   qrencode
```

Pre-installed on macOS (no action needed):

```
openssl   curl   python3
```

## How It Works

1. A `tmux` session named `remote` is created (or reused)
2. `ttyd` attaches to that session and serves it on `localhost:7681`
3. `Caddy` wraps it with basic auth (`admin` / random 8-char hex password) on port `7682`
4. `ngrok` tunnels port `7682` to a public HTTPS URL
5. Credentials are saved to `/tmp/remote-terminal.creds` for later retrieval

## Security Notes

- Each `/start-remote` generates a fresh random password
- The session is protected by HTTP basic auth
- The ngrok URL changes on every restart
- Run `/stop-remote` when you are done to close public access
