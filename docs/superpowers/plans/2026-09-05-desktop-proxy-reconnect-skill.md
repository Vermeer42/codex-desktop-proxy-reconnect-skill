# Desktop Proxy Reconnect Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish a reusable Codex skill that safely diagnoses and resolves Windows desktop reconnect loops caused by local proxy/WebSocket configuration.

**Architecture:** Keep the operational instructions concise in `SKILL.md`; move diagnostic rationale and failure routing into one reference file; publish a human-facing README for installation and safe use.

**Tech Stack:** Markdown, Git, GitHub CLI.

**Spec:** `docs/superpowers/specs/2026-09-05-desktop-proxy-reconnect-skill-design.md`

## Global Constraints

- Windows only; target ChatGPT Desktop and Codex Desktop reconnect loops involving a local proxy.
- Detect the proxy endpoint; never hard-code `127.0.0.1:7897`.
- Require explicit user approval before changing `.env`.
- Preserve unrelated `.env` entries and never expose credentials.

---

### Task 1: Write the reusable skill and supporting guidance

**Files:**
- Create: `SKILL.md`
- Create: `references/troubleshooting.md`
- Create: `README.md`

**Interfaces:**
- Consumes: the user's symptom, Windows proxy state, and the resolved Codex home directory.
- Produces: a diagnosis, an approval-gated configuration update, and restart guidance.

- [ ] **Step 1: Write the failing validation target**

Define the required invariants: valid `name` and `description` frontmatter, explicit approval gate, dynamic proxy detection, and no hard-coded proxy port.

- [ ] **Step 2: Run validation before implementation**

Run: `python C:\\Users\\86177\\.codex\\skills\\.system\\skill-creator\\scripts\\quick_validate.py C:\\Users\\86177\\Projects\\codex-desktop-proxy-reconnect-skill`

Expected: validation fails because `SKILL.md` does not yet exist.

- [ ] **Step 3: Implement the skill and documentation**

Write a compact `SKILL.md`, a troubleshooting reference, and a README that describe the approval-gated workflow and its boundaries.

- [ ] **Step 4: Run the validation target again**

Run: `python C:\\Users\\86177\\.codex\\skills\\.system\\skill-creator\\scripts\\quick_validate.py C:\\Users\\86177\\Projects\\codex-desktop-proxy-reconnect-skill`

Expected: exit code 0.

- [ ] **Step 5: Commit**

Run: `git add AGENTS.md SKILL.md README.md references docs && git commit -m "feat: add desktop proxy reconnect skill"`

### Task 2: Publish the repository

**Files:**
- Modify: `AGENTS.md` (status only)

**Interfaces:**
- Consumes: a verified clean Git repository and the authenticated GitHub CLI account.
- Produces: a public GitHub repository with `main` pushed.

- [ ] **Step 1: Verify repository state**

Run: `git status --short` and `git log -1 --oneline`

Expected: clean worktree and a skill commit.

- [ ] **Step 2: Create and push the public repository**

Run: `gh repo create codex-desktop-proxy-reconnect-skill --public --source . --remote origin --push --description "Approval-gated Windows proxy diagnostics for Codex and ChatGPT Desktop reconnect loops."`

Expected: GitHub CLI returns the repository URL and pushes `main`.
