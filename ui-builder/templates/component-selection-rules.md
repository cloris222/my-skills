# Component Selection Rules

# Component Selection Priority

Always follow this order:

1. Existing business component
2. Shared project component
3. UI library component ({{UI_FRAMEWORK}})
4. Compose existing components
5. Create new component

Never directly create a new component before checking existing ones.

---

# Required Search Process

Before implementing UI:

1. Search shared components directory
2. Search existing feature pages
3. Search UI library documentation
4. Reuse existing implementation patterns

---

# Shared Component Preference

If a shared business component exists:

- use it directly
- do not bypass it with raw UI library components

Example:

Use: `<UserTable />`

Avoid: direct `<{{UI_TABLE_COMPONENT}} />` usage

when project wrapper already exists.

---

# Wrapper Component Rule

{{WRAPPER_COMPONENTS_RULE_DETAIL}}

Because wrappers usually contain:

- unified styling
- business logic
- accessibility handling
- loading states
- responsive behavior

---

# New Component Creation Rules

Only create new shared components if:

- repeated usage is expected
- no equivalent component exists
- the pattern is reusable
- implementation matches project architecture

Avoid creating components for one-time use only.
