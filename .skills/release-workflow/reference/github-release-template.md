# GitHub Release Description Template

Format for the `gh release create` notes body for MINOR/MAJOR releases. Cited from the `release-workflow` SKILL.md.

## GitHub Releases (Grouped Tags)

GitHub Releases group one or more tags into a single release announcement. Use them for milestones (MINOR/MAJOR) — never for routine PATCH tags.

**When to create a GitHub Release:**

- **Every MINOR version** (`x.Y.0`) is a release
- **Every MAJOR version** (`X.0.0`) is a release
- Optionally after multiple PATCH tags that accumulate significant changes

All MINOR/MAJOR versions must be added to the **Current releases** list in `AGENTS.md`.

**Release description format:**

```markdown
## Release Title
<Short, descriptive title summarizing the main changes>

## Version Range
vX.X.X → vY.Y.Y

---

## What's New

<Brief intro paragraph mentioning key highlights and rule count change>

### New Rules

| Rule | Description |
|------|-------------|
| `rule-name` | What it does |

### Enhancements

| Rule | Enhancement |
|------|-------------|
| `rule-name` | What was improved |

### New Features

- Feature 1 description
- Feature 2 description

### Bug Fixes

- Fix 1 description
- Fix 2 description

### Documentation

- Doc change 1
- Doc change 2

## Installation

\`\`\`bash
npm install eslint-plugin-code-style@Y.Y.Y
\`\`\`

## Stats

- Total Rules: X (was Y)
- All rules are auto-fixable with `eslint --fix`

**Full Changelog**: https://github.com/ESLint-Plugin-Code-Style/plugin/compare/vX.X.X...vY.Y.Y
```

**Steps to create a GitHub Release:**

1. Either use `gh release create vX.Y.Z --title "..." --notes "..."` OR go to repository → Releases → "Draft a new release"
2. Choose the latest tag (e.g., `vX.Y.Z`)
3. Set release title (short, descriptive)
4. Paste the release description following the format above
5. Confirm `CHANGELOG.md` matches the release description exactly
6. Publish

---

