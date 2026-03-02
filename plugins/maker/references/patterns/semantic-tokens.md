# Semantic Tokens

> **Frontend apps only.** This pattern applies exclusively to projects with a browser-based UI. Backend services, APIs, CLIs, and libraries have no use for CSS tokens. Do not load or apply this pattern for non-UI projects.

Three-layer CSS custom property architecture for runtime-switchable theming. Used by `maker:architect` and `maker:build` when the project includes semantic tokens.

---

## Overview

```
Profile files (concrete values)
    ↓
tokens.css (profile → semantic mapping)
    ↓
index.css (framework directives + base styles)
    ↓
Components (consume semantic tokens only)
```

Components never touch profile tokens or raw values. They use semantic tokens via `var(--color-bg)` etc.

---

## Three Axes

| Axis | Profile prefix | Controls | Default file |
|---|---|---|---|
| Theme (color) | `--color-profile-` | Background, text, accent, error, warning, success | `themes/theme-light.css` |
| Density (spacing) | `--density-profile-` | Space, radius, motion duration/easing | `density/density-default.css` |
| Typography (fonts) | `--type-profile-` | Font families, sizes, weights, line heights | `typography/typography-default.css` |

---

## Token Naming Convention

| Layer | Pattern | Example |
|---|---|---|
| Profile (concrete) | `--{axis}-profile-{token}` | `--color-profile-bg: #ffffff` |
| Semantic (app-level) | `--{token}` | `--color-bg: var(--color-profile-bg)` |

Profile tokens declare concrete values. Semantic tokens reference profile tokens. Components use semantic tokens.

---

## File Specifications

### themes/theme-light.css

```css
/* theme-light.css — concrete light theme color values */
:root[data-theme="light"],
:root {
  --color-profile-bg: #ffffff;
  --color-profile-bg-subtle: #f8f9fa;
  --color-profile-surface: #f1f3f5;
  --color-profile-border: #dee2e6;
  --color-profile-text: #212529;
  --color-profile-text-subtle: #495057;
  --color-profile-accent: #228be6;
  --color-profile-accent-hover: #1c7ed6;
  --color-profile-accent-text: #ffffff;
  --color-profile-error: #fa5252;
  --color-profile-warning: #fd7e14;
  --color-profile-success: #40c057;
}
```

### density/density-default.css

```css
/* density-default.css — concrete default spacing, rhythm, and motion values */
:root {
  /* Spacing */
  --density-profile-space-1: 0.25rem;
  --density-profile-space-2: 0.5rem;
  --density-profile-space-3: 0.75rem;
  --density-profile-space-4: 1rem;
  --density-profile-space-6: 1.5rem;
  --density-profile-space-8: 2rem;
  --density-profile-space-12: 3rem;
  --density-profile-space-16: 4rem;

  /* Border radius */
  --density-profile-radius-sm: 0.25rem;
  --density-profile-radius-md: 0.5rem;
  --density-profile-radius-lg: 0.75rem;
  --density-profile-radius-full: 9999px;

  /* Motion */
  --density-profile-duration-fast: 100ms;
  --density-profile-duration-normal: 200ms;
  --density-profile-duration-slow: 300ms;
  --density-profile-ease-default: cubic-bezier(0.4, 0, 0.2, 1);
}
```

### typography/typography-default.css

```css
/* typography-default.css — concrete default font profile values */
:root {
  /* Font families */
  --type-profile-family-sans: ui-sans-serif, system-ui, -apple-system, sans-serif;
  --type-profile-family-mono: ui-monospace, "Cascadia Code", "Fira Code", monospace;

  /* Font sizes */
  --type-profile-size-xs: 0.75rem;
  --type-profile-size-sm: 0.875rem;
  --type-profile-size-base: 1rem;
  --type-profile-size-lg: 1.125rem;
  --type-profile-size-xl: 1.25rem;
  --type-profile-size-2xl: 1.5rem;
  --type-profile-size-3xl: 1.875rem;

  /* Font weights */
  --type-profile-weight-normal: 400;
  --type-profile-weight-medium: 500;
  --type-profile-weight-semibold: 600;
  --type-profile-weight-bold: 700;

  /* Line heights */
  --type-profile-leading-tight: 1.25;
  --type-profile-leading-normal: 1.5;
  --type-profile-leading-relaxed: 1.75;
}
```

### tokens.css

