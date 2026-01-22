# Claude Code Team Configuration

Shared Claude Code configuration for the team, including custom agents, skills, and commands for product management, strategy, and engineering workflows.

## Quick Start

1. **Clone this repo**:
   ```bash
   git clone <repo-url> ~/wayflyer-claude-config
   ```

2. **Set up global MCP servers** (work across ALL projects):
   ```bash
   # Copy the MCP config to your global settings
   # First, backup existing settings if any
   cp ~/.claude/settings.json ~/.claude/settings.json.bak 2>/dev/null || true

   # Merge MCP servers into your global settings
   # Or manually copy the mcpServers block from global-settings.template.json
   # into ~/.claude/settings.json
   ```

3. **Copy agents and skills** to your home directory:
   ```bash
   cp -r ~/wayflyer-claude-config/.claude/agents ~/.claude/
   cp -r ~/wayflyer-claude-config/.claude/skills ~/.claude/
   ```

4. **Set up environment variables** for Jira:
   ```bash
   # Add to your ~/.zshrc or ~/.bashrc
   export JIRA_API_TOKEN="your-api-token"
   ```

5. **Restart Claude Code** to pick up the new configuration.

## Configuration Levels

| Level | File | Scope |
|-------|------|-------|
| **Global** | `~/.claude/settings.json` | All projects |
| **Project** | `.mcp.json` in project root | Single project only |
| **Project** | `.claude/settings.json` in project | Single project only |

### Global MCP Setup (Recommended)

For MCPs that should work everywhere, add them to `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "notion": {
      "type": "http",
      "url": "https://mcp.notion.com/mcp"
    },
    "supernova": {
      "type": "http",
      "url": "https://mcp.supernova.io/mcp/ds/575774/dataset/613448"
    },
    "mcp-atlassian": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://wayflyer.atlassian.net",
        "JIRA_USERNAME": "your-email@wayflyer.com",
        "JIRA_API_TOKEN": "${JIRA_API_TOKEN}"
      }
    }
  }
}
```

See `global-settings.template.json` for a complete template.

### Project-Level MCP Setup

For project-specific MCPs, use `.mcp.json` in the project root. This is useful for:
- MCPs that only apply to certain projects
- Sharing MCP config with team via git

## What's Included

### Agents

| Agent | Description | Use When |
|-------|-------------|----------|
| **Product Manager** | PRDs, user stories, roadmapping | Planning features, writing specs |
| **Product Strategist** | Market analysis, competitive intel, pricing | Analyzing markets, planning launches |
| **User Researcher** | Transcript analysis, insight extraction | Synthesizing qualitative research |
| **Designer** | UI prototypes, component implementation | Creating interfaces |
| **Design Reviewer** | Design critique, accessibility audit | Reviewing designs |
| **Setup Auditor** | Configuration review | Validating .claude setup |

### Skills (Slash Commands)

| Skill | Description |
|-------|-------------|
| `/prd` | Generate a Product Requirements Document |
| `/gtm-plan` | Create a go-to-market plan |
| `/competitive-analysis` | Analyze competitors |
| `/board-deck` | Create executive board deck |
| `/sprint-check` | Comprehensive sprint analysis |
| `/sprint-health` | Quick sprint health check |
| `/kanban-check` | Kanban board flow analysis |
| `/jirareview <TICKET>` | Review a Jira ticket |
| `/research` | Analyze transcripts/interviews |

### MCP Integrations

- **Atlassian**: Jira sprint management, ticket operations, board analysis
- **GitHub**: Pull requests, issues, repository operations
- **Notion**: Documentation, knowledge base, databases
- **Supernova**: Design system tokens, components, documentation

## Configuration Files

```
.claude/
├── settings.json       # Permissions and hooks
├── CLAUDE.md          # Project memory (customize this!)
├── agents/            # Custom agent definitions
│   ├── product-manager.md
│   ├── product-strategist.md
│   ├── user-researcher.md
│   ├── designer.md
│   ├── design-reviewer.md
│   └── setup-auditor.md
├── skills/            # Slash command skills
│   ├── prd/
│   ├── gtm-plan/
│   ├── competitive-analysis/
│   ├── board-deck/
│   ├── sprint-check/
│   ├── sprint-health/
│   ├── kanban-check/
│   ├── jirareview/
│   └── research/
└── commands/          # (empty - add your own)

.mcp.json              # MCP server configuration
```

## Customization

### Personal Overrides

Create `.claude/settings.local.json` for personal settings that won't be committed:

```json
{
  "model": "claude-sonnet-4-20250514"
}
```

Create `CLAUDE.local.md` for personal project notes.

### Adding New Agents

1. Create a new file in `.claude/agents/your-agent.md`
2. Include YAML front matter:
   ```yaml
   ---
   name: "Agent Name"
   description: "When to use this agent"
   color: "blue"
   model: "sonnet"
   tools: ["Read", "Write", "Edit", "Grep", "Glob"]
   ---
   ```
3. Add agent instructions below the front matter

### Adding New Skills

1. Create `.claude/skills/your-skill/SKILL.md`
2. Include YAML front matter:
   ```yaml
   ---
   name: your-skill
   description: What this skill does
   argument-hint: "[optional arguments]"
   ---
   ```
3. Add skill instructions below

## MCP Authentication

### HTTP MCPs (OAuth)

These use browser-based OAuth - Claude Code will prompt you to authenticate:

- **Notion**: Connects to your Notion workspace
- **Supernova**: Connects to your Supernova design system
- **GitHub** (optional): Connects to your GitHub account

### Local MCPs (API Token)

These run locally and use environment variables:

- **mcp-atlassian**: Requires `JIRA_API_TOKEN` environment variable
  ```bash
  # Get your token from: https://id.atlassian.com/manage-profile/security/api-tokens
  export JIRA_API_TOKEN="your-token-here"
  ```

## Setup Auditor Hook

The configuration includes a hook that notifies you when `.claude/` files are modified, reminding you to run the setup auditor. To run a full audit:

```
Ask Claude: "Run the setup-auditor agent to review my configuration"
```

## Contributing

1. Make changes to agents, skills, or settings
2. Test your changes locally
3. Run the setup auditor to validate
4. Submit a PR with description of changes

## Troubleshooting

### MCP server not connecting
- Verify environment variables are set
- Check that `uvx` is installed (`pip install uvx`)
- Try running `uvx mcp-atlassian` directly to test

### Agent not appearing
- Ensure front matter is valid YAML
- Check that the file is in `.claude/agents/`
- Restart Claude Code

### Skill not working
- Verify file is at `.claude/skills/<name>/SKILL.md`
- Check YAML front matter syntax
- Ensure `name` matches the directory name
