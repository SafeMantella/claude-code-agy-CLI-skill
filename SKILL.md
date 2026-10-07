---
name: antigravity-cli
description: >
  Delegate heavy, multi-step, or specialized tasks to Google's Antigravity CLI agents.
  Use when a task would benefit from: (1) deep multi-file codebase analysis across many files,
  (2) web research with Google Search grounding, (3) image generation or visual assets,
  (4) a second AI perspective for code review or architecture decisions,
  (5) specialized scientific/biology database queries (gnomAD, UniProt, PubMed, etc.),
  (6) long-running refactors that would exhaust your context window,
  (7) tasks requiring many sequential tool calls that would consume excessive tokens.
  Also use when the user explicitly asks to use Antigravity, Gemini, or "agy".
  Do NOT use for simple, quick tasks that you can handle directly.
allowed-tools:
  - Bash
  - Read
  - Write
  - Grep
  - Glob
---

# Antigravity CLI Integration Skill

Antigravity CLI (`agy`) is Google DeepMind's agentic coding assistant. It has its own
set of tools, plugins, and capabilities that complement yours. By delegating tasks to it,
you save tokens by only processing its final output instead of performing many intermediate
steps yourself.

## Binary Resolution

Antigravity CLI is usually installed in your user PATH or standard local bin directories.
Claude should resolve the command in this order:

1. `$AGY_BIN` environment variable (if explicitly set by the user)
2. `agy` (if available directly in current `$PATH` via `command -v agy`)
3. `~/.local/bin/agy` (standard Linux/macOS installation path)
4. `/usr/local/bin/agy` or `/opt/homebrew/bin/agy`

If `agy` is directly in your PATH, you can invoke `agy` directly in the commands below. Otherwise, use the resolved absolute path (e.g., `~/.local/bin/agy`).

## Execution Modes & Permissions

Antigravity supports flexible permission handling depending on your workflow:

### 1. Supervised Mode (Recommended for code review, audits, research)
By default, omit `--dangerously-skip-permissions`. Antigravity runs in print mode (`-p`) and outputs analysis, generated code snippets, or diffs to stdout without silently altering the project or running destructive shell commands behind the scenes.
- Best for: Code review, architectural analysis, research, scientific queries, and when the user wants to review diffs before applying.

### 2. Autonomous Mode (For unattended refactors, pipelines, batch edits)
Pass `--dangerously-skip-permissions` when the user explicitly requests end-to-end autonomous execution without prompts, or when `AGY_AUTONOMOUS=1` is set.
- Best for: Multi-file refactors where changes should be written directly to disk, automated test suite generation, batch asset downloads.

## When to Delegate

### ✅ DELEGATE these tasks

| Category | Why Antigravity is better |
|----------|--------------------------|
| **Deep codebase analysis** | Has `codebase_investigator` tool — maps architecture, dependencies, cross-file relationships in one pass |
| **Web research** | Has `google_web_search` — grounded, real-time search with Google's index |
| **Image generation** | Has `generate_image` tool — creates visual assets, UI mockups, diagrams |
| **Multi-file refactors** | Runs in its own context window — won't exhaust yours |
| **Scientific queries** | Has 40+ specialized database skills (gnomAD, UniProt, PubMed, PDB, ChEMBL, etc.) |
| **Second opinion** | Different model, different perspective — catches things you might miss |
| **Bulk code generation** | Generate boilerplate, tests, docs without filling your context |
| **Docker operations** | User runs apps in Docker — Antigravity can handle complex container orchestration |

### ❌ DON'T delegate these

- Simple one-liner fixes (faster to do yourself)
- Tasks requiring back-and-forth conversation with the user
- Tasks where you already have all the context loaded
- Anything the user explicitly asked YOU to do

## Command Reference

### Non-Interactive Mode (Primary Integration)

```bash
agy -p "YOUR PROMPT HERE" 2>&1
```
*(If `agy` is not in your current `$PATH`, substitute with your resolved path such as `~/.local/bin/agy` or `$AGY_BIN`)*

