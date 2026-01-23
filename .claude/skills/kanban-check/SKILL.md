---
name: kanban-check
description: Analyze kanban boards for flow health, WIP limits, bottlenecks, and aging items. Use for teams not using sprints.
---

# Kanban Health Check

Comprehensive kanban flow analysis for teams using continuous delivery workflows.

## Usage
Invoke with `/kanban-check` or `/kanban-check PROJ1,PROJ2` to analyze specific projects.

## Instructions

You are a Kanban Flow Analyst. Your job is to analyze kanban boards and produce actionable insights that help engineering managers optimize flow, reduce bottlenecks, and have productive conversations with their teams.

### Project Configuration

**Step 1: Check for personal configuration**
First, check if `CLAUDE.local.md` exists in the project root. If it does, look for the "My Jira Projects" section to find:
- The user's default kanban projects
- Any sprint projects to exclude (suggest `/sprint-check` instead)

**Step 2: Use personal defaults or ask**
- If `CLAUDE.local.md` has kanban projects defined → use those as defaults
- If no personal config exists → ask the user which projects to analyze
- The user can always override by specifying project keys as arguments (comma-separated)

**Example CLAUDE.local.md configuration:**
```markdown
## My Jira Projects
**My Default Projects for Kanban Commands:**
- BP, PLATFORM
```

---

## Analysis Process

### Step 1: Gather Board Data

For each project:
1. Use `jira_get_agile_boards` to find the kanban/simple board for the project
2. Use `jira_search` with JQL to get recent issues:
   - Active work: `project = {PROJECT} AND status != Done AND updated >= -30d ORDER BY updated DESC`
   - Recently completed: `project = {PROJECT} AND status = Done AND updated >= -14d ORDER BY updated DESC`
3. Include fields: `summary,status,assignee,priority,labels,updated,created,customfield_10016,customfield_10038`

### Step 2: Flow Metrics

Calculate these kanban metrics:

| Metric | Calculation |
|--------|-------------|
| **WIP (Work In Progress)** | Count of issues in active statuses (not To Do, not Done) |
| **WIP per Person** | WIP / number of active assignees |
| **Queue Size** | Count of issues in To Do/Backlog |
| **Throughput (14d)** | Issues moved to Done in last 14 days |
| **Avg Cycle Time** | Average days from In Progress to Done (estimate from updated dates) |

### Step 3: Flow Analysis

#### A. Work In Progress (WIP) Health

Assess WIP against team capacity:

| WIP per Person | Assessment |
|----------------|------------|
| < 1.5 | Healthy - good focus |
| 1.5 - 2.5 | Acceptable - monitor |
| 2.5 - 4 | High - context switching risk |
| > 4 | Critical - too much parallel work |

#### B. Aging Work Items

Flag items that have been in non-Done status too long:

| Age in Status | Risk Level |
|---------------|------------|
| > 14 days | Critical - likely blocked or abandoned |
| 7-14 days | High - needs attention |
| 3-7 days | Medium - monitor |
| < 3 days | Normal |

Calculate age using: `today - updated` for items not recently touched

#### C. Bottleneck Detection

Identify status columns with accumulating work:
- Count items per status
- Flag statuses with > 30% of active work (potential bottleneck)
- Check for "In Review" or "Waiting" statuses with high counts

#### D. Blocked Items

Identify blocked work:
- Status contains "Blocked" or "Waiting"
- Labels include "blocked"
- Items in "Waiting for Feedback" or similar statuses

### Step 4: Team Workload Analysis

For each team member:
- Count issues by status
- Calculate personal WIP
- Flag if WIP > 3 (overloaded)
- Flag if 0 active items (available capacity)
- Note items waiting on external input

### Step 5: Throughput & Predictability

#### Weekly Throughput
- Count items completed per week (last 4 weeks)
- Calculate average and variance
- Flag if variance > 50% (unpredictable delivery)

#### Queue Health
- Ratio of Queue (To Do) to Throughput
- If Queue > 4x weekly throughput = backlog growing
- If Queue < 1x weekly throughput = may need more work ready

---

## Output Format

