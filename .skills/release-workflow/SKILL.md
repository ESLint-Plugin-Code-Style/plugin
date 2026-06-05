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

**Every version is published as a GitHub Release** — PATCH, MINOR, and MAJOR. The Releases tab is the source of truth; nothing is skipped. Only the note *format* differs by type:

| Change Type | Version Bump | Example | GitHub Release | Release format |
|-------------|--------------|---------|----------------|----------------|
| Bug fix | PATCH | 1.5.2 → 1.5.3 | **Yes** | small title + fix notes, no version range |
| Enhancement to existing rule | PATCH | 1.5.3 → 1.5.4 | **Yes** | small title + notes, no version range |
| New rule | MINOR | 1.5.4 → 1.6.0 | **Yes** | title + description + version range + stats |
| Breaking change | MAJOR | 1.6.0 → 2.0.0 | **Yes** | title + description + version range + stats |
| Docs only | PATCH | 1.5.2 → 1.5.3 | **Yes** | small title + notes, no version range |

---

## Commit Message Format

Follow [Conventional Commits](https://www.conventionalcommits.org/) specification:

```text
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

```text
chore: release vX.Y.Z - brief description of changes
```

**Good:**

```text
chore: release v1.7.2 - fix double comma bug in enum/interface format
chore: release v1.7.1 - multiple rule fixes for destructuring and ternaries
chore: release v1.6.0 - add 3 new rules and enhance ternary formatting
```

**Avoid:**

```text
chore: bump version to 1.7.2
chore: version bump
chore: v1.7.2
```

---

## End-to-End Release Steps

1. **Make and commit your code changes** (`feat:`, `fix:`, etc.).
2. **Bump version in `package.json`** per SemVer guide above.
3. **Bump the `version` field in `metadata.json`** (ALWAYS — it must match `package.json`; the website shows the current version from it) and update any rule descriptions/examples/options/flags that changed (website auto-syncs).
4. **Update `CHANGELOG.md`**:
   - Add new entry at top in the appropriate format (PATCH tag vs MINOR/MAJOR release — see below).
   - Add comparison-link reference at bottom: `[X.Y.Z]: https://github.com/ESLint-Plugin-Code-Style/plugin/compare/vPREV...vX.Y.Z`
5. **Build the plugin:** `npm run build` (regenerates `dist/index.js` with new version banner).
6. **Commit the release bundle** with a descriptive subject:


   ```bash
   git add package.json package-lock.json dist/ CHANGELOG.md metadata.json
   git commit -m "chore: release vX.Y.Z - brief description"

   ```


   For releases that bundle the bug-fix commit and the release-bump together, prefer a single descriptive commit (`feat:` or `fix:` style) so the tagged commit is self-explanatory.
7. **Create the annotated tag:**

   ```bash
   git tag -a vX.Y.Z -m "vX.Y.Z - Brief Title


   - Bullet summary 1

   - Bullet summary 2"
   ```

8. **Push (requires explicit user approval per CLAUDE.md):**


   ```bash
   git push https://github.com/ESLint-Plugin-Code-Style/plugin.git main
   git push https://github.com/ESLint-Plugin-Code-Style/plugin.git vX.Y.Z
   ```

9. **Create the GitHub Release** (every version — see section below).
10. **Publish to npm** (requires explicit user approval): `npm publish`.
    - **Every GitHub Release must be shipped to npm in the same release.** A release that isn't on npm is not installable — keep the two in lockstep: tag → GitHub Release → `npm publish`, all for the same version.

> **GitHub Releases vs npm — expected difference:** the GitHub **Releases** tab is the full version history (every tag). The npm **Versions** tab lists only versions actually published (and never unpublished). Historic versions that were tagged but never shipped (failed/skipped/unpublished) make the Releases count exceed the npm count — that is expected and correct. Going forward, every new release ships to npm, so new versions appear in both.

---

## CHANGELOG.md Formats

Two distinct entry shapes — see **`reference/changelog-formats.md`** for the full templates and field rules:

- **PATCH format** — small title + fix notes, no version range / stats / full-changelog body, but requires comparison-link reference at bottom of CHANGELOG.md
- **Release format** (MINOR/MAJOR) — release title + description + version range + consolidated changes + stats + Full Changelog link

Section types (Added, Changed, Enhanced, Fixed, Deprecated, Removed, Security, Documentation, Stats) and usage guidance also in `reference/changelog-formats.md`.

---

## GitHub Releases

**Every version gets a GitHub Release** — PATCH, MINOR, and MAJOR. The Releases tab is the source of truth for the full version history. The note format follows the version type:

- **PATCH** — small title (e.g. the fix summary, or `vX.Y.Z`) + the CHANGELOG fix notes. No version range, no stats.
- **MINOR / MAJOR** — release title + description + version range + consolidated changes + stats + Full Changelog link.

**Release description format and templates:** see **`reference/github-release-template.md`**. Use it for the `gh release create vX.Y.Z --notes "..."` body or the manual GitHub Releases UI.

**Steps (every version):**

1. Either `gh release create vX.Y.Z --title "..." --notes "..."` OR Releases UI → Draft a new release
2. Choose the tag (`vX.Y.Z`)
3. Set a short descriptive release title
4. Paste the release description per `reference/github-release-template.md` (PATCH or MINOR/MAJOR template)
5. Confirm `CHANGELOG.md` matches the release description
6. Publish

---

## Release Checklist (MINOR / MAJOR)

- [ ] All code changes committed and tested (`npm run build` succeeds)
- [ ] `metadata.json` `version` bumped (must match `package.json`) + any rule changes (website auto-syncs)
- [ ] `package.json` version bumped
- [ ] `CHANGELOG.md` entry uses release format (title + description + version range + consolidated changes + stats + Full Changelog link)
- [ ] Comparison link added at bottom of `CHANGELOG.md`
- [ ] Annotated tag created with descriptive message
- [ ] Pushed main + tag with explicit user approval
- [ ] GitHub Release created with full description
- [ ] Verified GitHub Release content matches CHANGELOG entry exactly
- [ ] Published to npm (`npm publish`) — every release ships to npm

## PATCH Checklist

- [ ] Code change committed and tested
- [ ] `metadata.json` `version` bumped (must match `package.json`) + rule changes if any
- [ ] `package.json` version bumped (`x.x.+1`)
- [ ] `CHANGELOG.md` entry uses PATCH format (small title + fix notes, no version range, no stats, no Full Changelog body)
- [ ] Comparison link added at bottom of `CHANGELOG.md`
- [ ] Annotated tag created
- [ ] Pushed main + tag with explicit user approval
- [ ] GitHub Release created (small title + fix notes, no version range)
- [ ] Published to npm (`npm publish`) — every release ships to npm

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

# 2. Bump version in package.json AND metadata.json (1.5.2 → 1.5.3, must match)
# 3. Update CHANGELOG.md (PATCH format: small title + fix notes) + comparison link
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

# 8. Create the GitHub Release (small title + fix notes, no version range)
gh release create v1.5.3 --repo ESLint-Plugin-Code-Style/plugin \
  --title "v1.5.3 - Fix Edge Case in rule-name" \
  --notes "### Fixed
- Fixed edge case handling in rule-name
- Improved error messages"

# 9. Publish to npm (every release ships to npm)
npm publish
```

---

## Release lifecycle (at a glance)

```text
code change
  └─ bump package.json + metadata.json version (match)
       └─ write CHANGELOG entry (PATCH = title + notes | MINOR/MAJOR = + range + stats)
            └─ npm run build
                 └─ commit + annotated tag
                      └─ push main + tag
                           ├─ GitHub Release (gh release create)   ← full history (every tag)
                           └─ npm publish                          ← installable (every release)
                                └─ push of metadata/CHANGELOG also fires the website sync
                                     (see the website-sync skill)
```

Every version flows tag → GitHub Release → npm publish. The Releases tab is the complete history; npm holds the installable subset.

---

## Cross-References

- **Rule add/edit/remove workflow:** see `manage-rule` skill — covers per-rule file updates (src, README, rules/<cat>.md, metadata.json, index.d.ts, configs)
- **Documentation accuracy audit:** see `audit-docs` skill — counts and links verification before release
- **Config consistency:** see `review-config` skill — validate recommended configs before MINOR/MAJOR
- **Website sync:** see `website-sync` skill — how a release's `metadata.json` / `CHANGELOG.md` push propagates to the docs site