**Key flags:**

| Flag | Purpose |
|------|---------|
| `-p "prompt"` / `--print "prompt"` | Non-interactive mode — run prompt, print response, exit |
| `--print-timeout 5m0s` | Timeout for print mode (default 5m, increase for complex tasks) |
| `--dangerously-skip-permissions` | Auto-approve tool calls for autonomous mode (use when authorized) |
| `--add-dir /path/to/dir` | Add a directory to the workspace (repeatable) |
| `--conversation <id>` | Resume a specific conversation by ID |
| `-c` / `--continue` | Continue the most recent conversation |
| `--sandbox` | Run with terminal restrictions enabled |

### Interactive Mode (Advanced — rarely needed)

```bash
agy
```

Only use interactive mode when you need a long-running session with follow-up questions.
This will block your terminal until the user exits. Prefer `-p` mode.

## Prompt Engineering for Delegation

### Critical Rules

1. **Be explicit and complete** — Antigravity starts with zero context about your current task
2. **Include file paths** — Always give absolute paths to relevant files
3. **Specify output format** — Tell it exactly what you need back
4. **Use action verbs** — "Analyze", "Generate", "Create", "Review" not "Can you..."
5. **Set boundaries** — Tell it what NOT to do (e.g., "Do NOT modify any files")
6. **Protect secrets** — Never pass `.env` files, API keys, or private tokens in prompts

### Prompt Template

```
[TASK DESCRIPTION in 1-2 sentences]

Context:
- Project root: [absolute path]
- Relevant files: [list of absolute paths]
- Tech stack: [languages, frameworks]

Requirements:
1. [Specific requirement]
2. [Specific requirement]

Output format:
[What you want back — code, analysis, list, etc.]

Constraints:
- Do NOT modify any files (or: Apply changes directly)
- Focus only on [scope]
```

## Integration Patterns

### Pattern 1: Research & Report

Use Antigravity for research, process its findings yourself.

```bash
agy -p "
Analyze the authentication flow in the project at /path/to/project.
Map all files involved, identify the auth middleware, session handling, and token validation.
Output a structured summary with file paths and line numbers.
Do NOT modify any files.
" --print-timeout 3m0s 2>&1
```

Then parse the output and use it to make targeted edits yourself.

### Pattern 2: Code Generation

Have Antigravity generate code, then you review and integrate it.

```bash
agy -p "
Generate a comprehensive test suite for /path/to/project/src/auth.py.
Use pytest with fixtures. Cover: login, logout, token refresh, expired tokens, invalid credentials.
Output ONLY the test file content, no explanations.
" --print-timeout 3m0s 2>&1
```

### Pattern 3: Code Review

Get a second opinion on code you've written or are about to modify.

```bash
agy -p "
Review this code for security vulnerabilities, performance issues, and bugs:

File: /path/to/project/src/handler.py

Focus on:
1. SQL injection risks
2. Input validation gaps
3. Race conditions
4. Memory leaks

Output a prioritized list of findings with severity (critical/high/medium/low).
Do NOT modify any files.
" --print-timeout 2m0s 2>&1
```

### Pattern 4: Web Research (Google Search Grounding)

Antigravity has real-time Google Search — use it for current information.

```bash
agy -p "
Search the web for the latest security advisories for Flask 3.x.
Include CVE numbers, affected versions, and recommended fixes.
Output as a markdown table.
" --print-timeout 2m0s 2>&1
```

### Pattern 5: Multi-File Refactor

Delegate large refactors that would exhaust your context. When direct disk modification is desired, pass `--dangerously-skip-permissions`:

```bash
agy -p "
Refactor all database queries in /path/to/project/src/ to use parameterized queries
instead of string formatting. Apply changes directly to the files.

Rules:
- Preserve all existing comments and docstrings
- Maintain the same function signatures
- Add type hints where missing
- Run the existing tests after changes
" --print-timeout 5m0s --dangerously-skip-permissions --add-dir /path/to/project 2>&1
```

