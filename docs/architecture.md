# Architecture Notes

## Current design

The project is orchestrated in n8n. A webhook receives lead details, a validation step normalizes fields and checks required data, and HubSpot is queried by email to identify an existing contact. An AI evaluation step generates a lead score and priority for the CRM record.

## Data contract

Expected lead fields:

- `name`
- `email`
- `company`
- `service_needed`
- `budget`
- `message`
- `source`

Validation and CRM property mappings should be confirmed against the current n8n workflow before importing or deploying an export.

## Integration boundaries

- n8n handles orchestration and transformation.
- HubSpot stores contact and qualification data.
- OpenAI is used for AI-assisted evaluation.

## Safety and privacy

Use synthetic test data in this public repository. Store credentials in n8n's credential manager or a secure deployment environment. Do not commit customer records, access tokens, webhook URLs containing secrets, or raw credential exports.

## Implementation status

This document describes the current intended design, not a guarantee that every planned branch is complete. The workflow export and detailed deployment instructions will be added after a security and functionality review.
