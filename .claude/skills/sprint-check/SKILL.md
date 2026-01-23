---
name: sprint-check
description: Analyze active sprints across Jira projects for hygiene, risks, and team workload. Use for standups, sprint reviews, or sprint health visibility.
---

# Sprint Check

Comprehensive sprint hygiene analysis for team conversations and execution tracking.

## Usage
Invoke with `/sprint-check` or `/sprint-check PROJ1,PROJ2` to analyze specific projects.

## Instructions

You are a Sprint Hygiene Analyst. Your job is to analyze active sprints and produce actionable insights that help engineering managers have productive conversations with their teams about execution.

### Default Projects
If no projects are specified, analyze these projects:
- BANK (Wayflyer Banking)
- EF (Embedded Finance)
- FUNDE (Funding Team)
- UN (Underwriting Services)

The user can override by specifying project keys as arguments (comma-separated).

### Projects Using Kanban (Not Sprint-Based)
These projects use kanban and should NOT be included in sprint hygiene checks:
- BP (Broker Portal) - uses kanban board

### Story Points Configuration
Different projects use different custom fields for story points:

| Project | Field | Field ID |
|---------|-------|----------|
| EF | Story Points | `customfield_10038` |
| FUNDE | Story Points | `customfield_10038` |
| UN | Story point estimate | `customfield_10016` |
| BANK | Not using story points | - |

When fetching issues, include both fields: `customfield_10016,customfield_10038`

---

## Analysis Process

### Step 1: Gather Sprint Data

For each project:
1. Use `jira_get_agile_boards` to find the board for the project
2. Use `jira_get_sprints_from_board` with `state=active` to get active sprints
3. Use `jira_get_sprint_issues` with `limit=50` and include story point fields:
   - Fields: `summary,status,assignee,priority,labels,updated,created,customfield_10016,customfield_10038`
   - `customfield_10016` = "Story point estimate" (used by UN)
   - `customfield_10038` = "Story Points" (used by EF, FUNDE)
4. If needed, paginate to get all issues

### Step 2: Core Sprint Metrics

Calculate these fundamental metrics:

| Metric | Calculation |
|--------|-------------|
| **Total Issues** | Count of all issues in sprint |
| **Completion %** | (Done issues / Total) * 100 |
| **Time Elapsed %** | (Days since start / Total sprint days) * 100 |
| **Completion Gap** | Completion % - Time Elapsed % (negative = behind) |
| **Story Points Committed** | Sum of all story points |
| **Story Points Completed** | Sum of story points for Done issues |

### Step 3: Risk Analysis

#### A. Ticket-Level Risk Scoring

Score each incomplete ticket (not Done) on these factors:

| Risk Factor | Points | Condition |
|-------------|--------|-----------|
| **Blocked** | +30 | Status contains "Blocked" or has blocking flag |
| **Unassigned** | +20 | No assignee |
| **Stale** | +15 | No updates in > 3 days while In Progress |
| **Large ticket** | +15 | Story points >= 8 (or largest 20% if no points) |
| **Still in To Do** | +10 | Status is To Do/Open/Backlog |
| **No story points** | +5 | Missing estimation |
| **Late sprint days** | +10 | Added after sprint started (check created date vs sprint start) |

**Risk Tiers:**
- **Critical (40+):** Almost certain to slip
- **High (25-39):** Likely to slip without intervention
- **Medium (10-24):** Monitor closely
- **Low (0-9):** On track

#### B. Sprint-Level Risk Assessment

| Indicator | Red Flag Threshold |
|-----------|-------------------|
| Completion Gap | < -20% (significantly behind timeline) |
| Blocked Issues | > 10% of remaining work |
| Unassigned Issues | > 15% of remaining work |
| Stale Issues | > 20% of in-progress work |
| Scope Creep | > 20% of issues added mid-sprint |

### Step 4: Team Workload Analysis

For each team member:
- Count issues by status
- Flag if > 5 issues assigned (potential overload)
- Flag if > 3 issues In Progress simultaneously (context switching)
- Note if any team member has 0 issues (capacity available?)

### Step 5: Additional Hygiene Checks

#### Scope Creep Detection
Compare issue `created` dates against sprint `startDate`:
- Count tickets added after sprint started
- Calculate % of sprint that is "unplanned work"
- Flag if > 20% scope creep

#### Work In Progress (WIP) Limits
- Count total "In Progress" + "In Review" issues
- Compare against team size (ideal: ~1.5 per person)
- Flag WIP > 2x team size as bottleneck risk

#### Review Bottleneck
- Count issues in "In Review" or "Code Review" status
- Check how long they've been there (use `updated` field)
- Flag if items in review > 2 days (review capacity issue)

#### Estimation Health
- % of issues with story points
- Flag sprints with < 80% estimated as "estimation hygiene needed"
- Note any tickets with 0 story points that are Done (estimation baseline)

#### Dependency Tracking
- Check for linked issues (blockers)
- Note cross-team dependencies
- Flag external dependencies that aren't resolved

#### Bug Ratio
- Calculate (Bug issues / Total issues) * 100
- Flag if > 30% of sprint is bugs (tech debt indicator)
- Note if bugs are concentrated with specific assignees

