<!--
Thanks for sending a pull request. Please fill in every relevant section.
For multi-rule changes, repeat the rule-specific sections per rule.
-->

## Summary

<!-- One-paragraph description of the change. What does this PR do and why? -->

## Type of Change

<!-- Check all that apply. -->

- [ ] 🐞 Bug fix (PATCH)
- [ ] ✨ New rule (MINOR)
- [ ] ✨ New option for an existing rule (MINOR)
- [ ] 🔧 New auto-fix added to an existing rule (MINOR)
- [ ] 🛠️ Behavior change to an existing rule (PATCH or MINOR)
- [ ] 💥 Breaking change (MAJOR) — changed default, removed/renamed rule
- [ ] 📝 Documentation only (PATCH or no-tag if contributor docs only)
- [ ] 🧹 Internal / chore (no tag) — skills, AGENTS.md, repo hygiene

## Related Issue

<!-- Link the issue this PR closes. Use "Closes #123" so GitHub auto-closes it on merge. -->

Closes #

## Changes

<!-- Bullet list of the concrete changes in this PR. -->

-
-

## Rule(s) Affected

<!-- List the `code-style/...` rules added or changed in this PR. -->

-

## Checklist

<!-- Tick every box that applies. Unchecked boxes from skipped items are OK. -->

### Code

- [ ] `npm run build` succeeds and `dist/index.js` is up to date
- [ ] Tested locally in `_tests_/v9/<config>/` with `npm run lint` and `npm run lint:fix`
- [ ] No regressions in other rules

### Per-rule docs

- [ ] Updated `src/rules/<category>.js` JSDoc (description, Good/Bad, Options)
- [ ] Updated `rules/<category>.md` if user-visible behavior or options changed
- [ ] Updated `metadata.json` rule entry (`description`, `rationale`, `goodExample`, `badExample`, `options[]`, `isFixable`, `isConfigurable`) — the docs website syncs from this file

### README + counts

- [ ] Updated `README.md` Rules Summary table row (added/edited rule + correct emojis 🔧 ⚙️)
- [ ] Updated `README.md` Quick Start example (alphabetically sorted)
- [ ] Updated all rule-count strings (run `audit-docs` skill, or update the locations listed in `.skills/audit-docs/SKILL.md`)

### Configs

- [ ] Added/updated rule in `recommended-configs/v*/<config>/eslint.config.js`
- [ ] Mirrored in `_tests_/v9/<config>/eslint.config.js`
- [ ] Updated `recommended-configs/v*/<config>/README.md` if counts changed

### Release (only for changes that ship to npm)

- [ ] Bumped `package.json` version per SemVer (see `.skills/release-workflow/SKILL.md`)
- [ ] Added CHANGELOG entry (release format for MINOR/MAJOR, simple tag format for PATCH)
- [ ] Added comparison link reference at bottom of `CHANGELOG.md`
- [ ] For MINOR/MAJOR: updated `Current releases` list in `AGENTS.md`

### TypeScript

- [ ] Updated `index.d.ts` (`RuleNames` + `PluginRules`) if a rule was added or renamed
- [ ] TypeScript-only rules added only to `-ts-tw` configs

## Screenshot or Code Sample (if relevant)

<!-- Paste before/after of a code snippet that illustrates the change, or a screenshot of the lint output. -->

## Notes for Reviewers

<!-- Anything reviewers should pay attention to: tricky edge cases, deferred follow-ups, etc. -->