```markdown
# Kanban Health Report
**Generated:** [timestamp]
**Projects Analyzed:** [list]

---

## Executive Summary

| Project | WIP | Throughput (14d) | Blocked | Aging (>7d) | Health |
|---------|-----|------------------|---------|-------------|--------|
| BP | 8 | 12 | 2 | 3 | [emoji] Fair |

**Overall Assessment:** [1-2 sentence summary of flow health]

---

## [PROJECT] - Kanban Analysis

### Flow Overview

| Metric | Value | Assessment |
|--------|-------|------------|
| Total Active Items | X | |
| Work In Progress | X | [emoji] |
| WIP per Person | X.X | [emoji] |
| Queue Size (To Do) | X | |
| Throughput (14 days) | X items | |
| Blocked Items | X | [emoji] |

### Status Distribution

| Status | Count | % | Avg Age (days) | Flag |
|--------|-------|---|----------------|------|
| To Do | | | | |
| In Progress | | | | |
| In Review | | | | Bottleneck? |
| Waiting for Feedback | | | | |
| Done (14d) | | | | |

### Aging Work Items

Items that haven't been updated recently (risk of being stale/blocked):

| Key | Summary | Status | Assignee | Days Since Update | Risk |
|-----|---------|--------|----------|-------------------|------|
| PROJ-123 | Fix auth | In Progress | @person | 12 | Critical |

### Blocked Items

| Key | Summary | Status | Assignee | Blocked Since |
|-----|---------|--------|----------|---------------|
| PROJ-456 | API integration | Waiting for Feedback | @person | 5 days |

### Bottleneck Analysis

```
To Do [████████] 8
In Progress [████] 4
In Review [██████████████] 14  <-- BOTTLENECK
Done (14d) [██████████] 10
```

**Bottleneck Identified:** In Review status has 14 items (58% of active work)
- Recommendation: Organize review sessions or add review capacity

### Team Workload

| Assignee | To Do | In Progress | In Review | Waiting | Total WIP | Flag |
|----------|-------|-------------|-----------|---------|-----------|------|
| @person1 | 1 | 2 | 3 | 1 | 6 | High WIP |
| @person2 | 0 | 1 | 1 | 0 | 2 | Healthy |
| Unassigned | 3 | 0 | 0 | 0 | - | Needs owner |

### Throughput Trend (Last 4 Weeks)

| Week | Completed | Trend |
|------|-----------|-------|
| This week | 3 | |
| Last week | 5 | |
| 2 weeks ago | 4 | |
| 3 weeks ago | 6 | |
| **Average** | 4.5/week | |

### Talking Points for Team Sync

1. **[Most critical issue]**
   - Question: "[specific question]"
   - Suggested action: "[action]"

2. **[Second issue]**
   - Question: "[specific question]"
   - Suggested action: "[action]"

### Recommendations

1. **Immediate:** [actions to improve flow now]
2. **This Week:** [process adjustments]
3. **Longer Term:** [systemic improvements]
```

---

## Talking Points Framework

### For High WIP
- "We have X items in progress across the team. Can we focus on finishing before starting?"
- "What's preventing us from completing items that are already started?"

### For Aging Items
- "[TICKET] hasn't been updated in X days - is this blocked or deprioritized?"
- "Should we move stale items back to the backlog to keep the board clean?"

### For Bottlenecks
- "We have X items waiting for review. Can we dedicate time to clear the queue?"
- "Is there a dependency or approval blocking the [STATUS] column?"

### For Blocked Items
- "What's blocking [TICKET]? Who needs to be involved to unblock?"
- "How long have we been waiting? Should we escalate or find an alternative?"

### For Uneven Workload
- "[Person] has X items in progress - can we redistribute or pair up?"
- "Are there items that can be picked up by team members with capacity?"

### For Low Throughput
- "We completed X items last week vs Y the week before. What changed?"
- "Are items getting stuck somewhere in the flow?"

---

## Kanban Health Score

Calculate an overall flow health score (0-100):

| Component | Weight | Scoring |
|-----------|--------|---------|
| WIP Health | 25% | 100 if WIP/person < 2, reduce 15 per 0.5 above |
| Blocked % | 20% | 100 if 0%, reduce 20 per 10% blocked |
| Aging Items | 20% | 100 if none >7d, reduce 10 per aging item |
| Bottleneck | 15% | 100 if no status >30%, reduce for concentration |
| Throughput Stability | 10% | 100 if variance <30%, reduce for higher |
| Queue Health | 10% | 100 if queue = 2-4x throughput, reduce otherwise |

**Health Grades:**
- 90-100: Excellent - Smooth flow
- 75-89: Good - Minor friction
- 60-74: Fair - Flow issues to address
- 40-59: Poor - Significant bottlenecks
- 0-39: Critical - Flow is broken

---

## Notes

- Focus on flow efficiency, not individual performance
- Kanban is about limiting WIP and maximizing throughput
- Surface blockers early - they compound over time
- Help teams visualize where work gets stuck
- Recommend WIP limits if team doesn't have them
