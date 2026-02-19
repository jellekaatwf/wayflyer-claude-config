---
description: Scan your AI setup (Claude Code, Cursor, other tools) and sync discovered use cases to the team's AI Use Cases database in Notion
argument-hint: [claude-code|cursor|all] defaults to auto-detect
---

# AI Setup Sync

You are scanning the current user's AI tool configuration and syncing discovered use cases to the **AI Use Cases** Notion database.

## Configuration

- **Notion Database Data Source**: `collection://a35171b0-db88-42a1-b2de-c5f58a85e753`
- **Database ID**: `ee036114a1074f268cc965b993b8f70a`

## Arguments

The user can optionally specify: `$ARGUMENTS`

- If empty: auto-detect which AI tools are installed
- If "claude-code": only scan Claude Code config
- If "cursor": only scan Cursor config
- If "all": scan everything

## Workflow Phase Categories

Every use case must be classified into exactly one of these 6 categories (these match the PM initiative lifecycle on the AI-Native page):

| Category | What belongs here | Example keywords |
|---|---|---|
| **Discovery & Research** | Customer research, market analysis, competitor intelligence, transcript analysis, tool assessments | research, transcript, customer, competitor, market, interview, discovery, assess, survey |
| **Strategy & Roadmapping** | Strategic planning, OKRs, prioritisation, positioning, GTM planning, framework analysis | strategy, roadmap, OKR, planning, prioritisation, framework, SWOT, positioning, GTM |
| **Specification & Design** | PRDs, specs, user stories, prototyping, design reviews, wireframes | PRD, spec, design, prototype, wireframe, user story, requirements, acceptance criteria |
| **Delivery & Execution** | Sprint management, kanban, ticket review, deployment, QA, daily planning, code review | sprint, kanban, ticket, jira, delivery, deploy, standup, backlog, code, build, daily |
| **Analytics & Measurement** | Dashboards, metrics, experiment design, data queries, product analytics | metrics, analytics, dashboard, experiment, data, posthog, measurement, query |
| **Communication & Alignment** | Status updates, exec reports, board decks, retros, meeting summaries, enablement materials | communication, update, report, deck, presentation, enablement, retro, summary, email |

If a use case is ambiguous, ask the user which category fits best using AskUserQuestion.

## Sync Process

### Step 1: Identify the Current User

Use `mcp__notion__notion-search` with `query_type: "user"` to search for the current user by their name or email. Store their Notion user ID (format: `user://UUID`) for:
- Setting the "Submitted by" field
- Deduplication queries

If you cannot determine who the user is, use AskUserQuestion to ask for their name.

### Step 2: Auto-detect Environment

Check for the existence of AI tool configurations. Use Glob and Read tools to scan:

**Claude Code** (check these paths):
- `~/.claude/skills/*/SKILL.md` (skills with YAML front matter)
- `~/.claude/commands/*.md` (commands with YAML front matter)
- `~/.claude/settings.json` (MCP servers, plugins)
- `~/.claude/settings.local.json` (local MCP servers)
- `~/.claude/CLAUDE.md` (global instructions, look for workflow-specific sections)
- Project-level `.claude/` directory if running inside a project

**Cursor** (check these paths):
- `.cursorrules` in the current directory or home directory
- `.cursor/rules/*.md` in the current directory
- `~/.cursor/rules/*.md` globally

Report what you found:
```
Detected: Claude Code setup
- X skills found
- Y commands found
- Z MCP servers configured
- N plugins enabled
```

**If nothing or very little is found**, use AskUserQuestion to ask:
1. "I couldn't find much AI configuration on your machine. Which AI tools do you primarily use for your PM work?" (options: Claude Code, Cursor, ChatGPT, Copilot, Other)
2. "Where do you keep your AI-related config or rules files? Are they in a different directory or project?"
3. "Do you use AI for your PM work at all currently? If so, describe 2-3 of your most common AI workflows."

This ensures the command doesn't silently produce empty results for PMs who have non-standard setups or primarily use browser-based tools.

### Step 3: Scan Claude Code Configuration

For each discovered component, extract a use case entry:

**From skills (`~/.claude/skills/*/SKILL.md`):**
- Read the YAML front matter for `name` and `description`
- Use Case title = `/{name} {brief description from first line of description}`
- Classify into one of the 6 categories using the keyword table above
- Tool(s) used = `["Claude Code"]`

**From commands (`~/.claude/commands/*.md`):**
- Read the YAML front matter for `description`
- Use Case title = `/{filename-without-extension} {description}`
- Classify into one of the 6 categories
- Tool(s) used = `["Claude Code"]`
- **Skip this command itself** (`sync-ai-setup.md`) and `ai-native-eval.md` from the scan

**From settings (MCP servers):**
- Extract MCP server names from `mcpServers` keys in settings files
- Do NOT create individual use case entries for MCP servers
- Instead, note them as context (e.g., "Jira MCP connected" informs that delivery/execution workflows are possible)

**From plugins:**
- Extract `enabledPlugins` from settings.json
- Note as context, not as individual entries

### Step 4: Scan Cursor Configuration

