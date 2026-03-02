# Installation

## Claude Code

Install this repository as a plugin directly into Claude Code:

```bash
claude plugin install https://github.com/rp7o/zero-to-launch.git
```

Or clone manually and point Claude Code at the directory:

```bash
git clone https://github.com/rp7o/zero-to-launch.git ~/.zero-to-launch
claude plugin install ~/.zero-to-launch
```

Once installed, skills are available as slash commands: `/maker:kickoff`, `/shaper:kickoff`, `/maker:build`, etc.

---

## Other Agents (Gemini, Codex, Cursor, Copilot, etc.)

Clone the repo to a stable path:

```bash
git clone https://github.com/rp7o/zero-to-launch.git ~/.zero-to-launch
```

Then add the following snippet to your agent instruction file (`GEMINI.md`, `AGENTS.md`, `.cursorrules`, `COPILOT-instructions.md`, etc.):

```markdown
## Agentic Toolkit

This project uses the zero-to-launch for business validation and engineering workflows.
The toolkit is installed at: `~/.zero-to-launch` *(update this path to match your installation)*

To use it:
- Read `~/.zero-to-launch/plugins/shaper/README.md` for business validation workflows (shaper).
- Read `~/.zero-to-launch/plugins/maker/README.md` for engineering workflows (maker).
- Execute those workflows in the context of THIS project's directory.
- Do not modify the toolkit files. Treat them as read-only skill definitions.

Context for this project lives in:
- `handbook/` — product, architecture, design, and engineering decisions
- `.maker/project_state.md` — current engineering state and decision log
- `.shaper/business_state.md` — upstream business validation context
```

---

## Multi-Agent Projects

When Claude Code, Gemini, Codex, Cursor, and others all work on the same codebase:

- The `AGENTS.md` file written by `maker:kickoff` becomes the coordination layer. Each agent reads it on startup and uses it to understand the project.
- All agents point to the same `handbook/`, `.maker/`, and `.shaper/` state — one source of truth regardless of which agent is active.
- The toolkit itself is read-only shared infrastructure. No agent modifies the plugin files under `~/.zero-to-launch`.

**Tip:** Keep your agent instruction file (`GEMINI.md`, `.cursorrules`, etc.) minimal — just point to the toolkit path and the context folders. The toolkit READMEs handle the rest.
