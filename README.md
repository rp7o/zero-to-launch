# Zero to Launch

A collection of AI plugins and skills designed to turn Claude Code into a specialized product and engineering team.

## Included Plugins

### 🏗️ Maker (`/plugins/maker`)
The Maker plugin is a comprehensive Software Development Life Cycle (SDLC) companion. It gives Claude the ability to act as an AI Product Manager, Software Architect, and Senior Engineer.

- **`/maker:discover`**: Interactive product definition (JTBD, Lean Canvas, MVP scoping).
- **`/maker:architect`**: System design and stack selection.
- **`/maker:design`**: UX/UI pattern definition.
- **`/maker:build`**: Autonomous execution of the architecture plan.
- **`/maker:research`**: Market and technical research.

[Read the full Maker documentation](./plugins/maker/README.md)

### 💡 Shaper (`/plugins/shaper`)
The Shaper plugin is a business strategy and validation companion. It operates entirely upstream of engineering, helping you validate your idea before writing code.

- **`/shaper:kickoff`**: Initialize business context.
- **`/shaper:canvas`**: Build the 9-block Lean Canvas.
- **`/shaper:market`**: Competitive analysis via subagents.
- **`/shaper:validate`**: The Go / No-Go Gate.
- **`/shaper:pitch`**: Generate marketing materials.

[Read the full Shaper documentation](./plugins/shaper/README.md)

## Installation

See [INSTALLATION.md](./INSTALLATION.md) for setup instructions — Claude Code, Gemini, Codex, Cursor, and multi-agent projects.

## Contributing

We welcome contributions! Whether you are improving an agent's prompt, fixing validation scripts, or adding entirely new skills, please read our [Contributing Guide](CONTRIBUTING.md) before opening a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
