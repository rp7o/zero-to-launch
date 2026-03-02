# Contributing to Zero to Launch

First off, thank you for considering contributing to Zero to Launch!

Because these plugins run inside Claude Code, they rely heavily on prompt engineering, structured output, and strict agent boundaries. We want to ensure that any contributions maintain a high standard of reliability.

## Repository Structure

- `plugins/`: Contains all the individual plugins.
  - `plugins/{plugin_name}/`: A specific plugin (e.g., `maker`, `shaper`).
    - `agents/`: System prompts for specialized sub-agents (e.g., `tech-scout`, `market-researcher`).
    - `skills/`: The actual commands exposed to the user (e.g., `discover`, `canvas`).
    - `references/`: Shared templates and routing protocols specific to that plugin.

## How to Contribute

1. **Fork and Clone**: Fork this repository and clone it locally.
2. **Branch**: Create a new branch for your feature or fix (`git checkout -b feature/amazing-skill`).
3. **Develop**:
   - If you are adding a new skill to a plugin, make sure you update its `references/shared-protocols.md` to explain how it interacts with other skills.
   - If modifying an agent, ensure you maintain the structured output formats.
4. **Validating Frontmatter**: 
   All `SKILL.md` and agent files use YAML frontmatter that Claude relies on. You *must* validate this before opening a PR.
   
   Ensure you have [Bun](https://bun.sh/) installed, then run:
   ```bash
   bun i yaml
   bun .github/scripts/validate-frontmatter.ts
   ```
5. **Bump Version**: 
   If your change modifies plugin behavior, you must bump the version number in `plugins/{plugin_name}/.claude-plugin/plugin.json` (and `.claude-plugin/marketplace.json` if applicable). Our CI workflow will block PRs that have modified files but no version bump.
6. **Test Locally**:
   Load your modified plugin into a local Claude Code project to verify the AI behaves properly.
7. **Commit and Push**: Commit your changes and push to your fork.
8. **Open a PR**: Fill out the Pull Request template completely, including evidence of the prompt working.

## Adding a New Skill

When adding a new command (e.g., `/{plugin_name}:deploy`):
1. Create `plugins/{plugin_name}/skills/deploy/SKILL.md`.
2. Provide a clear `name`, `description`, and `when_to_use` in the frontmatter.
3. Explicitly tell the AI what to read and what to write.
4. Update `plugins/{plugin_name}/references/shared-protocols.md` to include your new skill in the routing table.
