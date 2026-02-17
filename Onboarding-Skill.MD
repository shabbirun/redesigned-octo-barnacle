---
name: client-onboarding
description: Complete client onboarding automation from inquiry email to project setup. Use this skill when users say "onboard this client", "set up project for [name]", "process client inquiry", "create project from email", or when they mention starting work with a new client. Handles email extraction, folder creation, contract/invoice generation, Notion project setup, and welcome email drafting. This skill is especially valuable when starting any new client engagement, even if the user doesn't explicitly ask for full onboarding - proactively suggest it when you see client project discussions.
compatibility: Requires Gmail MCP, Notion (MCP preferred, Chrome fallback), docx skill, and template files in user's workspace
---

# Client Onboarding Automation

This skill automates the complete client onboarding workflow from initial inquiry email through project setup and welcome communication.

## When to Use This Skill

Trigger when users mention:
- "Onboard [client name]"
- "Set up project for [client]"
- "Process this client inquiry"
- "New client email from [name]"
- Starting work with a new client (be proactive here!)

## Workflow Overview

The onboarding process follows these steps in sequence:

1. **Extract client details** from email (Gmail)
2. **Create project folder structure** for organization
3. **Generate contract and invoice** from templates
4. **Set up Notion project** with tasks and timeline
5. **Draft welcome email** with next steps

Each step builds on the previous one, so maintain context throughout.

## Step 1: Find and Extract Client Details from Email

### Finding the Email

Ask the user for the client name or email sender, then search Gmail:

```
Search for: from:[client-name] OR [client-email]
```

If multiple emails exist (common for scoping discussions), read the most recent one first. If it references earlier conversations, offer to read the thread.

**Why this matters**: Client projects often involve multiple emails where scope evolves. The latest email usually has the final agreed scope, but earlier messages may contain important context about requirements or budget negotiations.

### Extract These Details

Create a structured summary with:

**Client Information:**
- Company name
- Primary contact name and title
- Email address
- Phone number (if provided)
- Location (city/state)

**Project Details:**
- Project name/description
- Start date (stated or inferred)
- Target completion date or duration
- Hard deadlines (conferences, launches, etc.) - these are critical!

**Scope & Deliverables:**
- List of all deliverables mentioned
- Technology/platform preferences
- Integration requirements
- Any specific features or requirements

**Financial:**
- Total project budget
- Payment terms (e.g., 50% upfront, 50% on completion)
- Payment method preferences

**Timeline Context:**
- Any urgent deadlines
- Events driving the timeline (conferences, product launches)
- Estimated duration mentioned

### Example Extraction Format

```markdown
## Client: Greenline Digital
**Contact:** Marcus Chen, Co-Founder
**Email:** marcus@greenlinedigital.com
**Phone:** (512) 555-0147
**Location:** Austin, TX

## Project: Website Redesign & Automation Setup
**Budget:** $8,500 (50% upfront, 50% completion)
**Timeline:** 6-8 weeks
**Hard Deadline:** March 15, 2026 (conference)

## Deliverables:
- Website redesign (WordPress → Webflow optional)
- Lead capture automation system
- HubSpot CRM integration
- Basic SEO setup
- 30-minute team training

## Key Context:
Current site is 3 years old and doesn't reflect quality of their work.
Chaotic lead management via shared inbox needs automation.
```

Present this summary to the user and ask if anything is missing before proceeding.

## Step 2: Create Project Folder Structure

Create an organized folder structure in the user's mounted workspace:

```
[Client_Name]_[Project_Name]/
├── Contracts/
├── Invoices/
├── Deliverables/
│   ├── Design/
│   ├── Development/
│   ├── Content/
│   └── [Domain-specific folders]
├── Communications/
│   ├── Emails/
│   ├── Meeting_Notes/
│   └── Calls/
├── Assets/
│   ├── Branding/
│   ├── Images/
│   └── Documents/
└── Project_Docs/
```

**Naming convention**: Use underscores and no spaces (e.g., `Greenline_Digital_Website_Redesign`). This ensures compatibility across all systems.

**Why this structure**: Each subfolder has a specific purpose:
- **Contracts/Invoices**: Legal/financial documents separate from project work
- **Deliverables**: Organized by type for easy handoff
- **Communications**: Searchable record of all client interactions
- **Assets**: Client-provided materials in one place
- **Project_Docs**: Internal planning documents (briefs, timelines)

### Create Project Brief

Save the extracted details as a markdown file in `Project_Docs/PROJECT_BRIEF.md`. Include:
- All client information
- Scope of work with checklist format
- Budget breakdown
- Timeline with milestones
- Next steps section

This becomes the single source of truth for the project.

## Step 3: Generate Contract and Invoice

### Locate Templates

Templates should be in the user's workspace. Common locations:
- `Example Templates/`
- `Templates/`
- Root of mounted folder

Look for:
- `contract-template.docx` or similar
- `invoice-template.docx` or similar

If templates aren't found, ask the user where they're located.

### Use the DOCX Skill

**Important**: You MUST use the `docx` skill for this step. Call it with the Skill tool:

