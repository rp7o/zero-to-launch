# Instructions for AI Agents Working on This Repository

This repository contains Claude Code plugins and skills. It is designed to be usable both natively inside Claude Code **and** by any external AI agent (e.g. Antigravity, Cursor, Copilot) that has access to the file system.

## 🚀 Modes of Operation

Because this is a repository of AI skills, external agents will encounter it for one of two reasons:

### Mode A: Developing the Plugins (You are in this repo)
If your current working directory is or contains this repository (e.g. you are modifying `plugins/maker/`), you are in **Developer Mode**.
1. Read this `AGENTS.md` file first to understand architectural rules.
2. Your gateway to understanding the plugin structure is:
   ```
   plugins/{plugin_name}/README.md
   ```
   That file explains every skill, the overall workflow, the file structure, and how to navigate further.
3. Read `plugins/{plugin_name}/references/shared-protocols.md` to understand how skills wire together.
4. **Do not use the skills to generate software.** Your job is to improve the prompts, agents, and workflows.

### Mode B: Consuming the Plugins (You are in a different repo)
If this repository is cloned somewhere else on the filesystem (e.g., `~/.zero-to-launch/`), and your current working directory is a different software project (e.g., `~/my-saas-app/`), you are in **Consumer Mode**.
1. You are here to use the zero-to-launch skills to build software for the user.
2. **Stop reading this file.**
3. Go directly to `plugins/{plugin_name}/README.md` to see what tools are available to you (e.g., discover, architect, validate).
4. Execute those workflows in the context of the user's project.

---

Do not guess the structure. Follow the gateway documents.

You are an agent assisting with the development or usage of Claude Code plugins in this repository. When generating code, modifying skills, or adding agents (Developer Mode), you MUST adhere to the following architectural rules:

## 1. Internal Consistency and Routing
- **Shared Protocols:** If you add a new skill to a plugin, you MUST update `references/shared-protocols.md`. Do not let skills become orphaned.
- **Handbook Output:** Skills that generate documentation must write to the unified `handbook/` directory. Do not invent new output folders unless explicitly requested.

## 2. Plugin Structure
- **Self-Contained:** Because there is no cross-plugin dependency manager, each plugin (e.g., `plugins/maker/`) must be entirely self-contained. 
- **Agent Limits:** When creating sub-agents (e.g., `market-scout`), you MUST explicitly include rules in their prompts to bound their execution (e.g., "Limit search to top 3-4 results" and "Do not spawn sub-agents"). 

## 3. Deployment Workflow
- **Version Bumping:** If you modify a `SKILL.md` or an agent's prompt, you MUST bump the semantic version in the specific plugin's `.claude-plugin/plugin.json` (and the root `marketplace.json` if applicable).
- **Frontmatter Validation:** Every `SKILL.md` and agent file must contain valid YAML frontmatter. If you make changes, remind the user to run `bun .github/scripts/validate-frontmatter.ts`.

## 4. Product vs. Engineering Mindset
- The toolkit aggressively separates Business Definition via `shaper` (`validate`, `canvas`) from Engineering via `maker` (`architect`, `build`). Do not write prompts that mix technical solutioning into the business discovery phase.
