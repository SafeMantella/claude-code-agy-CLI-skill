# Antigravity CLI — Advanced Patterns

Orchestration patterns for complex multi-agent workflows.

## Pattern 1: Research → Decide → Execute

The most common pattern. Use Antigravity for research, make decisions yourself,
then either execute yourself or delegate execution.

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Antigravity  │────▶│  Claude Code │────▶│ Claude Code  │
│  RESEARCH    │     │   DECIDE     │     │  or Agy      │
│  (delegate)  │     │  (yourself)  │     │  EXECUTE     │
└─────────────┘     └─────────────┘     └─────────────┘
```

**Example: Choosing a library**

```bash
# Step 1: Delegate research
RESEARCH=$(/Users/pedroarfux/.local/bin/agy -p "
Search the web for the best Python ORMs in 2026.
Compare: SQLAlchemy, Tortoise-ORM, SQLModel, Peewee, Django ORM (standalone).
For each, provide: performance benchmarks, async support, learning curve, community size.
Output as a comparison table.
" --print-timeout 3m0s 2>&1)

# Step 2: You read the output, make the decision
# Step 3: You implement with the chosen library
```

## Pattern 2: Parallel Delegation

Run multiple Antigravity tasks simultaneously for independent subtasks.

```bash
# Launch multiple tasks in background
/Users/pedroarfux/.local/bin/agy -p "Analyze auth module in /path/to/project" --print-timeout 3m0s 2>&1 > /tmp/agy-auth.txt &
PID1=$!

/Users/pedroarfux/.local/bin/agy -p "Analyze database module in /path/to/project" --print-timeout 3m0s 2>&1 > /tmp/agy-db.txt &
PID2=$!

/Users/pedroarfux/.local/bin/agy -p "Analyze API routes in /path/to/project" --print-timeout 3m0s 2>&1 > /tmp/agy-api.txt &
PID3=$!

# Wait for all to complete
wait $PID1 $PID2 $PID3

# Read results
cat /tmp/agy-auth.txt
cat /tmp/agy-db.txt
cat /tmp/agy-api.txt
```

**⚠️ Warning:** Running too many parallel instances may hit API rate limits.
Limit to 2-3 concurrent tasks.

## Pattern 3: Conversational Depth

For complex tasks requiring follow-up, use conversation continuation.

```bash
# Initial analysis
/Users/pedroarfux/.local/bin/agy -p "
Analyze the database performance in /path/to/project.
Identify the 5 slowest query patterns.
" --print-timeout 3m0s 2>&1

# Follow-up with context from previous conversation
/Users/pedroarfux/.local/bin/agy -c -p "
For the top 3 slowest queries you found, generate optimized versions
with proper indexing suggestions.
" --print-timeout 3m0s 2>&1

# Final follow-up
/Users/pedroarfux/.local/bin/agy -c -p "
Generate the migration file to add those indexes. Use Alembic format.
Output ONLY the migration file content.
" --print-timeout 3m0s 2>&1
```

## Pattern 4: Generate → Review Loop

Use Antigravity to generate, then review its own output for quality assurance.

```bash
# Step 1: Generate
/Users/pedroarfux/.local/bin/agy -p "
Generate a REST API handler for user management (CRUD) in Flask.
Include: input validation, error handling, pagination, rate limiting.
Apply to /path/to/project/src/api/users.py
" --print-timeout 3m0s --dangerously-skip-permissions --add-dir /path/to/project 2>&1

# Step 2: Self-review (new conversation = fresh perspective)
/Users/pedroarfux/.local/bin/agy -p "
Review /path/to/project/src/api/users.py for:
1. Security vulnerabilities (SQL injection, XSS, CSRF)
2. Missing error handling
3. Performance issues
4. API design best practices violations

Output a prioritized list of findings.
Do NOT modify the file.
" --print-timeout 2m0s 2>&1

# Step 3: You decide which findings to fix (yourself or delegate)
```

## Pattern 5: Context Bridge

When you need Antigravity to work with context you already have, pipe it in.

```bash
# Pipe your current context as input
echo "Here is the error trace:
$(cat /path/to/error.log | tail -50)

And here is the relevant code:
$(cat /path/to/handler.py)

Diagnose the root cause and suggest a fix." | /Users/pedroarfux/.local/bin/agy -p "$(cat)" --print-timeout 2m0s 2>&1
```

Alternative approach — write context to a temp file:

```bash
# Write your analysis to a temp file
cat > /tmp/agy-context.md << 'EOF'
# Current State
- We're migrating from Flask to FastAPI
- 15 of 30 routes have been migrated
- The following routes are failing tests: ...

# Task
Analyze the remaining 15 unmigrated routes and generate a migration plan.
EOF

/Users/pedroarfux/.local/bin/agy -p "$(cat /tmp/agy-context.md)" --print-timeout 5m0s --add-dir /path/to/project 2>&1
```

## Pattern 6: Workspace Scoping

Target Antigravity to specific directories for focused analysis.

```bash
# Add specific directories to workspace
/Users/pedroarfux/.local/bin/agy -p "
Analyze the test coverage gaps in this project.
Focus on the src/ directory and tests/ directory.
Identify untested functions and suggest test cases.
" --add-dir /path/to/project/src --add-dir /path/to/project/tests --print-timeout 3m0s 2>&1
```

## Pattern 7: Staged Refactor

Break large refactors into stages, using Antigravity for each stage.

```bash
# Stage 1: Analysis
ANALYSIS=$(/Users/pedroarfux/.local/bin/agy -p "
Analyze /path/to/project for all uses of the deprecated 'old_api' module.
List every file and function that imports or uses it.
Do NOT modify any files.
" --print-timeout 3m0s 2>&1)

# Stage 2: You create the migration plan from the analysis

# Stage 3: Delegate the actual refactor per module
for module in auth users billing; do
  /Users/pedroarfux/.local/bin/agy -p "
  In /path/to/project/src/${module}/:
  Replace all uses of 'old_api' with 'new_api'.
  The mapping is:
  - old_api.fetch() → new_api.get()
  - old_api.send() → new_api.post()
  - old_api.modify() → new_api.patch()
  Apply changes directly. Run tests after.
  " --print-timeout 3m0s --dangerously-skip-permissions --add-dir /path/to/project 2>&1
done
```

## Pattern 8: Docker Context

Since the user runs apps in Docker, delegate container-aware tasks.

```bash
/Users/pedroarfux/.local/bin/agy -p "
The application runs in Docker. Analyze the docker-compose setup at /path/to/project.
Then run these commands inside the container to diagnose the issue:
  docker compose exec web python -c 'from app import db; print(db.engine.url)'
  docker compose exec web flask db current
  docker compose exec web flask db heads

Report the database migration status and any inconsistencies.
" --print-timeout 3m0s --dangerously-skip-permissions 2>&1
```

## Anti-Patterns (What NOT to Do)

### ❌ Don't delegate trivial tasks
```bash
# BAD — this wastes time and tokens
/Users/pedroarfux/.local/bin/agy -p "Add a comment to line 5 of /path/to/file.py" 2>&1
# Just do it yourself!
```

### ❌ Don't delegate without context
```bash
# BAD — Antigravity has no idea what "the bug" is
/Users/pedroarfux/.local/bin/agy -p "Fix the bug" 2>&1
# GOOD — be specific about the problem
/Users/pedroarfux/.local/bin/agy -p "Fix the TypeError in /path/file.py:42 where 'NoneType' has no attribute 'id'" 2>&1
```

### ❌ Don't forget to validate output
```bash
# BAD — blindly trusting generated code
RESULT=$(/Users/pedroarfux/.local/bin/agy -p "Generate auth middleware" 2>&1)
echo "$RESULT" > /path/to/middleware.py  # Dangerous!

# GOOD — review first, then apply selectively
RESULT=$(/Users/pedroarfux/.local/bin/agy -p "Generate auth middleware" 2>&1)
# Read RESULT, review the code, then write only the parts you trust
```

### ❌ Don't run too many parallel instances
```bash
# BAD — will hit rate limits
for i in $(seq 1 10); do
  /Users/pedroarfux/.local/bin/agy -p "Task $i" &
done
# GOOD — limit to 2-3 concurrent
```
