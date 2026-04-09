# Claude Code Configuration

> For general project instructions, see [AGENTS.md](./AGENTS.md).

## Claude-Specific Behavior

When working on this codebase, Claude Code should:

- Do NOT include `Co-Authored-By` lines in commits
- Do NOT include Claude Code signature/footer in commits
- Keep commit messages clean and standard (no AI attribution)
- When user asks to "commit" with approval, follow the full release workflow in [AGENTS.md](./AGENTS.md#release-steps)
- **CRITICAL: `metadata.json` must be updated in the same commit** whenever you change anything about rules (descriptions, examples, options, rationale, fixable/configurable flags), version, or categories. This is the single source of truth for the documentation website — the website auto-syncs from it via GitHub Actions. Never manually edit the website repo for these changes.
- For git push, use HTTPS URL instead of SSH: `git push https://github.com/ESLint-Plugin-Code-Style/plugin.git <branch>`. This applies to all remote operations (push, creating PRs, closing issues). Use `gh` CLI for GitHub API operations (PRs, issues).
