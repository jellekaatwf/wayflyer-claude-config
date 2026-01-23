---
description: Interactively create a comprehensive CLAUDE.md file for your project
---

# Create CLAUDE.md

You are helping the user create a comprehensive `CLAUDE.md` file for their project. This file serves as project memory - it tells Claude about the codebase, team conventions, and how to work effectively in this project.

## Your Approach

Guide the user through a series of targeted questions, one section at a time. After each response, summarize what you learned and move to the next section. Be conversational and adapt your questions based on their answers.

## Step 1: Project Overview

Start by understanding the basics. Ask:

1. **What does this project do?** (Brief description of the product/service)
2. **What's the tech stack?** (Languages, frameworks, databases, infrastructure)
3. **Who are the target users?** (Internal tool, B2B, B2C, developers, etc.)

## Step 2: Team Context

Understand the team dynamics:

1. **What team owns this project?** (Team name, size)
2. **What are the communication channels?** (Slack channels, meeting cadence)
3. **Are there key stakeholders or subject matter experts?**

## Step 3: Architecture

Get the technical lay of the land:

1. **What are the main components/services?** (Monolith, microservices, frontend/backend split)
2. **How do they communicate?** (REST, GraphQL, message queues, etc.)
3. **What external services/APIs does it integrate with?**
4. **Where is it deployed?** (AWS, GCP, Vercel, etc.)

## Step 4: Coding Standards

Understand how the team writes code:

1. **What style guides or linters do you use?** (Prettier, ESLint, Black, etc.)
2. **What testing frameworks and coverage expectations?**
3. **What are the PR/code review conventions?**
4. **Any specific patterns or conventions unique to this project?**

## Step 5: Common Workflows

Learn the day-to-day:

1. **How do you run the project locally?** (Key commands)
2. **How do you run tests?**
3. **How does deployment work?** (CI/CD, manual steps)
4. **Any other common commands developers use frequently?**

## Step 6: External Integrations

Understand the tooling ecosystem:

1. **What project management tool?** (Jira, Linear, GitHub Issues)
2. **Where is documentation stored?** (Notion, Confluence, README files)
3. **Any design tools?** (Figma, Storybook)
4. **Other tools the team uses regularly?**

## Step 7: Team Preferences (Optional)

Any Claude-specific preferences:

1. **Are there templates Claude should follow?** (PRD templates, ticket formats)
2. **Any patterns Claude should prefer or avoid?**
3. **Specific terminology or naming conventions?**

## Output

After gathering all information, generate a complete `CLAUDE.md` file in this format:

```markdown
# Project Memory

## About This Project

[Description from Step 1]

## Team Context

- **Team**: [Team name]
- **Tech Stack**: [Languages, frameworks, tools]
- **Communication**: [Slack channels, meeting cadence]

## Architecture Overview

[Description from Step 3]

## Coding Standards

- **Language**: [e.g., TypeScript strict mode]
- **Style**: [e.g., Prettier, ESLint config]
- **Testing**: [e.g., Jest, minimum coverage requirements]
- **Documentation**: [e.g., JSDoc, inline comments policy]

## Common Commands

```bash
# Build
[command]

# Test
[command]

# Start dev server
[command]
```

## Key Directories

```
[Directory structure based on what you learned]
```

## External Integrations

- **Issue Tracking**: [Tool]
- **Documentation**: [Tool]
- **Design System**: [Tool if any]
- **CI/CD**: [Tool]

## Team Preferences

[Any Claude-specific guidance from Step 7]

---

*Last updated: [today's date]*
```

## Instructions

1. Start with a friendly greeting and explain what you're about to do
2. Ask questions one section at a time - don't overwhelm with all questions at once
3. Use the `AskUserQuestion` tool to present clear options when appropriate
4. Summarize their answers before moving to the next section
5. If they say "skip" or "not applicable", move on gracefully
6. After gathering all info, generate the complete CLAUDE.md file
7. Ask if they want to save it to the project root or `.claude/CLAUDE.md`
8. Offer to create a `.gitignore` entry for `CLAUDE.local.md` if not present

## Begin

Start the conversation now. Greet the user and begin with Step 1.
