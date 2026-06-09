# UI Architecture Rules

## Core Principle

The project follows a component-first UI architecture.

Never build primitive UI manually if an equivalent shared component or UI library component already exists.

Consistency is more important than isolated optimization.

---

# UI Implementation Priority

When implementing UI, follow this priority order strictly:

1. Existing business/shared components
2. UI library components
3. Existing {{STYLE_APPROACH}} utility classes
4. Existing layout systems
5. Scoped component {{STYLE_EXT}}
6. Raw HTML + custom styles

Raw HTML primitives should be treated as the last resort.

---

# Forbidden Implementations

Do NOT manually implement:

- buttons
- inputs
- selects
- modals
- dialogs
- dropdowns
- tabs
- tables
- pagination
- cards
- typography systems
- spacing systems
- flex helper systems

if equivalent components already exist in {{UI_FRAMEWORK}}.

---

# Styling Rules

Prefer:

1. existing utility classes
2. {{UI_FRAMEWORK}} props
3. component scoped {{STYLE_EXT}}

Avoid:

- arbitrary spacing values
- one-off utility classes
- deeply nested selectors
- inline styles
- duplicated typography styles
- duplicated flex wrappers

---

# Layout Rules

Prefer:

- existing grid/flex system
- {{LAYOUT_COMPONENTS}}
- shared layout wrappers

Avoid:

- unnecessary wrapper divs
- deeply nested layouts
- page-specific layout utilities

---

{{BREAKPOINTS_SECTION}}

# Before Creating New {{STYLE_EXT_UPPER}}

Must verify:

1. No existing utility class already solves the problem
2. No {{UI_FRAMEWORK}} prop solves the problem
3. No existing shared component solves the problem

---

# Consistency Rule

When implementing new pages or components:

- inspect existing similar pages first
- reuse existing patterns
- match existing spacing
- match typography conventions
- match interaction patterns

Consistency across the project is mandatory.
