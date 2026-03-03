# Boilerplate Samples

Ready-to-use configuration files for new frontend projects using `eslint-plugin-code-style`.

## What's Inside

| Folder / File | Description |
|---|---|
| `.editorconfig` | Editor configuration for consistent formatting across IDEs |
| `husky/pre-commit` | Pre-commit hook — runs ESLint with zero warnings tolerance |
| `husky/pre-push` | Pre-push hook — runs build and test coverage |
| `react/` | ESLint config + dependencies for **React** (JS only) |
| `react-ts/` | ESLint config + dependencies for **React + TypeScript** |
| `react-tw/` | ESLint config + dependencies for **React + Tailwind CSS** |
| `react-ts-tw/` | ESLint config + dependencies for **React + TypeScript + Tailwind CSS** |

## Which Variant?

| Stack | Folder |
|---|---|
| React (JavaScript only) | `react/` |
| React + TypeScript | `react-ts/` |
| React + Tailwind CSS | `react-tw/` |
| React + TypeScript + Tailwind CSS | `react-ts-tw/` |

## Setup Steps

1. **Pick your variant** from the table above.

2. **Copy the ESLint config** into your project root:
   ```bash
   cp samples/<variant>/eslint.config.js ./
   ```

3. **Merge devDependencies** from the sample `package.json` into your project's `package.json`, then install:
   ```bash
   npm install
   ```

4. **Copy the EditorConfig** into your project root:
   ```bash
   cp samples/.editorconfig ./
   ```

5. **Set up Husky** for git hooks:
   ```bash
   npm install --save-dev husky
   npx husky init
   cp samples/husky/pre-commit .husky/pre-commit
   cp samples/husky/pre-push .husky/pre-push
   ```

6. **Run your first lint**:
   ```bash
   npx eslint src/
   ```

7. **Auto-fix** existing issues:
   ```bash
   npx eslint src/ --fix
   ```
