---
description: Audit Claude Code configuration for best practices and issues
---

# Setup Auditor

Run a comprehensive audit of your Claude Code configuration.

## Instructions

Use the `(🔍) setup-auditor` agent to review:

1. **Skills** (`~/.claude/skills/`) - Check for proper YAML front matter, structure, and best practices
2. **Commands** (`~/.claude/commands/`) - Verify format and functionality
3. **Agents** (`~/.claude/agents/`) - Review personas, tool access, and role separation
4. **Settings** - Check permissions, plugins, and security posture

## Output

Provide:
- Executive summary with overall health assessment
- Detailed findings by component
- Critical issues that need immediate fixes
- Recommendations prioritized by impact

## When to Use

- After adding new skills, commands, or agents
- Periodic health checks (monthly)
- When something isn't working as expected
- Before sharing configuration with others
