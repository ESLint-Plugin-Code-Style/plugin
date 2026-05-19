---
name: website-sync
description: Understand and operate the metadata.json → docs website sync pipeline. Use when debugging the website auto-sync, adding or extending fields consumed by the website, making changes that require manual website edits, or onboarding to how the plugin and website repos interact via GitHub Actions.
---

# Documentation Website Sync

The documentation website lives in a **separate repository**: [`ESLint-Plugin-Code-Style/website`](https://github.com/ESLint-Plugin-Code-Style/website). Built with Next.js 15, React 19, Tailwind CSS v4, and TypeScript. Deployed at **https://www.eslint-plugin-code-style.org**.

> **CRITICAL invariant:** `metadata.json` in this plugin repo is the **single source of truth** for the documentation website. Every change to the plugin that affects rules, version, examples, options, descriptions, or categories MUST be reflected in `metadata.json` in the same commit. No exceptions. The website auto-syncs from this file via GitHub Actions — do not manually edit the website repo for these changes.

---

## Sync Pipeline (At a Glance)

```
Plugin change → update metadata.json in same commit → push to main
  → GitHub Action triggers website repo
  → Website auto-generates rules.ts, config.ts, navigation.ts
  → Vercel rebuilds and deploys
  → Users see the change live
```

---

## What to update in the plugin (per change type)

| Change Type | Plugin Files to Update |
|-------------|----------------------|
| New rule | `src/rules/<category>.js`, `metadata.json` (add rule to category), `rules/<category>.md` |
| Remove rule | `src/rules/<category>.js`, `metadata.json` (remove rule), `rules/<category>.md` |
| Edit rule (behavior/options/examples) | `src/rules/<category>.js`, `metadata.json` (update description/examples/options) |
| Version bump | `package.json` (version field), `metadata.json` (version field), `CHANGELOG.md` (new version entry) |
| Changelog update | `CHANGELOG.md` — website fetches it automatically via the sync script prebuild |
| Add/change good or bad examples | `metadata.json` (update goodExample/badExample for the rule) |
| Add/change rule options | `metadata.json` (update options array for the rule) |
| Change rule rationale | `metadata.json` (update rationale for the rule) |

The website syncs automatically after push. Do NOT manually edit the website repo for rule/version/example changes.

---

## What still requires manual website edits

Rare cases — edit the [`ESLint-Plugin-Code-Style/website`](https://github.com/ESLint-Plugin-Code-Style/website) repo directly:

- New static pages (e.g., a new guide page)
- Layout/design changes
- Configuration page content changes
- Getting started page content changes

---

## How the Sync Works — Full Architecture

The plugin and website are two independent repos in the same GitHub org. They do NOT depend on each other being on the same machine. The sync is entirely automated via GitHub Actions.

**The 3 files that power the sync:**

```
PLUGIN REPO                              WEBSITE REPO
────────────                             ────────────
metadata.json ─── single source ───►     scripts/sync-from-plugin.js
                  of truth                     │
                                               ├──► src/data/rules.ts      (auto-generated)
.github/workflows/                             ├──► src/data/config.ts     (auto-generated)
  sync-website.yml                             ├──► src/data/navigation.ts (auto-generated)
       │                                       └──► CHANGELOG.md           (fetched)
       │  sends "plugin-updated"
       │  event via repository_dispatch   .github/workflows/
       └──────────────────────────────►     sync-from-plugin.yml
                                                 │
                                                 └──► runs sync script
                                                      commits changes
                                                      Vercel deploys
```

**Step-by-step flow:**

```
1. Developer changes a rule in the plugin
   └── Updates metadata.json in the same commit (REQUIRED)

2. Push to main
   └── GitHub detects metadata.json changed

3. Plugin workflow (sync-website.yml) runs
   └── Sends "plugin-updated" event to website repo
       (uses WEBSITE_SYNC_TOKEN secret for cross-repo auth)

4. Website workflow (sync-from-plugin.yml) receives the event
   ├── Checks out the website code
   ├── Runs: node scripts/sync-from-plugin.js
   │   ├── Fetches metadata.json from GitHub raw URL
   │   ├── Fetches CHANGELOG.md from GitHub raw URL
   │   ├── Generates rules.ts (all rules with examples, options, descriptions)
   │   ├── Generates config.ts (version, URLs, counts)
   │   ├── Generates navigation.ts (sidebar category links)
   │   └── Saves CHANGELOG.md to website root
   ├── If any files changed → commits as github-actions[bot]
   └── Pushes to website main

5. Vercel detects the push → builds → deploys
   └── Users see the change live at eslint-plugin-code-style.org
```

---

## metadata.json Field Reference

What `metadata.json` contains and must always reflect the plugin's current state:

```json
{
    "version": "3.2.1",              ← must match package.json
    "githubUrl": "...",              ← org repo URL
    "npmUrl": "...",                 ← npm package URL
    "websiteUrl": "...",             ← website URL
    "eslintVersions": "v9 and v10",  ← supported ESLint versions
    "totalRules": 81,                ← aggregate counts
    "fixableRules": 71,
    "configurableRules": 22,
    "reportOnlyRules": 10,
    "categories": [                  ← every category with every rule
        {
            "name": "Arrays",
            "slug": "arrays",
            "description": "...",
            "rules": [
                {
                    "name": "array-callback-destructure",
                    "description": "...",   ← shown on website
                    "rationale": "...",      ← shown on website
                    "isFixable": true,
                    "isConfigurable": false,
                    "isTsOnly": false,
                    "goodExample": "...",    ← shown on website
                    "badExample": "...",     ← shown on website
                    "options": []            ← shown on website
                }
            ]
        }
    ]
}
```

---

## Debugging the Sync

If the website doesn't update after a push to `main`:

1. **Check the plugin's Actions tab** — `sync-website.yml` should show a successful run on the commit.
2. **Check the website repo's Actions tab** — `sync-from-plugin.yml` should be triggered by `repository_dispatch`. If not, the `WEBSITE_SYNC_TOKEN` secret on the plugin repo may be missing or expired.
3. **Inspect Vercel deployments** on the website project — the latest commit by `github-actions[bot]` should trigger a build.
4. **Manual re-run:** from the website repo, you can dispatch `sync-from-plugin.yml` workflow manually to re-pull `metadata.json` and `CHANGELOG.md` without a plugin push.

---

## Related Skills

- **`manage-rule`** — the per-rule workflow that includes the `metadata.json` field updates for each operation (add, edit, remove, add option, add auto-fix).
- **`release-workflow`** — the release/version workflow that bumps `metadata.json` `version` field alongside `package.json` and `CHANGELOG.md`.
- **`audit-docs`** — the documentation accuracy audit, which verifies that the rule-count fields in `metadata.json` (`totalRules`, `fixableRules`, `configurableRules`, `reportOnlyRules`) match the actual codebase counts.
