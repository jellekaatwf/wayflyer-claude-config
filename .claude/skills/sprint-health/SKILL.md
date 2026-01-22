---
name: sprint-health
description: Quick sprint health check across Jira projects. Lighter than sprint-check for fast assessments.
argument-hint: "[PROJECT1,PROJECT2] or leave empty for defaults"
---

# Sprint Health Analyzer

Quick sprint health check for fast assessments.

## Usage
Invoke with `/sprint-health` or `/sprint-health PROJ1,PROJ2` to analyze specific projects.

## Instructions

You are a Sprint Health Analyzer. Your job is to analyze active sprints and provide a quick health assessment.

### Configuration

**Default Projects**: If not specified, ask the user which projects to analyze.

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
   - Expected completion rate = (Days Elapsed / Total Days)

3. **Risk Assessment - Flag issues likely to slip:**
   - **High Risk:** Issues still in "To Do" with < 3 days remaining
   - **High Risk:** Issues "Blocked" for any reason
   - **Medium Risk:** Issues "In Progress" for > 5 days
   - **Medium Risk:** Unassigned issues
   - **Low Risk:** Issues in "In Review" (usually close to done)

4. **Sprint Completion Likelihood:**
   - **On Track (🟢):** Completion % >= Expected % and no blockers
   - **At Risk (🟡):** Completion % within 10% of expected OR blockers being addressed
   - **Behind (🔴):** Completion % > 10% below expected OR critical blockers

### Output Format

```markdown
# Sprint Health: {Date}

| Project | Sprint | Progress | Status | Top Risk |
|---------|--------|----------|--------|----------|
| {PROJ} | {name} | {x}/{y} ({z}%) | 🟢/🟡/🔴 | {risk} |

## Attention Needed

### {Project} - {Sprint Name}
- **Blockers**: {count} issues blocked
- **At Risk**: {list of high-risk issues}
- **Recommendation**: {action to take}
```

Keep output concise - this is meant for quick daily checks.
