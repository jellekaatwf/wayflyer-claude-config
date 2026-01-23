---
name: jirareview
description: Review a Jira ticket for completeness, quality, and technical feasibility
---

# Jira Ticket Review

You are conducting a review of a Jira ticket. **Critically, the review criteria depend on the issue type.** Fetch the ticket first, then apply the appropriate review framework.

## Usage

Invoke with `/jirareview TICKET-ID` to review a specific Jira ticket.

## Context

- PMs and Designers are responsible for customer/user research and validation - there is no dedicated research team
- Open questions should prompt the PM to think deeper, not prescribe solutions
- Ideas should include AI prototypes (Claude artifacts, v0, Lovable, Figma AI, etc.) to help visualize and validate concepts early

## Review Process

### Step 1: Fetch the Ticket

Use the `jira_get_issue` tool to retrieve the full ticket details with all fields:
- Set `fields` to `*all` to get all fields including custom fields
- Set `expand` to `renderedFields` to get formatted content

### Step 2: Identify Issue Type and Apply Correct Framework

**Check the issue type and use the matching review framework below.**

---

# FRAMEWORK A: Idea / Initiative / Epic Review

Use this framework for: **Idea, Initiative, Epic, Feature Request**

These are early-stage items focused on problem definition and value proposition - NOT implementation readiness.

## 1. Problem & Opportunity Assessment

| Criteria | Status | Notes |
|----------|--------|-------|
| **Problem Statement** | ✅/❌ | Is the problem clearly articulated? |
| **Target Users/Customers** | ✅/❌ | Who experiences this problem? |
| **Current State** | ✅/❌ | How is this handled today? |
| **Impact/Urgency** | ✅/❌ | Why does this matter now? |

## 2. Value Proposition & Outcomes

| Criteria | Status | Notes |
|----------|--------|-------|
| **Proposed Solution** | ✅/❌ | Is there a clear solution direction? |
| **Expected Outcomes** | ✅/❌ | What does success look like? |
| **Success Metrics** | ✅/❌ | How will we measure impact? Are metrics specific and measurable? |
| **Strategic Alignment** | ✅/❌ | Does this align with company/team goals? |

## 3. Scope & Feasibility

| Criteria | Status | Notes |
|----------|--------|-------|
| **Scope Definition** | ✅/❌ | Is scope clear? What's in/out? |
| **Dependencies Identified** | ✅/❌ | Teams, systems, or other work this depends on |
| **Risks Identified** | ✅/❌ | Key risks and open questions called out |
| **Rough Sizing** | ✅/❌ | Is there a sense of effort (S/M/L)? |
| **AI Prototype** | ✅/❌ | Is there an AI prototype linked or attached? (e.g., Claude artifact, v0, Lovable, Figma AI) |

## 4. Idea Quality Assessment

Evaluate:
- **Clarity**: Can someone unfamiliar understand the idea quickly?
- **Completeness**: Are there obvious gaps in thinking?
- **Testability**: Could this be validated before full build?
- **Differentiation**: What makes this approach unique or better?

## 5. Socratic Questions

Apply the Socratic Questioning Framework to surface assumptions and sharpen thinking. Pick 3-5 of the most relevant questions:

### Problem Clarity
- "What specific user pain point does this solve?" (Look for concrete examples, not abstract statements)
- "How do we know this is a real problem?" (Push for evidence: user interviews, support tickets, data)
- "Who experiences this problem most acutely?"
- "What's the cost of NOT solving this?"

### Solution Validation
- "Why is this the right solution for that problem?"
- "What alternatives were considered? Why were they rejected?"
- "What's the simplest version that solves the core problem?"
- "How will users discover this feature?"

### Success Criteria
- "How will we know if this is successful?"
- "What would make this a failure?"
- "What metric are we trying to move? By how much?"

### Constraints & Trade-offs
- "What are we NOT going to do as part of this?"
- "If we had half the time/resources, what would we cut?"
- "What existing features or workflows does this affect?"

### Strategic Fit
- "Why is this the right thing to build RIGHT NOW?"
- "What happens if we wait 6 months to build this?"

**Red flags to watch for:**
- Vague language ("users want better...", "this will improve...")
- Solution-first thinking (can describe feature but not the problem)
- Lack of evidence ("I think users would like...")
- Unclear success criteria

## 6. Recommendations

- What needs to be answered before this moves forward?
- Suggestions to strengthen the idea
- Potential next steps (e.g., customer conversations, prototype testing, technical spike, stakeholder alignment)

### Output Format for Ideas

