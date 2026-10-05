# Security and trust boundary

This is an unofficial compatibility adapter. It modifies code inside a Grok Bot cloud computer and therefore deserves the same caution as any developer tool that can execute code and use a computer on your behalf.

The official source is <https://github.com/promptadvisers/grokrouter>. The supported installer shells can be built locally from that source. GrokRouter does not ask users to bypass an unknown-developer or signature warning for a downloaded binary.

## What the installer can access

The macOS installer validates `/Applications/Grok Bot.app`. The Windows installer locates the official app, requires Grok Bot 0.30.0, and requires a valid Authenticode signature before continuing. Each restarts Grok Bot with an Electron diagnostic port bound only to `127.0.0.1` and uses the local connection to operate the already-visible noVNC Bot computer. The Mac app does not request operating-system Accessibility, Screen Recording, or Full Disk Access permissions; the Windows renderer runs with context isolation, no Node integration, and the Electron sandbox enabled.

Inside the Bot computer, the bootstrap can write under `/home/box/sand-data/grokbot-router`, back up and atomically replace `/home/box/sand-host/host-main.cjs`, install pinned runtime dependencies, restart the Grok host process, and invoke the installed Codex or Claude Code login flow.

## Credentials

- Codex authentication is handled by the pinned Codex CLI/SDK device flow inside the Bot computer.
- Claude Code authentication is handled by the pinned Claude Code CLI flow inside the Bot computer; the router stores only the provider session identifier, not an OAuth credential.
- DeepSeek and OpenRouter keys entered in GrokRouter are passed over the loopback-only DevTools session directly to `window.desktop.secrets.upsert` and Grok Bot's protected Secrets store. The installer clears those fields after the protected handoff.
- The runtime reads DeepSeek/OpenRouter keys from the explicit child environment allowlist or Grok Bot Secrets at request time.
- Credentials are never intentionally printed, included in provider state, included in release artifacts, or sent to audit logs.
- `grokbot-router doctor` reports presence/status only and redacts account email output.

## Network destinations

Depending on selected providers, the Bot computer can connect to npm during installation, OpenAI/Codex endpoints for Codex operation, Anthropic/Claude endpoints for Claude Code, `api.deepseek.com` for native DeepSeek Responses requests, and `openrouter.ai` for optional OpenRouter compatibility. The selected provider receives the Grok conversation and any attachments/tool results required for that routed turn.

## Integrity and recovery

The release archive has an external SHA-256 file. Its bootstrap validates an internal `SHA256SUMS` manifest before executing. The host patch requires a known stock hash plus three exact source anchors. It saves verified stock and timestamped pre-change backups, syntax-checks the generated host, and activates it atomically. Restore also syntax-checks the verified stock backup before atomic replacement.

The installer deliberately refuses unknown Grok Bot builds. `--allow-unknown-host` exists only for synthetic tests and must never appear in distributed commands.

## Reporting

Report suspected credential exposure, unsafe patch behavior, or unintended tool access through a private GitHub security advisory. Do not put secrets or private Grok transcripts in a public issue. Rotate any credential that may have been exposed and restore stock Grok Bot before further diagnosis.
