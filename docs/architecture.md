# Architecture

## Complete system overview

The solution is made up of two connected n8n workflows:

1. **AI Sales Agent** — the customer-facing conversational workflow. A prospect starts the conversation through Telegram. The AI agent gathers the lead details and, when ready, calls the **Submit Qualified Lead** workflow tool.
2. **AI-Powered Lead Management System** — the downstream workflow that validates the submitted data, checks HubSpot for an existing contact, performs AI lead scoring, updates CRM properties, and creates the appropriate follow-up actions.

The lead-management workflow also exposes an HTTP Webhook trigger for direct lead intake and independent testing. Therefore, Telegram is the entry point for the complete conversational solution, while the webhook is an alternative entry point into the lead-management component.

## End-to-end flow

```text
Prospect
   |
   v
Telegram
   |
   v
AI Sales Agent (n8n)
   |
   | gathers lead details
   v
Submit Qualified Lead (workflow tool)
   |
   v
Lead Management workflow
   |
   +---- Webhook trigger (alternative direct intake)
   |
   v
Normalize / Validate
   |
   v
HubSpot contact search by email
   |
   v
Create or update contact
   |
   v
OpenAI lead evaluation
   |
   v
Update HubSpot qualification properties
   |
   v
Hot / Warm / Cold routing
   |             |             |
   v             v             v
Hot actions   Warm actions   Cold actions
Deal + task   Email + task   Email + task
+ email
```

## Lead-management processing flow

1. **Intake** receives lead details from the AI Sales Agent's workflow tool or from the direct webhook.
2. **Normalization** maps incoming values into a consistent structure.
3. **Validate Lead** checks the name, email, phone, and budget. Invalid submissions stop before CRM processing.
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

- **Telegram:** prospect-facing chat channel for the AI Sales Agent
- **n8n:** agent orchestration, workflow execution, and JavaScript transformations
- **OpenAI API:** conversational intelligence and lead qualification
- **HubSpot CRM:** contacts, custom qualification properties, deals, and tasks
- **Gmail:** internal sales notifications
- **Webhook / REST API:** alternative direct lead intake and service communication

## Lead data contract

The lead-management workflow uses these fields where supplied:

- `name`
- `email`
- `phone`
- `company`
- `service_needed`
- `budget`
- `message`
- `source`
- `project_timeline`

The webhook receives values in the request body. The AI Sales Agent's workflow tool passes defined workflow inputs to the downstream workflow. These trigger shapes are different, so the lead-management workflow's normalization/mapping must handle both input structures. The webhook path has been functionally tested; the sub-workflow handoff should be separately verified before claiming that every trigger path is tested.

## Workflow export scope

The file `workflows/ai-powered-lead-management.json` is an export of the **lead-management component only**. It does not contain the Telegram trigger or the AI Sales Agent nodes because those belong to the separate upstream workflow. A complete reproducible project should include sanitized exports of both workflows.

## Test evidence

The lead-management webhook path was functionally tested on November 21, 2026. The scenarios and observed results are recorded in [Test Results](testing-results.md). Do not treat those webhook tests as proof that the Telegram-to-subworkflow handoff was independently tested.

## Security and reliability

- Store credentials in n8n's credential manager.
- Do not expose private webhook URLs or API tokens.
- Use least-privilege HubSpot credentials.
- Keep test/customer data out of public source control.
- Add error branches, retries, and execution monitoring before production use.
