# Rule Templates

Code scaffolds for new rule definitions and per-rule documentation pages. Cited from the `manage-rule` SKILL.md when authoring a new rule.

### A1. Rule Scaffold Template

Every rule follows this exact structure:

```javascript
/**
 * ───────────────────────────────────────────────────────────────
 * Rule: Rule Name Here
 * ───────────────────────────────────────────────────────────────
 *
 * Description:
 *   Brief description of what the rule does.
 *
 * Options:
 *   { optionName: defaultValue } - Description of option
 *
 * ✓ Good:
 *   // Example of correct code
 *
 * ✗ Bad:
 *   // Example of incorrect code
 */
const ruleName = {
    create(context) {
        const sourceCode = context.sourceCode || context.getSourceCode();
        const options = context.options[0] || {};
        const optionName = options.optionName !== undefined ? options.optionName : defaultValue;

        return {
            NodeType(node) {
                // Rule logic here

                // Report issues with auto-fix
                context.report({
                    fix: (fixer) => fixer.replaceText(node, "fixed code"),
                    message: "Error message describing the issue",
                    node,
                });
            },
        };
    },
    meta: {
        docs: { description: "Short description for documentation" },
        fixable: "code",  // Required for auto-fix rules
        schema: [
            {
                additionalProperties: false,
                properties: {
                    optionName: {
                        default: 3,
                        description: "Option description",
                        minimum: 1,
                        type: "integer",
                    },
                },
                type: "object",
            },
        ],
        type: "layout",  // "layout" for formatting, "suggestion" for conventions
    },
};
```

### A2. Per-Rule Docs Template (`rules/<category>.md`)

Each rule should have this format in its category file under `rules/`:

```markdown
### `rule-name`

**What it does:** One-line description of the rule's purpose.

**Why use it:** Optional context for why this rule is helpful.

> **Note:** Any special notes or dependencies (optional).

\`\`\`javascript
// ✅ Good — description
const example = "correct code";

// ❌ Bad — description
const example = "incorrect code";
\`\`\`

**Options:** (if rule has options)

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `optionName` | `string` | `"value"` | What the option does |

\`\`\`javascript
// Configuration example
"code-style/rule-name": ["error", { optionName: "value" }]
\`\`\`

---
```
