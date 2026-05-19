# CHANGELOG.md Entry Formats

Two entry shapes — Tag/PATCH (simple) and Release (MINOR/MAJOR, full). Cited from the `release-workflow` SKILL.md.

## CHANGELOG.md Formats

CHANGELOG follows [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/). It must list **ALL** version tags. Two distinct entry shapes depending on whether the version is a release or a patch tag.

### Tag/PATCH format (no GitHub Release)

Simpler — no title, no version range, no Full Changelog inside the entry body.

```markdown
## [1.11.2] - 2026-02-04

### Fixed

- **`rule-name`** - What was fixed
- **`another-rule`** - Another fix

---
```

Version bumps with no changes:

```markdown
## [1.0.19] - 2026-01-11

- Version bump

---
```

**Still required:** comparison-link reference at the bottom of `CHANGELOG.md`:

```markdown
[1.11.2]: https://github.com/ESLint-Plugin-Code-Style/plugin/compare/v1.11.1...v1.11.2
[1.11.1]: https://github.com/ESLint-Plugin-Code-Style/plugin/compare/v1.11.0...v1.11.1
```

### Release format (MINOR/MAJOR — published to GitHub Releases)

Full release entry. Title, version range, consolidated changes since the immediately preceding tag, stats, Full Changelog link.

**IMPORTANT:** The Version Range and Full Changelog link compare against the **immediately preceding version** (last published tag), NOT the previous MINOR/MAJOR release. Example: shipping v1.7.0 when the last tag was v1.6.6 → Version Range: `v1.6.6 → v1.7.0`, compare: `v1.6.6...v1.7.0`.

```markdown
## [X.Y.0] - YYYY-MM-DD

**Release Title (Brief Description of Main Features)**

**Version Range:** vImmediatelyPrecedingTag → vCurrent

### Added

**New Rules (N)**
- `rule-name` - Description 🔧

### Enhanced

- **`rule-name`** - What was enhanced (consolidate all enhancements since last release)

### Fixed

- **`rule-name`** - What was fixed (consolidate all fixes since last release)

### Stats

- Total Rules: XX (was YY)
- Auto-fixable: ZZ rules 🔧
- Configurable: NN rules ⚙️
- Report-only: N rules

**Full Changelog:** [vImmediatelyPrecedingTag...vCurrent](https://github.com/ESLint-Plugin-Code-Style/plugin/compare/vImmediatelyPrecedingTag...vCurrent)
```

**Required elements for a release entry:**

1. Release title in bold describing the main changes
2. Version Range showing immediately preceding tag → current version
3. Consolidated changes from all versions since last release
4. Stats section with rule counts
5. Full Changelog link at the end

---

## Section Types in CHANGELOG

| Section | Use for |
|---------|---------|
| **Added** | New rules, features, configurations |
| **Changed** | Breaking changes, behavior changes |
| **Enhanced** | Improvements to existing functionality |
| **Fixed** | Bug fixes |
| **Deprecated** | Features to be removed in a future version |
| **Removed** | Removed features |
| **Security** | Security fixes |
| **Documentation** | Doc-only changes |
| **Stats** | Rule counts (include for releases) |

---
