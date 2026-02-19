# TF2.2 - Implement Mock Services with Sample Data


**Depends on:** TF1.4, TF2.1
**Blocks:** TF2.4, TF2.5, TF2.7, TF2.8, TF2.9

**Context:**
Mock services allow frontend development without a running backend. They simulate realistic data and behaviors including state transitions, validation, and pagination.

Reference: `UI_Technical Specification V1 (FR) - IMPLEMENTATION_DETAILS.md` > "Pattern Factory" and all UI mockups for sample data

**Objective:**
Implement MockRequestService and MockWorkflowService with realistic sample data matching the UI mockups.

**Instructions:**

1. **Create `services/mock/mockData.ts`:**
   - Define sample workflows matching mockup data:
     - "Circuit Standard" (5 steps, PUBLISHED, 12 requests)
     - "Circuit Express" (3 steps, PUBLISHED, 5 requests)
     - "Circuit Direction" (4 steps, DRAFT, 0 requests) - Note: DRAFT workflows have no active requests since they cannot be used to create requests
   - Define sample requests matching mockup data:
     - "Developpeur Full Stack" (CDI, Senior, Technologies) - PENDING at step 3
     - "Chef de Projet Digital" (CDI, Confirme, Marketing) - PENDING
     - "Data Analyst" (CDD 18 mois, Finance) - PENDING
     - "UX Designer" (CDI, Junior, Product) - DRAFT
     - "Ingenieur DevOps" (CDI, Expert, Infrastructure) - REJECTED
   - Define sample history entries with realistic dates and users
   - Define sample users: Marie Leroy (RH), Jean Dupont (Manager), Sophie Martin (Direction)

2. **Create `services/mock/MockWorkflowService.ts`:**
   - Implement `IWorkflowService` with in-memory array
   - `fetchWorkflows()`: Return filtered/paginated workflows
   - `fetchWorkflowById()`: Return single workflow or throw 404
   - `createWorkflow()`: Add to array with generated ID, status DRAFT
   - `updateWorkflow()`: Update in array, only if DRAFT
   - `deleteWorkflow()`: Remove from array, only if no active requests
   - `changeWorkflowStatus()`: Validate transitions (DRAFT→PUBLISHED, PUBLISHED→UNPUBLISHED)
   - Add simulated 200-500ms delays with `await new Promise(resolve => setTimeout(resolve, delay))`

3. **Create `services/mock/MockRequestService.ts`:**
   - Implement `IRequestService` with in-memory array
   - `fetchRequests()`: Support filtering by status, workflowId, createdBy, search, sorting
   - `fetchRequestById()`: Return with full history
   - `createRequest()`: Create DRAFT with workflow info, empty business info
   - `saveRequest()`: Update fields, add EDITION history entry with field changes
   - `submitRequest()`: Validate required fields, transition DRAFT→PENDING, add SUBMISSION history
   - `approveStep()`: Handle ONE/ALL modes, advance step or approve request
   - `rejectRequest()`: Require reason, transition to REJECTED
   - `addComment()`: Add COMMENT history entry
   - `archiveRequest()`: Transition to ARCHIVED/ABANDONED
   - `duplicateRequest()`: Load source request (must be REJECTED), create a new DRAFT pre-filled with the same data, reset history
   - All mutations add history entries with timestamps

4. **Ensure mock services simulate error scenarios:**
   - Throw appropriate errors for invalid transitions
   - Simulate 409 Conflict for concurrent edit testing (optional flag)

**Acceptance Criteria:**
- Mock services implement all interface methods
- Sample data matches the UI mockups visually (same job titles, statuses, users)
- State transitions follow the same rules as the backend (DRAFT→PENDING→APPROVED/REJECTED)
- History entries are correctly generated on each action
- Pagination works correctly with configurable page size
- Filters (status, search, workflow) work on mock data
- Simulated delays provide realistic loading states
- `ServiceFactory.getRequestService()` returns MockRequestService when `useMockServices=true`
