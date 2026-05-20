# Antigravity CLI — Prompt Templates

Ready-to-use prompt templates for common delegation scenarios.
Copy, customize the bracketed values, and run.

## 1. Codebase Analysis

```bash
/Users/pedroarfux/.local/bin/agy -p "
Analyze the codebase at [PROJECT_PATH].

Focus on:
1. Overall architecture and design patterns used
2. Entry points and request flow
3. Database models and relationships
4. External service integrations
5. Authentication and authorization flow

Output a structured report with:
- Architecture diagram (text-based)
- File responsibility map
- Key dependencies
- Potential technical debt

Do NOT modify any files.
" --print-timeout 5m0s 2>&1
```

## 2. Security Audit

```bash
/Users/pedroarfux/.local/bin/agy -p "
Perform a security audit of [PROJECT_PATH].

Check for:
1. SQL injection vulnerabilities
2. XSS (Cross-Site Scripting) risks
3. CSRF protection gaps
4. Insecure authentication patterns
5. Hardcoded secrets or credentials
6. Insecure direct object references (IDOR)
7. Missing input validation
8. Insecure file upload handling

Output findings as a prioritized list:
- CRITICAL: [finding] in [file:line]
- HIGH: [finding] in [file:line]
- MEDIUM: [finding] in [file:line]
- LOW: [finding] in [file:line]

Include remediation recommendations for each finding.
Do NOT modify any files.
" --print-timeout 5m0s 2>&1
```

## 3. Test Suite Generation

```bash
/Users/pedroarfux/.local/bin/agy -p "
Generate comprehensive tests for [FILE_PATH].

Testing framework: [pytest/jest/mocha/etc.]
Test style: [unit/integration/e2e]

Cover these scenarios:
1. Happy path for all public functions
2. Edge cases (empty input, null, boundary values)
3. Error handling paths
4. [Any specific scenarios]

Requirements:
- Use fixtures for test data
- Add descriptive test names
- Include setup/teardown where needed
- Mock external dependencies

Output ONLY the complete test file content.
" --print-timeout 3m0s --dangerously-skip-permissions 2>&1
```

## 4. API Documentation Generation

```bash
/Users/pedroarfux/.local/bin/agy -p "
Generate API documentation for the REST endpoints in [PROJECT_PATH].

For each endpoint, document:
- HTTP method and URL
- Request parameters (query, path, body) with types
- Request/response examples (JSON)
- Authentication requirements
- Error responses
- Rate limiting (if applicable)

Output as OpenAPI 3.0 YAML specification.
Do NOT modify any files.
" --print-timeout 3m0s 2>&1
```

## 5. Database Schema Analysis

```bash
/Users/pedroarfux/.local/bin/agy -p "
Analyze the database schema defined in [PROJECT_PATH].

Provide:
1. Entity-Relationship diagram (text-based/mermaid)
2. Table relationships and foreign keys
3. Index analysis (missing indexes, unused indexes)
4. Normalization assessment
5. Migration history summary
6. Potential performance bottlenecks

Output as structured markdown.
Do NOT modify any files.
" --print-timeout 3m0s 2>&1
```

## 6. Performance Review

```bash
/Users/pedroarfux/.local/bin/agy -p "
Review [FILE_PATH] for performance issues.

Check for:
1. N+1 query patterns
2. Unnecessary database calls
3. Missing caching opportunities
4. Inefficient algorithms (O(n²) where O(n) is possible)
5. Memory leaks or excessive allocations
6. Blocking I/O in async context
7. Unoptimized loops

For each finding, provide:
- Location (file:line)
- Current complexity
- Suggested optimization
- Expected improvement

Do NOT modify any files.
" --print-timeout 3m0s 2>&1
```

## 7. Dependency Audit

```bash
/Users/pedroarfux/.local/bin/agy -p "
Audit the dependencies in [PROJECT_PATH].

Check:
1. Known vulnerabilities (search the web for CVEs)
2. Outdated packages with available updates
3. Unused dependencies
4. License compatibility issues
5. Dependency tree conflicts

Output as a markdown table:
| Package | Current | Latest | Vulnerabilities | License | Status |

Use Google Search for current vulnerability data.
" --print-timeout 3m0s 2>&1
```

## 8. Docker/Infrastructure Review

```bash
/Users/pedroarfux/.local/bin/agy -p "
Review the Docker configuration in [PROJECT_PATH].

Analyze:
1. Dockerfile best practices (multi-stage builds, layer caching)
2. docker-compose.yml service configuration
3. Volume mounts and networking
4. Environment variable handling
5. Health checks and restart policies
6. Image size optimization opportunities
7. Security (running as non-root, minimal base images)

Output findings with specific recommendations.
Do NOT modify any files.
" --print-timeout 3m0s 2>&1
```

## 9. Migration Planning

```bash
/Users/pedroarfux/.local/bin/agy -p "
Plan a migration for [PROJECT_PATH]:
FROM: [current technology/version]
TO: [target technology/version]

Analyze:
1. Breaking changes between versions
2. Deprecated APIs that need replacement
3. New features to leverage
4. Required dependency updates
5. Configuration changes needed
6. Estimated effort per file/module

Output a step-by-step migration plan with:
- Priority order
- Risk assessment per step
- Rollback strategy
- Testing requirements

Use Google Search for migration guides and changelogs.
Do NOT modify any files.
" --print-timeout 5m0s 2>&1
```

## 10. Code Translation

```bash
/Users/pedroarfux/.local/bin/agy -p "
Translate [FILE_PATH] from [SOURCE_LANGUAGE] to [TARGET_LANGUAGE].

Preserve:
- All business logic
- Error handling patterns
- Comments and documentation
- Variable naming conventions (adapted to target language idioms)

Use idiomatic [TARGET_LANGUAGE] patterns:
- [Specific patterns to use]
- [Libraries to prefer]

Output ONLY the translated file content.
" --print-timeout 3m0s 2>&1
```

## 11. Git History Analysis

```bash
/Users/pedroarfux/.local/bin/agy -p "
Analyze the git history of [PROJECT_PATH] for the last [N] commits.

Provide:
1. Summary of major changes
2. Most frequently modified files (hotspots)
3. Code churn analysis
4. Contributors and their focus areas
5. Any concerning patterns (large commits, force pushes)

Output as a structured report.
Do NOT modify any files.
" --print-timeout 3m0s 2>&1
```

## 12. Refactor with Tests

```bash
/Users/pedroarfux/.local/bin/agy -p "
Refactor [FILE_PATH] to [GOAL — e.g., extract classes, reduce complexity, apply SOLID].

Rules:
1. Write tests FIRST that capture current behavior
2. Apply refactoring incrementally
3. Run tests after each change
4. Preserve all public API signatures
5. Update docstrings to reflect changes

Output:
1. The test file
2. The refactored source file
3. Summary of changes made

Apply changes directly.
" --print-timeout 5m0s --dangerously-skip-permissions --add-dir [PROJECT_PATH] 2>&1
```
