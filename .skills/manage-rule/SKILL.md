---
name: manage-rule
description: Add, edit, or remove an ESLint rule. Includes all required file updates and documentation changes.
---

# Manage ESLint Rule

Complete workflow for adding, editing, or removing a rule from the plugin.

> **CRITICAL — metadata.json sync:** Any change to a rule's description, examples,
> options, rationale, or fixable/configurable flags MUST be reflected in
> `metadata.json` in the same commit. The documentation website
> (eslint-plugin-code-style.org) auto-syncs from `metadata.json` via GitHub Actions
> — do not manually edit the website repo. If the rule's name/category is added or
> removed, also bump the top-level counters (`totalRules`, `autoFixableRules`,
> `configurableRules`, `reportOnlyRules`) and matching count strings in
> `README.md`, `rules/README.md`, `AGENTS.md`, and per-config READMEs.
> Pipeline architecture, debugging steps, and full `metadata.json` field reference live in the **`website-sync`** skill.

## Operations

### Adding a New Rule

**Files to modify (in order):**

1. **`src/rules/<category>.js`** — Rule implementation
   - Add JSDoc block with description, Good/Bad examples, and options table
   - Add rule definition: `const ruleName = { create(), meta: {} }`
   - Add to `rules` object in the category's default export (alphabetically sorted)

2. **`index.d.ts`** — TypeScript types
   - Add to `RuleNames` type union (alphabetically sorted)
   - Add to `PluginRules` interface (alphabetically sorted)

3. **`README.md`** — Main documentation (4 sections!)
   - **Rule counts** (6 locations - see AGENTS.md "Rule Count Locations")
   - **Quick Start example** (~line 184) — Add rule alphabetically
   - **Rules Summary table** — Add row with description and emoji (🔧 auto-fixable, ⚙️ configurable)
   - **Detailed documentation** — Full section with examples and options

4. **`rules/<category>.md`** — Per-rule documentation page
   - Add detailed rule section: description, why, options table, good/bad examples, configuration snippets

5. **`metadata.json`** — Single source of truth for documentation website (CRITICAL)
   - Add rule entry under the appropriate category with: `name`, `description`, `rationale`, `isFixable`, `isConfigurable`, `isTsOnly`, `goodExample`, `badExample`, `options[]`
   - Bump top-level counters (`totalRules`, `autoFixableRules`, `configurableRules`, `reportOnlyRules`) as applicable
   - Website (eslint-plugin-code-style.org) auto-syncs from this file via GitHub Actions — do not edit website repo manually

6. **`AGENTS.md`** — Agent instructions
   - Update rule counts in "Current Counts" table
   - Add rule to appropriate category in "Rule Categories" section

7. **Config files** (alphabetically sorted)
   - `recommended-configs/react-ts-tw/eslint.config.js`
   - `recommended-configs/react/eslint.config.js` (skip if TypeScript-only)
   - `_tests_/v9/react-ts-tw/eslint.config.js`
   - `_tests_/v9/react/eslint.config.js` (skip if TypeScript-only)

8. **Config READMEs**
   - `recommended-configs/react-ts-tw/README.md`
   - `recommended-configs/react/README.md`

9. **Version bump, CHANGELOG, tag, GitHub Release** — MINOR (`x.+1.0`)
   - See the **`release-workflow`** skill for the complete release procedure (SemVer decisions, CHANGELOG release format, annotated-tag conventions, GitHub Release creation). Every version is published as a GitHub Release.

**TypeScript-only rules** (only add to `-ts-tw` configs):
- `component-props-inline-type`, `enum-format`, `enum-type-enforcement`, `interface-format`
- `no-inline-type-definitions`, `type-annotation-spacing`, `type-format`
- `typescript-definition-location`

---

### Editing an Existing Rule

**Bug fix (PATCH `x.x.+1`):**
- Fix in `src/rules/<category>.js` → Test
- Update `rules/<category>.md` if behavior/examples changed
- Update `metadata.json` rule entry if description/examples/rationale changed (website auto-syncs)
- For the version bump, CHANGELOG entry (PATCH format), comparison-link reference, commit, annotated tag, and GitHub Release — see the **`release-workflow`** skill

**Behavior change (PATCH/MINOR):**
- Update `src/rules/<category>.js` logic and JSDoc
- Update `README.md` rule documentation section (examples, description)
- Update `rules/<category>.md` per-rule docs
- Update `metadata.json` rule entry (`description`, `rationale`, `goodExample`, `badExample` — website auto-syncs)
- Test with `npm run lint` and `npm run lint:fix`

**Adding options (MINOR x.+1.0):**
- Add to `schema` in rule's `meta` object
- Handle in `create()` with default value
- Update JSDoc Options section
- Update README.md options table + add ⚙️ emoji in Rules Summary row
- Update `rules/<category>.md` per-rule docs — add options table + configuration example
- Update `metadata.json`: set `isConfigurable: true`, add option(s) to `options[]` array with `name/type/default/description`, bump `configurableRules` counter (and rule row in count strings everywhere if rule was previously non-configurable)