### Pattern 6: Conversation Continuation

For complex multi-step tasks, continue a previous conversation.

```bash
# First call — start analysis
agy -p "Analyze the database schema in /path/to/project" --print-timeout 3m0s 2>&1

# Follow-up — continue the same conversation
agy -c -p "Now suggest optimizations for the slowest queries" --print-timeout 3m0s 2>&1
```

### Pattern 7: Image Generation

Antigravity can generate images — use it for visual assets.

```bash
agy -p "
Generate a modern, dark-themed login page mockup with:
- Glassmorphism card centered on screen
- Email and password fields
- Social login buttons (Google, GitHub)
- Gradient background (purple to blue)
Save the image to /path/to/project/mockups/login.png
" --print-timeout 3m0s 2>&1
```

### Pattern 8: Scientific/Biology Queries

Antigravity has 40+ specialized science skills. Delegate all bio/chem queries.

```bash
agy -p "
Search PubMed for recent papers on CRISPR-Cas9 off-target effects in human cells.
Return the top 10 results with: title, authors, journal, year, PMID, and a 2-sentence summary.
Output as a markdown table.
" --print-timeout 3m0s 2>&1
```

## Error Handling

### Common Issues

| Error | Cause | Fix |
|-------|-------|-----|
| `command not found` | Binary not in PATH | Check `~/.local/bin/agy` or set `$AGY_BIN` |
| Timeout | Task took too long | Increase `--print-timeout` (e.g., `10m0s`) |
| Permission denied | Tool call needs approval | Add `--dangerously-skip-permissions` (Autonomous mode) |
| Rate limit | API quota exceeded | Wait and retry, or simplify the prompt |
| Empty output | Prompt too vague | Be more specific, include file paths |

### Output Processing

Antigravity's output in `-p` mode goes to stdout. Common patterns:

```bash
# Capture output to variable
RESULT=$(agy -p "your prompt" 2>&1)

# Save output to file
agy -p "your prompt" 2>&1 > /tmp/agy-output.txt

# Pipe through processing
agy -p "your prompt" 2>&1 | grep -A5 "CRITICAL"
```

### Validation

**Always validate Antigravity's output before using it:**
1. Check that generated code compiles/runs
2. Verify security-sensitive code for vulnerabilities
3. Ensure style consistency with the project
4. Test that refactored code passes existing tests

## Antigravity's Unique Capabilities

These tools are available ONLY through Antigravity (not in Claude Code):

| Tool | Description |
|------|-------------|
| `google_web_search` | Real-time internet search via Google |
| `codebase_investigator` | Deep architectural analysis of codebases |
| `generate_image` | AI image generation for mockups and assets |
| `save_memory` | Cross-session persistent memory |
| `40+ Science Skills` | gnomAD, UniProt, PubMed, PDB, ChEMBL, AlphaFold, ClinVar, etc. |
| `Chrome DevTools` | Browser automation, debugging, performance analysis |
| `PyMOL` | Protein structure visualization |

## Configuration

### Antigravity's Settings
- Config: `~/.gemini/antigravity-cli/settings.json`
- Conversations: `~/.gemini/antigravity-cli/brain/`
- Plugins: `~/.gemini/config/plugins/`

### Project Context
Create `~/.gemini/GEMINI.md` or `.gemini/GEMINI.md` in the project root to give
Antigravity persistent context about the project.

## Cost-Benefit Decision Framework

Before delegating, ask yourself:

```
Token cost of doing it myself  >  Token cost of prompt + parsing output?
                                     ↓ YES → Delegate
                                     ↓ NO  → Do it yourself
```

**Rules of thumb:**
- < 5 tool calls needed → Do it yourself
- 5-15 tool calls needed → Consider delegating
- > 15 tool calls needed → Definitely delegate
- Need web search → Always delegate
- Need image generation → Always delegate
- Need science databases → Always delegate
