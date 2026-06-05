---
name: website-sync
description: Understand and operate the metadata.json → docs website sync pipeline. Use when debugging the website auto-sync, adding or extending fields consumed by the website, making changes that require manual website edits, or onboarding to how the plugin and website repos interact via GitHub Actions.
---

# Documentation Website Sync

The documentation website lives in a **separate repository**: [`ESLint-Plugin-Code-Style/website`](https://github.com/ESLint-Plugin-Code-Style/website). Built with Next.js 15, React 19, Tailwind CSS v4, and TypeScript. Deployed at **<https://www.eslint-plugin-code-style.org>g>**.

> **CRITICAL invariant:** `metadata.json` in this plugin repo is the **single source of truth** for the documentation website. Every change to the plugin that affects rules, version, examples, options, descriptions, or categories MUST be reflected in `metadata.json` in the same commit. No exceptions. The website auto-syncs from this file via GitHub Actions — do not manually edit the website repo for these changes.

---

## Sync Pipeline (At a Glance)

```text
Plugin change → update metadata.json + CHANGELOG.md in same commit → push to main
  → Plugin GitHub Action pings the website repo (repository_dispatch)
  → Website Action runs scripts/sync-from-plugin.js
       → fetches metadata.json + CHANGELOG.md from the plugin (remote)
       → regenerates rules.ts, config.ts, navigation.ts, versions.ts + copies CHANGELOG.md
  → commits the regenerated files → Vercel rebuilds and deploys
  → Users see the change live
```

**Two source files drive everything** (`package.json` is only a trigger, never read by the script):

| Source (plugin) | Generates (website) |
|-----------------|---------------------|
| `metadata.json` | `src/data/rules.ts`, `src/data/config.ts`, `src/data/navigation.ts` |
| `CHANGELOG.md` | `src/data/versions.ts` + a copy at `website/CHANGELOG.md` (rendered by the changelog page) |

---

## Two roles — never competing

The sync has two distinct actors. They run one after the other, not as alternatives:

```text
scripts/sync-from-plugin.js              = the DELIVERY GUY
  brings plugin data into the website + WRITES committed files
  (rules.ts, config.ts, navigation.ts, versions.ts, CHANGELOG.md)

src/app/docs/changelog/page.tsx          = the CHEF
  parseChangelogHandler reads local CHANGELOG.md → renders the changelog page
src/components/version-selector.tsx
  imports versions.ts → renders the version dropdown
```

> The generated files (`rules.ts`, `config.ts`, `navigation.ts`, `versions.ts`, `CHANGELOG.md`) are **permanent, committed files** — NOT temp files. The script overwrites them; they stay in the repo and are read at build/render time. The `// AUTO-GENERATED — do not edit manually` header marks them.

---

## What to update in the plugin (per change type)

| Change Type | Plugin Files to Update |
|-------------|----------------------|
| New rule | `src/rules/<category>.js`, `metadata.json` (add rule to category), `rules/<category>.md` |
| Remove rule | `src/rules/<category>.js`, `metadata.json` (remove rule), `rules/<category>.md` |
| Edit rule (behavior/options/examples) | `src/rules/<category>.js`, `metadata.json` (update description/examples/options) |
| Version bump / release | `package.json` (version), `metadata.json` (version — must match), `CHANGELOG.md` (new entry) → then tag + GitHub Release + `npm publish` (see `release-workflow` skill) |
| Changelog update | `CHANGELOG.md` — the sync script copies it to the website and regenerates `versions.ts` from it |
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

**The files that power the sync:**

```text
PLUGIN REPO                              WEBSITE REPO
────────────                             ────────────
metadata.json ─── sources ──────────►    scripts/sync-from-plugin.js
CHANGELOG.md  ───────────────────────►        │
                                               ├──► src/data/rules.ts      (from metadata.json)
.github/workflows/                             ├──► src/data/config.ts     (from metadata.json)
  sync-website.yml                             ├──► src/data/navigation.ts (from metadata.json)
       │                                       ├──► CHANGELOG.md           (copied from plugin)
       │  watches: metadata.json,              └──► src/data/versions.ts   (from CHANGELOG.md)
       │           CHANGELOG.md,
       │           package.json  (trigger only)
       │  sends "plugin-updated"
       │  event via repository_dispatch   .github/workflows/
       └──────────────────────────────►     sync-from-plugin.yml
                                                 │
                                                 └──► runs sync script
                                                      commits changes
                                                      Vercel deploys
```

**Step-by-step flow (production):**

```text
1. Developer changes a rule / bumps version in the plugin
   └── Updates metadata.json (+ CHANGELOG.md for releases) in the same commit (REQUIRED)

2. Push to main
   └── Plugin workflow path-filter detects metadata.json / CHANGELOG.md / package.json changed

3. Plugin workflow (sync-website.yml) runs — a doorbell, runs no script
   └── Sends "plugin-updated" event to website repo
       (uses WEBSITE_SYNC_TOKEN secret for cross-repo auth)

4. Website workflow (sync-from-plugin.yml) receives the event
   ├── Checks out the website code
   ├── Runs: node scripts/sync-from-plugin.js   (NO arg = CI/remote mode)
   │   ├── Fetches metadata.json from GitHub raw URL
   │   ├── Generates rules.ts (all rules with examples, options, descriptions)
   │   ├── Generates config.ts (version, URLs, counts)
   │   ├── Generates navigation.ts (sidebar category links)
   │   ├── Fetches CHANGELOG.md from GitHub raw URL  → writes website/CHANGELOG.md
   │   └── Generates versions.ts FROM the fresh CHANGELOG.md (every version, isMajor flag)
   ├── If any files changed → commits as github-actions[bot]
   └── Pushes to website main

5. Vercel detects the push → runs `prebuild` (sync script again, safety refresh) → `next build` → deploys
   └── Users see the change live at eslint-plugin-code-style.org
```

> **Order matters:** the script copies/fetches `CHANGELOG.md` **before** generating `versions.ts`, so the dropdown data always reflects the just-synced changelog (no one-run lag).

---

## Local dev sync (unpushed plugin changes)

In dev, **nobody auto-runs the script** — `pnpm dev` only serves. You trigger it yourself, and you must point it at the **local** plugin so unpushed changes are picked up:

```bash
# from the website repo, with the plugin repo as a sibling folder
pnpm sync:local       # = node scripts/sync-from-plugin.js ../Plugin/metadata.json
pnpm dev
```

| Command | Reads from | Use when |
|---------|-----------|----------|
| `pnpm sync:local` | **local** sibling plugin (`../Plugin/…`) | plugin changed but **not pushed** yet |
| `pnpm sync` | **remote** plugin `main` (GitHub raw) | plugin already pushed |
| `prebuild` (auto before `pnpm build`) | **remote** plugin `main` | every production build |

## Two separate things — DATA vs DEPENDENCY (don't confuse them)

The sync script only regenerates **generated data** (`src/data/*.ts` — the rule
list, counts, changelog shown *on the site*). It does **not** touch the website's
npm dependency on the plugin.

The website also depends on `eslint-plugin-code-style` in its own `package.json`
to **lint its own source**. That is a normal npm dependency and is **never** bumped
by the sync pipeline or `prebuild`. Two ways to bump it after a plugin publish:

- **Dependabot (automatic, ≤daily, PR-gated)** — `.github/dependabot.yml` in the
  website watches npm and opens a `chore(deps): bump eslint-plugin-code-style …`
  PR when a newer version exists. It **polls on a schedule** (daily — the finest
  interval; not event-driven, so up to ~24 h after a publish). Use the Dependabot
  tab's **"Check for updates"** button to force an immediate run. Review/merge the
  PR, then `git pull`. The PR title shows the **manifest** floor (e.g. `from 3.1.1`,
  the `^3.1.1` range base), not the lockfile version — that is expected.
- **Manual (immediate)** — when you don't want to wait for the daily poll:

  ```bash
  pnpm update eslint-plugin-code-style   # → newest matching the ^range, updates lockfile
  pnpm lint && pnpm tsc --noEmit         # then re-fix / drop now-redundant overrides
  # commit package.json + pnpm-lock.yaml
  ```

Neither path drops scoped overrides or runs `eslint --fix` for you — that stays a
human step (only you know which override a plugin fix made redundant).

**Post-publish website checklist:**

1. `git pull` first — the plugin push already fired the bot data-sync, so remote
   `main` may have a `chore: auto-sync from plugin metadata` commit. Don't re-run
   `sync:local` over it.
2. Bump the dep: merge the **Dependabot PR** (wait ≤daily or hit "Check for
   updates"), OR run `pnpm update eslint-plugin-code-style` manually for immediate.
3. **If you used the Dependabot PR**, after `git pull` run **`pnpm install`** — the
   merged PR changes `package.json` + `pnpm-lock.yaml`, but your local
   `node_modules` is still on the old version until you install. (Skip this with
   the manual path: `pnpm update` already installs.) Verify:
   `cat node_modules/eslint-plugin-code-style/package.json | grep version`.
4. Re-add any rules / drop any overrides that were deferred until the plugin shipped.
5. `pnpm lint && pnpm tsc --noEmit`, commit, push.

`sync:local` is **optional** before pushing — the remote pipeline + `prebuild`
regenerate the data from the remote plugin anyway, so the deployed site is always
current. Run it only to preview new data in `pnpm dev` or to keep committed data
matching the deploy.

**How the arg switches mode** (`scripts/sync-from-plugin.js`):

```text
const metadataPath = process.argv[2]
  present → readFileSync(metadataPath)        ← LOCAL (also copies sibling ../Plugin/CHANGELOG.md)
  absent  → fetch(GITHUB_RAW_URL)             ← REMOTE
```

The arg is always the path to **metadata.json**; the script finds `CHANGELOG.md` as its sibling automatically. You never pass the changelog directly.

```text
LOCAL DEV                                  PRODUCTION
─────────                                  ──────────
you edit plugin (unpushed)                 you push plugin to main
   │                                           │
pnpm sync:local  (arg → local)             Plugin Action pings website
   │ reads ../Plugin/metadata.json             │
   │ copies ../Plugin/CHANGELOG.md          Website Action runs sync (no arg → remote)
   │ regenerates data files                    │ fetches metadata + CHANGELOG from GitHub raw
pnpm dev → page reads local files          commit → Vercel build (prebuild re-syncs) → deploy
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
