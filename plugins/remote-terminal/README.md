# remote-terminal

Control your Claude Code terminal from any mobile browser — no app required.

## What It Does

This plugin exposes your local terminal to any phone or tablet via a public HTTPS URL, secured with HTTP basic auth. It uses:

- **ttyd** — serves a terminal over HTTP
- **Caddy** — adds basic auth and reverse proxies ttyd
- **ngrok** — creates a public HTTPS tunnel

## Installation

```
/plugin marketplace add elliot9/remote-terminal
/plugin install remote-terminal@elliot9
```

## Commands

| Command | Description |
|---|---|
| `/start-remote` | Start the remote terminal session |
| `/stop-remote` | Stop all services and clean up |
| `/remote-status` | Show the active URL and credentials |

## Dependencies

Install missing tools with Homebrew:

```bash
brew install ttyd tmux caddy ngrok qrencode
```

`openssl`, `curl`, and `python3` are pre-installed on macOS.

## Usage

1. Run `/start-remote` — Claude will check dependencies, start all services, and display the URL + password (and a QR code if `qrencode` is installed)
2. Open the URL on your phone, enter `admin` and the password
3. You now have a full terminal in your mobile browser
4. Run `/stop-remote` when done to close public access

## Security

- A fresh random password is generated on every `/start-remote`
- The session is protected by HTTP basic auth
- Run `/stop-remote` when finished to revoke public access
