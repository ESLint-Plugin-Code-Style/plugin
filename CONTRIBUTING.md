# Contributing to eslint-plugin-code-style

Thanks for considering a contribution. This plugin enforces opinionated code-style rules for React/JSX (and TS-aware) projects, so any change to a rule's behavior or surface area affects every consumer downstream. Please read this guide before opening a PR.

## Table of Contents

- [Welcome](#welcome)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Repository Layout](#repository-layout)
- [Where to Start (Skills)](#where-to-start-skills)
- [Add a New Rule](#add-a-new-rule)
- [Edit an Existing Rule](#edit-an-existing-rule)
- [Test a Rule](#test-a-rule)
- [Release a New Version](#release-a-new-version)
- [Commit Conventions](#commit-conventions)
- [Pull Request Process](#pull-request-process)
- [Reporting Bugs](#reporting-bugs)
- [License](#license)

---

## Welcome

Contributions welcome for:

- **Bug fixes** to existing rules (rule misbehaves, autofix is wrong, false positive/negative)
- **New rules** that fit the plugin's React/JSX/TS code-style scope
- **New options** that extend an existing rule's flexibility
- **Documentation** improvements (rule docs, examples, options tables)
- **Test app code** that exercises rules more broadly

Out of scope: rules that contradict existing conventions, breaking changes without a strong rationale, vendor-specific lock-ins.

## Prerequisites

- Node.js ≥ 20
- npm
- A working knowledge of how ESLint rules and AST visitors work

## Setup

```bash
git clone https://github.com/ESLint-Plugin-Code-Style/plugin.git
cd plugin
npm install
npm run build
```

`npm run build` regenerates `dist/index.js` via esbuild. It must succeed before pushing.

## Repository Layout

```text
src/
  rules/                   # Rule implementations, grouped by category
  index.js                 # Plugin entry — registers all rules
dist/                      # Built output (committed; npm publishes this)
rules/                     # Per-category Markdown docs (user-facing)
recommended-configs/       # Shipped `eslint.config.js` configurations
_tests_/                   # Test apps (lint runs against these)
.skills/                   # Agent skills (contributor + Claude workflows)
metadata.json              # Single source of truth for the docs website
README.md                  # User-facing docs
AGENTS.md                  # Project orientation for contributors and AI agents
CLAUDE.md                  # Claude-specific session directives
CHANGELOG.md               # Per-tag release notes
```

## Where to Start (Skills)

Most contributor workflows live as **skills** in `.skills/`. Pick the one matching your task — each skill loads only the steps it needs:

| Skill | Use when |
|-------|---------|
| `.skills/manage-rule/SKILL.md` | Adding, editing, or removing a rule |
| `.skills/release-workflow/SKILL.md` | Bumping a version, writing the CHANGELOG entry, tagging, creating a GitHub Release |
| `.skills/audit-docs/SKILL.md` | Verifying rule-count uniformity across docs before a release |
| `.skills/review-config/SKILL.md` | Reviewing a recommended config for consistency |
| `.skills/test-rule/SKILL.md` | Smoke-testing an existing rule |
| `.skills/validate-types/SKILL.md` | Confirming `index.d.ts` matches the rules in `src/index.js` |
| `.skills/website-sync/SKILL.md` | Understanding the `metadata.json` → docs website sync pipeline, debugging the auto-sync, or making changes that require manual website edits |

Skills supersede the workflow sections that previously lived in `AGENTS.md`. If your task does not match a skill, `AGENTS.md` still holds project-wide orientation, conventions, and invariants.

## Add a New Rule

Full step-by-step in `.skills/manage-rule/SKILL.md`. High-level flow:

1. Pick or create the appropriate category file in `src/rules/`.
2. Add the rule implementation following the JSDoc + `create() + meta` scaffold in `.skills/manage-rule/reference/templates.md`.
3. Register the rule in `src/index.js` and `index.d.ts` (alphabetically sorted).
4. Add detailed docs to `rules/<category>.md`.
5. Update `metadata.json` (rule entry + counters). The docs website syncs from this file automatically — see `AGENTS.md` → Documentation Website.
6. Enable the rule in every relevant `recommended-configs/v*/<config>/eslint.config.js` and matching `_tests_/v9/<config>/eslint.config.js`.
7. Update rule-count strings everywhere — `audit-docs` skill lists the exact locations.
8. Test (see [Test a Rule](#test-a-rule)).
9. Release as MINOR (see [Release a New Version](#release-a-new-version)).

## Edit an Existing Rule

Same skill: `.skills/manage-rule/SKILL.md` → "Editing an Existing Rule" section. Choose the variant that matches your change:

| Variant | Version impact |
|---------|---------------|
| Bug fix | PATCH |
| Behavior change | PATCH or MINOR depending on scope |
| Add option | MINOR |
| Add auto-fix to a rule that didn't have one | MINOR |
| Change a default | MAJOR (breaking) |

Always update `rules/<category>.md` and `metadata.json` if the description, examples, or options change.

## Test a Rule

Manual workflow during development:

```bash
# 1. Pick the appropriate test app
cd _tests_/v9/react           # JS rules
# or
cd _tests_/v9/react-ts-tw     # TS + Tailwind rules

# 2. Create a temporary test file in src/
# 3. Add PASS and FAIL examples for your rule
# 4. Run the linter
npx eslint src/<your-test-file>          # Should report expected errors
npx eslint src/<your-test-file> --fix    # Should auto-fix correctly

# 5. Make sure no regressions across the rest of the test app
npx eslint src/

# 6. Delete the temporary test file before committing
```

Detailed workflow + verification commands: `.skills/manage-rule/reference/testing-and-verification.md`.

The `test-rule` skill describes a more general workflow for smoke-testing an existing rule.

## Release a New Version

The complete release procedure (SemVer choice, CHANGELOG format, annotated tag, GitHub Release, `npm publish`) lives in `.skills/release-workflow/SKILL.md`. Follow it end-to-end — releases must update both the npm-shipped files (`dist`, `index.d.ts`, `README.md`) and the website source of truth (`metadata.json`). **Every version (PATCH, MINOR, MAJOR) is published as a GitHub Release and shipped to npm** — the [Releases tab](https://github.com/ESLint-Plugin-Code-Style/plugin/releases) is the full-history source of truth (it may list more versions than npm, since some historic tags were never published — see the release-workflow skill).

## Commit Conventions

This project follows [Conventional Commits](https://www.conventionalcommits.org/). Common subjects:

```text
feat: add <rule-name> rule
fix: <short description of fix> in <rule-name>
feat: add <option-name> option to <rule-name>
chore: release vX.Y.Z - <brief description>
docs: <user-facing doc change>
```

Full commit-message guide: `.skills/release-workflow/SKILL.md`.

**Notes for Claude / AI agents:** commits in this repo do NOT include `Co-Authored-By` lines or AI signatures/footers (see `CLAUDE.md`).

## Pull Request Process

1. Fork the repository (or create a branch if you have push access).
2. Branch from `main`. Name it descriptively (`fix/folder-based-naming-services-dedupe`, `feat/no-hardcoded-strings-css-in-js-tags`, etc.).
3. Make your changes following the relevant skill workflow.
4. Run `npm run build` and verify `dist/index.js` is up-to-date.
5. Test your change in the appropriate `_tests_/v9/<config>/` app.
6. Update `metadata.json` and `rules/<category>.md` if the rule's user-visible surface changed.
7. Update `CHANGELOG.md` and bump `package.json` only if your change ships to npm consumers (see `release-workflow` skill for the file-by-file rule).
8. Open a PR against `main` with a clear summary and link to any related issue (`Closes #N`).
9. Wait for CI and review.

## Reporting Bugs

Please use the **Bug report** issue template. Include:

- Plugin version (`npm ls eslint-plugin-code-style`)
- ESLint version
- Minimal reproduction (a code snippet that triggers the issue)
- Expected vs actual behavior
- Screenshot of the editor / terminal error if relevant

For feature requests, use the **Feature request** template.

## License

By contributing, you agree that your contributions are licensed under the [MIT License](./LICENSE) that covers this project.
