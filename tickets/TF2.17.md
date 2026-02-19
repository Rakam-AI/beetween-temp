# TF2.17 - Implement Frontend E2E Tests


**Depends on:** TF2.16, all TF2.x view tickets
**Blocks:** None (final frontend quality gate)

**Context:**
End-to-end tests validate the complete user journeys through the application, ensuring all views, interactions, and flows work together correctly.

Reference: All specification documents for expected behavior

**Objective:**
Implement E2E test scenarios covering the main user journeys.

**Instructions:**

1. **Set up E2E testing infrastructure:**
   - Install and configure Playwright or Cypress
   - Configure to run against the dev server with mock services enabled
   - Set up test fixtures and helpers

2. **Scenario 1: Workflow Lifecycle (Admin)**
   - Login as admin user
   - Navigate to Workflows list
   - Create a new workflow:
     - Fill in name and description
     - Add 3 validation steps with validators
     - Configure validation modes (ONE and ALL)
     - Save as draft
   - Edit the workflow: modify a step
   - Publish the workflow
   - Verify it appears as "Publie" in the list
   - Unpublish the workflow
   - Delete the workflow (only possible when not in use)

3. **Scenario 2: Full Request Approval Flow**
   - Login as recruiter
   - Create a new request:
     - Select a published workflow
     - Fill in all required fields across tabs
     - Save as draft
   - Submit the request for validation
   - Switch to validator user (step 1)
   - View the pending request
   - Approve step 1 (with optional comment)
   - Switch to step 2 validator
   - Approve step 2
   - Verify request reaches APPROVED status

4. **Scenario 3: Request Rejection and Comment Flow**
   - Create and submit a request
   - Switch to validator
   - Add a comment on the request
   - Reject the request with a mandatory reason
   - Verify request is marked REJECTED
   - Verify history shows rejection reason and comment

5. **Scenario 4: Draft Abandonment**
   - Create a draft request
   - Archive (abandon) the draft
   - Verify confirmation modal appears
   - Confirm abandonment
   - Verify request status is ABANDONED

6. **Scenario 5: Permission-Based Access Control**
   - As non-admin: Verify workflow admin page is inaccessible
   - As recruiter: Verify can create requests
   - As validator (wrong step): Verify approve/reject buttons are hidden
   - As validator (correct step): Verify approve/reject buttons are visible

7. **Scenario 6: Form Validation**
   - Try to submit a request with missing required fields
   - Verify inline errors appear
   - Verify scroll to first error
   - Verify tab error indicators
   - Fill in required fields and submit successfully

8. **Scenario 7: Statistics Dashboard**
   - Navigate to statistics page
   - Verify KPI cards display
   - Verify charts render
   - Apply date filter and verify data updates

8. **Scenario 8: Request Duplication**
   - Create and submit a request
   - Switch to validator
   - Reject the request with a reason
   - Switch back to creator
   - Click "Dupliquer" on the rejected request
   - Verify a new DRAFT request is created with pre-filled data from the original
   - Modify fields and submit the duplicate
   - Verify original request remains REJECTED

9. **Scenario 9: ALL Validation Mode**
   - Create a workflow with a step using validation mode ALL and 2 validators
   - Create and submit a request using this workflow
   - Switch to validator 1: approve
   - Verify request is still PENDING (waiting for validator 2)
   - Switch to validator 2: approve
   - Verify request step advances (or is APPROVED if final step)
   - Verify history shows both approvals

**Acceptance Criteria:**
- All 9 scenarios pass successfully
- Tests run against mock services (no backend dependency)
- Tests are stable and not flaky
- Each scenario is self-contained (clean setup/teardown)
- Tests run in CI pipeline with `npm run test:e2e`
- Screenshots captured on failure for debugging
- Tests complete within reasonable time (<5 minutes total)
