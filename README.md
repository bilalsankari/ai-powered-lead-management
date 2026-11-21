# AI-Powered Lead Management System

An AI-assisted lead intake and CRM automation built with **n8n**, **OpenAI**, and **HubSpot**.

The workflow receives lead submissions, validates the data, checks for an existing HubSpot contact, uses AI to score and classify the lead, updates CRM properties, and triggers the appropriate sales follow-up actions.

> **Project status: Functionally tested.** On November 21, 2026, the working n8n workflow was tested with invalid input, a repeated email address, and Hot, Warm, and Cold lead scenarios. Email notifications and HubSpot tasks were confirmed; the Hot path also created a HubSpot deal. This is a local test result, not a claim of production deployment.

## What it does

1. **Lead intake** — accepts lead details through an n8n webhook.
2. **Validation** — checks required information and rejects invalid email addresses or incomplete data.
3. **Duplicate protection** — searches HubSpot by email and updates an existing contact rather than creating a second contact.
4. **AI qualification** — evaluates budget, buying intent, clarity, and urgency, then returns a score and sales recommendation.
5. **CRM enrichment** — stores lead score, priority, AI reasoning, and recommended action in HubSpot.
6. **Automated routing** — sends leads into Hot, Warm, or Cold follow-up paths.
7. **Sales actions** — sends Gmail notifications and creates HubSpot tasks; the Hot path also creates a deal.

## Lead scoring

| Category | Score | Automated actions |
|---|---:|---|
| Hot | 80–100 | Gmail notification, high-priority HubSpot task, HubSpot deal |
| Warm | 50–79 | Gmail notification, medium-priority HubSpot task |
| Cold | 0–49 | Gmail notification, low-priority HubSpot task |

## Technology stack

- **Workflow automation:** n8n
- **AI:** OpenAI API
- **CRM:** HubSpot
- **Notifications:** Gmail
- **Logic and transformation:** JavaScript
- **Integration:** REST APIs, JSON, webhooks

## Architecture

```text
Lead Webhook
    |
    v
Normalize & Validate
    |
    v
Search HubSpot by Email
    |
    v
Create / Update Contact
    |
    v
OpenAI Lead Evaluation
    |
    v
Update HubSpot Properties
    |
    v
Hot / Warm / Cold Routing
    |          |          |
    v          v          v
Hot actions  Warm actions Cold actions
Deal + task  Email + task Email + task
+ email
```

See [Architecture](docs/architecture.md) for more detail.

## Repository contents

- [n8n workflow export](workflows/ai-powered-lead-management.json)
- [Setup and import guide](docs/setup.md)
- [Architecture notes](docs/architecture.md)
- [Test results](docs/testing-results.md)

### Important note about the workflow export

The JSON currently in `workflows/` is an earlier sanitized snapshot and does **not** include all fixes made and verified in the live n8n workflow on November 21, 2026. Use it as a structural reference only, not as the latest tested import. A fresh export from the current n8n workflow is still needed before this repository can offer a fully reproducible import.

## Security

The public workflow snapshot has had credential references, webhook IDs, instance metadata, and personal notification addresses removed. Configure your own credentials inside n8n.

Never commit API keys, access tokens, passwords, private webhook URLs, customer records, or personal notification addresses. Use synthetic data in public examples.

## Production considerations

The workflow has passed the functional scenarios documented in [Test Results](docs/testing-results.md), but it has not been represented as a production deployment. Before using it with real customers, add/verify error handling, retries, monitoring, secure hosting, data-retention practices, and deployment reliability.

## Author

**Bilal Sankari** — AI Automation Developer

- GitHub: [@bilalsankari](https://github.com/bilalsankari)
