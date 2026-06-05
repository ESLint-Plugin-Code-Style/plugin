# Function Rules

### `function-call-spacing`

**What it does:** Removes any space between a function name and its opening parenthesis.

**Why use it:** Standard JavaScript convention. `fn()` is correct, `fn ()` looks like a typo and can cause confusion.

```javascript
// Good — no space before parenthesis
useDispatch();
myFunction(arg);
console.log("message");
array.map((x) => x * 2);
obj.method();

// Bad — space before parenthesis
useDispatch ();
myFunction (arg);
console.log ("message");
array.map ((x) => x * 2);
```

---

### `function-declaration-style`

**What it does:** Converts function declarations to `const` arrow function expressions. This is the auto-fixable companion to ESLint's built-in `func-style` rule.

**Why use it:** The built-in `func-style: ["error", "expression"]` rule reports function declarations but does not auto-fix them. This rule provides the auto-fix. Both rules should be used together for the best experience.

> **Important:** This rule depends on `func-style: ["error", "expression"]` being configured. If `func-style` is set to `"declaration"` or is disabled, do not enable this rule — it would conflict.

```typescript
// Good — arrow function expression
export const getToken = (): string | null => getCookie(tokenKey);

export const clearAuth = (): void => {
    removeToken();
    clearStorage();
};

const isAuthenticated = (): boolean => {
    const token = getToken();
    return !!token;
};

// Bad — function declaration
export function getToken(): string | null {
    return getCookie(tokenKey);
}

export function clearAuth(): void {
    removeToken();
    clearStorage();
}

function isAuthenticated(): boolean {
    const token = getToken();
    return !!token;
}
```

---

### `function-naming-convention`

**What it does:** Enforces naming conventions for functions:

- **camelCase** required
- **Verb prefix** required (get, set, fetch, post, put, patch, delete, is, has, can, should, click, submit, etc.)
- **Handler suffix** required (all functions must end with `Handler`)
- **Auto-fixes** `handleXxx` to `xxxHandler` (avoids redundant `handleClickHandler`)
- **Auto-fixes** PascalCase to camelCase for verb-prefixed functions

**Folder exemption:** Functions defined in `reducers/` folder are exempted from verb-prefix and Handler-suffix checks. `folder-based-naming-convention` enforces the `<name>Reducer` convention there — combining Handler would produce ugly compound names like `authReducerHandler`, and reducers are conventionally named with nouns (e.g., `authReducer`) rather than verb-prefixed actions.

**Why use it:** Function names should describe actions. Verb prefixes make the purpose immediately clear, and consistent Handler suffix makes event handlers easy to identify.

```javascript
// Good — verb prefix + Handler suffix
function getUserDataHandler() {}
function setUserNameHandler(name) {}
function clickHandler() {}
function submitHandler() {}
function isValidEmailHandler(email) {}
function hasPermissionHandler(user) {}
function canAccessHandler(resource) {}
const fetchUsersHandler = async () => {};

// Bad (auto-fixed) — handleXxx → xxxHandler
function handleClick() {}    // → clickHandler
function handleSubmit() {}   // → submitHandler
function handleChange() {}   // → changeHandler

// Bad (auto-fixed) — missing Handler suffix
function getUserData() {}    // → getUserDataHandler
function setUserName() {}    // → setUserNameHandler
function fetchUsers() {}     // → fetchUsersHandler

// Bad (auto-fixed) — PascalCase to camelCase
function GetUserData() {}    // → getUserDataHandler
function FetchStatus() {}    // → fetchStatusHandler
```

---

### `function-object-destructure`

**What it does:** Two responsibilities:

1. Enforces that non-component functions should not destructure parameters in the function signature — use a typed parameter and destructure at the top of the function body. Also reports dot-notation access of object params (suggests destructuring).
2. Enforces a configurable style for module imports — by default, module imports (e.g., `api`, `utils`, `services`) are accessed via dot notation rather than destructured, but JSX usage is exempted. Behavior is controlled by the `moduleImportStyle` option.

**Why use it:** Keeping function signatures clean improves readability. Dot notation for module imports (e.g., `api.getUser()` instead of `const { getUser } = api`) improves searchability across the codebase and prevents naming collisions. JSX usage stays as destructure since `<Button />` reads cleaner than `<ui.Button />`.