---

## Output Format

```markdown
# Sprint Hygiene Report
**Generated:** [timestamp]
**Projects Analyzed:** [list]

---

## Executive Summary

| Project | Sprint | Status | Completion | Gap | Top Risk |
|---------|--------|--------|------------|-----|----------|
| PROJ | Sprint 42 | [emoji] At Risk | 45% | -15% | 3 blocked |

**Overall Assessment:** [1-2 sentence summary of biggest concerns across all sprints]

---

## [PROJECT] - [Sprint Name]

### Sprint Overview
- **Duration:** [start] to [end]
- **Days Remaining:** [X] of [Y] total
- **Timeline Progress:** [X]%

### Completion Status

| Metric | Value | Assessment |
|--------|-------|------------|
| Issues Done | X/Y | [emoji] |
| Story Points | X/Y committed, Z completed | [emoji] (if tracked) |
| Completion % (issues) | X% | |
| Completion % (points) | X% | (if tracked) |
| Expected % | Y% | |
| **Gap** | Z% | [On Track / Behind / Ahead] |

> Note: Story points tracked via `customfield_10038` (EF, FUNDE) or `customfield_10016` (UN). BANK does not use story points.

### Issue Breakdown

| Status | Count | % | Story Points | SP % |
|--------|-------|---|--------------|------|
| Done | | | | |
| In Review | | | | |
| In Progress | | | | |
| To Do | | | | |
| Blocked | | | | |
| **Total** | | 100% | | 100% |

> Story points column shows sum of points per status. "SP %" shows percentage of total committed points.

### High Risk Tickets

Issues most likely to slip (sorted by risk score):

| Key | Summary | Assignee | Status | Risk Score | Reasons |
|-----|---------|----------|--------|------------|---------|
| PROJ-123 | Fix auth bug | Unassigned | Blocked | 50 (Critical) | Blocked, Unassigned, 5d stale |

### Hygiene Flags

| Check | Status | Details |
|-------|--------|---------|
| Scope Creep | [emoji] | X% added mid-sprint (Y tickets) |
| WIP Limits | [emoji] | X items in progress vs Y team members |
| Review Bottleneck | [emoji] | X items in review, oldest: Y days |
| Estimation | [emoji] | X% of tickets estimated |
| Bug Ratio | [emoji] | X% of sprint is bugs |

### Team Workload

| Assignee | To Do | In Progress | In Review | Done | Total | Flag |
|----------|-------|-------------|-----------|------|-------|------|
| @person | 2 | 3 | 1 | 4 | 10 | High WIP |

### Talking Points for Team Sync

Based on the analysis, discuss these items with the team:

1. **[Most critical issue]**
   - Question to ask: "[specific question]"
   - Action needed: "[suggested action]"

2. **[Second issue]**
   - Question to ask: "[specific question]"
   - Action needed: "[suggested action]"

3. **[Third issue]**
   - Question to ask: "[specific question]"
   - Action needed: "[suggested action]"

### Recommendations

1. **Immediate:** [actions for this sprint]
2. **Next Sprint:** [process improvements to consider]
```

---

## Talking Points Framework

Generate specific questions for team conversations based on findings:

### For Blocked Items
- "What's blocking [TICKET]? Who do we need to pull in to unblock it?"
- "Has this been escalated? What's the expected unblock date?"

### For Stale Items
- "I noticed [TICKET] hasn't been updated in X days - is this still actively being worked?"
- "Are there hidden blockers we should surface?"

### For Overloaded Team Members
- "You have X items in progress - can we help prioritize or redistribute?"
- "Is there anything we can descope or move to next sprint?"

### For Scope Creep
- "We added X tickets mid-sprint. Were these urgent? Should we discuss sprint protection?"
- "What caused the unplanned work? Can we prevent it next sprint?"

### For Low Completion
- "We're at X% complete with Y days left. What's our realistic completion target?"
- "Which items should we consider moving to next sprint now vs. at the last minute?"

### For Review Bottlenecks
- "We have X items waiting for review. Can we organize a review session?"
- "Should we rotate reviewers or pair up to clear the queue?"

---

## Sprint Health Score

Calculate an overall sprint health score (0-100):

| Component | Weight | Scoring |
|-----------|--------|---------|
| Completion Gap | 30% | 100 if gap >= 0, reduce 5 points per -5% gap |
| Blocked % | 20% | 100 if 0%, reduce 10 points per 5% blocked |
| Stale % | 15% | 100 if 0%, reduce 10 points per 10% stale |
| Estimation % | 10% | Equal to estimation % |
| WIP Health | 15% | 100 if WIP ratio < 2, reduce for higher |
| Scope Stability | 10% | 100 - scope creep % |

**Health Grades:**
- 90-100: Excellent - Sprint is healthy
- 75-89: Good - Minor issues to monitor
- 60-74: Fair - Intervention recommended
- 40-59: Poor - Significant risks, action needed
- 0-39: Critical - Sprint likely to fail without major changes

---

## Notes

- Always surface actionable insights, not just data
- Focus on helping teams succeed, not blame
- Highlight patterns across sprints when relevant
- If this is a recurring check, note improvements or regressions from previous runs
