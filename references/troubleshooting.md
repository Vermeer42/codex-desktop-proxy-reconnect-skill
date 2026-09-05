# Troubleshooting Reference

## What This Workaround Addresses

Desktop AI apps can use two network styles:

- HTTPS is a normal request-and-response connection.
- WebSocket is a long-lived connection used for realtime updates and streaming output.

On Windows, a local proxy can make ordinary HTTPS requests work while a WebSocket connection does not receive the same proxy routing. The visible result can be repeated reconnecting, a timeout, or a fallback from WebSocket to HTTPS. Explicit `HTTP_PROXY` and `HTTPS_PROXY` values can resolve this specific mismatch by telling the application where the local proxy is.

This is an evidence-backed workaround, not a universal root-cause fix. A closely matching report is [openai/codex#29958](https://github.com/openai/codex/issues/29958).

## Reading Windows Proxy State

The Windows system proxy is commonly available under:

```powershell
Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings' |
  Select-Object ProxyEnable, ProxyServer, AutoConfigURL
```

Interpret the result carefully:

- `ProxyEnable = 1` with `ProxyServer = 127.0.0.1:7897` is a direct local proxy endpoint.
- `ProxyServer` may use a per-protocol format such as `http=127.0.0.1:7890;https=127.0.0.1:7890`. Use the HTTPS endpoint when available; otherwise use the compatible HTTP endpoint only when the user confirms it is their local HTTP proxy.
- `AutoConfigURL` points to a PAC script. Do not infer a proxy endpoint from it.
- A SOCKS-only endpoint is not automatically interchangeable with an HTTP proxy. Do not write it into `HTTP_PROXY` or `HTTPS_PROXY` without a user-confirmed HTTP proxy listener.

## Configuration Target

Resolve the configuration directory in this order:

1. `CODEX_HOME` from the active or user environment, if defined.
2. `%USERPROFILE%\.codex` otherwise.

The target file is `.env` inside that directory. The configuration should look like this after approval, substituting the actual detected endpoint:

```env
HTTP_PROXY="http://127.0.0.1:7897"
HTTPS_PROXY="http://127.0.0.1:7897"
NO_PROXY="localhost,127.0.0.1,::1"
```

The example address and port are illustrative only. Do not copy them unless they match the active local proxy.

## When Not To Apply the Workaround

Do not modify `.env` when any of these is true:

- The user has no configured local proxy and does not use a VPN/proxy client.
- HTTPS requests fail too, suggesting a broader network or service problem.
- The failure is clearly an authentication, account, firewall, certificate, or outage issue.
- The system proxy endpoint is unavailable, ambiguous, credentialed, PAC-only, or SOCKS-only.
- The user has not explicitly approved the proposed file update.

## If It Does Not Help

After a full app restart, check in this order:

1. The local proxy software is running and listening on the detected address and port.
2. The system proxy has not changed since `.env` was written.
3. The `.env` file is in the resolved Codex home, not a similarly named directory.
4. The desktop application process was fully restarted.
5. The proxy client supports WebSocket traffic through its HTTP proxy listener.
6. The symptom is not a broader outage or account/authentication problem.

If the proxy endpoint changed, update the three managed variables only after showing the new endpoint and receiving approval again.
