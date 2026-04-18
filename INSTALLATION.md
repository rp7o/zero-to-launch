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

## Codex App

Codex uses a local marketplace file plus per-plugin manifests.

This repository now includes a repo-scoped Codex marketplace at:

```text
.agents/plugins/marketplace.json
```

and plugin manifests at:

```text
plugins/maker/.codex-plugin/plugin.json
plugins/shaper/.codex-plugin/plugin.json
```

### Option 1: Use this repo directly

Clone the repo somewhere stable:

```bash
git clone https://github.com/rp7o/zero-to-launch.git ~/.zero-to-launch
```

Open that folder as a workspace in the Codex app, then restart Codex so it reloads local marketplaces.

After restart:

1. Open `Plugins` in Codex.
2. Choose the `Zero to Launch` marketplace.
3. Install `maker` and/or `shaper`.

Codex discovers repo-local marketplaces from:

```text
$REPO_ROOT/.agents/plugins/marketplace.json
```

and expects each plugin to expose:

```text
$REPO_ROOT/plugins/<plugin-name>/.codex-plugin/plugin.json
```

### Option 2: Install as a personal Codex marketplace

If you want the plugins available across projects, copy the repo or plugin folders into a stable home directory and create a personal marketplace:

```bash
git clone https://github.com/rp7o/zero-to-launch.git ~/.zero-to-launch
mkdir -p ~/.agents/plugins
mkdir -p ~/.codex/plugins
cp -R ~/.zero-to-launch/plugins/maker ~/.codex/plugins/maker
cp -R ~/.zero-to-launch/plugins/shaper ~/.codex/plugins/shaper
```

Then create `~/.agents/plugins/marketplace.json`:

```json
{
  "name": "zero-to-launch",
  "interface": {
    "displayName": "Zero to Launch"
  },
  "plugins": [
    {
      "name": "maker",
      "source": {
        "source": "local",
        "path": "./.codex/plugins/maker"
      },
      "policy": {
        "installation": "AVAILABLE",
        "authentication": "ON_INSTALL"
      },
      "category": "Productivity"
    },
    {
      "name": "shaper",
      "source": {
        "source": "local",
        "path": "./.codex/plugins/shaper"
      },
      "policy": {
        "installation": "AVAILABLE",
        "authentication": "ON_INSTALL"
      },
      "category": "Productivity"
    }
  ]
}
```

Restart Codex, open `Plugins`, select the `Zero to Launch` marketplace, and install the plugins you want.

### Notes

- Codex resolves `source.path` relative to the marketplace root, not relative to `.agents/plugins/`.
- After changing a local plugin, restart Codex so the updated files are picked up.
- Codex installs plugins into its cache after you install them from the marketplace.

Official Codex plugin docs:

- [Plugins](https://developers.openai.com/codex/plugins)
- [Build plugins](https://developers.openai.com/codex/plugins/build)

---

## Other Agents (Gemini, Cursor, Copilot, etc.)

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
