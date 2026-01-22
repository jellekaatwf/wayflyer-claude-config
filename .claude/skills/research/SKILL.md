---
name: research
description: Analyze transcripts or interviews as a user researcher. Extracts pain points, feature requests, objections, competitor mentions, use cases, and buying signals.
argument-hint: "[transcript URL or 'paste']"
---

# User Research Analysis

This skill invokes the User Researcher agent to analyze transcripts and interviews.

## Usage

- `/research` - Start research analysis (will prompt for input)
- `/research paste` - Analyze transcript you'll paste
- `/research <url>` - Analyze transcript from URL

## How It Works

When invoked, use the Task tool to spawn the user-researcher agent:

```
Task tool:
- subagent_type: "user-researcher"
- prompt: Include any context from the user (URL, focus area, etc.)
```

## Agent Capabilities

The user-researcher agent will:
1. Ask clarifying questions about focus area and transcript source
2. Fetch or receive the transcript
3. Identify speakers (internal vs customer/prospect)
4. Extract customer-focused insights:
   - Pain points (with direct quotes)
   - Jobs to be done / outcomes they care about
   - Unmet needs and workarounds
   - Feature requests / needs
   - Objections / concerns
   - Competitor mentions
   - Buying signals
5. Output structured research summary

## Output

The agent produces a structured research document with:
- Executive summary
- Company/participant context
- Key findings tables (pain points, needs, objections, competitors)
- Recommendations for Product, Sales, and follow-up research
- Raw quotes for reference
