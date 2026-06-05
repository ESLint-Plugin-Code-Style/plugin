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
   - Total rules: 82
   - Auto-fixable: 72
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
   | `recommended-configs/{v9,v10}/{react,react-ts,react-tw,react-ts-tw}/README.md` | — | **8 files** (v9 + v10 × 4 variants). `Our N custom formatting rules` + `**N auto-fixable rules** (N total / N JavaScript-compatible out of N total, …)`. Counts differ per variant: full set vs JS-compatible (total − 9 TS-only). |
   | `metadata.json` | top | `"totalRules", "fixableRules", "configurableRules", "reportOnlyRules"` |

<https://www.eslint-plugin-code-style.org>
   The `metadata.json` counters drive the documentation website. See the **`website-sync`** skill for how the auto-sync pipeline propagates these values to <https://www.eslint-plugin-code-style.org>.

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

   # MISS-PROOF: after bumping counts, grep for the OLD numbers everywhere.
   # This must return NOTHING (except historical CHANGELOG entries + the
   # deprecation link). Replace OLD_TOTAL / OLD_FIXABLE with the previous values.
   grep -rnE "\bOLD_TOTAL\b|\bOLD_FIXABLE\b" \
     README.md AGENTS.md rules/README.md metadata.json \
     recommended-configs/*/*/README.md \
     | grep -viE "CHANGELOG|deprecat|v8\.53|/(issues|pull)/|http"
   ```

   > **Why counts get missed:** they appear in ~17 spots with different
   > phrasings (full-set vs JS-compatible-vs-total, per-variant config READMEs
   > across BOTH `v9/` and `v10/`, AGENTS' JS/TS split). The table above is a
   > map; the **miss-proof grep is the safety net** — always run it last and
   > confirm zero hits before considering the count change complete.

1. **Verify version consistency**

   ```bash
   grep '"version"' package.json

   git tag --sort=-version:refname | head -1
   ```

   - `package.json` version should match latest git tag
   - No outdated version references in docs

2. **Check paths exist**
   - Config paths mentioned in README
   - Test app paths
   - All internal markdown links

3. **Verify rule lists**
   - Rules in README "Rules Summary" table match actual rules
   - Rule categories in AGENTS.md are complete

## Report Format

```text
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
