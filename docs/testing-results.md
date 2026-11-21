# Functional Test Results

**Test date:** November 21, 2026  
**Environment:** Developer's local n8n instance connected to HubSpot, OpenAI, and Gmail  
**Test data:** Synthetic lead records

These results are based on the developer's reported execution results in the working n8n instance. They are functional checks, not a third-party audit or a production deployment certification.

## Results

| Scenario | Expected result | Observed result | Status |
|---|---|---|---|
| Invalid email | Validation rejects the lead and routes it away from CRM processing | Invalid email was marked invalid and sent to the false output | Passed |
| Duplicate email | Existing HubSpot contact is updated; no second contact is created | One contact remained after submitting the same email again | Passed |
| Hot lead (80–100) | Hot notification, high-priority task, and HubSpot deal | Hot lead created; notification received; task and deal created | Passed |
| Warm lead (50–79) | Warm notification and medium-priority task | Warm lead created; notification and task succeeded after correcting the task contact-ID mapping | Passed |
| Cold lead (0–49) | Cold notification and low-priority task | Cold lead created and branch completed successfully | Passed |

## Notes

- The duplicate test intentionally created another follow-up email and task; the important duplicate-protection result was that HubSpot still contained only one contact for the repeated email.
- The Warm task initially failed because it referenced the initial search response for the contact ID. The mapping was changed to use the contact returned by `Create or Update a Contact`, with `id` preferred and `vid` as fallback. The Warm task then succeeded.
- The Hot path was confirmed to create a HubSpot deal as well as its notification and task.
- These tests do not establish uptime, security hardening, retry behavior, load capacity, or production readiness.

## Remaining release tasks

- Export the latest working n8n workflow and replace the older sanitized snapshot in `workflows/`.
- Review the fresh export for secrets and personal data before committing.
- Add or verify error handling, retries, and monitoring before production use.
