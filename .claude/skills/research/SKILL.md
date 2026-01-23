---
name: research
description: Analyze sales call transcripts as a user researcher. Extracts pain points, feature requests, objections, competitor mentions, use cases, and buying signals from tldv transcripts stored in Notion.
---

This skill invokes the User Researcher agent to analyze sales call transcripts.

## How to Use

When the user invokes `/research`, use the Task tool to spawn the user-researcher agent:

```
Task tool:
- subagent_type: "user-researcher"
- prompt: Include any context from the user (Notion URL, focus area, etc.)
- run_in_background: true (unless user needs immediate response)
```

## Agent Capabilities

The user-researcher agent will:
1. Ask clarifying questions about focus area and transcript source
2. Fetch the transcript from Notion
3. Identify speakers (sales rep vs prospect)
4. Extract customer-focused insights (NOT call summaries):
   - Pain points (with direct quotes)
   - Jobs to be done / outcomes they care about
   - Unmet needs and workarounds
   - Feature requests / needs
   - Objections / concerns
   - Competitor mentions
   - Buying signals
5. Save research summary to `/Users/jelle.kaat/claude/wayflyer/research/`

## Invocation Examples

**With URL provided:**
```
/research https://www.notion.so/wayflyer/Meeting-Name-abc123
```
→ Spawn agent with: "Analyze this transcript: [URL]. Focus: general analysis."

**Without URL:**
```
/research
```
→ Spawn agent with: "Ask user for focus area and transcript URL, then analyze."

**With focus area:**
```
/research funding products
```
→ Spawn agent with: "Analyze transcript with focus on Funding products. Ask for URL."

## Output Location

Research summaries are saved to:
`/Users/jelle.kaat/claude/wayflyer/research/{date}-{company-name}-{topic}.md`

Example: `2026-01-21-allthegoods-banking.md`
