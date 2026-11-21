# AI-Powered Lead Management System

An AI-assisted lead intake and CRM automation project built with **n8n**, **OpenAI**, and **HubSpot**.

The goal is to help businesses process incoming leads more consistently by validating contact data, checking for existing contacts, evaluating lead quality, and keeping CRM records organized.

> **Project status: In development.** A sanitized workflow export and setup guide are included for review and testing. The export has not been re-tested end-to-end after sanitization and is not production-ready.

## Overview

The current workflow export includes:

1. Lead intake through an n8n webhook or sub-workflow trigger.
2. Lead field normalization and validation.
3. HubSpot contact search by email.
4. Contact create/update handling.
5. AI-based lead scoring and priority classification.
6. HubSpot contact-property updates.
7. Hot/cold email notifications, task creation, and hot-lead deal creation.

Some branches and CRM ID mappings still require fixes; see [Known Limitations](docs/setup.md#known-limitations-to-resolve-before-production).

## Technology Stack

| Technology | Role |
|---|---|
| n8n | Workflow orchestration |
| OpenAI API | AI-assisted lead evaluation |
| HubSpot CRM | Contacts, deals, tasks, and qualification properties |
| JavaScript | Data normalization and validation |
| REST APIs / JSON | Service integrations and data exchange |
| Webhooks | Lead intake |

## Architecture

```text
Webhook / Sub-workflow Trigger
          |
          v
     Edit Fields
          |
          v
   Validate Lead
          |
          v
   Search HubSpot
          |
          v
 Create / Update Contact
          |
          v
   OpenAI Evaluation
          |
          v
 Update CRM Properties
          |
          v
 Hot / Warm / Cold Routing
```

This diagram represents the intended processing path. See the setup guide for limitations in the current export.

## Repository Contents

- [Sanitized n8n workflow export](workflows/ai-powered-lead-management.json)
- [Import, configuration, and testing guide](docs/setup.md)
- [Architecture notes](docs/architecture.md)

## Quick Start

1. Import the JSON file into n8n.
2. Reconnect your own HubSpot, OpenAI, and Gmail credentials.
3. Replace the notification email placeholder in both Gmail nodes.
4. Create or verify the required HubSpot contact properties.
5. Review all known limitations and test with synthetic leads before activation.

See [docs/setup.md](docs/setup.md) for complete instructions and a sample webhook payload.

## Security

The public export has had credential references, webhook IDs, instance metadata, and the original notification email removed. Credentials must be configured inside n8n.

Never commit API keys, access tokens, passwords, private webhook URLs, customer records, or personal notification addresses. Use synthetic data in public examples.

## Disclaimer

This is a portfolio and learning project under active development. It is not presented as a production-ready CRM integration. Validate the workflow, API permissions, error handling, and data-protection requirements before using it with real customer data.

## Author

**Bilal Sankari**  
AI Automation Developer

- GitHub: [@bilalsankari](https://github.com/bilalsankari)
