# Architecture

## Overview

The AI-Powered Lead Management System is an n8n workflow that automates lead intake, validation, CRM contact handling, AI qualification, and sales follow-up.

## Processing flow

1. **Webhook intake** receives a JSON lead submission.
2. **Edit Fields / normalization** maps incoming values into a consistent structure.
3. **Validate Lead** checks the name, email, phone, and budget. Invalid submissions are stopped before CRM processing.
4. **HubSpot contact search** searches by email.
5. **Contact Exists? / Create or Update a Contact** ensures the lead is represented by one CRM contact. A repeated email updates the existing record rather than creating a duplicate.
6. **OpenAI evaluation** assesses budget, buying intent, clarity, and urgency. The model returns a numeric score, priority, reason, and recommended action.
7. **CRM update** stores qualification information on the HubSpot contact.
8. **Lead routing** separates the lead into Hot, Warm, or Cold follow-up paths.

## Scoring and actions

| Category | Condition | Actions |
|---|---|---|
| Hot | Score >= 80 | Create HubSpot deal, send Gmail notification, create high-priority HubSpot task |
| Warm | Score >= 50 and < 80 | Send Gmail notification, create medium-priority HubSpot task |
| Cold | Score < 50 | Send Gmail notification, create low-priority HubSpot task |

## Integrations

- **n8n:** workflow orchestration and JavaScript transformations
- **OpenAI API:** AI-assisted qualification
- **HubSpot CRM:** contacts, custom qualification properties, deals, and tasks
- **Gmail:** internal sales notifications
- **Webhook / REST API:** external lead intake and service communication

## Data contract

The webhook accepts these fields:

- `name`
- `email`
- `phone`
- `company`
- `service_needed`
- `budget`
- `message`
- `source`
- `project_timeline`

This workflow includes both a Webhook trigger and a When Executed by Another Workflow trigger. The current Edit Fields expressions in the supplied export read most values from `$json.body.*`, which matches webhook payloads but not the usual top-level input fields from an Execute Workflow trigger. Therefore, the sub-workflow trigger path needs a separate mapping fix and test before it can be described as verified. There are no Telegram nodes in this workflow export.

## Test evidence

The working local workflow was functionally tested on November 21, 2026. The scenarios and observed results are recorded in [Test Results](testing-results.md).

## Security and reliability

- Store credentials in n8n's credential manager.
- Do not expose private webhook URLs or API tokens.
- Use least-privilege HubSpot credentials.
- Keep test/customer data out of public source control.
- Add error branches, retries, and execution monitoring before production use.
