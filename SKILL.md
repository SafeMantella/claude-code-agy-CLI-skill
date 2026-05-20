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

## Binary Location

```
/Users/pedroarfux/.local/bin/agy
```

Always use the full path to avoid shell resolution issues.

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
/Users/pedroarfux/.local/bin/agy -p "YOUR PROMPT HERE" 2>&1
```

**Key flags:**

| Flag | Purpose |
|------|---------|
| `-p "prompt"` / `--print "prompt"` | Non-interactive mode — run prompt, print response, exit |
| `--print-timeout 5m0s` | Timeout for print mode (default 5m, increase for complex tasks) |
| `--dangerously-skip-permissions` | Auto-approve all tool calls (USE WITH CAUTION) |
| `--add-dir /path/to/dir` | Add a directory to the workspace (repeatable) |
| `--conversation <id>` | Resume a specific conversation by ID |
| `-c` / `--continue` | Continue the most recent conversation |
| `--sandbox` | Run with terminal restrictions enabled |

### Interactive Mode (Advanced — rarely needed)

```bash
/Users/pedroarfux/.local/bin/agy
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
/Users/pedroarfux/.local/bin/agy -p "
Analyze the authentication flow in the project at /Users/pedroarfux/my-project.
Map all files involved, identify the auth middleware, session handling, and token validation.
Output a structured summary with file paths and line numbers.
Do NOT modify any files.
" --print-timeout 3m0s 2>&1
```

Then parse the output and use it to make targeted edits yourself.

### Pattern 2: Code Generation

Have Antigravity generate code, then you review and integrate it.

```bash
/Users/pedroarfux/.local/bin/agy -p "
Generate a comprehensive test suite for /Users/pedroarfux/my-project/src/auth.py.
Use pytest with fixtures. Cover: login, logout, token refresh, expired tokens, invalid credentials.
Output ONLY the test file content, no explanations.
" --print-timeout 3m0s --dangerously-skip-permissions 2>&1
```

### Pattern 3: Code Review

Get a second opinion on code you've written or are about to modify.

```bash
/Users/pedroarfux/.local/bin/agy -p "
Review this code for security vulnerabilities, performance issues, and bugs:

File: /Users/pedroarfux/my-project/src/handler.py

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
/Users/pedroarfux/.local/bin/agy -p "
Search the web for the latest security advisories for Flask 3.x.
Include CVE numbers, affected versions, and recommended fixes.
Output as a markdown table.
" --print-timeout 2m0s 2>&1
```

### Pattern 5: Multi-File Refactor

Delegate large refactors that would exhaust your context.

```bash
/Users/pedroarfux/.local/bin/agy -p "
Refactor all database queries in /Users/pedroarfux/my-project/src/ to use parameterized queries
instead of string formatting. Apply changes directly to the files.

Rules:
- Preserve all existing comments and docstrings
- Maintain the same function signatures
- Add type hints where missing
- Run the existing tests after changes
" --print-timeout 5m0s --dangerously-skip-permissions --add-dir /Users/pedroarfux/my-project 2>&1
```

### Pattern 6: Conversation Continuation

For complex multi-step tasks, continue a previous conversation.

```bash
# First call — start analysis
/Users/pedroarfux/.local/bin/agy -p "Analyze the database schema in /Users/pedroarfux/my-project" --print-timeout 3m0s 2>&1

# Follow-up — continue the same conversation
/Users/pedroarfux/.local/bin/agy -c -p "Now suggest optimizations for the slowest queries" --print-timeout 3m0s 2>&1
```

### Pattern 7: Image Generation

Antigravity can generate images — use it for visual assets.

```bash
/Users/pedroarfux/.local/bin/agy -p "
Generate a modern, dark-themed login page mockup with:
- Glassmorphism card centered on screen
- Email and password fields
- Social login buttons (Google, GitHub)
- Gradient background (purple to blue)
Save the image to /Users/pedroarfux/my-project/mockups/login.png
" --print-timeout 3m0s --dangerously-skip-permissions 2>&1
```

### Pattern 8: Scientific/Biology Queries

Antigravity has 40+ specialized science skills. Delegate all bio/chem queries.

```bash
/Users/pedroarfux/.local/bin/agy -p "
Search PubMed for recent papers on CRISPR-Cas9 off-target effects in human cells.
Return the top 10 results with: title, authors, journal, year, PMID, and a 2-sentence summary.
Output as a markdown table.
" --print-timeout 3m0s 2>&1
```

## Error Handling

### Common Issues

| Error | Cause | Fix |
|-------|-------|-----|
| `command not found` | Binary not in PATH | Use full path: `/Users/pedroarfux/.local/bin/agy` |
| Timeout | Task took too long | Increase `--print-timeout` (e.g., `10m0s`) |
| Permission denied | Tool call needs approval | Add `--dangerously-skip-permissions` |
| Rate limit | API quota exceeded | Wait and retry, or simplify the prompt |
| Empty output | Prompt too vague | Be more specific, include file paths |

### Output Processing

Antigravity's output in `-p` mode goes to stdout. Common patterns:

```bash
# Capture output to variable
RESULT=$(/Users/pedroarfux/.local/bin/agy -p "your prompt" 2>&1)

# Save output to file
/Users/pedroarfux/.local/bin/agy -p "your prompt" 2>&1 > /tmp/agy-output.txt

# Pipe through processing
/Users/pedroarfux/.local/bin/agy -p "your prompt" 2>&1 | grep -A5 "CRITICAL"
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