```css
/* tokens.css — maps profile tokens to semantic app tokens */
@import "./themes/theme-light.css";
@import "./density/density-default.css";
@import "./typography/typography-default.css";

:root {
  /* Color */
  --color-bg: var(--color-profile-bg);
  --color-bg-subtle: var(--color-profile-bg-subtle);
  --color-surface: var(--color-profile-surface);
  --color-border: var(--color-profile-border);
  --color-text: var(--color-profile-text);
  --color-text-subtle: var(--color-profile-text-subtle);
  --color-accent: var(--color-profile-accent);
  --color-accent-hover: var(--color-profile-accent-hover);
  --color-accent-text: var(--color-profile-accent-text);
  --color-error: var(--color-profile-error);
  --color-warning: var(--color-profile-warning);
  --color-success: var(--color-profile-success);

  /* Spacing */
  --space-1: var(--density-profile-space-1);
  --space-2: var(--density-profile-space-2);
  --space-3: var(--density-profile-space-3);
  --space-4: var(--density-profile-space-4);
  --space-6: var(--density-profile-space-6);
  --space-8: var(--density-profile-space-8);
  --space-12: var(--density-profile-space-12);
  --space-16: var(--density-profile-space-16);

  /* Border radius */
  --radius-sm: var(--density-profile-radius-sm);
  --radius-md: var(--density-profile-radius-md);
  --radius-lg: var(--density-profile-radius-lg);
  --radius-full: var(--density-profile-radius-full);

  /* Motion */
  --duration-fast: var(--density-profile-duration-fast);
  --duration-normal: var(--density-profile-duration-normal);
  --duration-slow: var(--density-profile-duration-slow);
  --ease-default: var(--density-profile-ease-default);

  /* Typography */
  --font-sans: var(--type-profile-family-sans);
  --font-mono: var(--type-profile-family-mono);
  --text-xs: var(--type-profile-size-xs);
  --text-sm: var(--type-profile-size-sm);
  --text-base: var(--type-profile-size-base);
  --text-lg: var(--type-profile-size-lg);
  --text-xl: var(--type-profile-size-xl);
  --text-2xl: var(--type-profile-size-2xl);
  --text-3xl: var(--type-profile-size-3xl);
  --font-normal: var(--type-profile-weight-normal);
  --font-medium: var(--type-profile-weight-medium);
  --font-semibold: var(--type-profile-weight-semibold);
  --font-bold: var(--type-profile-weight-bold);
  --leading-tight: var(--type-profile-leading-tight);
  --leading-normal: var(--type-profile-leading-normal);
  --leading-relaxed: var(--type-profile-leading-relaxed);
}
```

### index.css

```css
/* index.css — Tailwind directives + semantic token import */
@import "./tokens.css";

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  html {
    font-family: var(--font-sans);
    color: var(--color-text);
    background-color: var(--color-bg);
    line-height: var(--leading-normal);
    font-size: var(--text-base);
  }
}
```

---

## Token Validator Script

`scripts/validate-tokens.ts` must check:

1. All required profile files exist (themes/, density/, typography/).
2. `tokens.css` imports all profile files.
3. Required semantic tokens are declared: `--color-bg`, `--color-text`, `--color-accent`, `--color-error`, `--space-4`, `--radius-md`, `--duration-normal`, `--font-sans`, `--text-base`.
4. Exit 1 on failure with clear error messages.

---

## Rules

1. **Profile files**: CSS custom property declarations only. No logic, no imports.
2. **tokens.css**: Imports profiles, re-maps to semantic names. No hardcoded values.
3. **Components**: Use semantic tokens (`var(--color-bg)`), never profile tokens or raw values.
4. **Runtime overrides**: Set semantic AND profile properties on `:root` via JS for dynamic themes.
5. **New profile**: New file + data attribute switch. Zero changes to tokens.css or components.

---

## Component Pattern

Components use Tailwind arbitrary-value syntax with semantic tokens:

```tsx
// Correct — semantic tokens via Tailwind arbitrary values
<div class="bg-[var(--color-bg)] text-[var(--color-text)] p-[var(--space-4)]">

// Wrong — raw values
<div class="bg-white text-gray-900 p-4">

// Wrong — profile tokens
<div class="bg-[var(--color-profile-bg)]">
```

For font properties that need `length:` disambiguation:
```tsx
<p class="text-[length:var(--text-base)]">
```

For font-family:
```tsx
<p class="font-[family-name:var(--font-sans)]">
```
