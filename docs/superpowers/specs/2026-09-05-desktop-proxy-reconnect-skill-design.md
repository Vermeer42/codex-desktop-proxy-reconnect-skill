# Desktop Proxy Reconnect Skill Design

## Purpose

Create a reusable Codex skill that helps diagnose and resolve a narrow Windows failure mode: ChatGPT Desktop or Codex Desktop repeatedly reconnects because its WebSocket transport does not use an otherwise working local HTTP(S) proxy.

## Scope

The skill applies only when a user reports reconnect loops and uses a local Windows proxy or VPN client. It must not treat every reconnect error as a proxy problem. It covers requests framed around either ChatGPT Desktop or Codex Desktop because the relevant local Codex configuration may be shared by the desktop environment.

## Workflow

1. Confirm the platform, symptom, and whether a local proxy is in use.
2. Read the Windows system proxy and existing process/configuration state without changing anything.
3. Identify the narrow signature: HTTPS works, while WebSocket reconnects, times out, or falls back.
4. Explain the evidence and proposed configuration change.
5. Obtain explicit user approval.
6. Write or update the resolved Codex `.env` with `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY`, using the detected proxy endpoint.
7. Verify the file without echoing secrets, then require a full desktop-app restart.

## Safety and Boundaries

- `127.0.0.1` means the user's own machine, but it is not assumed; the active proxy address and port are detected.
- Never hard-code port `7897`.
- Never overwrite an `.env` blindly; preserve unrelated variables and only update the three owned keys.
- Do not use credentials in output, commits, or examples.
- Stop and route to ordinary connectivity/authentication diagnostics if the signature does not match.

## Deliverables

- `SKILL.md`: compact operational guidance for an AI agent.
- `references/troubleshooting.md`: detailed diagnosis and recovery notes.
- `README.md`: human-facing installation, scope, and usage information.
