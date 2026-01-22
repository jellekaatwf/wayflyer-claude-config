# Claude Code Team Configuration

Shared Claude Code configuration for the team, including custom agents, skills, and commands for product management, strategy, and engineering workflows.

## Quick Start

1. **Clone this repo** into your project or home directory:
   ```bash
   git clone <repo-url> ~/.claude-team-config
   ```

2. **Copy to your project** (recommended) or symlink:
   ```bash
   # Option A: Copy to your project
   cp -r ~/.claude-team-config/.claude /path/to/your/project/
   cp ~/.claude-team-config/.mcp.json /path/to/your/project/

   # Option B: Use as your user-level config
   cp -r ~/.claude-team-config/.claude ~/.claude
   ```

3. **Set up environment variables** for MCP servers:
   ```bash
   export JIRA_URL="https://your-company.atlassian.net"
   export JIRA_USERNAME="your-email@company.com"
   export JIRA_API_TOKEN="your-api-token"
   ```

4. **Customize CLAUDE.md** for your specific project context.

5. **Run Claude Code** and trust the configuration when prompted.

## What's Included

### Agents

| Agent | Description | Use When |
|-------|-------------|----------|
| **Product Manager** | PRDs, user stories, roadmapping | Planning features, writing specs |
| **Product Strategist** | Market analysis, competitive intel, pricing | Analyzing markets, planning launches |
| **VP of Product** | Board decks, OKRs, portfolio management | Executive communications |
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
│   ├── vp-product.md
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

The MCP servers use HTTP remotes with OAuth authentication. When you first use them, Claude Code will prompt you to authenticate:

- **Atlassian**: Connects to your Atlassian account for Jira access
- **Notion**: Connects to your Notion workspace
- **Supernova**: Connects to your Supernova design system

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
