# Frontend Code Standards — Agent Instructions

Instructions for AI coding agents working on React/TypeScript frontend projects that use `eslint-plugin-code-style`. Follow these rules **while writing code** — do not rely on `eslint --fix` to correct violations after the fact.

> **Reference:** For the full project setup guide (ESLint config, Husky, EditorConfig), see [frontend-standards.md](./frontend-standards.md).

---

## Table of Contents

- [1. Naming Conventions](#1-naming-conventions)
- [2. File and Folder Structure](#2-file-and-folder-structure)
- [3. Formatting Rules](#3-formatting-rules)
- [4. Import and Export Patterns](#4-import-and-export-patterns)
- [5. React Component Patterns](#5-react-component-patterns)
- [6. TypeScript Patterns](#6-typescript-patterns)
- [7. JSX Patterns](#7-jsx-patterns)
- [8. Function Patterns](#8-function-patterns)
- [9. Hook Patterns](#9-hook-patterns)
- [10. Control Flow Patterns](#10-control-flow-patterns)
- [11. String Handling](#11-string-handling)
- [12. Quick Reference Cheat Sheet](#12-quick-reference-cheat-sheet)

---

## 1. Naming Conventions

These are the most critical rules. Get the name wrong and the linter will flag it.

### 1.1 Variables and Constants

Use **camelCase** for everything. No `UPPER_CASE` constants, no `snake_case`.

```typescript
// CORRECT
const userName = "John";
const maxRetries = 3;
const apiBaseUrl = "/api/v1";
const itemCount = 42;

// WRONG
const user_name = "John";
const MAX_RETRIES = 3;
const API_BASE_URL = "/api/v1";
```

### 1.2 Functions

Functions must be **camelCase**, start with a **verb**, and use the **Handler suffix** for event handlers. Always use **arrow function expressions**, never function declarations.

```typescript
// CORRECT
const fetchUserDataHandler = async () => { ... };
const validateEmailHandler = (email: string) => { ... };
const submitHandler = () => { ... };
const deleteItemHandler = (id: string) => { ... };
const formatDate = (date: Date) => { ... }; // utility, not a handler

// WRONG — handleXxx pattern (auto-fixed to xxxHandler)
const handleSubmit = () => { ... };
const handleDeleteItem = () => { ... };

// WRONG — no verb prefix
const userData = () => { ... };

// WRONG — function declaration (must be arrow expression)
function fetchData() { ... }
```

**Important:** Non-handler utility functions (used inside handlers or as pure transforms) do not need the Handler suffix. The Handler suffix is for functions that handle events or actions.

### 1.3 Components

Components use **PascalCase** and must be **arrow functions**.

```typescript
// CORRECT
const UserCard = () => <div />;
const DashboardView = () => <div />;

// WRONG
const userCard = () => <div />;
function UserCard() { return <div />; }
```

### 1.4 SVG Icon Components

SVG components must end with **Icon**. Components with "Icon" suffix must return SVG.

```typescript
// CORRECT
const ChevronIcon = () => <svg>...</svg>;
const SearchIcon = () => <svg>...</svg>;

// WRONG
const Chevron = () => <svg>...</svg>;
```

### 1.5 Classes

Classes must end with **Class** suffix.

```typescript
// CORRECT
class ApiServiceClass { ... }
class UserRepositoryClass { ... }

// WRONG
class ApiService { ... }
```

### 1.6 TypeScript Interfaces

**PascalCase** + **Interface** suffix. Properties in **camelCase**. Use **commas** (not semicolons). Properties sorted alphabetically.

```typescript
// CORRECT
export interface UserInterface {
    email: string,
    id: string,
    isActive: boolean,
    name: string,
}

// WRONG
export interface User {           // missing Interface suffix
    Email: string;                // PascalCase property, semicolon
    is_active: boolean;           // snake_case
}
```

### 1.7 TypeScript Types

**PascalCase** + **Type** suffix. Use **commas**. Properties sorted alphabetically.

```typescript
// CORRECT
export type ApiResponseType<T> = {
    data: T,
    error: string | null,
    status: number,
};

export type ButtonVariantType = "danger" | "ghost" | "primary";

// WRONG
export type ApiResponse = { ... };   // missing Type suffix
```

**Union type formatting:**
- Less than 5 members: single line
- 5 or more members: one per line

```typescript
// CORRECT — 3 members, single line
export type SizeType = "lg" | "md" | "sm";

// CORRECT — 6 members, multiline
export type ButtonVariantType =
    "danger"
    | "ghost"
    | "ghost-danger"
    | "link"
    | "muted"
    | "primary";
```

### 1.8 TypeScript Enums

**PascalCase** + **Enum** suffix. Members in **UPPER_CASE**. Use **commas**. Members sorted alphabetically.

```typescript
// CORRECT
export enum UserRoleEnum {
    ADMIN = "admin",
    GUEST = "guest",
    USER = "user",
}

// WRONG
export enum UserRole {            // missing Enum suffix
    active = "active",            // lowercase member
    inactive = "inactive";        // semicolon
}
```

### 1.9 Boolean Props and State

Boolean props must start with `is`, `has`, `with`, or `without`. Callback props must start with `on`.

```typescript
// CORRECT
interface ButtonPropsInterface {
    hasError: boolean,
    isDisabled: boolean,
    isLoading: boolean,
    onClick: () => void,
    onSubmit: (data: FormData) => void,
}

// WRONG
interface ButtonPropsInterface {
    disabled: boolean,       // should be isDisabled
    loading: boolean,        // should be isLoading
    click: () => void,       // should be onClick
    handleSubmit: () => void, // should be onSubmit
}
```

### 1.10 useState Boolean Variables

Boolean `useState` variables must start with `is`, `has`, `with`, or `without`.

```typescript
// CORRECT
const [isLoading, setIsLoading] = useState(false);
const [hasError, setHasError] = useState(false);
const [isVisible, setIsVisible] = useState(true);

// WRONG
const [loading, setLoading] = useState(false);
const [error, setError] = useState(false);
```

### 1.11 Files and Folders

All files and folders use **kebab-case**.

```
// CORRECT
user-card.tsx
use-create-user.ts
api-service.ts
dashboard-view.tsx

// WRONG
UserCard.tsx
useCreateUser.ts
apiService.ts
```

### 1.12 Folder-Based Component Naming

Components must include folder context in their name:

| Folder | Suffix | Example |
|---|---|---|
| `views/` | View | `DashboardView` |
| `layouts/` | Layout | `MainLayout` |
| `pages/` | Page | `HomePage` |
| `providers/` | Provider | `AuthProvider` |
| `contexts/` | Context | `AuthContext` |
| `reducers/` | Reducer | `UserReducer` |
| `theme/` | Theme | `DarkTheme` |
| `data/` | Data (camelCase) | `authData` |
| `constants/` | Constants (camelCase) | `apiConstants` |
| `strings/` | Strings (camelCase) | `loginStrings` |
| `services/` | Services (camelCase) | `userServices` |

Nested files chain folder names:

```typescript
// File: layouts/auth/login.tsx
export const LoginAuthLayout = () => <div />;

// File: atoms/input/password.tsx
export const PasswordInput = () => <input type="password" />;

// File: data/auth.ts
export const authData = { ... };
```

**No redundant folder suffix in file names:**

```
// CORRECT
layouts/main.tsx          → export const MainLayout
views/dashboard.tsx       → export const DashboardView

// WRONG
layouts/main-layout.tsx   → redundant "-layout"
views/dashboard-view.tsx  → redundant "-view"
```

---

## 2. File and Folder Structure

### 2.1 Standard Project Layout

```
src/
  assets/
  components/
  constants/
  contexts/
  data/
  enums/
  hooks/
  interfaces/
  layouts/
  pages/          (or views/)
  providers/
  routes/
  services/
  store/
  strings/
  types/
  utils/
```

### 2.2 TypeScript Definition Location

Interfaces, types, and enums must be in designated folders — never inline in component files.

```
// CORRECT
src/interfaces/user.ts     → export interface UserInterface { ... }
src/types/config.ts        → export type ConfigType = { ... }
src/enums/status.ts        → export enum StatusEnum { ... }

// WRONG — defining interface inside a component file
src/components/user-card.tsx → interface UserCardPropsInterface { ... }
```

### 2.3 Module Folders Need Index Files

Every module folder must have an `index.ts` re-exporting all contents:

```typescript
// src/components/index.ts
export { Button } from "./button";
export { Input } from "./input";
export { UserCard } from "./user-card";
```

Index files must contain **only** imports and re-exports. No code definitions.

### 2.4 Folder Structure Consistency

Within a module folder, use either **all flat files** or **all wrapped in subfolders** — never mix.

```
// CORRECT — flat
components/button.tsx
components/input.tsx
components/card.tsx

// CORRECT — wrapped (justified: button has multiple files)
components/button/index.tsx
components/button/helpers.ts
components/input/index.tsx
components/card/index.tsx

// WRONG — mixed
components/button.tsx
components/input/index.tsx
components/input/helpers.ts
```

### 2.5 Hook File Naming

Hook files in module subfolders must include the module name:

```
// CORRECT
hooks/users/use-create-user.ts    → export const useCreateUser
hooks/users/use-user-list.ts      → export const useUserList
hooks/use-debounce.ts             → export const useDebounce

// WRONG
hooks/users/use-create.ts         → missing module name
hooks/users/use-list.ts           → missing module name
```

---

## 3. Formatting Rules

### 3.1 General Style

- **Indentation:** 4 spaces (not 2, not tabs)
- **Quotes:** Double quotes `"..."` (not single)
- **Semicolons:** Always
- **Trailing commas:** Always in multiline
- **Object spacing:** `{ a, b }` (spaces inside braces)
- **No space** inside parentheses: `fn(x)` not `fn( x )`
- **Operator spacing:** `a + b` not `a+b`
- **No multiple empty lines:** Maximum 1 blank line between code
- **No empty lines** at file start or end
- **Comments** start with capital letter and space after `//`

### 3.2 Objects

- 1 property: single line `{ name: "John" }`
- 2+ properties: multiline, one per line, no empty lines between properties

```typescript
// CORRECT
const point = { x: 10 };
const user = {
    email: "john@example.com",
    name: "John",
    role: "admin",
};

// WRONG
const user = { name: "John", email: "john@example.com" };
```

### 3.3 Arrays

- 3 or fewer items: single line
- 4+ items: one per line
- Objects in arrays: always one per line

```typescript
// CORRECT
const colors = ["blue", "green", "red"];
const weekdays = [
    "Friday",
    "Monday",
    "Thursday",
    "Tuesday",
    "Wednesday",
];
const users = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
];

// WRONG
const weekdays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"];
const users = [{ id: 1, name: "Alice" }, { id: 2, name: "Bob" }];
```

### 3.4 Function Arguments

- 1 simple argument: single line
- 2+ arguments: one per line

```typescript
// CORRECT
fetchUser(userId);
setValue(
    "email",
    "user@example.com",
);
createUser(
    email,
    name,
    password,
);

// WRONG
setValue("email", "user@example.com");
createUser(name, email, password);
```

### 3.5 Arrow Functions

- Use implicit return when possible: `(x) => x * 2`
- Multiline JSX return: use parentheses `() => ( <div>...</div> )`
- Simple single-element JSX: collapse to one line
- Curried functions: keep on same line as `=>`

```typescript
// CORRECT
const double = (x) => x * 2;
const getName = (user) => user.name;
const Layout = ({ children }) => <Container>{children}</Container>;
const createAction = (type) => (payload) => ({ type, payload });

// WRONG
const double = (x) => { return x * 2; };
const Layout = ({ children }) => (
    <Container>{children}</Container>
);
const createAction = (type) =>
    (payload) => ({ type, payload });
```

### 3.6 Assignment Values

Values must start on the same line as `=`:

```typescript
// CORRECT
const name = "John";
const config = {
    debug: true,
};

// WRONG
const name =
    "John";
const config =
    {
        debug: true,
    };
```

### 3.7 Empty Lines After Blocks

Always add an empty line after closing `}` of if/for/while/try blocks before the next statement:

```typescript
// CORRECT
if (condition) {
    doSomething();
}

const x = 1;

// WRONG
if (condition) {
    doSomething();
}
const x = 1;
```

### 3.8 Blank Lines Between Statements

- Blank line before `return`
- Blank line after variable declarations
- Blank line between expressions

```typescript
// CORRECT
const name = "John";

doSomething(name);

return name;
```

---

## 4. Import and Export Patterns

### 4.1 Import Order

Imports are auto-sorted into groups. Write them in this order:

```typescript
// 1. External packages
import { useEffect, useState } from "react";
import { useNavigate } from "react-router-dom";

// 2. Internal aliases (@/)
import { Button } from "@/components";
import { useAuth } from "@/hooks";
import type { UserInterface } from "@/interfaces";

// 3. Relative imports (only within same module folder)
import { formatDate } from "./utils";
```

### 4.2 Import Format

- 1-3 specifiers: single line
- 4+ specifiers: one per line

```typescript
// CORRECT
import { Button, Input, Select } from "@/components";
import {
    useCallback,
    useEffect,
    useMemo,
    useState,
} from "react";

// WRONG
import { useState, useEffect, useCallback, useMemo, useRef } from "react";
```

### 4.3 Absolute Imports Only

Use `@/` alias imports from index files. No relative imports across modules.

```typescript
// CORRECT
import { UserCard } from "@/components";
import { useAuth } from "@/hooks";

// CORRECT — relative within same module folder
import { helperFn } from "./helper";

// WRONG — relative across modules
import { UserCard } from "../../components/user-card";

// WRONG — deep imports into component internals
import { Button } from "@/components/buttons/primary-button";
```

### 4.4 Named Exports Only

All exports must be named — no default exports.

```typescript
// CORRECT
export const UserCard = () => <div />;

// WRONG
export default UserCard;
```

### 4.5 Inline Export Declarations

Export at the declaration site, not in a grouped statement at the bottom.

```typescript
// CORRECT
export const apiUrl = "/api";
export const maxRetries = 3;

// WRONG
const apiUrl = "/api";
const maxRetries = 3;
export { apiUrl, maxRetries };
```

### 4.6 Index File Exports

Index files: no blank lines between exports, use shorthand re-exports.

```typescript
// CORRECT — index.ts
export { Button } from "./button";
export { Input } from "./input";
export { Select } from "./select";
```

Regular files: blank lines between exports.

```typescript
// CORRECT — regular file
export const apiUrl = "/api";

export const maxRetries = 3;
```

### 4.7 Type Imports

Always use `import type` for type-only imports.

```typescript
// CORRECT
import type { UserInterface } from "@/interfaces";
import type { ConfigType } from "@/types";

// WRONG
import { UserInterface } from "@/interfaces";
```

---

## 5. React Component Patterns

### 5.1 Component Code Order

Every component and hook must follow this internal ordering:

```typescript
const UserDashboard = ({ title } : { title: string }) => {
    // 1. Refs
    const inputRef = useRef<HTMLInputElement>(null);

    // 2. State
    const [isLoading, setIsLoading] = useState(false);

    // 3. Redux
    const dispatch = useDispatch();
    const user = useSelector(selectUser);

    // 4. Router
    const navigate = useNavigate();
    const { id } = useParams();

    // 5. Context
    const theme = useContext(ThemeContext);

    // 6. Custom hooks
    const { data } = useUserData(id);

    // 7. Derived state
    const fullName = `${user.firstName} ${user.lastName}`;

    // 8. useMemo
    const sortedItems = useMemo(() => items.sort(), [items]);

    // 9. useCallback
    const fetchData = useCallback(() => { ... }, []);

    // 10. Handlers
    const submitHandler = () => { ... };

    // 11. Effects
    useEffect(() => { ... }, []);

    // 12. Return
    return <div>{title}</div>;
};
```

### 5.2 Props Destructuring

Components must destructure props in the function parameter. Use inline type annotation (not interface reference).

```typescript
// CORRECT — destructured props with inline type
export const Button = ({
    isDisabled,
    label,
    onClick,
} : {
    isDisabled?: boolean,
    label: string,
    onClick: () => void,
}) => (
    <button
        disabled={isDisabled}
        type="button"
        onClick={onClick}
    >
        {label}
    </button>
);

// CORRECT — single prop stays on one line
export const Title = ({ text } : { text: string }) => <h1>{text}</h1>;

// WRONG — props as single object
export const Button = (props) => <button>{props.label}</button>;

// WRONG — interface reference
export const Button = ({ label }: ButtonPropsInterface) => ...;
```

**Inline type rules:**
- Exactly one space before and after colon: `} : {`
- Multiple props: each on its own line (both destructured and type)
- Trailing commas, not semicolons
- No empty lines between prop types

### 5.3 Non-Component Function Parameters

Non-component functions must NOT destructure in the signature. Destructure in the body instead.

```typescript
// CORRECT
const createUserHandler = async (data: CreateUserParamsInterface) => {
    const { age, email, name } = data;
    // ...
};

// WRONG — destructured in signature (only components can do this)
const createUserHandler = async ({
    age,
    email,
    name,
}: CreateUserParamsInterface) => { ... };
```

### 5.4 Arrow Function Components

All components must be arrow functions (not function declarations).

```typescript
// CORRECT
export const UserCard = () => <div />;

// WRONG
export function UserCard() { return <div />; }
```

---

## 6. TypeScript Patterns

### 6.1 Type Annotation Spacing

No space before colon, one space after. No space before generics or array brackets.

```typescript
// CORRECT
const name: string = "John";
const items: string[] = [];
const data: Array<number> = [];
const handler = (value: string): boolean => true;

// WRONG
const name : string = "John";
const name:string = "John";
const items: string [] = [];
const data: Array <number> = [];
```

### 6.2 Use Enum Values, Not String Literals

When a variable is typed with a `*Type`, use the corresponding enum.

```typescript
// CORRECT
const variant = ButtonVariantEnum.PRIMARY;

// WRONG
const variant: ButtonVariantType = "primary";
```

### 6.3 Extract Complex Union Types

Inline union types with 3+ members must be extracted to named types.

```typescript
// CORRECT
type ButtonVariantType = "danger" | "ghost" | "primary";

export const Button = ({
    variant,
} : {
    variant?: ButtonVariantType,
}) => { ... };

// WRONG — inline union too complex
export const Button = ({
    variant,
} : {
    variant?: "danger" | "ghost" | "primary",
}) => { ... };
```

---

## 7. JSX Patterns

### 7.1 Children Formatting

Multiple children: each on its own line. Single child: can stay inline.

```tsx
// CORRECT
<Container>
    <Header />
    <Content />
    <Footer />
</Container>

<Button>{buttonText}</Button>

// WRONG
<Container><Header /><Content /><Footer /></Container>
```

### 7.2 Conditional Rendering

No unnecessary parentheses around conditions or JSX in logical expressions.

```tsx
// CORRECT
{isLoading && <Spinner />}
{items.length > 0 && <List items={items} />}

// WRONG
{(isLoading) && (<Spinner />)}
```

### 7.3 Ternary in JSX

Simple ternaries: one line. Complex branches: parentheses with proper indentation.

```tsx
// CORRECT — simple
{isLoading ? <Spinner /> : <Content />}

// CORRECT — complex branches
{isLoading ? (
    <Spinner size="large" />
) : (
    <Content>
        <Header />
        <Body />
    </Content>
)}
```

### 7.4 JSX Parentheses Position

Opening `(` on same line as `return` or `=>`.

```tsx
// CORRECT
const Card = () => (
    <div>
        <h1>Title</h1>
    </div>
);

// WRONG
const Card = () =>
    (
        <div>
            <h1>Title</h1>
        </div>
    );
```

### 7.5 Prop Naming

- Regular props: `camelCase`
- `data-*` and `aria-*`: `kebab-case`
- Component reference props: `PascalCase`
- Props sorted alphabetically, callbacks last, shorthand last

```tsx
// CORRECT
<Button
    className="primary"
    data-testid="submit-btn"
    label="Submit"
    aria-label="Submit form"
    onClick={submitHandler}
    disabled
/>
```

### 7.6 Self-Closing Components

Use self-closing when no children.

```tsx
// CORRECT
<Button />
<Input type="text" />

// WRONG
<Button></Button>
<Input type="text"></Input>
```

### 7.7 No Empty Lines in JSX

No empty lines between children or after opening/before closing tags.

```tsx
// CORRECT
<div>
    <Header />
    <Content />
    <Footer />
</div>

// WRONG
<div>

    <Header />

    <Content />

</div>
```

### 7.8 Tailwind className

- Static classes only: use `"..."` string literal
- With dynamic expressions: use `` {`...`} `` template literal
- Dynamic expressions go at the end
- Short (3 or fewer classes): single line
- Long (4+ classes): one class per line

```tsx
// CORRECT — short, static
<div className="flex items-center gap-4" />

// CORRECT — long, static, multiline with string literal
<div
    className="
        flex
        items-center
        justify-between
        rounded-lg
        p-4
    "
/>

// CORRECT — with dynamic expression
<div
    className={`
        flex
        items-center
        justify-between
        ${className}
    `}
/>

// WRONG — template literal without expression
<div className={`flex items-center gap-4`} />

// WRONG — dynamic in middle
<div className={`flex ${className} items-center gap-4`} />
```

---

## 8. Function Patterns

### 8.1 Function Parameters

When multiline, each parameter on its own line. No empty lines between params.

```typescript
// CORRECT
const createUser = (
    email: string,
    name: string,
    password: string,
    role: string,
) => { ... };

// WRONG
const createUser = (name: string,
    email: string, password: string,
    role: string) => { ... };
```

### 8.2 No Space Before Parentheses

```typescript
// CORRECT
myFunction(arg);
useDispatch();
array.map((x) => x);

// WRONG
myFunction (arg);
useDispatch ();
```

### 8.3 Nested Call Closing Brackets

Chain closing brackets together on one line.

```typescript
// CORRECT
const StyledCard = styled(Card)(({ theme }) => ({
    color: theme.palette.text.primary,
}));

// WRONG
const StyledCard = styled(Card)(({ theme }) => ({
    color: theme.palette.text.primary,
})
);
```

---

## 9. Hook Patterns

### 9.1 Hook Callback Format

Hooks with callbacks must use multiline format with callback and deps clearly separated.

```typescript
// CORRECT
useEffect(
    () => {
        fetchData();
    },
    [userId],
);

useMemo(
    () => expensiveCalculation(items),
    [items],
);

// WRONG
useEffect(() => { fetchData(); }, [userId]);
```

### 9.2 Hook Dependency Arrays

- 2 or fewer deps: single line
- 3+ deps: one per line

```typescript
// CORRECT
useEffect(() => {}, [userId, token]);

useEffect(
    () => {},
    [
        refreshToken,
        token,
        userId,
    ],
);

// WRONG
useEffect(() => {}, [userId, token, refreshToken, apiUrl]);
```

### 9.3 Hook Function Names Must Match File Names

```
// File: hooks/users/use-create-user.ts
export const useCreateUser = () => { ... };  // CORRECT
export const useCreate = () => { ... };      // WRONG
```

---

## 10. Control Flow Patterns

### 10.1 If/Else Formatting

Opening `{` on same line. `else` on same line as `}`.

```typescript
// CORRECT
if (condition) {
    doSomething();
} else if (other) {
    doOther();
} else {
    doDefault();
}

// WRONG
if (condition)
{
    doSomething();
}
else
{
    doOther();
}
```

### 10.2 Consecutive If Blocks

Empty line between consecutive if blocks with block bodies.

```typescript
// CORRECT
if (!hasParams) return null;

if (status === "loading") {
    return <Loading />;
}

if (status === "error") {
    return <Error />;
}
```

### 10.3 Logical Expressions

- 3 or fewer operands: single line
- 4+ operands: one per line with operator at start

```typescript
// CORRECT
const isValid = a && b && c;

const isComplete = hasAddress
    && hasEmail
    && hasName
    && hasPhone;

// WRONG
const isComplete = hasName && hasEmail && hasPhone && hasAddress;
```

### 10.4 Multiline If Conditions

4+ conditions: one per line.

```typescript
// CORRECT
if (
    hasPermission &&
    isAuthenticated &&
    isEnabled &&
    !isExpired
) {
    allowAccess();
}

// WRONG
if (isAuthenticated && hasPermission && !isExpired && isEnabled) {}
```

### 10.5 Ternary Conditions

- 3 or fewer operands in condition: always single line
- 4+ operands: multiline

```typescript
// CORRECT — simple condition, single line
const x = a && b && c ? "yes" : "no";

// CORRECT — complex condition, multiline
const style = variant === "ghost"
    || variant === "muted"
    || variant === "primary"
    || variant === "danger"
    ? "transparent"
    : "solid";
```

### 10.6 Switch Cases

No empty lines after `case:` or between cases.

```typescript
// CORRECT
switch (status) {
    case "error":
        return "Failed";
    case "pending":
        return "Waiting";
    case "success":
        return "Done";
    default:
        return "Unknown";
}
```

---

## 11. String Handling

### 11.1 No Hardcoded User-Facing Strings

Import strings from `@/constants`, `@/strings`, or `@/data`. Use enums for status codes, roles, etc.

```typescript
// CORRECT
import { buttonLabels } from "@/strings";
import { HttpStatusEnum, UserRoleEnum } from "@/enums";

<button>{buttonLabels.submit}</button>

if (status === HttpStatusEnum.NOT_FOUND) { ... }
if (role === UserRoleEnum.ADMIN) { ... }

// WRONG
<button>Submit Form</button>
if (status === "404") { ... }
if (role === "admin") { ... }
```

Technical strings (CSS classes, URLs, path segments, types, keys) are allowed inline.

### 11.2 Comments

- Space after `//`
- Capitalize first letter
- No blank lines between consecutive file-top comments
- Use `//` for single-line, `/* */` for multi-line

```typescript
// CORRECT
// This is a comment

/*
 * This is a multi-line
 * block comment
 */

// WRONG
//no space
// lowercase start
```

---

## 12. Quick Reference Cheat Sheet

```
NAMING
  files/folders:    kebab-case              user-card.tsx
  variables:        camelCase               userName
  functions:        camelCase + verb        fetchDataHandler
  handlers:         camelCase + Handler     submitHandler
  components:       PascalCase              UserCard
  SVG icons:        PascalCase + Icon       ChevronIcon
  classes:          PascalCase + Class      ApiServiceClass
  interfaces:       PascalCase + Interface  UserInterface
  types:            PascalCase + Type       ConfigType
  enums:            PascalCase + Enum       StatusEnum
  enum members:     UPPER_CASE             ADMIN
  boolean props:    is/has/with/without     isDisabled
  callback props:   on + PascalCase         onClick
  boolean state:    is/has/with/without     isLoading
  hook functions:   use + camelCase         useCreateUser
  hook files:       use-kebab-case          use-create-user.ts

FORMATTING
  indent:           4 spaces
  quotes:           double "..."
  semicolons:       always
  trailing commas:  always (multiline)
  objects:          multiline at 2+ props
  arrays:           multiline at 4+ items
  imports/exports:  multiline at 4+ specifiers
  function args:    multiline at 2+ args
  hook deps:        multiline at 3+ deps
  union types:      multiline at 5+ members
  logical exprs:    multiline at 4+ operands

PATTERNS
  function style:   arrow expression (never function declaration)
  exports:          named only (no default)
  imports:          absolute @/ from index files
  type imports:     import type { ... }
  component props:  inline type, destructured
  other fn params:  typed param, destructure in body
  TS definitions:   in interfaces/, types/, enums/ folders
  strings:          import from constants/strings modules
  code order:       refs → state → redux → router → context → hooks → derived → memo → callback → handlers → effects → return
```
