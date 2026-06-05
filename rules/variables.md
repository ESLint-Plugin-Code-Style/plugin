# Variable Rules

### `variable-naming-convention`

**What it does:** Enforces naming conventions for variables:

- **camelCase** for all variables and constants
- **PascalCase** for React components and classes
- **camelCase with `use` prefix** for hooks

**Why use it:** Consistent naming makes code predictable. You can tell what something is by how it's named.

```javascript
// Good — correct conventions
const userName = "John";           // camelCase for variables
const itemCount = 42;              // camelCase for variables
const maxRetries = 3;              // camelCase for constants
const apiBaseUrl = "/api";         // camelCase for constants
const UserProfile = () => <div />; // PascalCase for components
const useAuth = () => {};          // camelCase with use prefix for hooks

// Bad — wrong conventions
const user_name = "John";          // snake_case
const MAX_RETRIES = 3;             // should be camelCase
const userProfile = () => <div />; // should be PascalCase
const UseAuth = () => {};          // hooks should be camelCase
```

**Options:**

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `allowedCases` | `Array<{ paths: string[], cases: CaseName[] }>` | `[]` | Path-scoped allowed naming cases. Opt-in only — no defaults. Each entry whitelists the listed cases for files matching any of its glob paths. Useful for Redux action types, constants folders, etc. |

**Supported case names:** `camelCase`, `PascalCase`, `SCREAMING_SNAKE_CASE`, `snake_case`, `kebab-case`

**Path glob syntax:** `*` matches anything within a path segment; `**` matches any number of segments. Examples: `**/types/**`, `**/actions/*.ts`, `src/redux/**`.

**Configuration example — Redux action types in `**/types/**`:**

```javascript
"code-style/variable-naming-convention": ["error", {
    allowedCases: [
        // Allow SCREAMING_SNAKE_CASE in Redux types + actions folders
        { paths: ["**/types/**", "**/actions/**"], cases: ["SCREAMING_SNAKE_CASE"] },

        // Allow PascalCase namespace-like constants in constants folder
        { paths: ["**/constants/**"], cases: ["PascalCase", "SCREAMING_SNAKE_CASE"] },
    ]
}]
```

```javascript
// With the config above:

// src/types/user.ts — PASSES (SCREAMING_SNAKE_CASE allowed in **/types/**)
export const FETCH_USER_REQUEST = "FETCH_USER_REQUEST";
export const FETCH_USER_SUCCESS = "FETCH_USER_SUCCESS";

// src/utils/api.ts — STILL FLAGGED (no path match, autofix to camelCase)
const MAX_RETRIES = 3;             // → maxRetries

// src/constants/http.ts — PASSES (PascalCase namespace constant)
export const HttpStatus = { OK: 200, NOT_FOUND: 404 };
```

<br />

---

[<- Back to Rules Index](./README.md) | [<- Back to Main README](../../README.md)
