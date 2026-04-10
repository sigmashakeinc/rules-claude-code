# rules-claude-code

Claude Code governance rules — guards against the documented CVEs and security incidents that put developer machines at risk. Blocks malicious hook execution (CVE-2025-59536), API credential theft (CVE-2026-21852), command injection bypasses, and source code exfiltration via debug source maps.

**13 rules · 3 files**

![rules-claude-code — AI agent governance demo](demo.cast)

> [▶ Watch interactive demo on SigmaShake Hub](https://hub.sigmashake.com/ruleset/rules-claude-code)

## Install

```bash
ssg hub pull rules-claude-code
```

Available on the [SigmaShake Hub](https://hub.sigmashake.com) — the open registry for AI agent governance rules. Compatible with Claude Code and any AI coding agent using the `ssg` hook protocol.

## Rules

### claudecode_exec_injection.rules — Command execution safety (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-claude-hook-execution` | DENY | error | CVE-2025-59536: Blocks .claude/hooks/ script execution via project configs |
| `no-bash-command-substitution-bypass` | DENY | error | Blocks nested command substitution used to bypass safety blacklists |
| `no-node-source-map-exfil` | DENY | error | Blocks NODE_OPTIONS source-map flags used for source code exfiltration |
| `ask-claude-config-directory-modify` | ASK | warning | Prompts before any .claude/ directory structure modification |
| `log-rapid-sequential-commands` | LOG | info | Audits rapid simple commands that overwhelm the safety rule cap |

### claudecode_read_credential_theft.rules — Credential read protection (4 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-read-claude-credential-files` | DENY | error | CVE-2026-21852: Blocks reading .claude/ credential and token files |
| `no-read-anthropic-config` | DENY | error | Blocks Anthropic credential storage access outside the official CLI |
| `ask-read-claude-project-config` | ASK | warning | Prompts before reading .claude/settings, hooks, or CLAUDE.md |
| `log-claude-session-file-access` | LOG | info | Audit trail for all .claude/ JSON and MD file reads |

### claudecode_write_config_tampering.rules — Config write protection (4 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-write-claude-hooks` | DENY | error | CVE-2025-59536: Blocks writing any file to .claude/hooks/ |
| `no-write-claude-credentials` | DENY | error | CVE-2026-21852: Blocks writing to Claude credential storage |
| `ask-write-claude-settings` | ASK | warning | Prompts before writing .claude/settings, .clauderc, or commands |
| `log-claudemd-write` | LOG | info | Audit trail for CLAUDE.md writes (persistent prompt injection risk) |

## Why this matters

Claude Code is uniquely dangerous as an attack surface because it has **privileged access to your local machine** and processes config files from cloned repositories without sandboxing.

- **CVE-2025-59536** (Check Point Research): Malicious hooks in `.claude/` config files execute automatically when a developer opens a project. A bad actor commits a `.claude/hooks/` script to a repository — the next developer who opens it unknowingly runs arbitrary code.
- **CVE-2026-21852** (Claude Code < v2.0.65): API credentials can be exfiltrated through malicious project configurations that read `~/.claude/credentials` and transmit them.
- **Safety rule cap bypass** (The Register, April 2026): Claude Code stops enforcing safety rules after a threshold number of commands — attackers exploit this by issuing many benign commands first.
- **Source code leak** (March 2026): Anthropic accidentally published a debug source map on npm that allowed reconstruction of 500,000+ lines of internal TypeScript. Attackers now know the full validation logic.

## Compatible AI clients

- Claude Code (primary target)
- Any AI coding agent using the `ssg` hook protocol

## About

Part of the [SigmaShake Hub](https://hub.sigmashake.com) — open-source governance rules for AI coding agents.
Install the `ssg` CLI to enforce these rules: `npm install -g @sigmashake/ssg`
