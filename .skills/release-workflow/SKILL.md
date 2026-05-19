---
name: release-workflow
description: Complete release workflow for the plugin — versioning (SemVer), CHANGELOG entries (release vs tag format), git tagging, GitHub Releases, and commit-message conventions. Use whenever shipping a new version (PATCH, MINOR, or MAJOR) or backfilling a release entry.
---

# Release Workflow

End-to-end workflow for publishing a new version of `eslint-plugin-code-style`. Covers SemVer decisions, version bumps, CHANGELOG formatting (release vs tag), commit messages, annotated tags, GitHub Releases, and the `metadata.json` sync requirement.

> **CRITICAL — metadata.json sync:** Every release MUST include any rule-related `metadata.json` updates in the same commit/tag. The documentation website (eslint-plugin-code-style.org) auto-syncs from `metadata.json` via GitHub Actions. See the **`manage-rule`** skill for per-rule field updates and the **`website-sync`** skill for the full pipeline architecture and debugging.

---

## Versioning (SemVer)

Format: `MAJOR.MINOR.PATCH` (e.g., `1.2.8`).

| Change Type | Version Bump | Examples |
|-------------|--------------|----------|
| **PATCH** | `x.x.+1` | Bug fixes, typo corrections, doc updates |
| **MINOR** | `x.+1.0` | New rules, new features, new options, new auto-fix |
| **MAJOR** | `+1.0.0` | Breaking changes, removed/renamed rules, changed defaults |

**Decision guide:**

- New rule → MINOR
- Auto-fix added to existing rule → MINOR
- New option added → MINOR
- Bug fix → PATCH
- Doc-only update → PATCH
- Change default values → MAJOR (breaking)
- Rename/remove rule → MAJOR (breaking)

| Change Type | Version Bump | Example | GitHub Release? |
|-------------|--------------|---------|-----------------|
| Bug fix | PATCH | 1.5.2 → 1.5.3 | No |
| Enhancement to existing rule | PATCH | 1.5.3 → 1.5.4 | No |
| New rule | MINOR | 1.5.4 → 1.6.0 | **Yes** |
| Breaking change | MAJOR | 1.6.0 → 2.0.0 | **Yes** |
| Docs only | PATCH | 1.5.2 → 1.5.3 | No |

---

## Commit Message Format

