# Component Discovery Rules

# Component Priority

When implementing UI, always search components in this order:

1. Business-specific shared components
2. Base/shared UI wrapper components
3. {{UI_FRAMEWORK}} components
4. Raw HTML (last resort)

---

# Shared Component Discovery

Project shared components are located in:

```
{{COMPONENT_SEARCH_PATHS}}
```

Before creating new UI:

- search for existing reusable components
- inspect similar pages
- reuse existing patterns

---

# {{UI_FRAMEWORK}} Usage Rules

Prefer {{UI_FRAMEWORK}} components over raw HTML.

Examples:

Use:

{{UI_COMPONENTS_LIST}}

Avoid:

- custom button wrappers
- custom flex systems
- custom form controls

unless project wrapper components already exist.

---

{{UI_COMPOSABLES_SECTION}}

# Shared Wrapper Component Rule

{{WRAPPER_COMPONENT_RULES}}

---

# Layout Rules

Prefer:

- {{LAYOUT_COMPONENTS}}
- existing utility classes

Avoid:

- custom flex wrappers
- duplicated grid systems
- ad-hoc alignment classes

---

# Typography Rules

Prefer:

- existing typography utilities
- {{UI_FRAMEWORK}} typography-related props

Avoid:

- custom font-size classes
- arbitrary typography systems

---

# Before Creating New Component

Must verify:

1. No similar shared component exists
2. No {{UI_FRAMEWORK}} component already solves the problem
3. No existing layout pattern already exists

New component creation requires justification.
