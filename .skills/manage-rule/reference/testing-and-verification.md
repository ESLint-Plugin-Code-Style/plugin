# Testing & Verification

Quick test workflow during rule development plus verification commands to confirm rules are wired into every required location. Cited from the `manage-rule` SKILL.md.

### A4. Testing the Rule

Quick verification workflow during development:

```bash
# 1. Build the plugin so dist/index.js reflects src/ changes
npm run build

# 2. Create a temp test file in the appropriate test project
#    _tests_/v9/react-ts-tw/  → TS rules
#    _tests_/v9/react/        → general JS rules
touch _tests_/v9/react-ts-tw/src/test-rule-name.tsx

# 3. Add a PASS case and a FAIL case in the temp file

# 4. Run the linter
cd _tests_/v9/react-ts-tw
npx eslint src/test-rule-name.tsx          # report errors
npx eslint src/test-rule-name.tsx --fix    # verify autofix
npx eslint src/                            # check no regressions

# 5. Clean up the temp test file (always)
rm _tests_/v9/react-ts-tw/src/test-rule-name.tsx
```

See `test-rule` skill for the more general "test an existing rule" workflow.

### A5. Verification Commands

```bash
# Total rules
grep -rc "^const [a-zA-Z]* = {$" src/rules/

# Rules in index.d.ts
grep -c 'code-style/' index.d.ts

# Rules in recommended config
grep -c '"code-style/' recommended-configs/react-ts-tw/eslint.config.js

# Find rules missing from README
grep -oE '"[a-z-]+":' src/index.js | tr -d '":' | sort > /tmp/a.txt
grep -oE '\`[a-z-]+\`' README.md | tr -d '\`' | sort | uniq > /tmp/b.txt
comm -23 /tmp/a.txt /tmp/b.txt
```

See `audit-docs` skill for the full documentation-consistency audit (rule counts in every location, broken links, version mismatches).
