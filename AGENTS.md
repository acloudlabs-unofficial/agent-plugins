# Agent Plugins for Alibaba Cloud (Unofficial)

> [!CAUTION]
> **UNOFFICIAL PROJECT** — Not affiliated with Alibaba Cloud. Exercise caution in production environments.

> **Note:** `CLAUDE.md` is a symlink to this file. Only edit `AGENTS.md` — changes apply to both automatically.

## TL;DR Pitch

This repository supports **plugins** - bundles of skills, MCP servers, and agent configurations that extend AI coding agent capabilities for Alibaba Cloud services. The `acloudlabs-unofficial/agent-plugins` marketplace will include plugins for various Alibaba Cloud services as they are developed.

## Core Concepts

### Plugins vs Skills vs MCP Servers

| Concept         | What It Is                                                                         | Example                                       |
| --------------- | ---------------------------------------------------------------------------------- | --------------------------------------------- |
| **Plugin**      | A distributable bundle (skills + MCP servers + agents)                             | `deploy-on-alicloud`                          |
| **Skill**       | Instructions that auto-trigger based on user intent (YAML frontmatter description) | "deploy to Alibaba Cloud" triggers the skill  |
| **MCP Server**  | External tool integration via Model Context Protocol                               | Alibaba Cloud pricing or documentation server |
| **Marketplace** | Registry of plugins users can install                                              | `acloudlabs-unofficial/agent-plugins`         |

### Key Design Decision: Skills Auto-Trigger

Skills are **NOT** slash commands. The agent determines when to use a skill based on the `description` field in YAML frontmatter. If user says "host this on Alibaba Cloud", the agent matches that intent to the deploy skill's description and invokes it.

## Directory Structure

```
agent-plugins/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace registry
├── .github/
│   ├── workflows/                # CI (build, lint, security, etc.)
│   ├── ISSUE_TEMPLATE/
│   └── ...
├── docs/                         # Role-specific guides
│   ├── DESIGN_GUIDELINES.md      # Plugin design best practices
│   ├── DEVELOPMENT_GUIDE.md      # Contributor setup and workflow
│   ├── MAINTAINERS_GUIDE.md      # Reviewer/maintainer processes
│   └── TROUBLESHOOTING.md        # Plugin troubleshooting
├── plugins/
│   └── <plugin-name>/
│       ├── .claude-plugin/
│       │   └── plugin.json       # Plugin manifest
│       ├── .mcp.json             # MCP server definitions
│       └── skills/
│           └── <skill-name>/
│               ├── SKILL.md      # Main skill (auto-triggers)
│               └── references/
│                   └── ...       # Reference documents
├── schemas/                      # JSON schemas for manifests
│   ├── marketplace.schema.json
│   ├── plugin.schema.json
│   ├── mcp.schema.json
│   └── skill-frontmatter.schema.json
├── tools/                        # Lint, validation, and eval scripts
├── mise.toml                     # Tool versions and tasks
├── dprint.json
├── .markdownlint-cli2.yaml
├── .pre-commit-config.yaml
└── README.md
```

## Plugin Structure

Each plugin lives under `plugins/<plugin-name>/` and contains:

### Required Files

- **`.claude-plugin/plugin.json`** — Plugin manifest with name, description, version, keywords
- **`.mcp.json`** — MCP server definitions (can be empty `{"mcpServers": {}}`)
- **`skills/<skill-name>/SKILL.md`** — Main skill file with YAML frontmatter

### SKILL.md Frontmatter

```yaml
---
name: skill-name
description: >
  When to use this skill — this text drives auto-triggering.
  Be specific about user intents that should activate this skill.
---
```

### References

Place supporting documents in `skills/<skill-name>/references/`. Skills can reference these files for detailed guidance without bloating the main skill prompt.

## Development Workflow

1. Create a new plugin directory under `plugins/`
2. Add required manifest files
3. Write the skill with clear frontmatter description
4. Add reference documents as needed
5. Test with Claude Code or other supported agents
6. Submit a PR

## Validation

Run validation checks before submitting:

```bash
mise run lint          # Lint all files
mise run validate      # Run validation checks
mise run fmt:check     # Check formatting
```
