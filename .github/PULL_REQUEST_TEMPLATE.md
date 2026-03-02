## What does this PR do?
<!-- Describe the purpose of this PR. Are you adding a new skill, updating an agent's prompt, or fixing a bug in the documentation? -->

## Type of Change
- [ ] 🐛 Bug fix (fixes an issue with existing prompts/workflows)
- [ ] ✨ New Skill or Agent (adds new commands or agent roles)
- [ ] 🗣️ Prompt Refinement (improves the behavior or output of an existing agent)
- [ ] 📝 Documentation Update (updates README, plugins.json, or shared protocols)

## Testing the AI Behavior
<!-- Because Claude plugins rely on LLM behavior, please provide evidence that your changes work as intended. -->
- [ ] I loaded this plugin locally and successfully tested the prompt/skill changes.
- [ ] **Prompt Evidence:** Please paste a short transcript or snippet showing the agent successfully executing your new skill or prompt.

## Plugin Checklist
- [ ] I have run the frontmatter validation script (`bun .github/scripts/validate-frontmatter.ts`) and it passes.
- [ ] If I added a new skill, I have updated the `references/shared-protocols.md` to reflect how it interacts with other skills.
- [ ] I bumped the version number in `.claude-plugin/plugin.json` (and `.claude-plugin/marketplace.json` if applicable).
- [ ] My instructions follow the project's formatting rules (e.g., using GitHub-flavored markdown).
