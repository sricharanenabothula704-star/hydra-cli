# HYDRA

**AI bug-bounty CLI for authorized security testing — built on a fork of jcode.**

Hydra wraps jcode's fast Rust agent harness and specializes it for authorized
bug-bounty and security-research workflows: every free model provider, a large
skills/methodology library, and curated OSINT/recon MCP servers.

> **Authorized testing only.** Use Hydra solely against targets you are
> explicitly permitted to test. A deterministic scope gate is planned (deferred).

## What this fork changes vs. upstream jcode

- **Branding:** the CLI is `hydra` (a `hydra` binary is built alongside the
  internal `jcode` binary, which is kept so the background daemon and self-dev
  paths keep working). `--help` / `--version` identify as HYDRA.
- **Skills:** Hydra's bug-bounty skill library (`SKILL.md` playbooks) loads from
  `~/.jcode/skills`, which jcode reads natively. Install them with the setup
  step below.
- **Providers:** jcode already ships the providers Hydra needs — `openrouter`,
  `nvidia-nim`, `groq`, `cerebras`, `deepseek`, `mistral`, `togetherai`,
  `lmstudio`, `ollama`, `openai-compatible`, plus Anthropic/OpenAI/Gemini.
  Pick one with `hydra --provider <id>` or `/model` in a session.
- **MCP:** a curated, opt-in set of OSINT/recon MCP servers ships as
  [`docs/hydra-mcp.example.json`](hydra-mcp.example.json). Copy it to
  `~/.jcode/mcp.json`, set the env vars, then `hydra mcp list` / `hydra mcp test <name>`.
  Nothing runs until you enable it.

## Build

```bash
# Windows: run from a shell with the MSVC build tools loaded (vcvars64), then:
cargo --config "build.rustc-wrapper=''" build --release --bin hydra
# Linux/macOS:
cargo build --release --bin hydra
```

The binary is `target/release/hydra` (`hydra.exe` on Windows).

## Setup skills

Hydra loads skills from `~/.jcode/skills`. Copy the methodology library there:

```bash
mkdir -p ~/.jcode/skills
cp -r /path/to/hydra/skills/* ~/.jcode/skills/
```

## The 7 heads (methodology)

jcode has no persona-agents; Hydra expresses its methodology as skills plus a
coordinator prompt. The classic Hydra roles map to phases of a hunt:

| Head | Role |
|---|---|
| RECON | attack-surface mapping |
| HUNTER | recon + exploitation |
| VERIFIER | false-positive gate |
| AUDITOR | source-code audit |
| PLAN | attack planning |
| REPORTER | submission-ready reports |
| DEBUG | self-improvement |

Use jcode's **swarm** to run several of these in parallel in one repo.
