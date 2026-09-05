---
name: desktop-proxy-reconnect
description: Diagnose Windows ChatGPT Desktop or Codex Desktop reconnect loops that may be caused by a local HTTP(S) proxy not being applied to WebSocket traffic. Use only for reconnect, WebSocket timeout, or HTTPS fallback symptoms when a local proxy/VPN is involved.
---

# Desktop Proxy Reconnect

Use this skill for a narrow Windows connectivity issue: a desktop AI app can reach ordinary HTTPS endpoints through a local proxy, but its realtime WebSocket connection repeatedly reconnects, times out, or falls back to HTTPS.

Do not assume every reconnect error is a proxy issue. Do not modify configuration until the user explicitly approves the exact change.

## Diagnose First

Confirm all of the following before proposing a change:

- The user is on Windows and is using ChatGPT Desktop or Codex Desktop.
- The symptom is a repeated reconnect loop, a WebSocket timeout, or a WebSocket-to-HTTPS fallback.
- The user uses a local proxy or VPN client, or Windows has a configured proxy.

Read the following without changing state:

1. The Windows system proxy, including its address and port.
2. Existing `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` variables in the process/user environment.
3. The resolved Codex home directory: use `CODEX_HOME` if it is set; otherwise use `%USERPROFILE%\\.codex`.
4. The contents of `<Codex home>\\.env`, if present.

Treat `127.0.0.1` as a local-machine address, not as a required proxy address. Treat the port as environment-specific. If the system proxy uses a PAC URL, credentials, or a format that cannot be safely converted to an HTTP proxy endpoint, stop and explain that the skill cannot safely apply this workaround.

Read [troubleshooting guidance](references/troubleshooting.md) when the system proxy format is ambiguous, the symptom does not match cleanly, or the proposed change fails.

## Decide

The workaround is appropriate only when the evidence points to this pattern:

- HTTPS works through the Windows system proxy.
- The desktop app's realtime connection fails, reconnects, or falls back from WebSocket transport.
- Explicit proxy environment variables are missing, stale, or point to a different endpoint from the active local proxy.

Explain the causal chain in plain language: ordinary HTTPS requests and a persistent WebSocket connection can use proxy settings differently; explicit proxy variables make the selected local proxy available to both transports.

If the pattern is not supported, do not write `.env`. Route the user to normal connectivity, sign-in, service-status, firewall, or application-update checks instead.

## Ask Before Writing

Show the user the exact non-secret endpoint you detected and the target file. Explain that you will only update these keys:

```env
HTTP_PROXY="http://<detected-host>:<detected-port>"
HTTPS_PROXY="http://<detected-host>:<detected-port>"
NO_PROXY="localhost,127.0.0.1,::1"
```

Ask for explicit approval. Do not treat an earlier general troubleshooting request as approval to change configuration.

## Apply Safely After Approval

After approval:

1. Create `<Codex home>\\.env` if it does not exist.
2. Update only `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY`; preserve all unrelated lines and comments.
3. Use the detected endpoint. Do not hard-code `127.0.0.1`, port `7897`, or any other proxy value.
4. Do not print proxy credentials. If credentials are present, stop and ask the user to configure them privately.
5. Re-read the file and verify only that the three intended keys are present and use the selected non-secret endpoint.
6. Tell the user to fully quit and restart ChatGPT Desktop or Codex Desktop. Closing a chat window may not restart the process.

## Explain Future Changes

- Changing a VPN server usually does not require editing `.env` when the local proxy address and port remain unchanged.
- Changing the local proxy port, proxy application, or listening interface requires re-checking `.env`.
- Reinstalling a desktop app or changing `CODEX_HOME` may change where the configuration is read from.
