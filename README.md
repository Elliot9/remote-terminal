# elliot9/remote-terminal

A Claude Code plugin marketplace containing the `remote-terminal` plugin.

## Plugins

### remote-terminal

Control your Claude Code terminal from any mobile browser — no app required, using ttyd + Caddy + ngrok.

## Installation

**Step 1 — add this marketplace:**

```
/plugin marketplace add elliot9/remote-terminal
```

**Step 2 — install the plugin:**

```
/plugin install remote-terminal@elliot9
```

## Commands

| Command | Description |
|---|---|
| `/start-remote` | Start the remote terminal session |
| `/stop-remote` | Stop all services and clean up |
| `/remote-status` | Show the active URL and credentials |
