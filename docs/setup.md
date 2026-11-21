# Setup and Import Guide

## Status

This is a sanitized development export of the AI-Powered Lead Management System. It is shared for portfolio review and learning. It has **not** been re-tested end-to-end after export and should not be treated as production-ready.

## Requirements

- A working n8n instance
- HubSpot private app/service-key credentials with access to CRM contacts, deals, and tasks
- OpenAI credentials for the model node
- Gmail credentials if email notifications are enabled
- The HubSpot contact properties listed below

## Import

1. In n8n, open **Workflows** and choose **Import from File**.
2. Select `workflows/ai-powered-lead-management.json`.
3. Open each HubSpot, OpenAI, and Gmail node and select your own credentials.
4. In both Gmail nodes, replace `REPLACE_WITH_YOUR_NOTIFICATION_EMAIL@example.com` with your own notification address.
5. Save the workflow.
6. Review the known limitations below and test with synthetic data before activating it.

Credential references and the original webhook ID have been removed from this public export. You must reconnect credentials and n8n will generate a webhook identifier for the imported workflow.

## Webhook input

The webhook uses `POST` and the path `lead-intake`.

Example JSON payload:

```json
{
  "name": "Jordan Example",
  "email": "jordan@example.com",
  "phone": "+15550102020",
  "company": "Example Company",
  "service_needed": "Website redesign",
  "budget": 5000,
  "project_timeline": "Within 30 days",
  "message": "We need a new website for our business.",
  "source": "website"
}
```

The workflow also includes a **When Executed by Another Workflow** trigger for use as a sub-workflow. Its defined inputs include name, email, phone, company, service_needed, budget, message, and source.

## HubSpot properties

Create or verify these contact properties in HubSpot before testing the update step. Use the exact internal names:

- `lead_score`
- `lead_priority`
- `ai_reason`
- `recommended_action`
- `ai_lead_status`

The contact search and upsert nodes also use standard contact fields such as email, firstname, lastname, phone, and company.

## Known limitations to resolve before production

These findings come from a static review of the exported JSON, not a live execution:

1. **Webhook field mapping:** the `Edit Fields` node reads `$json.name`, `$json.email`, etc. Confirm whether your webhook trigger supplies these at the root or under `$json.body`. If under `body`, update the mappings so webhook and sub-workflow inputs are both handled correctly.
2. **Task association for new contacts:** the hot/cold task nodes refer to `HTTP Request` search results for the contact ID. When the contact search returns no match, `results[0].id` will not exist. Use the ID returned by the create/update contact node for task associations, after confirming its actual output shape.
3. **HubSpot contact ID field:** two nodes reference `.vid` from the HubSpot contact node. Confirm the current node output; if it returns `id` rather than `vid`, update both expressions.
4. **Warm-lead handling:** the score branch for warm leads currently has no downstream action. Add the intended notification or follow-up action if warm leads should be processed.
5. **Duplicate-check branches:** both outputs of `Contact Exists?` currently connect to the same create/update contact node. This may be intentional upsert behavior, but the condition is currently redundant.

## Safe testing checklist

- Use synthetic test contacts and a test HubSpot portal where possible.
- Test an invalid email and missing required fields.
- Test an existing contact and a brand-new contact.
- Test scores in each range: hot (80–100), warm (50–79), and cold (0–49).
- Confirm contact properties, deal/task associations, and email destinations.
- Do not use real customer data in public examples or pinned execution data.

## Security

Never commit API keys, access tokens, private customer records, personal notification addresses, or production webhook URLs. Store credentials in n8n's credential manager, not in workflow JSON or source control.
