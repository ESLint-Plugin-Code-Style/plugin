# Claude Code Configuration

> For general project instructions, see [AGENTS.md](./AGENTS.md).

## Claude-Specific Behavior

When working on this codebase, Claude Code should:

- Do NOT include `Co-Authored-By` lines in commits
- Do NOT include Claude Code signature/footer in commits
- Keep commit messages clean and standard (no AI attribution)
- When user asks to "commit" with approval, follow the full release workflow in [AGENTS.md](./AGENTS.md#release-steps)
- The documentation website is in a separate repo (`ESLint-Plugin-Code-Style/website`). When ANY rule, count, version, or configuration changes, sync the website **in the same session**:
  1. Check if the website repo exists on disk (look for a sibling `website` directory or search nearby)
  2. If not found, clone it: `gh repo clone ESLint-Plugin-Code-Style/website` to a sibling directory
  3. Make the updates, commit, and push
  4. Do NOT defer website updates to a later session
- For git push, use HTTPS URL instead of SSH: `git push https://github.com/ESLint-Plugin-Code-Style/plugin.git <branch>`. This applies to all remote operations (push, creating PRs, closing issues). Use `gh` CLI for GitHub API operations (PRs, issues).
