# 桌面端代理重连排障 Skill

一个需要用户明确批准后才会修改配置的 Codex Skill，用于诊断 Windows 上一种特定的重连问题：ChatGPT Desktop 或 Codex Desktop 反复重连，而本地代理虽然能处理 HTTPS，却没有被实时 WebSocket 连接正确使用。

## 它会做什么

- 读取 Windows 系统代理和现有 Codex 代理配置。
- 先判断是否符合“HTTPS 正常、WebSocket 失败”的特征，再建议修复方案。
- 使用当前实际的代理地址和端口，不假定为 `127.0.0.1:7897`。
- 在更新 Codex `.env` 前明确征求用户批准。
- 保留 `.env` 中无关的设置，并提醒用户彻底退出后重启桌面应用。

## 它不会做什么

- 不会把所有重连问题都误判为代理问题。
- 不会修改 Windows 系统代理。
- 未经批准不会写入任何配置。
- 不会自动处理仅 PAC、仅 SOCKS 或带凭据的代理。

## 安装

把下面这句话发送给 Codex：

> 请从 GitHub 仓库 `Vermeer42/codex-desktop-proxy-reconnect-skill` 的根目录安装名为 `desktop-proxy-reconnect` 的 Skill；安装后，诊断我 Windows 上 ChatGPT Desktop 或 Codex Desktop 的反复重连问题。先读取系统代理和现有配置，未经我明确批准不要修改任何文件。

安装完成后，该 Skill 会在下一轮对话中可用。

<details>
<summary>手动安装（备用）</summary>

```powershell
git clone https://github.com/Vermeer42/codex-desktop-proxy-reconnect-skill.git `
  "$env:USERPROFILE\.codex\skills\desktop-proxy-reconnect"
```

安装后重启 Codex。
</details>

## 典型触发方式

可以对 Codex 这样说：

> Codex Desktop 在我第一次发消息时会反复重连。我使用本地 VPN/代理。请先诊断，未经我同意不要修改配置。

## 为什么有效

HTTPS 是普通的请求-响应连接，WebSocket 则是保持不断开的实时连接；它们在 Windows 上未必以同样方式继承系统代理。若证据表明 HTTPS 可用而 WebSocket 重连，明确设置 `HTTP_PROXY` 与 `HTTPS_PROXY` 可以让应用同时知道本地代理入口。

这是一种有证据支持的绕过方案，并非对所有重连问题的通用根治。一个高度相似的公开报告见 [openai/codex#29958](https://github.com/openai/codex/issues/29958)。

## 许可证

MIT

---

# English

## Desktop Proxy Reconnect Skill

An approval-gated Codex skill for diagnosing a narrow Windows issue: ChatGPT Desktop or Codex Desktop repeatedly reconnects because a local proxy handles HTTPS but is not being applied to the app's WebSocket transport.

### What It Does

- Reads Windows proxy settings and existing Codex proxy configuration.
- Identifies the HTTPS-works/WebSocket-fails pattern before proposing a workaround.
- Uses the active proxy address and port instead of assuming `127.0.0.1:7897`.
- Requests explicit approval before updating the Codex `.env` file.
- Preserves unrelated `.env` settings and reminds the user to fully restart the desktop app.

### What It Does Not Do

- It does not diagnose every reconnect issue.
- It does not change system proxy settings.
- It does not write any configuration without approval.
- It does not handle PAC-only, SOCKS-only, or credentialed proxies automatically.

### Install

Send the following instruction to Codex:

> Install the Skill named `desktop-proxy-reconnect` from the root of the GitHub repository `Vermeer42/codex-desktop-proxy-reconnect-skill`. Then diagnose repeated reconnecting in ChatGPT Desktop or Codex Desktop on my Windows computer. Read the system proxy and current configuration first, and do not modify any files without my explicit approval.

The Skill is available on the next turn after installation.

<details>
<summary>Manual installation (fallback)</summary>

```powershell
git clone https://github.com/Vermeer42/codex-desktop-proxy-reconnect-skill.git `
  "$env:USERPROFILE\.codex\skills\desktop-proxy-reconnect"
```

Restart Codex after installation.
</details>

### Typical Trigger

Tell Codex something like:

> Codex Desktop reconnects on my first message. I use a local VPN/proxy. Please diagnose it before changing anything.

### Why This Exists

HTTPS and WebSocket connections may not receive Windows system proxy settings in the same way. An explicit `HTTP_PROXY` and `HTTPS_PROXY` setting can resolve that mismatch when the evidence fits. See [openai/codex#29958](https://github.com/openai/codex/issues/29958) for a closely matching report.

### License

MIT
