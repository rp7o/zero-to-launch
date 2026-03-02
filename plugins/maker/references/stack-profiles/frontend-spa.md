# Frontend SPA

Single-page application with client-side rendering, semantic design tokens, and interactive UI.

**Uses patterns**: `patterns/semantic-tokens.md` (unless "No Semantic Tokens" variant is selected)

## When to Use

- User describes a UI, page, screen, component, or interactive experience.
- The project is fully client-side — no server component.
- User wants runtime-switchable design (themes, density, typography).
- Default choice when the project shape is "frontend app."

## Stack

| Component | Choice | Version | Rationale |
|---|---|---|---|
| Runtime | Bun | latest | Fast, TypeScript-native |
| UI | Preact | ^10.25.4 | Lightweight, sufficient for SPAs |
| CSS | Tailwind + semantic tokens | ^3.4.17 | Token system enables runtime design changes |
| Dev server | Vite + @preact/preset-vite | ^7.x / ^2.x | Standard Preact dev server. Bun --hot doesn't support browser apps. |
| Linter | Biome | ^1.9.4 | Fast, zero-config, handles formatting too |
| Testing | Bun test | built-in | No extra dependency |
| TypeScript | strict | ^5.7.3 | Type safety |
| Types | @types/bun | ^1.2.4 | Bun runtime types |

## Folder Model

```
/
├── index.html
├── src/
│   ├── app/
│   │   └── main.tsx
│   ├── components/
│   ├── lib/
│   └── styles/
│       ├── themes/theme-light.css
│       ├── density/density-default.css
│       ├── typography/typography-default.css
│       ├── tokens.css
│       └── index.css
├── scripts/
│   └── validate-tokens.ts
└── handbook/
    └── tasks/
```

### Dependency Direction

`app → components → lib`. Lib must not import components or app.

## Config Blueprints

### package.json

```json
{
  "name": "[project-name]",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "typecheck": "tsc --noEmit",
    "lint": "biome check src scripts",
    "lint:fix": "biome check --write src scripts",
    "test": "bun test",
    "validate:tokens": "bun run scripts/validate-tokens.ts",
    "validate": "bun run validate:tokens && bun run lint && bun run typecheck && bun test && bun run build"
  },
  "dependencies": {
    "preact": "^10.25.4"
  },
  "devDependencies": {
    "@biomejs/biome": "^1.9.4",
    "@preact/preset-vite": "^2.x",
    "@types/bun": "^1.2.4",
    "tailwindcss": "^3.4.17",
    "typescript": "^5.7.3",
    "vite": "^7.x"
  }
}
```

### tsconfig.json

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "jsx": "react-jsx",
    "jsxImportSource": "preact",
    "lib": ["ESNext", "DOM", "DOM.Iterable"],
    "baseUrl": ".",
    "paths": { "~/*": ["./src/*"] },
    "noEmit": true,
    "skipLibCheck": true
  },
  "include": ["src", "scripts"]
}
```

### biome.json

```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "organizeImports": { "enabled": true },
  "linter": { "enabled": true, "rules": { "recommended": true } },
  "formatter": { "enabled": true, "indentStyle": "space", "indentWidth": 2, "lineWidth": 100 },
  "javascript": { "formatter": { "quoteStyle": "double", "trailingCommas": "all" } },
  "files": { "ignore": ["dist", "node_modules"] }
}
```

### vite.config.ts

```typescript
import preact from "@preact/preset-vite";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [preact()],
  resolve: { alias: { "~": "/src" } },
});
```

### tailwind.config.ts

```typescript
import type { Config } from "tailwindcss";

export default {
  content: ["./index.html", "./src/**/*.{ts,tsx}"],
  theme: { extend: {} },
  plugins: [],
} satisfies Config;
```

### .gitignore

```
node_modules/
dist/
.DS_Store
*.local
bun.lockb
```

### index.html

```html
<!doctype html>
<html lang="en" data-theme="light" data-density="default" data-typography="default">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>[Project Name]</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/app/main.tsx"></script>
  </body>
</html>
```

## Quality Gates

Run in this order. All must pass.

1. `bun run validate:tokens` — Token files exist, imports correct, required semantic tokens declared.
2. `bun run lint` — Biome linting + formatting.
3. `bun run typecheck` — TypeScript strict mode.
4. `bun test` — Bun test runner.
5. `bun run build` — Vite production build.

Chained: `bun run validate` runs all five in order.

Triage order on failure: tokens → lint → typecheck → test → build.

## Known Issues

- **Vite is required for dev server.** `bun run --hot` does NOT work for browser apps (no `document`). Bun's built-in bundler works for production builds but can't serve browser apps during development.
- **Tailwind PostCSS warnings.** `@tailwind` directives produce warnings in Bun's built-in CSS handler. Use Vite for dev and build. Full PostCSS pipeline is a deferred decision.
- **Preact JSX namespace.** Preact requires `import type { JSX } from "preact"` for explicit JSX return types. The global `JSX` namespace is not available with `jsxImportSource`.

---

## Variants

### Variant: Use React Instead of Preact

Swap Preact for React when user requests.

| Config | Change |
|---|---|
| package.json | `react` + `react-dom` + `@types/react` + `@types/react-dom` instead of `preact` |
| tsconfig.json | `jsxImportSource: "react"` |
| vite.config.ts | `@vitejs/plugin-react` instead of `@preact/preset-vite` |
| index.html | Same structure |
| Components | `import { useState } from "react"` instead of `"preact/hooks"` |

### Variant: No Semantic Tokens

Tailwind without the token system. For simpler projects or when the user opts out.

| Aspect | Change |
|---|---|
| No src/styles/themes/ | No profile files |
| No src/styles/density/ | No profile files |
| No src/styles/typography/ | No profile files |
| No src/styles/tokens.css | No semantic mapping |
| src/styles/index.css | Tailwind directives only, no token imports |
| No validate:tokens | Remove from validate script |
| No data-* attributes on html | Not needed |
| Components | Use Tailwind utilities directly (no arbitrary var() values) |

### Variant: API Only

No UI. Server-side only. This is a significant shape change — consider using a dedicated `api-server.md` profile if the project is complex.

| Aspect | Change |
|---|---|
| No index.html | Server entry instead |
| No src/styles/ | No CSS tokens |
| No src/components/ | No UI components |
| No tailwindcss | Not needed |
| No vite | Use `bun run --hot src/server.ts` for dev |
| No validate:tokens | Remove from validate script |
| Folder model | `src/routes/`, `src/handlers/`, `src/lib/` |

#### package.json (API variant)

```json
{
  "name": "[project-name]",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "bun run --hot src/server.ts",
    "build": "bun build src/server.ts --outdir dist --target bun",
    "typecheck": "tsc --noEmit",
    "lint": "biome check src scripts",
    "lint:fix": "biome check --write src scripts",
    "test": "bun test",
    "validate": "bun run lint && bun run typecheck && bun test && bun run build"
  },
  "devDependencies": {
    "@biomejs/biome": "^1.9.4",
    "@types/bun": "^1.2.4",
    "typescript": "^5.7.3"
  }
}
```