```
Skill: docx
Task: Customize contract template with client details
```

The docx skill will:
1. Unpack the template
2. Replace placeholders with actual values
3. Validate and pack the final document

### Contract Customization

Replace these placeholders:
- `[YOUR NAME / BUSINESS NAME]` → Your name
- `[YOUR ADDRESS]` → Your business address
- `[YOUR EMAIL]` → Your email
- `[YOUR PHONE]` → Your phone
- `[CLIENT NAME / COMPANY]` → Client company name
- `[CLIENT ADDRESS]` → Client location
- `[CLIENT EMAIL]` → Client email
- `[CLIENT PHONE]` → Client phone
- `[DATE]` → Current date
- `[PROJECT NAME]` → Project title
- `[DETAILED DESCRIPTION]` → Scope details
- `[START DATE]` → Project start date
- `[END DATE]` → Target completion
- `[LIST OF DELIVERABLES]` → Deliverables list
- `[TOTAL AMOUNT]` → Budget
- `[PAYMENT SCHEDULE]` → Payment terms
- `[PAYMENT METHOD]` → Payment method
- `[STATE/JURISDICTION]` → State for legal jurisdiction

Save as: `[Client_Name]_Service_Agreement.docx` in the Contracts folder.

### Invoice Customization

For the initial deposit invoice:
- `[INV-001]` → `INV-[ClientCode]-001` (e.g., INV-GRN-001)
- `[DATE]` → Current date
- `[DUE DATE]` → "Due Upon Receipt" or specific date
- `[PROJECT NAME]` → Project description
- `[Service/Deliverable]` → "50% Project Deposit - [Project Name]"
- `[RATE]` → Total project amount
- `[AMOUNT]` → 50% of total
- `[SUBTOTAL]` → Deposit amount
- `[TAX AMOUNT]` → Tax if applicable (usually $0)
- `[TOTAL]` → Deposit amount

Save as: `Invoice_[ClientCode]-001_Deposit.docx` in the Invoices folder.

**Note**: Leave placeholders for your bank details, address, and phone - the user will fill these in before sending.

## Step 4: Set Up Notion Project

### Try MCP First, Fall Back to Chrome

**Preferred Method**: Notion MCP (if available)
```
Use Notion MCP tools to:
1. Search for "Projects" database
2. Create new page in database
3. Set properties and content
```

**Fallback Method**: Chrome automation
```
1. Open Notion in Chrome
2. Navigate to Projects database
3. Create project through UI
```

**Why this order**: MCP is faster and more reliable, but Chrome provides visual confirmation and works when MCP isn't configured.

### Project Properties

Set these database properties:
- **Project Name**: `[Client Name] — [Project Description]`
- **Status**: "Not started" (or "🟡 Onboarding")
- **Priority**: "High" if hard deadline, otherwise "Medium"
- **End Date**: Hard deadline or target completion
- **Budget**: Total project amount
- **Client**: Company name
- **Contact**: Name and email

### Project Content

Add this content to the project page:

**About Project Section:**
```markdown
Client: [Company] ([Contact Name], [Email])
Location: [Location]
Budget: [Amount] ([Payment Terms])

Project Overview:
[Brief description of what needs to be done and why]

Deliverables:
• [Deliverable 1]
• [Deliverable 2]
• [etc.]
```

**Action Items Section:**

Create tasks based on deliverables and timeline. Use this pattern:

**Phase 1: Kickoff & Discovery**
- Kickoff meeting with [Contact] ([Date - next week])
- Collect brand assets and content
- [Domain-specific discovery tasks]

**Phase 2: [Main Work Phase]**
- [Design/Development/Implementation tasks]
- [Client review milestones]

**Phase 3: [Integration/Polish Phase]**
- [Technical integration tasks]
- [Testing tasks]

**Phase 4: Launch & Handoff**
- Final client review
- Go-live (before [Hard Deadline] if applicable)
- Training session
- Final invoice

**Why task phases**: Breaking work into phases helps both you and the client see progress. Each phase should have a clear outcome that moves the project forward.

### Timeline Considerations

When setting task dates:
- **Kickoff meeting**: Within 3-5 days of contract signing
- **Phase durations**: Divide total timeline into roughly equal phases
- **Buffer before deadline**: If there's a hard deadline, finish 3-5 days early
- **Client review time**: Always include 3-5 days for client feedback

## Step 5: Draft Welcome Email

Create a professional but warm welcome email in `Communications/Emails/Welcome_Email_Draft.md`.

### Email Structure

**Subject Line**: Clear and action-oriented
```
Examples:
- "Ready to Launch Your New Website — Contract & Next Steps"
- "Let's Get Started on [Project Name]"
- "[Client Name] Project Kickoff — What's Next"
```

**Opening**: Warm and enthusiastic
```
Express excitement about working together.
Reference their specific situation (e.g., conference deadline, current pain point).
```

**What's Attached Section**: Clear list
```
1. Service Agreement
2. Invoice for deposit
```

