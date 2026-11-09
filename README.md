# SiteAlchemy

A pair of agent skills for analyzing a reference website and recreating its design:

1. **`design-system-extractor`** — inspects a website and generates a documented `DESIGN_SYSTEM.md`.
2. **`website-recreation`** — reads a `DESIGN_SYSTEM.md` and implements a responsive website that follows it.

These skills are designed to be reusable across projects and compatible with agent environments that support the open `SKILL.md` convention.

## Repository structure

The repository should have this structure:

```text
.
├── README.md
└── skills/
    ├── design-system-extractor/
    │   └── SKILL.md
    └── website-recreation/
        └── SKILL.md
```

If your repository currently stores the skill folders at its root, either keep that structure and adjust the copy commands, or move them into `skills/` as shown above.

## Requirements

- Git, to clone or update this repository.
- An AI coding agent that supports agent skills or project instructions.
- **Chrome DevTools MCP** for browser-based inspection and visual validation. The skills check whether it is actually available before relying on it.

### About Chrome DevTools MCP

The skills do not install or configure MCP servers automatically. Configure the Chrome DevTools MCP server in the agent environment you use, then verify that its tools are available to the agent.

If the MCP is unavailable:
- `design-system-extractor` must stop before claiming browser inspection or producing an extracted system based on guesses.
- `website-recreation` may implement from an existing `DESIGN_SYSTEM.md`, but must report that browser-based visual QA was not performed.

MCP setup differs by agent and environment. Follow the official Chrome DevTools MCP installation instructions for your setup.

---

## Install from GitHub

Clone the repository:

```bash
git clone https://github.com/najm-x/SiteAlchemy.git
cd SiteAlchemy
```

The commands below assume the repository structure shown above.

### 1. Claude Code

For project-level installation, copy the skills into `.claude/skills/`:

```bash
mkdir -p .claude/skills

cp -R skills/design-system-extractor .claude/skills/
cp -R skills/website-recreation .claude/skills/
```

For user-level installation (available across your Claude Code projects), copy them into:

```bash
mkdir -p ~/.claude/skills

cp -R skills/design-system-extractor ~/.claude/skills/
cp -R skills/website-recreation ~/.claude/skills/
```

### 2. OpenAI Codex

For project-level installation, use `.agents/skills/`:

```bash
mkdir -p .agents/skills

cp -R skills/design-system-extractor .agents/skills/
cp -R skills/website-recreation .agents/skills/
```

For user-level installation, use the Codex skills directory:

```bash
mkdir -p ~/.agents/skills

cp -R skills/design-system-extractor ~/.agents/skills/
cp -R skills/website-recreation ~/.agents/skills/
```

If your Codex setup uses a custom skills directory, install the folders there instead.

### 3. Cursor

For project-level installation, use `.cursor/skills/`:

```bash
mkdir -p .cursor/skills

cp -R skills/design-system-extractor .cursor/skills/
cp -R skills/website-recreation .cursor/skills/
```

### 4. Gemini CLI

For project-level installation, use `.gemini/skills/` if your installed Gemini CLI version supports Agent Skills:

```bash
mkdir -p .gemini/skills

cp -R skills/design-system-extractor .gemini/skills/
cp -R skills/website-recreation .gemini/skills/
```

Check your Gemini CLI version's documentation for the currently supported skill discovery path.

### 5. GitHub Copilot in VS Code

For environments that support Agent Skills, install the folders under `.github/skills/`:

```bash
mkdir -p .github/skills

cp -R skills/design-system-extractor .github/skills/
cp -R skills/website-recreation .github/skills/
```

Agent Skills support and discovery paths can depend on the VS Code and GitHub Copilot versions in use. If your version does not discover this location, consult its current documentation or use the supported project-level skill directory.

---

## Install only one skill

You can install either skill independently.

### Design System Extractor only

```bash
mkdir -p .agents/skills
cp -R skills/design-system-extractor .agents/skills/
```

### Website Recreation only

```bash
mkdir -p .agents/skills
cp -R skills/website-recreation .agents/skills/
```

The examples above use the Codex project-level directory. For another agent, use the corresponding directory from its installation section.

---

## How to use the skills

### Workflow A — Extract a design system

1. Open the website project or a suitable workspace in your agent.
2. Make sure Chrome DevTools MCP is connected and its tools are available.
3. Ask the agent to use the extractor skill:

```text
Use the design-system-extractor skill.

Analyze this website:
https://example.com

Inspect all accessible page templates, responsive layouts,
components, styles, and interactions.

Create a comprehensive DESIGN_SYSTEM.md in the current workspace.
Clearly distinguish verified values from visual estimates and unknowns.
```

Expected output:

```text
DESIGN_SYSTEM.md
```

### Workflow B — Recreate a website using the design system

1. Open the target implementation project in your agent.
2. Place the generated `DESIGN_SYSTEM.md` in the project root (or tell the agent its exact path).
3. Describe the pages and functionality you want.
4. Ask the agent to use the recreation skill:

```text
Use the website-recreation skill.

Read ./DESIGN_SYSTEM.md first.

Implement the requested website in the existing project.
Preserve the current framework and project structure.
Follow the documented design tokens, components, page layouts,
responsive behavior, and interactions.

If the reference URL is available, compare the implementation
against it using Chrome DevTools MCP.
Run the available lint, type-check, test, and build commands.
Report any assumptions and visual differences.
```

### Workflow C — Complete pipeline

After the extractor has generated `DESIGN_SYSTEM.md`, run the recreation skill in the implementation project. The second skill is intentionally separate so you can reuse the same design system for multiple projects or technology stacks.

```text
Reference website
      |
      v
design-system-extractor
      |
      v
DESIGN_SYSTEM.md
      |
      v
website-recreation
      |
      v
Implemented and tested website
```

---

## Updating the skills

To update a cloned copy of this repository:

```bash
git pull
```

Then copy the updated skill folders into the relevant agent skill directory again. If you installed them with a different method, follow that method to refresh the files.

## Troubleshooting

### The agent cannot find the skill

- Confirm the folder name matches the skill name.
- Confirm `SKILL.md` is directly inside the skill folder.
- Confirm you installed it in a skill discovery path supported by your agent version.
- Restart or reload the agent session if its documentation recommends doing so.

Correct:

```text
.agents/
└── skills/
    └── design-system-extractor/
        └── SKILL.md
```

Incorrect:

```text
.agents/
└── skills/
    └── design-system-extractor/
        └── design-system-extractor/
            └── SKILL.md
```

### Chrome DevTools MCP is not available

The skill cannot make an MCP server appear simply by being installed. Configure the MCP server in your agent's MCP settings, restart/reload the environment if required, and verify that its browser tools are exposed.

### The extractor cannot inspect every page

Some pages may require authentication, be blocked, fail to load, or be outside the supplied scope. The skill should list these limitations rather than pretending that every route was inspected.

---

## Notes

- These skills document and reproduce observable design behavior; they do not guarantee pixel-perfect duplication.
- Use only assets and branding that you own or are authorized to reuse.
- The extractor distinguishes `Verified`, `Observed`, `Estimated`, and `Unknown` evidence.
- The recreation skill should preserve the target project's existing framework and conventions unless you explicitly request otherwise.
- Agent support and skill discovery paths may change. If an agent does not recognize a path listed here, use that agent's current official documentation.

## License

This project is licensed under the MIT License.
See the [LICENSE](LICENSE) file for details.