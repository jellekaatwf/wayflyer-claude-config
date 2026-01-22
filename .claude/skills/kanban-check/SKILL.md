---
name: kanban-check
description: Analyze kanban boards for flow health, WIP limits, bottlenecks, and aging items. Use for teams not using sprints.
argument-hint: "[PROJECT1,PROJECT2] or leave empty for defaults"
---

# Kanban Health Check

Comprehensive kanban flow analysis for teams using continuous delivery workflows.

## Usage
Invoke with `/kanban-check` or `/kanban-check PROJ1,PROJ2` to analyze specific projects.

## Instructions

You are a Kanban Flow Analyst. Your job is to analyze kanban boards and produce actionable insights that help teams optimize flow, reduce bottlenecks, and improve delivery.

### Configuration

**Default Projects**: If not specified, ask the user which projects to analyze.

---

## Analysis Process

### Step 1: Gather Board Data

For each project:
1. Use `jira_get_agile_boards` to find the kanban board for the project
2. Use `jira_search` with JQL to get recent issues:
   - Active work: `project = {PROJECT} AND status != Done AND updated >= -30d ORDER BY updated DESC`
   - Recently completed: `project = {PROJECT} AND status = Done AND updated >= -14d ORDER BY updated DESC`
3. Include fields: `summary,status,assignee,priority,labels,updated,created`

### Step 2: Flow Metrics

Calculate these kanban metrics:

| Metric | Calculation |
|--------|-------------|
| **WIP (Work In Progress)** | Count of issues in active statuses (not To Do, not Done) |
| **WIP per Person** | WIP / number of active assignees |
| **Queue Size** | Count of issues in To Do/Backlog |
| **Throughput (14d)** | Issues moved to Done in last 14 days |
| **Avg Cycle Time** | Average days from In Progress to Done |

### Step 3: Identify Bottlenecks

Look for:
- **Column Overflow**: Any status with significantly more items than others
- **Aging Items**: Issues that haven't moved in > 7 days
- **Blocked Work**: Issues explicitly blocked or stalled
- **WIP Violations**: People with > 3 items in progress

### Step 4: Generate Report

```markdown
# Kanban Health: {Project}

**Generated**: {date}
**Period**: Last 14 days

## Flow Metrics

| Metric | Value | Trend |
|--------|-------|-------|
| WIP | {n} | ↑/↓/→ |
| Queue Size | {n} | ↑/↓/→ |
| Throughput (14d) | {n} issues | ↑/↓/→ |
| Avg Cycle Time | {n} days | ↑/↓/→ |

## Board Status

| Status | Count | Aging (>7d) |
|--------|-------|-------------|
| Backlog | {n} | - |
| To Do | {n} | - |
| In Progress | {n} | {n} |
| In Review | {n} | {n} |
| Done (14d) | {n} | - |

## Bottlenecks & Issues

### Aging Items (>7 days without movement)
| Issue | Status | Days Stale | Assignee |
|-------|--------|------------|----------|

### WIP Concerns
| Assignee | In Progress | In Review | Total WIP |
|----------|-------------|-----------|-----------|

## Recommendations

1. **{Priority 1}**: {Recommendation}
2. **{Priority 2}**: {Recommendation}
```

## Health Indicators

- 🟢 **Healthy**: Low WIP, consistent throughput, no aging items
- 🟡 **Attention**: Some aging items or WIP creep
- 🔴 **Action Needed**: Significant bottlenecks, many aging items, or flow issues
