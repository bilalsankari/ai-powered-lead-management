# Functional Test Report

**Project:** AI-Powered Lead Management System  
**Test date:** November 21, 2026  
**Environment:** Developer's self-hosted n8n instance integrated with Telegram, OpenAI, HubSpot, and Gmail  
**Test scope:** Lead-management workflow and Telegram AI Sales Agent handoff, using synthetic test lead data  
**Result:** 6 functional scenarios reported as passed

## 1. Test summary

| Test ID | Scenario | Expected behavior | Observed result | Status |
|---|---|---|---|---|
| TC-01 | Invalid email validation | Reject the lead and prevent CRM processing | The invalid email was rejected and routed to the validation IF node's false output | **PASS** |
| TC-02 | Duplicate contact protection | Find the existing HubSpot contact instead of creating a second contact | After submitting the same email again, HubSpot still contained one contact for that email | **PASS** |
| TC-03 | Hot lead routing | For a score of 80–100, send the Hot notification, create a HubSpot task, and create a deal | The Hot lead path completed; the email was received and the HubSpot task and deal were created | **PASS** |
| TC-04 | Warm lead routing | For a score of 50–79, send the Warm notification and create a HubSpot task | The Warm email and task were successfully created after correcting the task's contact-ID mapping | **PASS** |
| TC-05 | Cold lead routing | For a score below 50, send the Cold notification and create a HubSpot task | The Cold lead path completed successfully | **PASS** |
| TC-06 | Telegram-to-CRM end-to-end handoff | Collect lead details in Telegram, submit them through the AI Sales Agent's workflow tool, process the lead, and create the expected CRM and notification records | The developer reported that the test contact appeared in HubSpot with AI scoring and priority, a Gmail notification was received, and a HubSpot task and deal were created | **PASS** |

These outcomes are based on the developer's reported results from executions in the working n8n environment and manual checks in HubSpot and Gmail. They are functional test results, not an independent audit. No raw execution IDs, screenshots, or timestamped logs are included in this report.

## 2. Test details

### TC-01 — Invalid email validation

- **Purpose:** Confirm that invalid lead data does not proceed into CRM processing.
- **Test input:** A synthetic lead with an invalid email value: `invalid-email`.
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

### TC-06 — Telegram AI Sales Agent end-to-end integration

- **Purpose:** Verify that a customer conversation in Telegram can trigger the downstream lead-management workflow and produce CRM and notification records.
- **Test lead:** Synthetic customer profile for Daniel Carter, Carter Digital.
- **Lead details supplied in the conversation:**
  - **Email:** `daniel.carter.test@example.com`
  - **Phone:** `+96170111222`
  - **Service:** Professional business website
  - **Requirements:** Five pages, contact form, and WhatsApp integration
  - **Budget:** 8,000
  - **Timeline:** One month
  - **Source:** Telegram
- **Test method:** The customer shared the project requirements in Telegram. The AI Sales Agent summarized the information, asked for the missing project timeline, and continued the lead-collection conversation. The developer then reported the downstream submission and checked the resulting records.
- **Expected:** The lead is submitted through the AI Sales Agent's **Submit Qualified Lead** tool to the **AI-Powered Lead Management System**. The downstream workflow validates and processes the lead, updates HubSpot with AI qualification data, sends the relevant Gmail notification, and creates the appropriate HubSpot follow-up task and deal when the lead qualifies for the Hot path.
- **Observed (developer-reported):**
  - Daniel Carter appeared as a contact in HubSpot.
  - AI lead score and priority were present.
  - Gmail notification was received.
  - HubSpot follow-up task was created.
  - HubSpot deal was created.
- **Result:** PASS, based on the developer's manual verification of the resulting records.

**Evidence limitation:** This result is based on the developer's report of the Telegram conversation and downstream records. Raw n8n execution logs, execution IDs, screenshots, and the exact AI-generated score are not attached here.

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
- Telegram AI Sales Agent handoff was reported successful, with the resulting HubSpot contact, AI qualification properties, Gmail notification, task, and deal manually verified.

### Not yet evidenced in this report

- Raw n8n execution exports, screenshots, or timestamped logs for each test.
- Exact AI-generated scores and model responses for each sample lead.
- Load, concurrency, retry, uptime, security, and production-deployment testing.
- Independent verification of the Telegram handoff outside the developer's reported test and manual record checks.

The Telegram AI Sales Agent and lead-management workflow are separate workflows connected through the **Submit Qualified Lead** workflow tool. TC-06 records the developer-reported end-to-end test of this integration; the earlier webhook tests alone would not establish that handoff.

## 5. Overall conclusion

The lead-management workflow has passed the five functional scenarios documented above, and the Telegram-to-CRM integration has additionally passed one developer-reported end-to-end functional test. The combined tests cover input validation, duplicate-contact protection, AI-based lead routing, Telegram lead intake, HubSpot contact updates, task/deal creation, and Gmail notifications.

**This project is functionally tested in the developer's environment, but it is not represented as production-certified or independently audited.**

## 6. Recommended next evidence to collect

For a stronger public portfolio, capture and retain the following when available:

1. A successful n8n execution showing the Telegram-triggered AI Sales Agent and its **Submit Qualified Lead** tool call.
2. The corresponding successful execution of the lead-management workflow.
3. A HubSpot contact record showing the AI qualification properties, with personal details hidden.
4. The test lead's HubSpot deal and task.
5. The received Gmail notification, with private information hidden.

Before publishing evidence, hide API credentials, access tokens, private webhook URLs, personal email addresses, phone numbers, and customer-identifying information.
