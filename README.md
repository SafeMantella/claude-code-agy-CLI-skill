# 🚀 Claude Code × Antigravity CLI Skill

> **Multi-agent skill for Claude Code** that delegates heavy tasks to Google's Antigravity CLI agents — saving tokens while unlocking web search, image generation, codebase analysis, and 40+ scientific database integrations.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-blueviolet)](https://docs.anthropic.com/en/docs/claude-code)
[![Antigravity CLI](https://img.shields.io/badge/Antigravity_CLI-agy-orange)](https://blog.google/technology/google-deepmind/antigravity/)

> **Migrating from `gemini-cli`?** This skill is its direct successor. The Gemini CLI binary stops working on **June 18, 2026** — swap in this skill and the `agy` binary to keep everything working.

---

## Why?

Claude Code is great at interactive coding. Antigravity CLI (`agy`) has exclusive tools Claude doesn't — **Google Search, image generation, Chrome DevTools, and 40+ scientific databases**. This skill lets Claude intelligently delegate to Antigravity when it makes sense, then process only the final output.

**Result:** You get the best of both AI agents while Claude uses fewer tokens.

```
You ──► Claude Code ──► "this needs web search" ──► agy -p "..." ──► result ──► Claude Code ──► You
             │                                                                         │
       keeps context lean                                              only processes final output
```

## What Claude Code Can Delegate

| Capability | Why Delegate? |
|-----------|---------------|
| 🔍 **Web Research** | `google_web_search` grounding is more authoritative and real-time than Claude Code's DuckDuckGo |
| 🎨 **Image Generation** | Antigravity has `generate_image` — Claude Code doesn't |
| 🏗️ **Codebase Architecture** | Antigravity's `codebase_investigator` maps entire projects in one pass |
| 🧬 **Science Queries** | 40+ specialized skills: gnomAD, UniProt, PubMed, AlphaFold, ChEMBL... |
| 🌐 **Browser Debugging** | Chrome DevTools Protocol for performance, a11y, network inspection |
| 🔄 **Multi-File Refactors** | Runs in its own context — won't exhaust Claude's window |
| 👀 **Code Review** | Different model = different perspective = catches different bugs |
| 📝 **Bulk Generation** | Tests, docs, boilerplate without filling Claude's context |

## Quick Start

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- [Antigravity CLI](https://blog.google/technology/google-deepmind/antigravity/) (`agy`) installed and authenticated

### Install

```bash
# Clone into Claude Code's skills directory
git clone git@github.com:SafeMantella/claude-code-agy-CLI-skill.git ~/.claude/skills/antigravity-cli
```

Claude Code auto-discovers skills from `~/.claude/skills/` on startup.

### Set your binary path

The skill hardcodes the path to your `agy` binary. Find it and update `SKILL.md`:

```bash
# Find your agy binary
which agy || find ~/.local/bin /usr/local/bin /opt/homebrew/bin -name "agy" 2>/dev/null
```

Open `SKILL.md` and replace the path under `## Binary Location` with your output above.

### Verify

Start a new Claude Code session and try:

```
> Research the latest security advisories for Flask 3.x
```

Claude should automatically delegate this to Antigravity. You'll see it run `agy -p "..."` in its tool calls.

## How It Works

### Auto-Discovery

Claude Code reads all `~/.claude/skills/*/SKILL.md` files at startup. The YAML frontmatter tells Claude **when** to activate the skill:

```yaml
name: antigravity-cli
description: >
  Delegate heavy, multi-step, or specialized tasks to Google's Antigravity CLI agents.
  Use when a task would benefit from: deep codebase analysis, web research with Google
  Search grounding, image generation, code review from a second AI perspective,
  specialized scientific database queries, or long-running refactors...
```

### Delegation Decision

Claude uses a simple heuristic:

```
Token cost of doing it myself  >  Token cost of prompt + parsing output?
                                     ↓ YES → Delegate to agy
                                     ↓ NO  → Do it myself
```

Rules of thumb baked into the skill:

| Tool calls needed | Action |
|-------------------|--------|
| < 5 | Claude does it directly |
| 5–15 | Claude considers delegating |
| > 15 | Claude delegates |
| Web search / images / science DBs | Always delegates (exclusive capabilities) |

### Command Pattern

```bash
/path/to/agy -p "YOUR PROMPT" --print-timeout 3m0s 2>&1
```

| Flag | Purpose |
|------|---------|
| `-p` | Non-interactive mode: run, print, exit |
| `--print-timeout` | Max wait time (default 5m) |
| `--dangerously-skip-permissions` | Auto-approve tool calls (needed for file writes) |
| `--add-dir /path` | Add workspace directories |
| `-c` / `--continue` | Continue most recent conversation |

## Skill Files

| File | Description |
|------|-------------|
| [`SKILL.md`](SKILL.md) | Core skill — triggers, command reference, 8 integration patterns, error handling |
| [`templates.md`](templates.md) | 12 ready-to-use prompt templates for common scenarios |
| [`patterns.md`](patterns.md) | 8 advanced orchestration patterns with anti-patterns |
| [`reference.md`](reference.md) | Capability comparison matrix between Claude Code & Antigravity |

## Claude Code + agy: Real Use Cases and Example Prompts

| You say to Claude Code | Why agy handles it |
|------------------------|---------------------|
| *"What are the breaking changes in React 19?"* | `google_web_search` grounding — more authoritative and real-time than DuckDuckGo |
| *"Check our dependencies for known CVEs"* | Google Search grounding surfaces current advisories; Claude's training has a knowledge cutoff |
| *"Compare Prisma vs Drizzle vs TypeORM for this stack"* | Google Search grounding for current benchmarks, GitHub pulse, and community sentiment |
| *"Generate a dark-theme dashboard UI mockup"* | Image generation via `generate_image` — Claude Code cannot produce images |
| *"Map the entire codebase: architecture, deps, entry points"* | `codebase_investigator` does this in one holistic pass; Claude would need dozens of reads |
| *"Refactor all 30 API routes to async/await"* | >15 file edits — delegating keeps Claude's context window free |
| *"Review this auth module for security vulnerabilities"* | Different model, different blind spots — catches issues Claude might normalize |
| *"Look up allele frequency for rs1234567 in gnomAD"* | Requires the `gnomad-database` skill, one of 40+ science DBs exclusive to agy |
| *"Get the AlphaFold structure for UniProt P12345"* | Requires `alphafold-database` + `uniprot-database` skills (exclusive to agy) |
| *"Why does this page score 43 on Lighthouse?"* | Chrome DevTools Protocol access — Claude Code has no browser integration |

## Advanced Patterns

The skill includes 8 orchestration patterns. Here are the highlights:

### Parallel Delegation
```bash
# Run multiple analyses simultaneously
agy -p "Analyze auth module" > /tmp/auth.txt &
agy -p "Analyze database module" > /tmp/db.txt &
wait
```

### Conversational Depth
```bash
# Multi-step analysis with context retention
agy -p "Analyze the database schema"
agy -c -p "Now suggest index optimizations for the slowest queries"
agy -c -p "Generate the Alembic migration file"
```

### Research → Decide → Execute
```bash
# Antigravity researches, Claude decides, then executes
RESEARCH=$(agy -p "Compare SQLAlchemy vs SQLModel vs Tortoise-ORM for async FastAPI")
# Claude reads output, makes the decision, implements
```

See [`patterns.md`](patterns.md) for all 8 patterns with full examples.

## Customization

### Adding Project Context

Create `.gemini/GEMINI.md` in your project root to give Antigravity persistent context:

```markdown
# My Project
Flask 3.x backend with PostgreSQL. Docker Compose deployment.
Main entry: app/__init__.py
Tests: pytest in tests/
```

### Adjusting Delegation Thresholds

Edit the "Cost-Benefit Decision Framework" section in `SKILL.md` to tune when Claude delegates vs. handles tasks itself.

## Compatibility

| Component | Minimum Version |
|-----------|----------------|
| Claude Code | 2.1+ (Skills support) |
| Antigravity CLI (`agy`) | 0.2.x+ |
| macOS / Linux | Tested on macOS, should work on Linux |

## Contributing

PRs welcome! Ideas for improvement:

- [ ] Add more prompt templates for specific frameworks (Django, Next.js, Flutter)
- [ ] Add Windows support and path detection
- [ ] Create a `/delegate` custom command for explicit invocation
- [ ] Add output parsing helpers for structured data extraction
- [ ] Template for MCP server creation via Antigravity

## License

[MIT](LICENSE) — use it however you want.

---

<p align="center">
  <b>Built with 🧠 Claude Code + 🚀 Antigravity CLI</b><br>
  <sub>Two AI agents are better than one.</sub>
</p>
