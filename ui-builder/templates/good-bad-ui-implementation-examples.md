# Good / Bad UI Implementation Examples

This document defines approved and forbidden frontend implementation patterns.

The goal is to enforce:

- consistent UI architecture
- reusable component usage
- minimal custom styling
- maintainable DOM structure
- {{UI_FRAMEWORK}} first strategy

---

<!-- GENERATE_EXAMPLES
Generate 4 GOOD/BAD example pairs for this project's UI stack.

Project context:
- UI Framework: {{UI_FRAMEWORK}}
- Wrapper components: {{WRAPPER_COMPONENTS_LIST}}
- Styling: {{STYLE_APPROACH}}
- Layout components: {{LAYOUT_COMPONENTS}}

Each pair must follow this structure:
# GOOD EXAMPLE N — [descriptive title]
## Good
[vue template code using {{UI_FRAMEWORK}} + wrapper components]
## Why This Is Good
[3-5 bullet points]
---
# BAD EXAMPLE N — [same title, bad version]
## Bad
[vue template code using raw HTML / duplicated CSS]
## Why This Is Bad
[3-5 bullet points]
---

Required examples (in order):
1. Standard Page Header (header with title + action button + data display)
2. Form Layout (form with inputs, submit button)
3. Scoped CSS Usage (minimal custom styles, only when necessary)
4. {{UI_FRAMEWORK}} Responsive Layout (breakpoints / responsive grid)

Rules for generating examples:
- GOOD examples: always use {{UI_FRAMEWORK}} components + wrapper components (if any)
- BAD examples: always show the anti-pattern (raw HTML, duplicated CSS, arbitrary values)
- Use {{I18N_FUNCTION}}("key") for all display text in GOOD examples
- Style file extension: {{STYLE_EXT}}
-->

---

# Core Rule

Custom {{STYLE_EXT_UPPER}} should exist only when:

- existing utility classes are insufficient
- {{UI_FRAMEWORK}} props are insufficient
- business-specific styling is required

Otherwise:

- reuse wrapper components
- reuse {{UI_FRAMEWORK}}
- reuse utility classes
- reuse existing layout patterns