Follow [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>: <subject>

[optional body]
```

**Types:**

- `feat` — new feature or rule
- `fix` — bug fix
- `docs` — documentation only
- `refactor` — code change that neither fixes a bug nor adds a feature
- `chore` — maintenance (deps, configs, releases)

**Subject line rules:**

- Lowercase (except proper nouns)
- No trailing period
- Maximum 72 characters
- Imperative mood ("add" not "added")

**Version-bump commit subject:**

```
chore: release vX.Y.Z - brief description of changes
```

**Good:**

```
chore: release v1.7.2 - fix double comma bug in enum/interface format
chore: release v1.7.1 - multiple rule fixes for destructuring and ternaries
chore: release v1.6.0 - add 3 new rules and enhance ternary formatting
```

**Avoid:**

```
chore: bump version to 1.7.2
chore: version bump
chore: v1.7.2
```

---

## End-to-End Release Steps

1. **Make and commit your code changes** (`feat:`, `fix:`, etc.).
2. **Bump version in `package.json`** per SemVer guide above.
3. **Update `metadata.json`** if rule descriptions/examples/options/flags changed (website auto-syncs).
4. **Update `CHANGELOG.md`**:
   - Add new entry at top in the appropriate format (release vs tag — see below).
   - Add comparison-link reference at bottom: `[X.Y.Z]: https://github.com/ESLint-Plugin-Code-Style/plugin/compare/vPREV...vX.Y.Z`
5. **For MINOR/MAJOR:** update the **Current releases** list in `AGENTS.md`.
6. **Build the plugin:** `npm run build` (regenerates `dist/index.js` with new version banner).
7. **Commit the release bundle** with a descriptive subject:
   ```bash
   git add package.json package-lock.json dist/ CHANGELOG.md metadata.json AGENTS.md
   git commit -m "chore: release vX.Y.Z - brief description"
   ```
   For releases that bundle the bug-fix commit and the release-bump together, prefer a single descriptive commit (`feat:` or `fix:` style) so the tagged commit is self-explanatory.
8. **Create the annotated tag:**
   ```bash
   git tag -a vX.Y.Z -m "vX.Y.Z - Brief Title

   - Bullet summary 1
   - Bullet summary 2"
   ```
9. **Push (requires explicit user approval per CLAUDE.md):**
   ```bash
   git push https://github.com/ESLint-Plugin-Code-Style/plugin.git main
   git push https://github.com/ESLint-Plugin-Code-Style/plugin.git vX.Y.Z
   ```
10. **For MINOR/MAJOR:** create the GitHub Release (see section below).
11. **Optional — publish to npm** (requires explicit user approval): `npm publish`.

---

## CHANGELOG.md Formats

Two distinct entry shapes — see **`reference/changelog-formats.md`** for the full templates and field rules:

- **Tag/PATCH format** — simple entry, no title/version-range/full-changelog body, but requires comparison-link reference at bottom of CHANGELOG.md
- **Release format** (MINOR/MAJOR) — release title + version range + consolidated changes + stats + Full Changelog link

Section types (Added, Changed, Enhanced, Fixed, Deprecated, Removed, Security, Documentation, Stats) and usage guidance also in `reference/changelog-formats.md`.

---

## GitHub Releases (Grouped Tags)

GitHub Releases group one or more tags into a single release announcement. Use them for milestones (MINOR/MAJOR) — never for routine PATCH tags. Every MINOR/MAJOR must be added to the **Current releases** list in `AGENTS.md`.

**Release description format and template:** see **`reference/github-release-template.md`**. Use it for the `gh release create vX.Y.Z --notes "..."` body or the manual GitHub Releases UI.

**Steps:**

1. Either `gh release create vX.Y.Z --title "..." --notes "..."` OR Releases UI → Draft a new release
2. Choose the latest tag (`vX.Y.Z`)
3. Set a short descriptive release title
4. Paste the release description per `reference/github-release-template.md`
5. Confirm `CHANGELOG.md` matches the release description exactly
6. Publish

---

## Release Checklist (MINOR / MAJOR)

- [ ] All code changes committed and tested (`npm run build` succeeds)
- [ ] `metadata.json` updated for any rule changes (website auto-syncs)
- [ ] `package.json` version bumped
- [ ] `CHANGELOG.md` entry uses release format (title + version range + consolidated changes + stats + Full Changelog link)
- [ ] Comparison link added at bottom of `CHANGELOG.md`
- [ ] `AGENTS.md` "Current releases" list updated
- [ ] Annotated tag created with descriptive message
- [ ] Pushed main + tag with explicit user approval
- [ ] GitHub Release created with full description
- [ ] Verified GitHub Release content matches CHANGELOG entry exactly

## PATCH Checklist

- [ ] Code change committed and tested
- [ ] `metadata.json` updated if rule description/examples changed
- [ ] `package.json` version bumped (`x.x.+1`)
- [ ] `CHANGELOG.md` entry uses simple tag format (no title, no version range, no Full Changelog body)
- [ ] Comparison link added at bottom of `CHANGELOG.md`
- [ ] Annotated tag created
- [ ] Pushed main + tag with explicit user approval
- [ ] No GitHub Release needed

---

## Verifying CHANGELOG Coverage

CHANGELOG must list every tag. Verify:

```bash
echo "Tags: $(git tag | wc -l)"
echo "CHANGELOG: $(grep -c '^## \[' CHANGELOG.md)"

git tag -l | sort -V > /tmp/tags.txt
grep '^## \[' CHANGELOG.md | sed 's/.*\[\([^]]*\)\].*/v\1/' | sort -V > /tmp/changelog.txt
diff /tmp/tags.txt /tmp/changelog.txt
```

---

## Example — End-to-End PATCH

```bash
# 1. Make changes and commit
git add src/rules/<file>.js
git commit -m "fix: handle edge case in rule-name"

# 2. Bump package.json (1.5.2 → 1.5.3)
# 3. Update CHANGELOG.md (tag format) + comparison link
# 4. Build
npm run build

# 5. Stage + commit release bundle with descriptive message
git add package.json package-lock.json dist/ CHANGELOG.md metadata.json
git commit -m "chore: release v1.5.3 - fix edge case in rule-name"

# 6. Create annotated tag
git tag -a v1.5.3 -m "v1.5.3 - Fix Edge Case in rule-name

- Fixed edge case handling in rule-name
- Improved error messages"

# 7. Push (with user approval)
git push https://github.com/ESLint-Plugin-Code-Style/plugin.git main
git push https://github.com/ESLint-Plugin-Code-Style/plugin.git v1.5.3
```

---

## Cross-References

- **Rule add/edit/remove workflow:** see `manage-rule` skill — covers per-rule file updates (src, README, rules/<cat>.md, metadata.json, index.d.ts, configs)
- **Documentation accuracy audit:** see `audit-docs` skill — counts and links verification before release
- **Config consistency:** see `review-config` skill — validate recommended configs before MINOR/MAJOR
