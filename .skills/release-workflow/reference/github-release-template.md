# GitHub Release Description Template

Format for the `gh release create` notes body. Cited from the `release-workflow` SKILL.md.

## GitHub Releases

**Every version is published as a GitHub Release** — PATCH, MINOR, and MAJOR. The Releases tab is the source of truth for the full version history. Use the template that matches the version type below.

**When to create a GitHub Release:** always — for every PATCH, MINOR, and MAJOR version.

### PATCH release template

Small title + the fix notes. No version range, no stats, no Full Changelog block.

```markdown
## <Brief fix summary>

### Fixed

- **`rule-name`** - What was fixed
- **`another-rule`** - Another fix
```

(Title may simply be `vX.Y.Z` for trivial patches.)

### MINOR / MAJOR release template

Full description:

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

**Steps to create a GitHub Release (every version):**

1. Either use `gh release create vX.Y.Z --title "..." --notes "..."` OR go to repository → Releases → "Draft a new release"
2. Choose the tag (e.g., `vX.Y.Z`)
3. Set release title (short, descriptive)
4. Paste the release description following the matching template above (PATCH or MINOR/MAJOR)
5. Confirm `CHANGELOG.md` matches the release description
6. Publish

---

