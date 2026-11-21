# AI-Powered Lead Management System

An AI-assisted lead intake and CRM automation project built with **n8n**, **OpenAI**, and **HubSpot**.

The goal is to help businesses process incoming leads more consistently by validating contact data, checking for existing contacts, evaluating lead quality, and keeping CRM records organized.

> **Project status: In development.** This repository documents the project and its current design. It does not yet contain a reviewed, sanitized n8n workflow export.

## Overview

The workflow is designed around this lead-processing sequence:

1. **Receive a lead** through an n8n webhook.
2. **Normalize and validate** the submitted fields, including the email address and budget.
3. **Check for duplicates** in HubSpot using the email address.
4. **Create or update CRM information** according to the duplicate-check result.
5. **Use an AI evaluation step** to produce a lead score, priority, and supporting rationale.
6. **Store relevant qualification details** in HubSpot contact properties.

The implementation is being developed and tested incrementally. Exact behavior may change as the workflow is refined.

## Technology Stack

| Technology | Role |
|---|---|
| n8n | Workflow orchestration |
| OpenAI API | AI-assisted lead evaluation |
| HubSpot CRM | Contact records and qualification properties |
| JavaScript | Data normalization and validation |
| REST APIs / JSON | Service integrations and data exchange |
| Webhooks | Lead intake |

## Planned Architecture

```text
Lead Source
    |
    v
n8n Webhook
    |
    v
Normalize & Validate Lead
    |
    v
Search HubSpot for Existing Contact
    |
    v
AI Lead Evaluation
    |
    v
Create / Update HubSpot Contact
    |
    v
Save Lead Score, Priority & AI Rationale
```

This diagram describes the intended project flow; implementation details are subject to change.

## Example Lead Data

A lead submission may contain fields such as:

| Field | Description |
|---|---|
| name | Contact's full name |
| email | Contact email used for duplicate matching |
| company | Company or organization |
| service_needed | Requested service |
| budget | Estimated budget |
| message | Additional context from the lead |
| source | Where the lead originated |

Do not use real customer information in public examples. Use synthetic data when testing or documenting this project.

## Configuration

The workflow is intended to use credentials configured securely inside n8n:

- HubSpot private app access token
- OpenAI API key

**Never commit API keys, access tokens, passwords, webhook secrets, customer records, or credential exports.** Use n8n credentials or environment variables for secrets. Replace any exposed credential immediately.

## Current Development Focus

- [x] Receive lead data through a webhook
- [x] Normalize and validate incoming lead fields
- [x] Search HubSpot contacts by email to detect duplicates
- [x] Generate an AI-based lead score and priority in development testing
- [ ] Finalize reliable create/update behavior for existing and new contacts
- [ ] Add clear error handling and execution logging
- [ ] Test edge cases with synthetic leads
- [ ] Publish a sanitized workflow export and setup guide

## Repository Contents

The repository currently contains project documentation. A sanitized n8n workflow export and reproducible setup instructions will be added after they have been reviewed to remove credentials, private URLs, and personal/customer data.

## Disclaimer

This is a portfolio and learning project under active development. It is not presented as a production-ready CRM integration. Validate the workflow, API permissions, error handling, and data-protection requirements before using it with real customer data.

## Author

**Bilal Sankari**  
AI Automation Developer

- GitHub: [@bilalsankari](https://github.com/bilalsankari)
