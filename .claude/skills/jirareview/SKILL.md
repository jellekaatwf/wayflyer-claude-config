---
name: jirareview
description: Review a Jira ticket for completeness, quality, and technical feasibility
argument-hint: "<TICKET-ID>"
---

# Jira Ticket Review

You are conducting a review of a Jira ticket. **The review criteria depend on the issue type.** Fetch the ticket first, then apply the appropriate review framework.

## Usage

Invoke with `/jirareview TICKET-ID` to review a specific Jira ticket.

## Review Process

### Step 1: Fetch the Ticket

Use the `jira_get_issue` tool to retrieve the full ticket details:
- Set `fields` to `*all` to get all fields including custom fields
- Set `expand` to `renderedFields` to get formatted content

### Step 2: Identify Issue Type and Apply Framework

Check the issue type and use the matching review framework below.

---

# FRAMEWORK A: Idea / Initiative / Epic Review

Use for: **Idea, Initiative, Epic, Feature Request**

These are early-stage items focused on problem definition and value proposition.

## 1. Problem & Opportunity Assessment

| Criteria | Status | Notes |
|----------|--------|-------|
| **Problem Statement** | ✅/❌ | Is the problem clearly articulated? |
| **Target Users** | ✅/❌ | Who experiences this problem? |
| **Current State** | ✅/❌ | How is this handled today? |
| **Impact/Urgency** | ✅/❌ | Why does this matter now? |

## 2. Value Proposition

| Criteria | Status | Notes |
|----------|--------|-------|
| **Proposed Solution** | ✅/❌ | Is the solution clearly described? |
| **Expected Outcome** | ✅/❌ | What changes if we build this? |
| **Success Metrics** | ✅/❌ | How will we measure success? |

## 3. Scope & Feasibility

| Criteria | Status | Notes |
|----------|--------|-------|
| **Scope Defined** | ✅/❌ | Is it clear what's in/out? |
| **Dependencies** | ✅/❌ | Are blockers identified? |
| **Rough Sizing** | ✅/❌ | Is there a sense of effort? |

---

# FRAMEWORK B: Story / Task / Bug Review

Use for: **Story, Task, Bug, Sub-task**

These are implementation-ready items that need to be actionable.

## 1. Clarity & Completeness

| Criteria | Status | Notes |
|----------|--------|-------|
| **Clear Title** | ✅/❌ | Does title describe the work? |
| **User Story Format** | ✅/❌ | As a... I want... So that...? |
| **Acceptance Criteria** | ✅/❌ | Are ACs specific and testable? |
| **Description** | ✅/❌ | Is context provided? |

## 2. Technical Readiness

| Criteria | Status | Notes |
|----------|--------|-------|
| **Technical Approach** | ✅/❌ | Is implementation clear? |
| **Dependencies** | ✅/❌ | Are blockers identified? |
| **Estimated** | ✅/❌ | Is effort estimated? |

## 3. Quality

| Criteria | Status | Notes |
|----------|--------|-------|
| **Testable** | ✅/❌ | Can QA verify this? |
| **Edge Cases** | ✅/❌ | Are edge cases considered? |
| **Documentation** | ✅/❌ | Does this need docs? |

---

## Output Format

```markdown
# Ticket Review: {TICKET-ID}

**Title**: {title}
**Type**: {issue type}
**Status**: {status}
**Assignee**: {assignee}

## Review Summary

**Overall**: 🟢 Ready / 🟡 Needs Work / 🔴 Not Ready

{1-2 sentence summary}

## Checklist

{Use appropriate framework checklist}

## Recommendations

1. **{Priority}**: {Specific recommendation}
2. **{Priority}**: {Specific recommendation}

## Questions for Author

- {Question that needs clarification}
```