**Configurable options:**

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `moduleImportStyle` | `"smart" \| "strict-dot" \| "destructure"` | `"smart"` | How to handle module imports — see modes below |

**Modes:**

| Mode | Behavior |
|------|----------|
| `"smart"` (default) | Flag destructure of module imports → autofix to dot notation. **Exception:** if a destructured prop is used **only** as a JSX element, it is kept in the destructure (no error). Mixed-use destructures are partially rewritten — JSX-only props stay, regular props become dot notation. |
| `"strict-dot"` | Pure dot notation. JSX components become `<ui.Button />` instead of `<Button />`. No exceptions. |
| `"destructure"` | Opposite direction — flag dot notation `ui.X` and autofix to destructure. Inserts `const { X, Y } = ui;` at top of containing scope and replaces all uses. |

**Nested destructure with aliases is fully supported** — the rule recursively flattens `const { photographers: { activate: x } } = apisUrls` and replaces `x` references with `apisUrls.photographers.activate`.

```typescript
// Good — typed param with destructuring in body
const createUserHandler = async (data: CreateUserParamsInterface) => {
    const { age, email, isActive, name } = data;

    // Use age, email, isActive, name...
};

// Good — React components CAN destructure in signature
const UserCard = ({
    name,
    email,
}: {
    name: string,
    email: string,
}) => (
    <div>{name} - {email}</div>
);

// Good — module import uses dot notation (smart/strict-dot)
import { api } from "@/api";

const user = api.getUser(id);

// Good — JSX-only destructure preserved in smart mode (default)
import { ui } from "@/utils";

const { Button, Card } = ui;

const View = () => (
    <Card>
        <Button>Click</Button>
    </Card>
);

// Bad — non-component function destructures in signature
const createUserHandler = async ({
    age,
    email,
}: CreateUserParamsInterface) => {
    // ...
};

// Bad — accessing param via dot notation (should destructure)
const processDataHandler = (data: DataInterface) => {
    console.log(data.id);      // Bad: use destructuring
    return data.value * 2;     // Bad: use destructuring
};

// Bad — destructuring module imports for regular (non-JSX) code
import { utils } from "@/utils";

const { formatDate, validateEmail } = utils;   // Bad
const today = formatDate(new Date());          // Will be autofixed to utils.formatDate(...)
```

**Configuration examples:**

```javascript
// Default — smart mode (JSX exception)
"code-style/function-object-destructure": "error",

// Strict dot notation everywhere, no JSX exception
"code-style/function-object-destructure": ["error", { moduleImportStyle: "strict-dot" }],

// Opposite direction — enforce destructure of module imports
"code-style/function-object-destructure": ["error", { moduleImportStyle: "destructure" }],
```

> **Note:** Modules tracked are imports from paths matching: `api`, `apis`, `config`, `configs`, `constants`, `data`, `helpers`, `lib`, `routes`, `services`, `utils`, `utilities`. Imports from other paths are not affected.

---

### `function-params-per-line`

**What it does:** When function parameters span multiple lines, ensures each parameter is on its own line with consistent indentation.

**Why use it:** Mixed formatting (some params on same line, some on different lines) is confusing. One per line is scannable and easy to edit.

```javascript
// Good — each param on own line
function createUser(
    name,
    email,
    password,
    role,
) {}

const handler = (
    event,
    context,
    callback,
) => {};

// Good — short params can stay on one line
function add(a, b) {}

// Bad — mixed formatting
function createUser(name,
    email, password,
    role) {}

// Bad — some on same line, some not
const handler = (event, context,
    callback) => {};
```

---

### `no-empty-lines-in-function-params`

**What it does:** Removes empty lines within function parameter lists — between parameters and after opening/before closing parentheses.

**Why use it:** Empty lines in parameter lists waste space and make parameters harder to scan as a group.

```javascript
// Good — no empty lines
function createUser(
    name,
    email,
    role,
) {}

const handler = (
    event,
    context,
) => {};

// Bad — empty line between params
function createUser(
    name,

    email,

    role,
) {}

// Bad — empty line after opening paren
const handler = (

    event,
    context,
) => {};
```

<br />

---

[<- Back to Rules Index](./README.md) | [<- Back to Main README](../../README.md)
