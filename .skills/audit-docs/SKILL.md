---
name: audit-docs
description: Verify documentation accuracy across all files. Check rule counts, version references, and links. Use before releases or after adding rules.
---

# Audit Project Documentation

Verify documentation is accurate and consistent across all files.

## Steps

1. **Count actual rules**
   ```bash
   # Total rules
   grep -rc "^const .* = {$" src/rules/

   # Auto-fixable breakdown
   grep -rc 'fixable: "code"' src/rules/
   grep -rc 'fixable: "whitespace"' src/rules/
   ```

2. **Check rule count references (must be uniform across ALL files)**

   When adding/removing rules, update counts in every location below. All counts (total rules, auto-fixable, configurable, report-only) must match everywhere.

   **Current Counts (update these values when changing rules):**
   - Total rules: 81
   - Auto-fixable: 71
   - Configurable: 22 (rules with ⚙️ that have options)
   - Report-only: 10

   **Files & approximate line locations to update:**

   | File | Line(s) | What to update |
   |------|---------|----------------|
   | `README.md` | ~22 | `*81 rules (71 auto-fixable, 22 configurable)*` |
   | `README.md` | ~30 | `**81 custom rules** (71 auto-fixable, 22 configurable)` |
   | `README.md` | ~39 | `71 of 81 rules support auto-fix` |
   | `README.md` | ~100 | `**71 rules** support automatic fixing... **22 rules** have configurable options. 10 rules are report-only` |
   | `README.md` | ~272 | `**81 rules total** — 71 with auto-fix, 22 configurable` |
   | `README.md` | ~409 | `71 of 81 rules support auto-fixing` |
   | `rules/README.md` | ~3 | `**81 rules total** — 71 with auto-fix, 22 configurable` |
   | `AGENTS.md` | ~7 | `81 custom formatting rules (71 auto-fixable, 22 configurable, 10 report-only)` |
   | `AGENTS.md` | ~37 | `(72 rules in JS projects, 81 in TS projects)` |
   | `AGENTS.md` | ~675 | `all 81 rules` |
   | `AGENTS.md` | ~733 | `71 auto-fixable rules, 22 configurable rules, 10 report-only` |
   | `recommended-configs/react/README.md` | ~286 | `**71 auto-fixable rules** (81 total, 22 configurable, 10 report-only)` |
   | `recommended-configs/react-ts/README.md` | ~361 | same shape |
   | `recommended-configs/react-tw/README.md` | ~321 | `**71 auto-fixable rules** (72 JavaScript-compatible rules out of 81 total)` |
   | `recommended-configs/react-ts-tw/README.md` | ~396 | same shape as `react/` |
   | `metadata.json` | top | `"totalRules": 81, "autoFixableRules": 71, "configurableRules": 22, "reportOnlyRules": 10` |

   The `metadata.json` counters drive the documentation website. See the **`website-sync`** skill for how the auto-sync pipeline propagates these values to https://www.eslint-plugin-code-style.org.

   **Quick Verification Commands:**

   ```bash
   # Count total rules
   grep -rc "^const [a-zA-Z]* = {$" src/rules/

   # Count auto-fixable (code)
   grep -rc 'fixable: "code"' src/rules/

   # Count auto-fixable (whitespace)
   grep -rc 'fixable: "whitespace"' src/rules/

   # Count configurable rules (rules with ⚙️ in README table)
   grep "| \`" README.md | grep -c "⚙️"

   # Find all rule count mentions (excluding CHANGELOG)
   grep -rn "[0-9][0-9] rules\|[0-9][0-9] auto" --include="*.md" | grep -v CHANGELOG
   ```

3. **Verify version consistency**
   ```bash
   grep '"version"' package.json
   git tag --sort=-version:refname | head -1
   ```
   - `package.json` version should match latest git tag
   - No outdated version references in docs

4. **Check paths exist**
   - Config paths mentioned in README
   - Test app paths
   - All internal markdown links

5. **Verify rule lists**
   - Rules in README "Rules Summary" table match actual rules
   - Rule categories in AGENTS.md are complete

## Report Format

```
Actual rule count: X
Auto-fixable: Y (Z code + W whitespace)
Report-only: X - Y

Rule count references (must be uniform):
- AGENTS.md (Project Overview): X [OK/OUTDATED]
- AGENTS.md (Code Structure): X [OK/OUTDATED]
- AGENTS.md (Current Counts breakdown): Z code + W whitespace [OK/OUTDATED]
- README.md: X [OK/OUTDATED]

Version:
- package.json: X.X.X
- Latest tag: vX.X.X
- Match: Yes/No

Broken links:
- [link text](path) - File not found

Missing rules in docs:
- rule-name

Extra rules in docs (not in src/rules/):
- old-rule-name
```
