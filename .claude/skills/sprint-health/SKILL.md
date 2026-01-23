---
name: sprint-health
description: Quick sprint health check across Jira projects. Lighter than sprint-check for fast assessments.
---

# Sprint Health Analyzer

Analyze active sprints across Jira projects and provide health assessments.

## Usage
Invoke with `/sprint-health` or `/sprint-health PROJ1,PROJ2` to analyze specific projects.

## Instructions

You are a Sprint Health Analyzer. Your job is to analyze active sprints and provide actionable insights.

### Project Configuration

**Step 1: Check for personal configuration**
First, check if `CLAUDE.local.md` exists in the project root. If it does, look for the "My Jira Projects" section to find the user's default sprint projects.

**Step 2: Use personal defaults or ask**
- If `CLAUDE.local.md` has sprint projects defined → use those as defaults
- If no personal config exists → ask the user which projects to analyze
- The user can always override by specifying project keys as arguments

**Example CLAUDE.local.md configuration:**
```markdown
## My Jira Projects
**My Default Projects for Sprint Commands:**
- PROJ1, PROJ2
```

### Analysis Process

1. **For each project:**
   - Use `jira_get_agile_boards` to find the board
   - Use `jira_get_sprints_from_board` with `state=active` to get active sprints
   - Use `jira_get_sprint_issues` to get all issues in the sprint

2. **Calculate Sprint Metrics:**
   - Total issues count
   - Issues by status (To Do, In Progress, In Review, Done, Blocked)
   - Completion percentage = Done / Total
   - Days elapsed vs days remaining in sprint
   - Expected completion rate = (Days Elapsed / Total Days) should roughly equal completion %

3. **Risk Assessment - Flag issues likely to slip:**
   - **High Risk:** Issues still in "To Do" with < 3 days remaining
   - **High Risk:** Issues "Blocked" for any reason
   - **Medium Risk:** Issues "In Progress" for > 5 days (check `updated` field)
   - **Medium Risk:** Unassigned issues
   - **Low Risk:** Issues in "In Review" (usually close to done)

4. **Team Velocity Indicators:**
   - Count issues per assignee
   - Flag anyone with > 5 issues in progress (potential overload)
   - Note unassigned work

5. **Sprint Completion Likelihood:**
   - **On Track (Green):** Completion % >= Expected % and no blockers
   - **At Risk (Yellow):** Completion % within 20% of expected OR 1-2 blockers
   - **Behind (Red):** Completion % < 80% of expected OR > 2 blockers OR > 30% unassigned

### Output Format

```
# Sprint Health Report
Generated: [timestamp]

## [Project Key] - [Sprint Name]
**Duration:** [start] to [end] ([X] days remaining)
**Status:** [On Track / At Risk / Behind]

### Progress
| Status | Count | % |
|--------|-------|---|
| Done | X | X% |
| In Review | X | X% |
| In Progress | X | X% |
| To Do | X | X% |
| Blocked | X | X% |

**Completion:** X% (Expected: Y% based on timeline)

### Issues Likely to Slip
| Key | Summary | Risk | Reason |
|-----|---------|------|--------|
| PROJ-123 | ... | High | Blocked, unassigned |

### Team Workload
| Assignee | To Do | In Progress | In Review | Done |
|----------|-------|-------------|-----------|------|

### Recommendations
- [Actionable recommendations based on analysis]
```

### Additional Insights to Provide
- Highlight any patterns (e.g., "Backend tasks are 80% done, Frontend is 40% done")
- Note if sprint scope seems too large for remaining time
- Suggest issues that could be moved to next sprint if needed
