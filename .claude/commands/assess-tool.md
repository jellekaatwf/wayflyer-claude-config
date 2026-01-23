---
description: Assess a SaaS tool for Wayflyer's security and compliance requirements before starting a trial
argument-hint: <tool_url>
---

# SaaS Tool Security Assessment

You are conducting a security and compliance assessment for a SaaS tool that Wayflyer is considering for a trial. This assessment answers the key questions from Wayflyer's tool trial process.

## Input

The user provides a URL to the tool's website: `$ARGUMENTS`

## Assessment Questions

You must find answers to these 5 questions:

1. **SSO Support**: Does the tool offer a tier with SSO support?
2. **Data Storage Location**: In what country does the system store data?
3. **Data Training Policy**: Does the vendor commit to not training their models on or retaining customer data?
4. **Data Deletion**: Can they provide a certificate of data deletion at the end of the trial?
5. **Compliance Certifications**: Does the vendor claim GDPR compliance and display SOC 2 or ISO 27001 certification on their website?

## Research Process

### Step 1: Identify the Tool
Extract the tool name and domain from the provided URL.

### Step 2: Gather Information
Use WebFetch and WebSearch to find relevant pages. Search for and fetch:

- **Pricing page** (for SSO tier information)
- **Security/Trust page** (often at /security, /trust, or /trust-center)
- **Privacy Policy** (for data storage, retention, and training policies)
- **Terms of Service** (for data handling commitments)
- **Compliance page** (for SOC 2, ISO 27001, GDPR)
- **Help/FAQ articles** about security and data handling

Useful search queries:
- `[tool name] SSO pricing`
- `[tool name] SOC 2 certification`
- `[tool name] ISO 27001`
- `[tool name] GDPR compliance`
- `[tool name] data residency location`
- `[tool name] AI training data policy`
- `[tool name] data deletion`

### Step 3: Compile the Assessment

Present findings in this format:

```
## Tool Assessment: [Tool Name]

**URL**: [tool website]
**Assessment Date**: [date]

### 1. SSO Support
**Finding**: [Yes/No/Partial]
**Details**: [Which tier offers SSO, pricing if available]
**Source**: [URL where this was found]

### 2. Data Storage Location
**Finding**: [Country/Region or Unknown]
**Details**: [Specific data centers, cloud providers mentioned]
**Source**: [URL where this was found]

### 3. Data Training/Retention Policy
**Finding**: [Commits to no training / Unclear / Trains on data]
**Details**: [Specific language from their policies]
**Source**: [URL where this was found]

### 4. Data Deletion Certificate
**Finding**: [Available / Not mentioned / Unknown]
**Details**: [Process for requesting deletion, any mentions of certificates]
**Source**: [URL where this was found]

### 5. Compliance Certifications
**GDPR**: [Compliant / Claims compliance / Not mentioned]
**SOC 2**: [Type I/II certified / In progress / Not mentioned]
**ISO 27001**: [Certified / In progress / Not mentioned]
**Source**: [URLs where certifications were found]

---

### Summary
[Brief 2-3 sentence summary of overall security posture]

### Recommendation
[Proceed with trial / Proceed with caution / Requires further investigation / Not recommended]

### Questions for Vendor
[List any unanswered questions that should be asked directly to the vendor before proceeding]
```

## Important Notes

- If information cannot be found, clearly state "Not found on website - recommend asking vendor directly"
- Always include source URLs so findings can be verified
- Be factual and cite specific language when possible
- Don't assume or infer - only report what is explicitly stated
- If the tool is AI-based, pay special attention to data training policies

## After Assessment

Ask the user if they would like to:
1. Create an entry in the Wayflyer Trial Record Database in Notion
2. Get a summary formatted for Slack to share with the team
