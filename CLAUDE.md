# Claude Code Configuration

> For general project instructions, see [AGENTS.md](./AGENTS.md).

## Claude-Specific Behavior

When working on this codebase, Claude Code should:

- Do NOT include `Co-Authored-By` lines in commits
- Do NOT include Claude Code signature/footer in commits
- Keep commit messages clean and standard (no AI attribution)
- When user asks to "commit" with approval, follow the full release workflow in [AGENTS.md](./AGENTS.md#release-steps)
- Website sync is automated via `metadata.json`. When changing rules, version, or config, update `metadata.json` in the same commit. The website auto-syncs via GitHub Actions after push. No need to manually edit the website repo for rule/version changes.
- For git push, use HTTPS URL instead of SSH: `git push https://github.com/ESLint-Plugin-Code-Style/plugin.git <branch>`. This applies to all remote operations (push, creating PRs, closing issues). Use `gh` CLI for GitHub API operations (PRs, issues).