**Adding auto-fix (MINOR x.+1.0):**
- Add `fixable: "code"` or `fixable: "whitespace"` to `meta`
- Add `fix()` function in `context.report()`
- Add 🔧 emoji in README Rules Summary table
- Update auto-fixable counts in ALL docs (must be uniform everywhere)
- Update `metadata.json`: set `isFixable: true`, bump `autoFixableRules` counter (website auto-syncs)
- Update AGENTS.md "Current Counts" breakdown (code vs whitespace counts)

**Changing defaults (MAJOR +1.0.0):**
- This is a breaking change
- Update default value
- Update all documentation
- Commit with `!`: `feat!: change default`

---

### Removing a Rule

**IMPORTANT:** This is a BREAKING CHANGE requiring MAJOR version (+1.0.0)

**Files to modify:**

1. **`src/rules/<category>.js`** — Remove JSDoc, rule definition, and from `rules` object
2. **`index.d.ts`** — Remove from `RuleNames` and `PluginRules`
3. **`README.md`** — Remove from all 4 sections (counts, Quick Start, table, detailed docs)
4. **`AGENTS.md`** — Update counts and remove from categories
5. **Config files** — Remove from all 4 config files
6. **Config READMEs** — Update counts

---

## Testing

```bash
cd _tests_/v9/react-ts-tw  # or _tests_/v9/react for JS rules

# Create temp test file with valid and invalid code
# then run:
npm run lint           # Check errors are reported
npm run lint:fix       # Verify auto-fix works
cat src/test-file.tsx  # Verify fixed code

# Clean up temp test file
rm src/test-file.tsx
```

## Verification

```bash
# Count rules in each location
grep -rc "^const .* = {$" src/rules/
grep -c 'code-style/' index.d.ts
grep -c '"code-style/' recommended-configs/react-ts-tw/eslint.config.js
```

All counts should match. Use `audit-docs` skill to verify documentation consistency.

## Commit Message Format

- New rule: `feat: add rule-name rule`
- Bug fix: `fix: description of fix in rule-name`
- Options: `feat: add optionName option to rule-name`
- Remove: `feat!: remove rule-name rule`

---

## Comment Style

The codebase uses two distinct comment styles depending on location and purpose. Follow them when authoring or editing rule files for consistency.

### File-top rule JSDoc — `/** ... */` block

Every rule definition has a JSDoc block at the top describing the rule. Always use the block form, never consecutive `//`.

```js
/**
 * ───────────────────────────────────────────────────────────────
 * Rule: My Rule Name
 * ───────────────────────────────────────────────────────────────
 *
 * Description:
 *   What the rule enforces.
 *
 * Options:
 *   { myOption: "value" } (default: "value")
 *
 * ✓ Good:
 *   // example
 *
 * ✗ Bad:
 *   // example
 */
const myRule = { create(context) { ... }, meta: { ... } };
```

### Inline comments — `//` per line

For all inline comments inside the rule body — whether single-line or multi-line — use consecutive `//` lines. Do NOT use `/* ... */` block style for inline comments. This matches the dominant codebase convention (47+ multi-line `//` blocks across `src/rules/`, only ~3 outliers).

**Good (preferred — `//` consecutive):**

```js
// Skip Handler suffix / verb prefix checks for functions in reducers folder.
// Reducer files follow the `<name>Reducer` convention enforced by
// folder-based-naming-convention — Handler suffix would produce
// ugly compound names like `authReducerHandler`.
if (isInDropHandlerFolderHandler()) return;
```

**Avoid (inline block style — only for top-of-rule JSDoc):**

```js
/*
 * Skip Handler suffix / verb prefix checks for functions in reducers folder.
 * ...
 */
if (isInDropHandlerFolderHandler()) return;
```

### Why this split

| Comment type | Style | Reasoning |
|--------------|-------|-----------|
| File-top rule docs | `/** */` | JSDoc-compatible — extracted by tooling, picked up by IDE hover, parsed for website docs |
| Inline (single + multi-line) | `//` | Codebase tradition; adding/removing lines requires no star-prefix maintenance; works naturally for inline code examples mixed with prose |

### Single-line `/* foo */` is auto-converted

The `code-style/comment-format` rule auto-fixes single-line block comments to `//`. Don't write single-line `/* foo */` — it will be rewritten on save. ESLint directive comments (`/* eslint-disable */`) are exempt.

---

## Reference Material

Detailed code templates, ESLint API patterns, and verification commands live in `reference/` to keep this SKILL.md focused on workflow. Load them on-demand:

| File | When to use |
|------|-------------|
| `reference/templates.md` | New rule — copy the rule scaffold template; or write the per-rule `rules/<category>.md` docs section |
| `reference/eslint-api.md` | Inside `create()` — token access, fixer methods, common node-type checks, skip-condition patterns |
| `reference/testing-and-verification.md` | After authoring/editing — quick temp-file test workflow and rule-count verification commands |
