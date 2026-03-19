---
description: Stop the active remote terminal session (ttyd, Caddy, ngrok)
allowed-tools: Bash
---

## Your task

Call Bash to run the following script. Do not ask for confirmation. Do not output any text before the tool call.

```
for pid_file in /tmp/ttyd.pid /tmp/caddy.pid /tmp/ngrok.pid; do
  if [ -f "$pid_file" ]; then
    kill "$(cat "$pid_file")" 2>/dev/null || true
    rm -f "$pid_file"
  fi
done
pkill -f ttyd         2>/dev/null || true
pkill -f "caddy run"  2>/dev/null || true
pkill -f "ngrok http" 2>/dev/null || true
rm -f /tmp/remote-terminal.Caddyfile /tmp/remote-terminal.creds
echo "Remote terminal stopped."
```

After the tool call completes, do not send any text response. The script output already confirms the result.