For Cursor users:
- Read `.cursorrules` or `.cursor/rules/*.md` files
- Parse for distinct workflow descriptions (rules that describe specific PM workflows)
- Each distinct workflow block becomes a potential use case
- Tool(s) used = `["Cursor"]`
- Classify each into one of the 6 categories

### Step 5: Interactive Questionnaire

After the config scan, use AskUserQuestion to fill gaps and validate findings.

**5a. Validate discovered use cases**

Present all discovered use cases in a table and ask:

"I found these AI workflows in your configuration. Please confirm they're correct, or tell me about any changes:"

| # | Use Case | Category | Tool |
|---|---|---|---|
| 1 | /prd PRD generation | Specification & Design | Claude Code |
| ... | ... | ... | ... |

"Are these correct? Any to remove or recategorize?"

**5b. Check for other AI tools**

Use AskUserQuestion (multiSelect: true):
"Beyond what I found in your config, do you use any other AI tools for PM work?"
- ChatGPT (web/app)
- Figma AI features
- Copilot (GitHub or Microsoft)
- Midjourney or other image generation
- None of the above

For each selected tool, ask a follow-up: "What do you use {tool} for? Which workflow phase does it cover?"

**5c. Fill coverage gaps**

For each of the 6 workflow phases where NO use case was found from the scan:

"I didn't find any AI workflows for **{Category}**. Do you use AI for any of these activities?"

Then list 2-3 example activities for that category. For example:
- Discovery & Research: "Customer call analysis, competitor monitoring, market research"
- Analytics & Measurement: "Dashboard creation, data queries, experiment design"

**5d. Frequency and time saved**

For each confirmed use case, ask:

"For each workflow, estimate how often you use it and time saved per week:"

Present options:
- Frequency: Daily / Weekly / Bi-weekly / Monthly / Ad-hoc
- Time saved: Minutes (<15m) / Moderate (15-60m) / Hours (1-4h) / Significant (4h+)

Batch this into a single question if possible (ask the user to review a table and provide corrections rather than asking 15 individual questions).

### Step 6: Deduplicate Against Existing Entries

Query the Notion database for entries already submitted by this user:

```sql
SELECT url, "Use Case", "Category" FROM "collection://a35171b0-db88-42a1-b2de-c5f58a85e753"
WHERE "Submitted by" LIKE '%{user_id}%'
```

For each discovered use case:
1. Check if a similar title already exists (fuzzy match; e.g., "/prd PRD generation" matches "/prd PRD generation agent")
2. If a match is found, skip it (don't create a duplicate)
3. If uncertain, ask the user: "This looks similar to an existing entry: '{existing title}'. Skip or create as new?"

### Step 7: Create New Entries in Notion

For each new (non-duplicate) use case, batch create using `mcp__notion__notion-create-pages`:

```json
{
  "parent": {"data_source_id": "a35171b0-db88-42a1-b2de-c5f58a85e753"},
  "pages": [{
    "properties": {
      "Use Case": "{title}",
      "Category": "{one of the 6 categories}",
      "Tool(s) used": "[\"Claude Code\"]",
      "Role": "[\"PM\"]",
      "Frequency": "{frequency}",
      "Time saved per week": "{time saved}",
      "Submitted by": "[\"user://{user_notion_id}\"]"
    }
  }]
}
```

Create all new entries in a single `create-pages` call if possible.

### Step 8: Output Summary

```
## AI Setup Sync Complete

**User:** {name}
**Environment:** {what was detected}

### New Use Cases Added: {count}
| Use Case | Category | Frequency | Time Saved |
|---|---|---|---|
| ... | ... | ... | ... |

### Skipped (already in database): {count}
| Use Case | Matched Existing Entry |
|---|---|
| ... | ... |

### Workflow Phase Coverage
| Phase | # Use Cases | Status |
|---|---|---|
| Discovery & Research | {n} | {Covered or GAP} |
| Strategy & Roadmapping | {n} | {Covered or GAP} |
| Specification & Design | {n} | {Covered or GAP} |
| Delivery & Execution | {n} | {Covered or GAP} |
| Analytics & Measurement | {n} | {Covered or GAP} |
| Communication & Alignment | {n} | {Covered or GAP} |

{If there are gaps, suggest specific tools or workflows the PM could adopt for those phases.}
```

## Important Rules

1. **Always ask before pushing.** Show the user the complete list of what will be created and get confirmation before calling `create-pages`.
2. **Skip meta-commands.** Do not create entries for `sync-ai-setup` or `ai-native-eval` themselves.
3. **One entry per workflow.** Don't split a single skill/command into multiple entries. Each skill, command, or distinct workflow = one entry.
4. **Respect the 6 categories.** Every entry must map to exactly one of the 6 workflow phase categories. No other values are allowed.
5. **Be honest about gaps.** If a PM has no AI usage in a phase, that's useful data. Don't manufacture entries.
6. **Batch the questionnaire.** Don't ask 20 individual questions. Group them logically and present summary tables for review.

## Error Handling

- If Notion API fails, report the error and stop
- If no AI tools are detected and the user confirms they don't use AI for PM work, create no entries and report that
- If the user cancels at any point, stop gracefully and report what was done
