# Shaper

A business strategy and validation companion for AI coding agents.

The Shaper plugin operates entirely upstream of the engineering process. It helps you define, validate, and refine your business model before you write a single line of code or make any technical decisions. 

Once your idea is validated, Shaper hands off a clean, structured product brief (`handbook/product/brief.md`) so that engineering plugins (like the `maker` plugin) can take over.

## What This Skill Does

**Validates the Idea** — Don't build something nobody wants. Shaper forces you to articulate the problem, the audience, and your unique value proposition before building.

**The 9-Block Lean Canvas** — Shaper uses the Lean Canvas framework to map out your entire business model, from cost structures to revenue streams and unfair advantages.

**Market Research** — Employs a `market-researcher` subagent to actually go out to the web, find your competitors, and analyze what they do well and where they fail.

**Harsh Validation** — Employs a `canvas-critic` subagent to rip apart your Lean Canvas looking for misaligned mechanics, bad assumptions, or weak value propositions, acting as the ultimate Go/No-Go gate.

**Pitch Generation** — Once you have a "Go", Shaper can translate your dry Lean Canvas into elevator pitches, landing page copy, or pitch deck outlines.

## Commands

| Command | Purpose | When to use |
|---|---|---|
| `shaper:kickoff` | Initialize business context | You have a spark of an idea. |
| `shaper:canvas` | Build the 9-block Lean Canvas | You need to map out the business mechanics. |
| `shaper:market` | Competitive analysis | You need to see if the market is too crowded or find gaps. |
| `shaper:validate` | The Go / No-Go Gate | Reviewing the model for flaws before starting engineering. |
| `shaper:pitch` | Generate marketing materials | You need to explain the idea to an investor or customer. |
| `shaper:status` | Show current business state and progress | When you need to see where you are in the process |
| `shaper:help` | Context-aware command menu | When you're unsure what to do next. |

## Workflow Example

```
shaper:kickoff    → "I want to build an Airbnb for dogs" -> Initializes state.
shaper:canvas     → Fills out the 9 blocks (Problem, Audience, UVP, Channels, etc.)
shaper:market     → Subagent finds 3 competitors, evaluates pricing & weakness. 
shaper:validate   → Subagent critiques the model. You fix flaws. You decide "Go". -> Handoff to Maker!
shaper:pitch      → "Give me a 30-second elevator pitch for this."
```

## How It Interacts With `maker`

Shaper and Maker are completely decoupled but share a standardized handoff protocol.

1. **Shaper** owns `.shaper/business_state.md` and `handbook/business/*`.
2. When `shaper:validate` reaches a "Go" decision, it writes the core Problem, Audience, and Value Proposition to `handbook/product/brief.md`.
3. If you subsequently run `maker:discover`, Maker will detect the brief, acknowledge that Shaper has already validated the business, and jump straight into technical and feature scoping.

## Files This Skill Creates in Your Project

| File | Purpose | Created by |
|---|---|---|
| `.shaper/business_state.md` | Internal state & draft canvas | `shaper:kickoff` / `shaper:canvas` |
| `handbook/business/lean-canvas.md` | The human-readable Lean Canvas | `shaper:canvas` |
| `handbook/business/competitors.md` | Market research findings | `shaper:market` |
| `handbook/business/pitch-elevator.md` | Elevator pitch copy | `shaper:pitch` |
| `handbook/business/pitch-landing-page.md` | Landing page copy | `shaper:pitch` |
| `handbook/business/pitch-deck.md` | Pitch deck outline | `shaper:pitch` |
| `handbook/product/brief.md` | **The Handoff artifact** | `shaper:validate` |

## Repository Structure

```
shaper/
├── README.md                    ← this file
├── .claude-plugin/plugin.json   ← manifest
├── agents/                      ← subagent prompt schemas
│   ├── market-researcher.md
│   └── canvas-critic.md
├── references/
│   ├── shared-protocols.md      ← core rules & interop standards
│   └── handbook-templates.md    ← output templates for handbook files
└── skills/
    ├── kickoff/SKILL.md
    ├── canvas/SKILL.md
    ├── market/SKILL.md
    ├── validate/SKILL.md
    ├── pitch/SKILL.md
    ├── status/SKILL.md
    └── help/SKILL.md
```