**Next Steps Section**: Organized and actionable
```
1. Kickoff Meeting — [Proposed timeframe]
   - Include what will be covered
   - Ask for their availability

2. Asset Collection — [What you need]
   - List specific items
   - Note that these can be gathered during kickoff

3. Timeline Overview
   - Phase breakdown
   - Key milestone dates
   - Emphasize meeting their deadline
```

**Expectations Section**: Set the tone
```
What they can expect from you:
- Communication style
- Update frequency
- Approach to collaboration
- Commitment to timeline
```

**Closing**: Professional but personable
```
Invitation to ask questions
Next action (send back availability for kickoff)
Positive closing statement
```

**P.S.**: Personal touch
```
Reference something specific from their email that shows you understand their needs.
```

### Email Tone Guidelines

- **Be professional but warm**: You're starting a relationship, not just a transaction
- **Be specific**: Use their actual project details, not generic language
- **Be proactive**: Anticipate what they need to know
- **Be reassuring**: They hired you to solve a problem; show you understand it

### Example Email Structure

```markdown
# Welcome Email Draft

**To:** [Contact] <[email]>
**From:** [Your name] <[your email]>
**Subject:** Ready to Launch [Project Name] — Contract & Next Steps
**Attachments:**
- [Client]_Service_Agreement.docx
- Invoice_[Code]-001_Deposit.docx

---

Hi [First Name],

Great to have you officially on board! I'm excited to help [Company] [achieve specific goal they mentioned].

## What's Attached

[List documents]

## Next Steps

**1. Kickoff Meeting — [Timeframe]**
[What will be covered]
[Request their availability]

**2. Asset Collection**
[List what you need]

**3. Timeline Overview**
[Phase breakdown with dates]

## What You Can Expect From Me

[Communication style and commitments]

[Invitation to ask questions]
[Next action]

Best,
[Your name]

---

P.S. — [Personal touch referencing their specific situation]
```

Save this as a draft - don't send it. The user will review and send when ready.

## Error Handling

### Gmail Not Available
If Gmail tools aren't accessible, ask the user to:
1. Paste the email content directly, or
2. Provide the key details manually

Then proceed with remaining steps.

### Templates Not Found
If document templates aren't in expected locations:
1. Ask where they're located
2. If none exist, offer to create basic templates
3. Or proceed without documents and note in project brief

### Notion Connection Issues
If both MCP and Chrome fail:
1. Create a detailed Notion setup document
2. Save it in Project_Docs/
3. Provide copy-paste ready content
4. Inform user they'll need to set up manually

### Missing Information
If critical details are missing from the email:
1. Note what's missing in the project brief
2. Add to the "Questions for kickoff meeting" section
3. Mention these in the welcome email draft
4. Continue with available information

## Quality Checks

Before completing, verify:

**✓ Folder Structure**
- All folders created in correct location
- Naming is consistent (no spaces)

**✓ Documents**
- Contract has all placeholders replaced
- Invoice calculates correctly (50% of total)
- Both documents saved in correct folders
- Filenames follow convention

**✓ Notion Project**
- All properties set correctly
- Hard deadline noted if applicable
- Tasks follow logical sequence
- Kickoff meeting task is for next week

**✓ Welcome Email**
- Uses actual client details (not placeholders)
- Timeline matches project setup
- Attachments list matches created documents
- Tone is appropriate for client relationship

**✓ Project Brief**
- All extracted details included
- Budget breakdown is clear
- Deliverables list is complete
- Next steps are actionable

## Completion Summary

When finished, provide the user with:

1. **Project folder path** with link to Project_Docs/PROJECT_BRIEF.md
2. **Contract path** with link
3. **Invoice path** with link
4. **Notion project confirmation** (link if Chrome was used)
5. **Welcome email path** with link

Use this format:

```markdown
## ✅ Client Onboarding Complete: [Client Name]

**Project Folder:** [View Project Brief](computer://path)

**Documents Created:**
- [View Contract](computer://path)
- [View Invoice](computer://path)

**Notion Project:** Set up with [X] tasks, priority [level], deadline [date]

**Welcome Email:** [View Draft](computer://path)

**Next Actions for You:**
1. Review and customize welcome email
2. Add your bank details to invoice
3. Send contract and invoice to client
4. Schedule kickoff meeting
```

## Tips for Success

**Be thorough with email extraction**: Missing details now means delays later. If unsure about scope or budget, flag it clearly.

**Maintain folder organization**: Consistent structure across all clients makes your life easier. Resist the urge to customize per project.

**Set realistic timelines**: When creating Notion tasks, pad estimates by 20% for client feedback and unexpected issues.

**Use task phases**: Phases help you and the client understand progress. Each phase should have a clear deliverable.

**Personalize the welcome email**: Generic emails feel transactional. Reference their specific situation to show you understand their needs.

**Flag hard deadlines**: Conference dates, product launches, etc. are immovable. Make these highly visible in Notion and timeline overviews.

## Customization Points

Users may have different:
- **Template locations**: Ask if not in default location
- **Folder preferences**: Some add Proposals/ or Testing/ folders
- **Payment terms**: Not always 50/50 split
- **Notion properties**: Some track different fields

Be flexible and ask when you encounter variations from the standard workflow.