```
# Idea Review: [TICKET-ID]

**Title**: [ticket summary]
**Type**: [issue type] | **Priority**: [priority] | **Status**: [status]
**Owner**: [assignee or "Unassigned"]

---

## 💡 Problem & Opportunity: X/10

[Assessment table]

**Summary**: [1-2 sentences on problem clarity]

---

## 🎯 Value Proposition & Outcomes: X/10

[Assessment table]

**Summary**: [1-2 sentences on outcome clarity]

---

## 📐 Scope & Feasibility: X/10

[Assessment table]

**Summary**: [1-2 sentences on scope clarity]

---

## 🔍 Idea Quality Assessment

[Evaluation of clarity, completeness, testability, differentiation]

---

## 🧠 Socratic Questions

Ask 3-5 of these questions to sharpen thinking:

1. **[Question from framework]**
   - Why this matters: [brief context]

2. **[Question from framework]**
   - Why this matters: [brief context]

3. **[Question from framework]**
   - Why this matters: [brief context]

**Red flags identified:** [Any vague language, solution-first thinking, lack of evidence, or unclear success criteria]

---

## ❓ Open Questions

- [Key questions that need answers]

## 💬 Recommendations

- [Suggestions to strengthen the idea]

---

## Overall Assessment

**Idea Maturity**: 🟢 Well-formed / 🟡 Needs refinement / 🔴 Too early

[2-3 sentence summary of idea quality and suggested next steps]
```

---

# FRAMEWORK B: Story / Task / Bug Review

Use this framework for: **Story, Task, Bug, Sub-task, Spike**

These are implementation-ready items that need clear requirements and acceptance criteria.

## 1. Completeness Check

| Field | Status | Notes |
|-------|--------|-------|
| **Summary** | ✅/❌ | Is it clear and descriptive? |
| **Description** | ✅/❌ | Is there sufficient detail? |
| **Issue Type** | ✅/❌ | Is it appropriately categorized? |
| **Priority** | ✅/❌ | Is priority set? |
| **Assignee** | ✅/❌ | Is someone assigned? |
| **Labels** | ✅/❌ | Are relevant labels applied? |
| **Epic Link** | ✅/❌ | Is it linked to an epic? |
| **Acceptance Criteria** | ✅/❌ | Are clear AC defined? |
| **Story Points/Estimate** | ✅/❌ | Is effort estimated? |

## 2. Requirements Quality

### Description Quality
- Is the problem/goal clearly stated?
- Is there enough context for someone unfamiliar?
- Are there user stories or use cases?

### Acceptance Criteria Quality
- Are AC specific and measurable?
- Are edge cases considered?
- Is the "definition of done" clear?

### Ambiguities & Gaps
- List any unclear requirements
- Identify missing information
- Note assumptions that should be validated

## 3. Technical Review

### Complexity Assessment
- **Estimated Complexity**: Low / Medium / High
- **Reasoning**: Why this complexity level?

### Technical Considerations
- Dependencies on other systems/tickets
- Potential technical challenges
- Architecture considerations

### Risks & Concerns
- What could go wrong?
- What needs clarification before implementation?

## 4. Socratic Review (for requirements quality)

If the ticket's problem statement or requirements are unclear, apply targeted Socratic questions:

- "What specific user pain point does this solve?" (if problem is vague)
- "How will we know if this is successful?" (if no clear AC)
- "What's the simplest version that solves the core problem?" (if over-scoped)
- "What are we NOT doing?" (if scope is unclear)

**Flag red flags:** Vague language, solution-first thinking, lack of evidence, unclear success criteria.

### Output Format for Stories/Tasks

```
# Ticket Review: [TICKET-ID]

**Title**: [ticket summary]
**Type**: [issue type] | **Priority**: [priority] | **Status**: [status]
**Assignee**: [assignee or "Unassigned"]

---

## 📋 Completeness Score: X/10

[Completeness table]

---

## 📝 Requirements Quality

### Description
[Assessment]

### Acceptance Criteria
[Assessment]

### Gaps & Ambiguities
- [Issues found]

---

## 🔧 Technical Review

**Complexity**: [Low/Medium/High]

### Considerations
- [Key technical points]

### Risks
- [Identified risks]

---

## 🧠 Socratic Review

[If requirements are unclear, include targeted questions]

**Questions to ask the PM/author:**
- [Relevant question based on gaps found]

**Red flags:** [Any vague language, solution-first thinking, or unclear success criteria]

---

## Summary

**Implementation Readiness**: 🟢 Ready / 🟡 Needs Work / 🔴 Not Ready

[2-3 sentence summary and key action items]
```

---

## After Review

Ask the user if they would like to:
1. Add a comment to the Jira ticket with this review
2. Create follow-up tickets for identified gaps
3. Review related tickets (epic, linked issues)
