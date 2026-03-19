---
description: Start a mobile-accessible remote terminal session via ttyd + Caddy + ngrok with token query param
allowed-tools: Bash, Read
---

## Context

- Missing dependencies: !`missing=(); for cmd in ttyd tmux caddy ngrok qrencode openssl curl python3; do command -v "$cmd" &>/dev/null || missing+=("$cmd"); done; printf '%s\n' "${missing[@]}"`

## Your task

You have the capability to call multiple tools in a single response. Do not ask for confirmation.

**Step 1** — If any of `ttyd`, `tmux`, `caddy`, `ngrok`, or `qrencode` appear in the missing dependencies above, call Bash to run `brew install ttyd tmux caddy ngrok qrencode` first.

**Step 2** — Call Bash to run this script:

```
TOKEN=$(openssl rand -hex 16)

if [ -n "$TMUX" ]; then
  SESSION=$(tmux display-message -p '#S')
else
  SESSION="remote"
  tmux new-session -d -s "$SESSION" -c "$PWD" 2>/dev/null || true
fi

pkill -f ttyd         2>/dev/null || true
pkill -f "caddy run"  2>/dev/null || true
pkill -f "ngrok http" 2>/dev/null || true
sleep 0.5

ttyd --port 7681 --writable tmux attach-session -t "$SESSION" >/tmp/ttyd.log 2>&1 &
echo $! >/tmp/ttyd.pid

install -m 600 /dev/null /tmp/remote-terminal.Caddyfile
cat >/tmp/remote-terminal.Caddyfile <<EOF
:7682 {
    @has_token query token=${TOKEN}
    @has_cookie expression \`{http.request.cookie.rt_session} == "${TOKEN}"\`

    handle @has_token {
        header +Set-Cookie "rt_session=${TOKEN}; Path=/; HttpOnly; Secure; SameSite=Strict; Max-Age=3600"
        reverse_proxy localhost:7681
    }

    handle @has_cookie {
        reverse_proxy localhost:7681
    }

    respond 403
}
EOF

caddy run --config /tmp/remote-terminal.Caddyfile >/tmp/caddy.log 2>&1 &
echo $! >/tmp/caddy.pid

ngrok http 7682 >/tmp/ngrok.log 2>&1 &
echo $! >/tmp/ngrok.pid

base_url=""; attempt=0
while [ -z "$base_url" ] && [ "$attempt" -lt 20 ]; do
  sleep 1; attempt=$((attempt + 1))
  base_url=$(curl -s http://localhost:4040/api/tunnels 2>/dev/null | python3 -c "
import sys, json
try:
    data = json.load(sys.stdin)
    for t in data.get('tunnels', []):
        if t.get('proto') == 'https':
            print(t['public_url']); break
except: pass
" 2>/dev/null)
done

if [ -z "$base_url" ]; then
  echo "❌ Could not get ngrok URL after 20s — check: cat /tmp/ngrok.log"
  exit 1
fi

full_url="${base_url}/?token=${TOKEN}"

install -m 600 /dev/null /tmp/remote-terminal.creds
printf '%s\n' "$full_url" >/tmp/remote-terminal.creds
install -m 600 /dev/null /tmp/remote-qr.txt
qrencode -l L -m 1 -t UTF8 "$full_url" >/tmp/remote-qr.txt
echo "$full_url"
```

**Step 3** — After the Bash call completes, read `/tmp/remote-qr.txt` using the Read tool, then output your response in exactly this format:

Remote terminal is ready. Scan the QR code or open the URL on your phone — no login required.

`URL`

```
(QR code content from the file)
```

Powered by ttyd + Caddy + ngrok. Run `/stop-remote` to shut down.
