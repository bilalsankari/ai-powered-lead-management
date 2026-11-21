# Functional Test Report

**Project:** AI-Powered Lead Management System  
**Test date:** November 21, 2026  
**Environment:** Developer's self-hosted n8n instance integrated with HubSpot, OpenAI, and Gmail  
**Test scope:** Lead-management workflow, using synthetic test lead data  
**Result:** 5 functional scenarios reported as passed

## 1. Test summary

| Test ID | Scenario | Expected behavior | Observed result | Status |
|---|---|---|---|---|
| TC-01 | Invalid email validation | Reject the lead and prevent CRM processing | The invalid email was rejected and routed to the validation IF node's false output | **PASS** |
| TC-02 | Duplicate contact protection | Find the existing HubSpot contact instead of creating a second contact | After submitting the same email again, HubSpot still contained one contact for that email | **PASS** |
| TC-03 | Hot lead routing | For a score of 80–100, send the Hot notification, create a HubSpot task, and create a deal | The Hot lead path completed; the email was received and the HubSpot task and deal were created | **PASS** |
| TC-04 | Warm lead routing | For a score of 50–79, send the Warm notification and create a HubSpot task | The Warm email and task were successfully created after correcting the task's contact-ID mapping | **PASS** |
| TC-05 | Cold lead routing | For a score below 50, send the Cold notification and create a HubSpot task | The Cold lead path completed successfully | **PASS** |

These outcomes are based on the developer's reported results from executions in the working n8n environment. They are functional test results, not an independent audit.

## 2. Test details

### TC-01 — Invalid email validation

- **Purpose:** Confirm that invalid lead data does not proceed into CRM processing.
- **Test input:** A synthetic lead with an invalid email value: invalid-email.
- **Expected:** The validation logic sets the lead as invalid and the workflow follows the false output of the validation IF node.
- **Observed:** The lead went to the false output.
- **Result:** PASS.

### TC-02 — Duplicate contact protection

- **Purpose:** Avoid creating multiple HubSpot contacts for the same email address.
- **Test method:** Submit a lead, then submit another lead using the same email address.
- **Expected:** The HubSpot search finds the existing contact and the workflow updates that contact rather than creating another one.
- **Observed:** HubSpot contained one contact for the repeated email after the second submission.
- **Result:** PASS.

### TC-03 — Hot lead routing

- **Purpose:** Verify the highest-priority sales path.
- **Expected:** A Hot lead (score 80–100) receives a notification, creates a HubSpot task, and creates a HubSpot deal.
- **Observed:** The Hot lead was processed; the email was received, and both the task and deal appeared in HubSpot.
- **Result:** PASS.

### TC-04 — Warm lead routing

- **Purpose:** Verify the middle-priority sales path.
- **Expected:** A Warm lead (score 50–79) receives a notification and creates a HubSpot task.
- **Observed:** The Warm email and HubSpot task were successfully created after a contact-ID mapping issue was corrected.
- **Result:** PASS.

### TC-05 — Cold lead routing

- **Purpose:** Verify the low-priority sales path.
- **Expected:** A Cold lead (score below 50) receives a notification and creates a HubSpot task.
- **Observed:** The Cold lead was processed successfully.
- **Result:** PASS.

## 3. Defect found and correction

During Warm lead testing, the HubSpot task creation request initially used an incorrect contact-ID reference. The task request was updated to use the contact returned by the **Create or Update a Contact** node, preferring its ID and retaining the VID fallback where applicable.

After the mapping correction, the Warm task was created successfully.

This documents an actual debugging cycle: reproduce the issue, trace the incorrect field mapping, correct it, and rerun the scenario.

## 4. Test coverage and limitations

### Confirmed in the reported test runs

- Invalid email is rejected by validation.
- Repeated email does not create a second HubSpot contact.
- Hot routing creates a notification, task, and deal.
- Warm routing creates a notification and task after the mapping fix.
- Cold routing completes its notification and task path.

### Not yet evidenced in this report

- An independently verified end-to-end Telegram conversation through the AI Sales Agent and its downstream workflow handoff.
- Raw n8n execution exports, screenshots, or timestamped logs for each test.
- Exact AI-generated scores and model responses for each sample lead.
- Load, concurrency, retry, uptime, security, and production-deployment testing.

The Telegram AI Sales Agent and lead-management workflow are separate workflows. Successful webhook tests of the lead-management workflow should not be presented as proof that the Telegram-to-subworkflow handoff has also passed an end-to-end test.

## 5. Overall conclusion

The lead-management workflow has passed the five functional scenarios documented above in the developer's test environment. The tests cover input validation, duplicate-contact protection, AI-based lead routing, HubSpot task/deal creation, and Gmail notifications.

**This project is functionally tested, but it is not represented as production-certified or independently audited.**

## 6. Recommended next evidence to collect

For a stronger public portfolio, capture and retain the following when available:

1. A successful n8n execution showing the validation and CRM path.
2. A HubSpot contact record showing the AI qualification properties.
3. A Hot lead's HubSpot deal and task.
4. A received Gmail notification with personal details hidden.
5. A Telegram conversation and corresponding downstream execution, once the handoff has been independently tested.

Before publishing evidence, hide API credentials, access tokens, private webhook URLs, email addresses, phone numbers, and customer-identifying information.
