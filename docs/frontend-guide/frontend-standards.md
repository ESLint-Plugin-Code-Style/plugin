# Frontend Project Standards Guide

A standardized configuration guide for all frontend (React) projects. This document covers linting, formatting, naming conventions, Git hooks, editor configuration, and project structure — ensuring every project follows the same rules from day one.

**Goal:** Consistent codebase, fewer review comments about style, faster PR cycles, cleaner diffs.

---

## Table of Contents

- [1. Linting (ESLint)](#1-linting-eslint)
  - [1.1 Installation](#11-installation)
  - [1.2 ESLint Flat Config](#12-eslint-flat-config)
  - [1.3 NPM Scripts](#13-npm-scripts)
- [2. Formatting — ESLint Instead of Prettier](#2-formatting--eslint-instead-of-prettier)
  - [2.1 Why No Prettier?](#21-why-no-prettier)
  - [2.2 What Handles Formatting](#22-what-handles-formatting)
- [3. Naming Conventions](#3-naming-conventions)
  - [3.1 File and Folder Naming](#31-file-and-folder-naming)
  - [3.2 Function Naming](#32-function-naming)
  - [3.3 Variable Naming](#33-variable-naming)
  - [3.4 Component Naming](#34-component-naming)
  - [3.5 Hook Naming](#35-hook-naming)
  - [3.6 TypeScript Definitions Naming](#36-typescript-definitions-naming)
  - [3.7 JSX Prop Naming](#37-jsx-prop-naming)
  - [3.8 Class Naming](#38-class-naming)
  - [3.9 Naming Conventions Summary Table](#39-naming-conventions-summary-table)
- [4. Import/Export Rules](#4-importexport-rules)
  - [4.1 Import Order and Sorting](#41-import-order-and-sorting)
  - [4.2 Import Format](#42-import-format)
  - [4.3 Absolute Imports Only](#43-absolute-imports-only)
  - [4.4 Named Exports Only](#44-named-exports-only)
  - [4.5 Index File Exports](#45-index-file-exports)
  - [4.6 TypeScript Type Imports](#46-typescript-type-imports)
- [5. Pre-Commit Hooks (Lint Check)](#5-pre-commit-hooks-lint-check)
- [6. Pre-Push Hooks (Build + Test Coverage)](#6-pre-push-hooks-build--test-coverage)
- [7. Husky Setup](#7-husky-setup)
  - [7.1 Installation](#71-installation)
  - [7.2 Create Hook Files](#72-create-hook-files)
  - [7.3 package.json Scripts](#73-packagejson-scripts)
  - [7.4 How It Works](#74-how-it-works)
- [8. EditorConfig](#8-editorconfig)
- [9. React Component Code Order](#9-react-component-code-order)
- [10. TypeScript Definition File Organization](#10-typescript-definition-file-organization)
- [11. Folder Structure Standards](#11-folder-structure-standards)
- [12. Quick Setup Checklist](#12-quick-setup-checklist)

---

## 1. Linting (ESLint)

All projects use **ESLint v9+ flat config** with `eslint-plugin-code-style` as the core plugin, combined with carefully selected third-party plugins.

### 1.1 Installation

**React + TypeScript projects** (most common stack):

```bash
npm install --save-dev \
  eslint \
  @eslint/js \
  @stylistic/eslint-plugin \
  @typescript-eslint/parser \
  @typescript-eslint/eslint-plugin \
  globals \
  eslint-plugin-react \
  eslint-plugin-react-hooks \
  eslint-plugin-import-x \
  eslint-plugin-jsx-a11y \
  eslint-plugin-check-file \
  eslint-plugin-perfectionist \
  eslint-plugin-simple-import-sort \
  eslint-plugin-code-style
```

**If using Tailwind CSS**, add:

```bash
npm install --save-dev eslint-plugin-tailwindcss
```

> For Tailwind CSS v4: use `"eslint-plugin-tailwindcss": "^4.0.0-beta.0"`
> For Tailwind CSS v3: use `"eslint-plugin-tailwindcss": "^3.18.2"`

### 1.2 ESLint Flat Config

Create `eslint.config.js` at the project root. Below is the **React + TypeScript** config (without Tailwind). For Tailwind variants, see the [recommended-configs](./recommended-configs/) folder.

```javascript
import js from "@eslint/js";
import stylistic from "@stylistic/eslint-plugin";
import tsParser from "@typescript-eslint/parser";
import tsPlugin from "@typescript-eslint/eslint-plugin";
import checkFile from "eslint-plugin-check-file";
import codeStyle from "eslint-plugin-code-style";
import importX from "eslint-plugin-import-x";
import jsxA11y from "eslint-plugin-jsx-a11y";
import perfectionist from "eslint-plugin-perfectionist";
import react from "eslint-plugin-react";
import reactHooks from "eslint-plugin-react-hooks";
import simpleImportSort from "eslint-plugin-simple-import-sort";
import globals from "globals";

export default [
    {
        ignores: [
            ".vite/**/*",
            "build/**",
            "coverage/**/*",
            "dist/**",
            "node_modules/**",
            "public/**/*",
        ],
    },
    js.configs.recommended,
    {
        files: ["**/*.{js,jsx,ts,tsx}"],
        languageOptions: {
            ecmaVersion: 2020,
            globals: {
                ...globals.browser,
                ...globals.es2020,
                ...globals.node,
            },
            parser: tsParser,
            parserOptions: {
                ecmaFeatures: {
                    jsx: true,
                    modules: true,
                },
            },
            sourceType: "module",
        },
        plugins: {
            "@stylistic": stylistic,
            "@typescript-eslint": tsPlugin,
            "check-file": checkFile,
            "code-style": codeStyle,
            "import-x": importX,
            "jsx-a11y": jsxA11y,
            perfectionist,
            react,
            "react-hooks": reactHooks,
            "simple-import-sort": simpleImportSort,
        },
        rules: {
            ...react.configs.recommended.rules,
            ...reactHooks.configs.recommended.rules,
            ...tsPlugin.configs.recommended.rules,

            // ── Stylistic (formatting) ────────────────────────────
            "@stylistic/comma-dangle": ["error", "always-multiline"],
            "@stylistic/comma-spacing": "error",
            "@stylistic/dot-location": "error",
            "@stylistic/function-paren-newline": "off",
            "@stylistic/indent": ["error", 4],
            "@stylistic/jsx-quotes": "error",
            "@stylistic/key-spacing": "error",
            "@stylistic/linebreak-style": "off",
            "@stylistic/multiline-comment-style": "error",
            "@stylistic/no-extra-semi": "error",
            "@stylistic/no-multi-spaces": "error",
            "@stylistic/no-multiple-empty-lines": [
                "error",
                { max: 1, maxBOF: 0, maxEOF: 0 },
            ],
            "@stylistic/nonblock-statement-body-position": "error",
            "@stylistic/object-curly-spacing": ["error", "always"],
            "@stylistic/padded-blocks": ["error", "never"],
            "@stylistic/padding-line-between-statements": [
                "error",
                { blankLine: "always", next: "return", prev: "*" },
                { blankLine: "always", next: "*", prev: ["const", "let", "var"] },
                { blankLine: "always", next: "*", prev: "expression" },
                { blankLine: "always", next: "expression", prev: "expression" },
            ],
            "@stylistic/quotes": "error",
            "@stylistic/semi": "error",
            "@stylistic/semi-spacing": "error",
            "@stylistic/semi-style": "error",
            "@stylistic/space-in-parens": "error",
            "@stylistic/space-infix-ops": "error",

            // ── TypeScript ────────────────────────────────────────
            "@typescript-eslint/consistent-type-imports": [
                "error",
                { fixStyle: "separate-type-imports", prefer: "type-imports" },
            ],
            "@typescript-eslint/no-explicit-any": "warn",
            "@typescript-eslint/no-unused-vars": [
                "error",
                { argsIgnorePattern: "^_", varsIgnorePattern: "^_" },
            ],

            // ── Base ESLint ───────────────────────────────────────
            "array-callback-return": "off",
            "arrow-body-style": ["error", "as-needed"],
            camelcase: "off",
            "capitalized-comments": ["error"],
            complexity: "off",
            "consistent-return": "off",
            curly: ["error", "multi-or-nest"],
            "default-case": "off",
            "default-param-last": 0,
            "dot-notation": ["error", { allowKeywords: false }],
            eqeqeq: "error",
            "func-style": ["error", "expression"],
            "max-depth": ["error", 4],
            "max-len": "off",
            "max-lines": "off",
            "max-lines-per-function": "off",
            "max-nested-callbacks": ["error", 4],
            "max-params": "off",
            "no-alert": "error",
            "no-await-in-loop": "error",
            "no-cond-assign": "error",
            "no-else-return": "error",
            "no-eq-null": "error",
            "no-fallthrough": "error",
            "no-inline-comments": "error",
            "no-irregular-whitespace": "error",
            "no-lone-blocks": "error",
            "no-lonely-if": "error",
            "no-nested-ternary": "off",
            "no-plusplus": "off",
            "no-redeclare": "off",
            "no-return-assign": "off",
            "no-self-compare": "error",
            "no-unexpected-multiline": "error",
            "no-unused-vars": "off",
            "no-use-before-define": "off",
            "no-useless-escape": "off",
            "no-var": "error",
            radix: 0,
            "sort-keys": "off",
            "valid-typeof": "error",
            "vars-on-top": "error",

            // ── File naming ───────────────────────────────────────
            "check-file/filename-naming-convention": [
                "error",
                { "**/*.{js,jsx,ts,tsx}": "KEBAB_CASE" },
                { ignoreMiddleExtensions: true },
            ],
            "check-file/folder-naming-convention": [
                "error",
                { "src/**": "KEBAB_CASE" },
            ],

            // ── Code Style (all 81 rules) ─────────────────────────
            "code-style/absolute-imports-only": "error",
            "code-style/array-callback-destructure": "error",
            "code-style/array-items-per-line": "error",
            "code-style/array-objects-on-new-lines": "error",
            "code-style/arrow-function-block-body": "error",
            "code-style/arrow-function-simple-jsx": "error",
            "code-style/arrow-function-simplify": "error",
            "code-style/assignment-value-same-line": "error",
            "code-style/block-statement-newlines": "error",
            "code-style/class-method-definition-format": "error",
            "code-style/class-naming-convention": "error",
            "code-style/classname-dynamic-at-end": "error",
            "code-style/classname-multiline": "error",
            "code-style/classname-no-extra-spaces": "error",
            "code-style/classname-order": "error",
            "code-style/comment-format": "error",
            "code-style/component-props-destructure": "error",
            "code-style/component-props-inline-type": "error",
            "code-style/curried-arrow-same-line": "error",
            "code-style/empty-line-after-block": "error",
            "code-style/enum-format": "error",
            "code-style/enum-type-enforcement": "error",
            "code-style/export-format": "error",
            "code-style/folder-based-naming-convention": "error",
            "code-style/folder-structure-consistency": "error",
            "code-style/function-arguments-format": "error",
            "code-style/function-call-spacing": "error",
            "code-style/function-declaration-style": "error",
            "code-style/function-naming-convention": "error",
            "code-style/function-object-destructure": "error",
            "code-style/function-params-per-line": "error",
            "code-style/hook-callback-format": "error",
            "code-style/hook-deps-per-line": "error",
            "code-style/hook-file-naming-convention": "error",
            "code-style/hook-function-naming-convention": "error",
            "code-style/if-else-spacing": "error",
            "code-style/if-statement-format": "error",
            "code-style/import-format": "error",
            "code-style/import-source-spacing": "error",
            "code-style/index-export-style": "error",
            "code-style/index-exports-only": "error",
            "code-style/inline-export-declaration": "error",
            "code-style/interface-format": "error",
            "code-style/jsx-children-on-new-line": "error",
            "code-style/jsx-closing-bracket-spacing": "error",
            "code-style/jsx-element-child-new-line": "error",
            "code-style/jsx-logical-expression-simplify": "error",
            "code-style/jsx-parentheses-position": "error",
            "code-style/jsx-prop-naming-convention": "error",
            "code-style/jsx-simple-element-one-line": "error",
            "code-style/jsx-string-value-trim": "error",
            "code-style/jsx-ternary-format": "error",
            "code-style/logical-expression-multiline": "error",
            "code-style/member-expression-bracket-spacing": "error",
            "code-style/module-index-exports": "error",
            "code-style/multiline-if-conditions": "error",
            "code-style/nested-call-closing-brackets": "error",
            "code-style/no-empty-lines-in-function-calls": "error",
            "code-style/no-empty-lines-in-function-params": "error",
            "code-style/no-empty-lines-in-jsx": "error",
            "code-style/no-empty-lines-in-objects": "error",
            "code-style/no-empty-lines-in-switch-cases": "error",
            "code-style/no-hardcoded-strings": "error",
            "code-style/no-inline-type-definitions": "error",
            "code-style/no-redundant-folder-suffix": "error",
            "code-style/object-property-per-line": "error",
            "code-style/object-property-value-brace": "error",
            "code-style/object-property-value-format": "error",
            "code-style/opening-brackets-same-line": "error",
            "code-style/prop-naming-convention": "error",
            "code-style/react-code-order": "error",
            "code-style/simple-call-single-line": "error",
            "code-style/single-argument-on-one-line": "error",
            "code-style/string-property-spacing": "error",
            "code-style/svg-icon-naming-convention": "error",
            "code-style/ternary-condition-multiline": "error",
            "code-style/type-annotation-spacing": "error",
            "code-style/type-format": "error",
            "code-style/typescript-definition-location": "error",
            "code-style/use-state-naming-convention": "error",
            "code-style/variable-naming-convention": "error",

            // ── Import rules ──────────────────────────────────────
            "import-x/no-cycle": 0,
            "import-x/no-default-export": "error",
            "import-x/no-named-as-default": 0,
            "import-x/no-named-as-default-member": 0,
            "import-x/no-unresolved": 0,
            "import-x/prefer-default-export": 0,

            // ── Accessibility ─────────────────────────────────────
            "jsx-a11y/anchor-is-valid": "off",
            "jsx-a11y/click-events-have-key-events": "off",
            "jsx-a11y/html-has-lang": "off",
            "jsx-a11y/label-has-associated-control": "off",
            "jsx-a11y/media-has-caption": "off",
            "jsx-a11y/no-static-element-interactions": "off",
            "jsx-a11y/tabindex-no-positive": "off",

            // ── Perfectionist (auto-sorting) ──────────────────────
            "perfectionist/sort-array-includes": ["error", { order: "asc", type: "natural" }],
            "perfectionist/sort-enums": ["error", { order: "asc", type: "natural" }],
            "perfectionist/sort-interfaces": ["error", { order: "asc", type: "natural" }],
            "perfectionist/sort-maps": ["error", { order: "asc", type: "natural" }],
            "perfectionist/sort-object-types": ["error", { order: "asc", type: "natural" }],
            "perfectionist/sort-objects": ["error", { ignoreCase: false, order: "asc", type: "natural" }],
            "perfectionist/sort-sets": ["error", { order: "asc", type: "natural" }],
            "perfectionist/sort-switch-case": ["error", { order: "asc", type: "natural" }],
            "perfectionist/sort-variable-declarations": ["error", { order: "asc", type: "natural" }],

            // ── React hooks ───────────────────────────────────────
            "react-hooks/exhaustive-deps": "off",
            "react-hooks/set-state-in-effect": 0,
            "react-hooks/use-memo": 0,

            // ── React ─────────────────────────────────────────────
            "react/button-has-type": 0,
            "react/display-name": "off",
            "react/forbid-prop-types": 0,
            "react/function-component-definition": [
                "error",
                { namedComponents: "arrow-function", unnamedComponents: "arrow-function" },
            ],
            "react/jsx-closing-bracket-location": ["error", "line-aligned"],
            "react/jsx-closing-tag-location": "error",
            "react/jsx-curly-spacing": ["error", { children: true, when: "never" }],
            "react/jsx-equals-spacing": "error",
            "react/jsx-filename-extension": [1, { extensions: [".jsx", ".tsx"] }],
            "react/jsx-first-prop-new-line": ["error", "multiline"],
            "react/jsx-indent": ["error", 4],
            "react/jsx-indent-props": ["error", 4],
            "react/jsx-max-props-per-line": "error",
            "react/jsx-newline": ["error", { prevent: true }],
            "react/jsx-one-expression-per-line": ["error", { allow: "single-child" }],
            "react/jsx-props-no-spreading": "off",
            "react/jsx-sort-props": [
                2,
                {
                    callbacksLast: true,
                    ignoreCase: false,
                    locale: "auto",
                    multiline: "last",
                    noSortAlphabetically: false,
                    shorthandFirst: false,
                    shorthandLast: true,
                },
            ],
            "react/jsx-wrap-multilines": [
                "error",
                {
                    arrow: "parens-new-line",
                    assignment: "parens-new-line",
                    condition: "parens-new-line",
                    declaration: "parens-new-line",
                    logical: "parens-new-line",
                    prop: "parens-new-line",
                    return: "parens-new-line",
                },
            ],
            "react/no-danger": 0,
            "react/prop-types": 0,
            "react/react-in-jsx-scope": "off",
            "react/require-default-props": 0,
            "react/self-closing-comp": "error",

            // ── Import sorting ────────────────────────────────────
            "simple-import-sort/exports": "error",
            "simple-import-sort/imports": "error",
        },
        settings: {
            "import-x/extensions": [".js", ".jsx", ".ts", ".tsx"],
            "import-x/parsers": {
                "@typescript-eslint/parser": [".ts", ".tsx"],
            },
            "import-x/resolver": {
                node: {
                    extensions: [".js", ".jsx", ".ts", ".tsx"],
                    paths: ["src"],
                },
            },
            react: { version: "detect" },
        },
    },
];
```

> **Other stack variants:** See the [recommended-configs](./recommended-configs/) folder for React-only (JS), React + Tailwind, and React + TypeScript + Tailwind configs.

### 1.3 NPM Scripts

Add these to `package.json`:

```json
{
    "scripts": {
        "lint": "eslint src/",
        "lint:fix": "eslint src/ --fix"
    }
}
```

---

## 2. Formatting — ESLint Instead of Prettier

### 2.1 Why No Prettier?

**We do NOT use Prettier.** Our ESLint setup fully replaces Prettier by combining three layers:

1. **`@stylistic/eslint-plugin`** — Handles all the formatting rules ESLint deprecated in v8.53.0 (indentation, quotes, semicolons, commas, spacing, etc.)
2. **`eslint-plugin-code-style`** — 81 custom rules covering formatting gaps: import/export layout, object formatting, JSX children positioning, function arguments layout, ternary formatting, and much more
3. **ESLint built-in rules** — Code quality rules (`eqeqeq`, `no-var`, `curly`, etc.)

This gives us **one tool** for both linting and formatting. No config conflicts between ESLint and Prettier, no fight over semicolons or quotes, no `.prettierrc` to maintain.

### 2.2 What Handles Formatting

| Formatting Concern | Handled By |
|---|---|
| Indentation (4 spaces) | `@stylistic/indent` |
| Semicolons (always) | `@stylistic/semi` |
| Quotes (double) | `@stylistic/quotes` |
| Trailing commas (multiline) | `@stylistic/comma-dangle` |
| Object spacing `{ a, b }` | `@stylistic/object-curly-spacing` |
| Empty lines (max 1) | `@stylistic/no-multiple-empty-lines` |
| Operator spacing `a + b` | `@stylistic/space-infix-ops` |
| Import format (collapse/expand) | `code-style/import-format` |
| Export format (collapse/expand) | `code-style/export-format` |
| Object layout (one prop per line) | `code-style/object-property-per-line` |
| Function args layout | `code-style/function-arguments-format` |
| JSX children positioning | `code-style/jsx-children-on-new-line` |
| Ternary formatting | `code-style/ternary-condition-multiline` |
| If/else spacing | `code-style/if-else-spacing` + `code-style/if-statement-format` |
| Block spacing | `code-style/block-statement-newlines` + `code-style/empty-line-after-block` |
| Comment formatting | `code-style/comment-format` |
| Type annotation spacing | `code-style/type-annotation-spacing` |

**Running `eslint src/ --fix` handles all formatting automatically.** No separate formatting step needed.

---

## 3. Naming Conventions

All naming conventions below are **enforced by `eslint-plugin-code-style`** and `eslint-plugin-check-file` rules. Violations are caught at lint time, and most are auto-fixable.

### 3.1 File and Folder Naming

**Enforced by:** `check-file/filename-naming-convention`, `check-file/folder-naming-convention`, `code-style/folder-based-naming-convention`, `code-style/hook-file-naming-convention`

All files and folders use **kebab-case**:

```
src/
  components/
    user-card/
      user-card.tsx
      user-card.test.tsx
    button/
      button.tsx
  hooks/
    use-auth.ts
    users/
      use-create-user.ts
      use-user-list.ts
  views/
    dashboard-view.tsx
    settings-view.tsx
  layouts/
    main-layout.tsx
  providers/
    auth-provider.tsx
  services/
    api-service.ts
  constants/
    app-constants.ts
  enums/
    user-role.ts
  interfaces/
    user.ts
  types/
    config.ts
```

**Rules:**
- Files: `kebab-case.tsx` (never `PascalCase.tsx` or `camelCase.tsx`)
- Folders: `kebab-case/` (never `PascalCase/` or `camelCase/`)
- Hook files: `use-{verb}-{module}.ts` (e.g., `use-create-user.ts`) or `use-{module}-list.ts`
- View files: `*-view.tsx`
- Layout files: `*-layout.tsx`
- Provider files: `*-provider.tsx`
- Service files: `*-service.ts`
- Constants files: `*-constants.ts`

### 3.2 Function Naming

**Enforced by:** `code-style/function-naming-convention`

Functions must be **camelCase** and **start with a verb**. Event handlers must use the **Handler suffix** pattern.

```typescript
// CORRECT
const fetchUserData = () => { ... };
const validateEmail = (email: string) => { ... };
const formatDate = (date: Date) => { ... };
const submitHandler = () => { ... };
const deleteItemHandler = (id: string) => { ... };

// WRONG - not verb-first
const userData = () => { ... };
const emailValidation = () => { ... };

// WRONG - handler pattern
const handleSubmit = () => { ... };      // auto-fixed to submitHandler
const handleDeleteItem = () => { ... };  // auto-fixed to deleteItemHandler
```

> `handleXxx` is auto-fixed to `xxxHandler` by the plugin.

### 3.3 Variable Naming

**Enforced by:** `code-style/variable-naming-convention`

```typescript
// CORRECT
const userName = "John";
const itemCount = 42;
const maxRetries = 3;
const isLoading = true;

// WRONG
const user_name = "John";   // snake_case
const MAX_RETRIES = 3;      // UPPER_CASE
const UserName = "John";    // PascalCase (reserved for components)
```

**Rules:**
- All variables and constants: `camelCase`
- Components: `PascalCase`
- Hooks: `use` prefix + `camelCase`

### 3.4 Component Naming

**Enforced by:** `code-style/variable-naming-convention`, `code-style/svg-icon-naming-convention`, `react/function-component-definition`

```typescript
// CORRECT
const UserCard = () => <div />;
const DashboardView = () => <div />;
const ChevronIcon = () => <svg>...</svg>;

// WRONG
const userCard = () => <div />;     // must be PascalCase
function UserCard() { return <div />; }  // must be arrow function
const Chevron = () => <svg>...</svg>;     // SVG icons must end with "Icon"
```

**Rules:**
- Components: `PascalCase` as arrow functions
- SVG icon components: must end with `Icon` suffix (e.g., `ChevronIcon`, `SearchIcon`)
- Components returning SVG must have the `Icon` suffix

### 3.5 Hook Naming

**Enforced by:** `code-style/hook-file-naming-convention`, `code-style/hook-function-naming-convention`, `code-style/use-state-naming-convention`

```typescript
// CORRECT - hook function naming
const useAuth = () => { ... };
const useCreateUser = () => { ... };
const useUserList = () => { ... };

// CORRECT - useState boolean naming
const [isLoading, setIsLoading] = useState(false);
const [hasError, setHasError] = useState(false);
const [isVisible, setIsVisible] = useState(true);

// WRONG
const [loading, setLoading] = useState(false);     // must be isLoading
const [error, setError] = useState(false);          // must be hasError
const [visible, setVisible] = useState(true);       // must be isVisible
```

**Rules:**
- Hook functions: `use` prefix + `camelCase`
- Hook file names must match function name: `use-create-user.ts` -> `useCreateUser`
- Boolean `useState` variables must start with `is`, `has`, `with`, or `without`

### 3.6 TypeScript Definitions Naming

**Enforced by:** `code-style/interface-format`, `code-style/type-format`, `code-style/enum-format`, `code-style/prop-naming-convention`

```typescript
// CORRECT - Interface (PascalCase + Interface suffix)
export interface UserInterface {
    email: string,
    id: string,
    name: string,
}

// CORRECT - Type (PascalCase + Type suffix)
export type ApiResponseType<T> = {
    data: T,
    status: number,
};

export type ButtonVariantType = "danger" | "ghost" | "primary";

// CORRECT - Enum (PascalCase + Enum suffix, UPPER_CASE members)
export enum UserRoleEnum {
    ADMIN = "admin",
    GUEST = "guest",
    USER = "user",
}

// CORRECT - Props (boolean: is/has prefix, callbacks: on prefix)
interface ButtonPropsInterface {
    hasError: boolean,
    isDisabled: boolean,
    isLoading: boolean,
    onClick: () => void,
    onSubmit: (data: FormData) => void,
}

// WRONG
export interface User { ... }          // missing Interface suffix
export type ApiResponse = { ... }      // missing Type suffix
export enum UserRole { ... }           // missing Enum suffix
export enum StatusEnum {
    active = "active",                 // members must be UPPER_CASE
}
```

**Rules:**
- Interfaces: `PascalCase` + `Interface` suffix, `camelCase` properties, commas (not semicolons)
- Types: `PascalCase` + `Type` suffix, `camelCase` properties, commas (not semicolons)
- Enums: `PascalCase` + `Enum` suffix, `UPPER_CASE` members
- Boolean props: `is`/`has`/`with`/`without` prefix
- Callback props: `on` prefix
- Properties sorted alphabetically (via `perfectionist`)

### 3.7 JSX Prop Naming

**Enforced by:** `code-style/jsx-prop-naming-convention`

```tsx
// CORRECT
<Component
    className="card"
    data-testid="user-card"
    aria-label="User card"
    isActive
    onClick={clickHandler}
/>

// Rules:
// - Regular props: camelCase
// - data-* and aria-*: kebab-case
// - Component reference props: PascalCase
// - Sorted alphabetically, callbacks last, shorthand last
```

### 3.8 Class Naming

**Enforced by:** `code-style/class-naming-convention`

```typescript
// CORRECT
class ApiServiceClass { ... }
class UserRepositoryClass { ... }

// WRONG
class ApiService { ... }    // missing Class suffix
class userService { ... }   // must be PascalCase
```

### 3.9 Naming Conventions Summary Table

| Element | Convention | Example | Enforced By |
|---|---|---|---|
| Files | kebab-case | `user-card.tsx` | `check-file/filename-naming-convention` |
| Folders | kebab-case | `user-card/` | `check-file/folder-naming-convention` |
| Variables | camelCase | `userName` | `code-style/variable-naming-convention` |
| Functions | camelCase, verb-first | `fetchData` | `code-style/function-naming-convention` |
| Event handlers | camelCase + Handler | `submitHandler` | `code-style/function-naming-convention` |
| Components | PascalCase | `UserCard` | `code-style/variable-naming-convention` |
| SVG icons | PascalCase + Icon | `ChevronIcon` | `code-style/svg-icon-naming-convention` |
| Hooks | use + camelCase | `useAuth` | `code-style/hook-function-naming-convention` |
| Hook files | use-kebab-case | `use-auth.ts` | `code-style/hook-file-naming-convention` |
| Boolean state | is/has/with/without | `isLoading` | `code-style/use-state-naming-convention` |
| Interfaces | PascalCase + Interface | `UserInterface` | `code-style/interface-format` |
| Types | PascalCase + Type | `ConfigType` | `code-style/type-format` |
| Enums | PascalCase + Enum | `StatusEnum` | `code-style/enum-format` |
| Enum members | UPPER_CASE | `ADMIN` | `code-style/enum-format` |
| Boolean props | is/has/with/without | `isDisabled` | `code-style/prop-naming-convention` |
| Callback props | on + PascalCase | `onClick` | `code-style/prop-naming-convention` |
| Classes | PascalCase + Class | `ApiServiceClass` | `code-style/class-naming-convention` |
| JSX props | camelCase | `className` | `code-style/jsx-prop-naming-convention` |
| data-*/aria-* | kebab-case | `data-testid` | `code-style/jsx-prop-naming-convention` |

---

## 4. Import/Export Rules

Import organization is fully covered by the combination of `eslint-plugin-simple-import-sort`, `eslint-plugin-import-x`, and `eslint-plugin-code-style`.

### 4.1 Import Order and Sorting

**Enforced by:** `simple-import-sort/imports`, `simple-import-sort/exports`

Imports are automatically sorted into groups:

```typescript
// 1. External packages (React first, then alphabetical)
import { useEffect, useState } from "react";
import { useNavigate } from "react-router-dom";

// 2. Internal aliases (@/ imports)
import { Button } from "@/components";
import { useAuth } from "@/hooks";
import { UserInterface } from "@/interfaces";

// 3. Relative imports
import { formatDate } from "./utils";
```

This is fully auto-fixable — just run `eslint --fix`.

### 4.2 Import Format

**Enforced by:** `code-style/import-format`

Imports are automatically collapsed or expanded based on the number of specifiers:

```typescript
// 1-3 specifiers: single line
import { Button, Input, Select } from "@/components";

// 4+ specifiers: multiline, one per line
import {
    Button,
    Checkbox,
    Input,
    Select,
} from "@/components";
```

### 4.3 Absolute Imports Only

**Enforced by:** `code-style/absolute-imports-only`

Use alias imports from index files. No relative imports between modules — relative imports are only allowed within the same module folder.

```typescript
// CORRECT
import { UserCard } from "@/components";
import { useAuth } from "@/hooks";
import { formatDate } from "@/utils";

// CORRECT - relative within the same module folder
import { helperFn } from "./helper";

// WRONG
import { UserCard } from "../../components/user-card";
import { useAuth } from "../hooks/use-auth";
```

### 4.4 Named Exports Only

**Enforced by:** `import-x/no-default-export`

All exports must be named exports — no default exports.

```typescript
// CORRECT
export const UserCard = () => <div />;
export const formatDate = (date: Date) => { ... };

// WRONG
export default UserCard;
const UserCard = () => <div />;
export default UserCard;
```

Named exports provide better refactor-ability and IDE autocomplete.

### 4.5 Index File Exports

**Enforced by:** `code-style/index-export-style`, `code-style/index-exports-only`, `code-style/module-index-exports`

Every module folder must have an `index.ts` that re-exports all its contents:

```typescript
// src/components/index.ts
export { Button } from "./button";
export { Input } from "./input";
export { UserCard } from "./user-card";
```

Index files must only contain exports — no code definitions.

### 4.6 TypeScript Type Imports

**Enforced by:** `@typescript-eslint/consistent-type-imports`

Type-only imports must use the `import type` syntax:

```typescript
// CORRECT
import type { UserInterface } from "@/interfaces";
import type { ConfigType } from "@/types";

import { formatDate } from "@/utils";

// WRONG
import { UserInterface } from "@/interfaces";  // should be import type
```

---

## 5. Pre-Commit Hooks (Lint Check)

**What runs:** ESLint on all staged files
**When:** Before every `git commit`
**Purpose:** Prevent committing code with lint errors

If any lint error is found, the commit is **blocked**. The developer must fix the errors before committing.

See [Section 7 (Husky Setup)](#7-husky-setup) for implementation.

---

## 6. Pre-Push Hooks (Build + Test Coverage)

**What runs:** Build + test coverage check
**When:** Before every `git push`
**Purpose:** Prevent pushing code that doesn't build or has failing tests

If the build fails or test coverage drops below the threshold, the push is **blocked**.

See [Section 7 (Husky Setup)](#7-husky-setup) for implementation.

---

## 7. Husky Setup

[Husky](https://typicode.github.io/husky/) enables Git hooks in Node.js projects.

### 7.1 Installation

```bash
# Install Husky
npm install --save-dev husky

# Initialize Husky (creates .husky/ directory)
npx husky init
```

This creates a `.husky/` directory at the project root and adds a `prepare` script to `package.json`.

### 7.2 Create Hook Files

**Pre-commit hook** — Create `.husky/pre-commit`:

```bash
npx eslint src/ --max-warnings 0
```

> `--max-warnings 0` ensures warnings also block the commit.

**Pre-push hook** — Create `.husky/pre-push`:

```bash
npm run build && npm run test:coverage
```

To create these files:

```bash
# Create pre-commit hook
echo "npx eslint src/ --max-warnings 0" > .husky/pre-commit

# Create pre-push hook
echo "npm run build && npm run test:coverage" > .husky/pre-push
```

### 7.3 package.json Scripts

Add all required scripts:

```json
{
    "scripts": {
        "prepare": "husky",
        "lint": "eslint src/",
        "lint:fix": "eslint src/ --fix",
        "build": "vite build",
        "test": "vitest run",
        "test:coverage": "vitest run --coverage --coverage.thresholds.lines=80 --coverage.thresholds.branches=80 --coverage.thresholds.functions=80 --coverage.thresholds.statements=80"
    }
}
```

> Adjust the `build` and `test` commands based on your project's tooling (Vite, Next.js, CRA, etc.). The coverage thresholds (80%) can be adjusted per project.

### 7.4 How It Works

```
Developer writes code
        |
   git commit
        |
   .husky/pre-commit runs
        |
   eslint src/ --max-warnings 0
        |
   Pass? ──> Commit created
   Fail? ──> Commit blocked, fix lint errors
        |
   git push
        |
   .husky/pre-push runs
        |
   npm run build && npm run test:coverage
        |
   Pass? ──> Push succeeds
   Fail? ──> Push blocked, fix build/tests
```

---

## 8. EditorConfig

Create `.editorconfig` at the project root. This ensures all team members' IDEs use the same base settings regardless of their personal editor preferences.

```ini
# Editor configuration, see http://editorconfig.org
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 4
insert_final_newline = true
trim_trailing_whitespace = true

[*.md]
max_line_length = off
trim_trailing_whitespace = false
```

**Key settings and why:**

| Setting | Value | Why |
|---|---|---|
| `charset` | `utf-8` | Universal encoding, prevents encoding issues |
| `indent_size` | `4` | Matches our ESLint `@stylistic/indent: 4` setting |
| `indent_style` | `space` | Spaces, not tabs — consistent rendering across all editors |
| `insert_final_newline` | `true` | POSIX compliance, prevents "no newline at end of file" warnings |
| `trim_trailing_whitespace` | `true` | Removes invisible trailing spaces that clutter diffs |

> Most editors support EditorConfig natively (VS Code, WebStorm, Vim, etc.). For VS Code, install the [EditorConfig extension](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig).

---

## 9. React Component Code Order

**Enforced by:** `code-style/react-code-order`

Every React component and hook must follow this internal ordering:

```typescript
const UserProfile = ({ userId }: { userId: string }) => {
    // 1. Props destructuring (already in params)

    // 2. Refs
    const inputRef = useRef<HTMLInputElement>(null);

    // 3. State
    const [isLoading, setIsLoading] = useState(false);
    const [userData, setUserData] = useState(null);

    // 4. Redux / Store
    const dispatch = useDispatch();
    const currentUser = useSelector(selectCurrentUser);

    // 5. Router
    const navigate = useNavigate();
    const { id } = useParams();

    // 6. Context
    const theme = useContext(ThemeContext);

    // 7. Custom Hooks
    const { data } = useUserData(userId);

    // 8. Derived / Computed values
    const fullName = `${userData?.firstName} ${userData?.lastName}`;

    // 9. Memoized values (useMemo)
    const sortedItems = useMemo(() => items.sort(), [items]);

    // 10. Callbacks (useCallback)
    const fetchData = useCallback(() => { ... }, []);

    // 11. Event Handlers
    const submitHandler = () => { ... };
    const deleteHandler = () => { ... };

    // 12. Effects (useEffect)
    useEffect(() => { ... }, []);

    // 13. Return (JSX)
    return <div>...</div>;
};
```

This is auto-fixable — the plugin will reorder code to match this pattern.

---

## 10. TypeScript Definition File Organization

**Enforced by:** `code-style/typescript-definition-location`

TypeScript definitions must be in designated folders:

```
src/
  interfaces/
    user.ts              → export interface UserInterface { ... }
    api-response.ts      → export interface ApiResponseInterface { ... }
  types/
    config.ts            → export type ConfigType = { ... }
    button-variant.ts    → export type ButtonVariantType = "primary" | "secondary";
  enums/
    user-role.ts         → export enum UserRoleEnum { ... }
    status.ts            → export enum StatusEnum { ... }
```

**Rules:**
- Interfaces go in `interfaces/` folder
- Types go in `types/` folder
- Enums go in `enums/` folder
- Do not define interfaces/types/enums inline in component files

---

## 11. Folder Structure Standards

**Enforced by:** `code-style/folder-structure-consistency`, `code-style/folder-based-naming-convention`, `code-style/no-redundant-folder-suffix`

Standard React project structure:

```
src/
  assets/              Static assets (images, fonts, icons)
  components/          Reusable UI components
    button/
      button.tsx
      index.ts
    user-card/
      user-card.tsx
      index.ts
    index.ts           Re-exports all components
  constants/           App-wide constants
  contexts/            React contexts
  enums/               TypeScript enums
  hooks/               Custom React hooks
  interfaces/          TypeScript interfaces
  layouts/             Page layouts
  pages/               Route-level page components (or views/)
  providers/           React providers
  routes/              Route configuration
  services/            API service modules
  store/               State management (Redux, etc.)
  types/               TypeScript types
  utils/               Utility functions
```

**Rules:**
- Each module folder has consistent structure (flat or wrapped)
- File names match folder conventions (view files use `-view` suffix, etc.)
- No redundant folder suffix in file names (e.g., `components/button/button.tsx` not `components/button/button-component.tsx`)
- Every module folder must have an `index.ts` re-exporting all its contents

---

## 12. Quick Setup Checklist

When creating a new frontend project or standardizing an existing one, follow this checklist:

- [ ] **ESLint**: Install dependencies and copy `eslint.config.js` from [Section 1](#1-linting-eslint)
- [ ] **EditorConfig**: Add `.editorconfig` from [Section 8](#8-editorconfig)
- [ ] **Husky**: Install and configure pre-commit + pre-push hooks from [Section 7](#7-husky-setup)
- [ ] **package.json scripts**: Add `lint`, `lint:fix`, `build`, `test`, `test:coverage`, `prepare` scripts
- [ ] **Folder structure**: Organize `src/` following [Section 11](#11-folder-structure-standards)
- [ ] **TypeScript definitions**: Move interfaces, types, enums to designated folders per [Section 10](#10-typescript-definition-file-organization)
- [ ] **Initial lint run**: Run `eslint src/ --fix` to auto-fix the entire codebase
- [ ] **Verify**: Run `eslint src/ --max-warnings 0` and fix any remaining issues
- [ ] **Commit**: Commit all config files (`.editorconfig`, `eslint.config.js`, `.husky/`)

After setup, all future commits and pushes will be automatically validated.

---

**This guide is maintained alongside [eslint-plugin-code-style](https://www.npmjs.com/package/eslint-plugin-code-style).** For detailed rule documentation and examples, see the [Rules Reference](./docs/rules/).
