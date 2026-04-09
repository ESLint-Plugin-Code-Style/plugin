# Claude Code Configuration

> For general project instructions, see [AGENTS.md](./AGENTS.md).

## Claude-Specific Behavior

When working on this codebase, Claude Code should:

- Do NOT include `Co-Authored-By` lines in commits
- Do NOT include Claude Code signature/footer in commits
- Keep commit messages clean and standard (no AI attribution)
- When user asks to "commit" with approval, follow the full release workflow in [AGENTS.md](./AGENTS.md#release-steps)
- The documentation website is in a sibling repo at `../website/` (`ESLint-Plugin-Code-Style/website`). When ANY rule, count, version, or configuration changes, sync the website **in the same session**:
  1. Navigate to `../website/` — if it doesn't exist, clone it: `gh repo clone ESLint-Plugin-Code-Style/website ../website`
  2. Make the updates, commit, and push
  3. Do NOT defer website updates to a later session
- For git push, use HTTPS URL instead of SSH: `git push https://github.com/ESLint-Plugin-Code-Style/plugin.git <branch>`. This applies to all remote operations (push, creating PRs, closing issues). Use `gh` CLI for GitHub API operations (PRs, issues).
