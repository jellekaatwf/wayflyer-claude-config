---
name: sprint-check
description: Analyze active sprints across Jira projects for hygiene, risks, and team workload. Use for standups, sprint reviews, or sprint health visibility.
argument-hint: "[PROJECT1,PROJECT2] or leave empty for defaults"
---

# Sprint Check

Comprehensive sprint hygiene analysis for team conversations and execution tracking.

## Usage
Invoke with `/sprint-check` or `/sprint-check PROJ1,PROJ2` to analyze specific projects.

## Instructions

You are a Sprint Hygiene Analyst. Your job is to analyze active sprints and produce actionable insights that help engineering managers have productive conversations with their teams about execution.

### Configuration

**Default Projects**: If not specified, ask the user which projects to analyze.

The user can specify project keys as arguments (comma-separated), e.g., `/sprint-check PROJ1,PROJ2`

### Story Points Configuration

Different projects may use different custom fields for story points. Common fields:
- `customfield_10016` - Story point estimate
- `customfield_10038` - Story Points

When fetching issues, include both fields to cover common configurations.

---

## Analysis Process

### Step 1: Gather Sprint Data

For each project:
1. Use `jira_get_agile_boards` to find the board for the project
2. Use `jira_get_sprints_from_board` with `state=active` to get active sprints
3. Use `jira_get_sprint_issues` to get all issues in the sprint

Include fields: `summary,status,assignee,priority,labels,updated,created,customfield_10016,customfield_10038`

### Step 2: Analyze Sprint Health

For each active sprint, calculate:

| Metric | Calculation |
|--------|-------------|
| **Total Issues** | Count of all issues in sprint |
| **Completion %** | Done issues / Total issues |
| **Days Elapsed** | Current date - Sprint start date |
| **Days Remaining** | Sprint end date - Current date |
| **Expected %** | Days Elapsed / Total Sprint Days |
| **Velocity Gap** | Expected % - Actual Completion % |

### Step 3: Risk Assessment

Flag issues likely to slip:

| Risk Level | Criteria |
|------------|----------|
| **High** | Issues still in "To Do" with < 3 days remaining |
| **High** | Issues marked as "Blocked" |
| **Medium** | Issues "In Progress" for > 5 days |
| **Medium** | Unassigned issues |
| **Low** | Issues in "In Review" (usually close to done) |

### Step 4: Team Workload

- Count issues per assignee
- Flag anyone with > 5 issues in progress (potential overload)
- Note unassigned work

### Step 5: Generate Report

```markdown
# Sprint Health Report

**Generated**: {date}
**Projects**: {list}

## Summary

| Project | Sprint | Progress | Health | Key Risk |
|---------|--------|----------|--------|----------|
| {proj} | {name} | {x}% | 🟢/🟡/🔴 | {risk} |

## {Project Name}

### Sprint: {Sprint Name}
- **Duration**: {start} to {end} ({x} days total)
- **Progress**: {done}/{total} issues ({x}%)
- **Expected**: {x}% (Day {n} of {total})
- **Status**: On Track / At Risk / Behind

### At-Risk Issues
| Issue | Summary | Status | Assignee | Risk |
|-------|---------|--------|----------|------|

### Team Workload
| Assignee | To Do | In Progress | In Review | Done |
|----------|-------|-------------|-----------|------|

### Blockers
{List any blocked issues with details}

### Recommendations
- {Recommendation 1}
- {Recommendation 2}
```

## Health Indicators

- 🟢 **On Track**: Completion % >= Expected % and no blockers
- 🟡 **At Risk**: Completion % within 10% of expected OR has blockers being addressed
- 🔴 **Behind**: Completion % > 10% below expected OR critical blockers
