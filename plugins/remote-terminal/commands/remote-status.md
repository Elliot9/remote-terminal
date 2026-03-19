---
description: Show the current remote terminal session URL and QR code
allowed-tools: Bash, Read
---

## Context

- Session URL: !`cat /tmp/remote-terminal.creds 2>/dev/null || echo "NO_SESSION"`

## Your task

If the session URL above shows `NO_SESSION`, tell the user there is no active session and to run `/start-remote`. Do not output anything else.

Otherwise:

**Step 1** — Call Bash to regenerate the QR code:

```
url=$(cat /tmp/remote-terminal.creds)
install -m 600 /dev/null /tmp/remote-qr.txt
qrencode -l L -m 1 -t UTF8 "$url" >/tmp/remote-qr.txt
```

**Step 2** — Read `/tmp/remote-qr.txt` using the Read tool, then output your response in exactly this format:

Active remote terminal session. Scan the QR code or open the URL on your phone.

`URL`

```
(QR code content from the file)
```

Run `/stop-remote` to shut down.
