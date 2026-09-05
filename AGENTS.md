# Project Guide

## Goal

Provide a reusable Codex skill for diagnosing Windows desktop-app reconnect loops caused by a local HTTP(S) proxy that is not applied to WebSocket traffic.

## Usage

Install this repository as a Codex skill. The main instructions are in `SKILL.md`.

## Safety Rules

- Diagnose before changing configuration.
- Read the active system proxy instead of hard-coding an address or port.
- Explain the proposed change and require explicit approval before writing `.env`.
- Do not request, expose, or store proxy credentials.

## Current Status

Published publicly at `https://github.com/Vermeer42/codex-desktop-proxy-reconnect-skill` on 2026-09-05.

## Key Decisions

- Scope: Windows, ChatGPT Desktop and Codex Desktop reconnect loops associated with a local proxy.
- Configuration target: resolve `CODEX_HOME` when defined, otherwise `%USERPROFILE%\\.codex\\.env`.
- Publication: public GitHub repository under the authenticated account.
