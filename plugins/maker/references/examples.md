# Worked Examples

Examples of each command's output. Use these to calibrate quality and format.

> These examples use the **frontend-spa** stack profile (Bun + Preact + Vite + Biome). The commands and file names shown are specific to that stack. Adapt to the project's actual stack decisions.

---

## Example: kickoff Session

User says: "I want to build a thing that shows random quotes when you click."

```
Agent: What's the project called? (working name is fine)
User: hello-click

Agent: How do you like to work with me?
- Thorough: I ask lots of questions, discover deeply before acting.
- Balanced: Some questions, then I move. Check in at key points.
- Fast: Minimal questions. I make reasonable defaults and go.
User: Balanced

Agent: Anything else I should know upfront? Hard constraints, deadlines, things to avoid?
User: A single page with hello world in it, clicking the word fetches a random quote. Also, use a free API, no keys needed.
```

Output:
```markdown
## Project Initialized

- **Name**: hello-click
- **Type**: Frontend app
- **Working style**: Balanced

### Suggested Next Step

→ `maker:discover` — Let's flesh out what you're building before making any technical decisions.
→ `maker:architect` — If you already know what you want, we can jump to structure.
```

---

## Example: discover Session (Balanced Mode)

Agent groups questions into small batches:

```
Agent: Who uses this, and what problem does it solve? And what must work for v1?
User: It's a personal demo project. v1 just needs to show "Hello World,"
      fetch a quote on click, and change the visual design randomly.

Agent: Got it. Any hard constraints? And is there anything explicitly NOT in v1?
User: Must be fully client-side. No auth, no server. Free quote API, no key.
      No saving quotes, no animations.

Agent: Do you have any visual references or a feel in mind?
User: No fixed design. The randomness IS the design — playful, different every click.

Agent: Based on our conversation, I'm deferring these:
- Specific quote API choice (will confirm during architecture — CORS must work)
- Transition animations (you said not in v1)
- Accessibility contrast checks (random colors may produce low contrast)
Any of these feel wrong to defer?

User: No, that's fine.
```

Output: handbook/product/ written with brief, plan, design, deferred.

---

## Example: architect Plan Presentation

```markdown
## Architecture Plan

### Project Shape
Frontend app — single page with interactive UI, no server component.

### Stack
| Component | Choice | Rationale |
|---|---|---|
| Runtime | Bun | Default, fast, TypeScript-native |
| UI | Preact | Lightweight, sufficient for single page |
| CSS | Tailwind + semantic tokens | Token system enables runtime design changes |
| Dev server | Vite + @preact/preset-vite | Standard Preact dev server. Bun --hot doesn't support browser apps. |
| Linter | Biome | Fast, zero-config, handles formatting too |
| Testing | Bun test | Built-in, no extra dependency |

### Folder Model
```
/
├── index.html
├── src/
│   ├── app/main.tsx
│   ├── components/HelloWorld.tsx
│   ├── lib/
│   │   ├── design.ts          ← random design generator
│   │   └── quotes.ts          ← quote API client
│   └── styles/                ← token system
├── scripts/validate-tokens.ts
└── handbook/
```

### Dependency Direction
app → components → lib. Lib must not import components or app.

### Quality Gates (ordered)
1. `bun run validate:tokens`
2. `bun run lint`
3. `bun run typecheck`
4. `bun test`
5. `bun run build`

### Technical Deferrals
- PostCSS pipeline (Tailwind @tailwind directives handled by Vite, not Bun)
- Tests (placeholder only for v1)
- Accessibility contrast checking

### Trade-offs
- Vite over Bun's built-in bundler: Bun build works for production but can't serve browser apps for development. Vite adds a dependency but solves this cleanly.
- Semantic tokens over plain Tailwind: slight complexity cost, but enables the runtime design randomization which is the core feature.

Does this plan look right? Any changes before I record it?
```

---

## Example: build Confirmation

```markdown
## Build Plan

### Config Files (6)
- package.json, tsconfig.json, biome.json, .gitignore, tailwind.config.ts, vite.config.ts

### Source Files (4)
- index.html, src/app/main.tsx, src/components/HelloWorld.tsx, src/lib/design.ts, src/lib/quotes.ts

### CSS Token System (5)
- themes/theme-light.css, density/density-default.css, typography/typography-default.css, tokens.css, index.css

### Scripts (1)
- scripts/validate-tokens.ts

### Agent Files (5)
- LLM.md, AGENTS.md, src/LLM.md, src/styles/LLM.md, scripts/LLM.md

### Tests (1)
- src/lib/quotes.test.ts (placeholder)

**Total: 22 files**

Ready to generate these files? Any changes?
```

---

## Example: Iteration (Re-running architect)

User returns and says: "Actually, use React instead of Preact."

```
Agent: Current architecture uses Preact. Switching to React changes:

1. **package.json**: preact → react + react-dom + @types/react + @types/react-dom
2. **tsconfig.json**: jsxImportSource → "react"
3. **vite.config.ts**: @preact/preset-vite → @vitejs/plugin-react
4. **Components**: import from "react" instead of "preact/hooks"

Decision Log update:
- Old: "Use Preact for UI" → Superseded
- New: "Use React for UI — user preference"

This makes the build output stale. You'll want to re-run `maker:build` after confirming.

Does this plan look right?
```

---

## Example: status Output

```markdown
## Project Status: hello-click

### Profile
- **Type**: Frontend app
- **Stack**: Bun + TypeScript + Preact + Tailwind + tokens
- **Working style**: Balanced

### Phase Progress
| Phase | Status | Last Updated |
|-------|--------|-------------|
| Product context | Complete | 2026-02-26 |
| Architecture | Implemented | 2026-02-26 |
| Implementation | Complete | 2026-02-26 |
| Quality gates | Passing | 2026-02-26 |

### Recent Decisions
- 2026-02-26: Use Vite for dev server (Bun --hot doesn't support browser apps)
- 2026-02-26: Use dummyjson.com/quotes/random (free, no key, CORS-friendly)
- 2026-02-26: Placeholder test only for v1

### Deferred
3 deferred decisions. PostCSS pipeline, real tests, accessibility contrast.

### Suggested Next Steps
→ `maker:discover` — Add a new feature.
```
