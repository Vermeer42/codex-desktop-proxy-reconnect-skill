# Desktop Proxy Reconnect Skill

An approval-gated Codex skill for diagnosing a narrow Windows issue: ChatGPT Desktop or Codex Desktop repeatedly reconnects because a local proxy handles HTTPS but is not being applied to the app's WebSocket transport.

## What It Does

- Reads Windows proxy settings and existing Codex proxy configuration.
- Identifies the HTTPS-works/WebSocket-fails pattern before proposing a workaround.
- Uses the active proxy address and port instead of assuming `127.0.0.1:7897`.
- Requests explicit approval before updating the Codex `.env` file.
- Preserves unrelated `.env` settings and reminds the user to fully restart the desktop app.

## What It Does Not Do

- It does not diagnose every reconnect issue.
- It does not change system proxy settings.
- It does not write any configuration without approval.
- It does not handle PAC-only, SOCKS-only, or credentialed proxies automatically.

## Install

Clone this repository into your Codex skills directory under the folder name `desktop-proxy-reconnect`:

```powershell
git clone https://github.com/Vermeer42/codex-desktop-proxy-reconnect-skill.git `
  "$env:USERPROFILE\.codex\skills\desktop-proxy-reconnect"
```

Restart Codex after installation. The skill can then be selected explicitly as `$desktop-proxy-reconnect`, or be discovered when a Windows desktop reconnect/proxy problem matches its description.

## Typical Trigger

Tell Codex something like:

> Codex Desktop reconnects on my first message. I use a local VPN/proxy. Please diagnose it before changing anything.

## Why This Exists

HTTPS and WebSocket connections may not receive Windows system proxy settings in the same way. An explicit `HTTP_PROXY` and `HTTPS_PROXY` setting can resolve that mismatch when the evidence fits. See [openai/codex#29958](https://github.com/openai/codex/issues/29958) for a closely matching report.

## License

MIT
