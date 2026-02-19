# TF1.6 - Set Up Composables & State Management


**Depends on:** TF1.1, TF1.3, TF1.4
**Blocks:** TF2.2, TF2.3, TF2.7, TF2.8, TF2.9

**Context:**
The application uses Nuxt composables with `useState` for global reactive state management. Each domain entity (requests, workflows) has a dedicated composable that manages fetching, caching, and mutations.

Reference: `UI_Technical Specification V1 (FR) - IMPLEMENTATION_DETAILS.md` > "Composables (State Management)"

**Objective:**
Create the state management composables that will be used by all views to interact with data.

**Instructions:**

1. **Create `composables/useRequests.ts`:**
   ```typescript
   export const useRequests = () => {
     const requests = useState<RequestListItem[]>('requests', () => [])
     const currentRequest = useState<Request | null>('current-request', () => null)
     const loading = useState<boolean>('requests-loading', () => false)
     const error = useState<string | null>('requests-error', () => null)
     const pagination = useState<PaginationInfo>('requests-pagination', () => defaultPagination)
     const filters = useState<RequestFilters>('requests-filters', () => ({}))

     const service = ServiceFactory.getRequestService()

     async function fetchRequests(): Promise<void> { ... }
     async function fetchRequestById(id: string): Promise<void> { ... }
     async function createRequest(workflowId: string): Promise<Request> { ... }
     async function saveRequest(input: RequestInput): Promise<void> { ... }
     async function submitRequest(id: string): Promise<void> { ... }
     async function approveStep(id: string, comment?: string): Promise<void> { ... }
     async function rejectRequest(id: string, reason: string, comment?: string): Promise<void> { ... }
     async function addComment(id: string, comment: string): Promise<void> { ... }
     async function archiveRequest(id: string): Promise<void> { ... }
     async function duplicateRequest(id: string): Promise<Request> { ... }
     function setFilters(newFilters: RequestFilters): void { ... }

     return {
       requests, currentRequest, loading, error, pagination, filters,
       fetchRequests, fetchRequestById, createRequest, saveRequest,
       submitRequest, approveStep, rejectRequest, addComment, archiveRequest,
       duplicateRequest, setFilters
     }
   }
   ```

2. **Create `composables/useWorkflows.ts`:**
   ```typescript
   export const useWorkflows = () => {
     const workflows = useState<Workflow[]>('workflows', () => [])
     const currentWorkflow = useState<Workflow | null>('current-workflow', () => null)
     const loading = useState<boolean>('workflows-loading', () => false)
     const error = useState<string | null>('workflows-error', () => null)
     const pagination = useState<PaginationInfo>('workflows-pagination', () => defaultPagination)
     const filters = useState<WorkflowFilters>('workflows-filters', () => ({}))

     const service = ServiceFactory.getWorkflowService()

     async function fetchWorkflows(filters?: WorkflowFilters): Promise<void> { ... }
     async function fetchWorkflowById(id: string): Promise<void> { ... }
     async function createWorkflow(input: WorkflowInput): Promise<Workflow> { ... }
     async function updateWorkflow(id: string, input: WorkflowInput): Promise<void> { ... }
     async function deleteWorkflow(id: string): Promise<void> { ... }
     async function changeStatus(id: string, status: 'PUBLISHED' | 'UNPUBLISHED'): Promise<void> { ... }

     return {
       workflows, currentWorkflow, loading, error, pagination, filters,
       fetchWorkflows, fetchWorkflowById, createWorkflow, updateWorkflow,
       deleteWorkflow, changeStatus
     }
   }
   ```

3. **Create `composables/useToast.ts`:**
   ```typescript
   export const useToast = () => {
     const toasts = useState<Toast[]>('toasts', () => [])

     function success(message: string, duration?: number): void { ... }  // default 3s
     function error(message: string, duration?: number): void { ... }    // default 5s
     function warning(message: string, duration?: number): void { ... }
     function info(message: string, duration?: number): void { ... }
     function remove(id: string): void { ... }

     return { toasts, success, error, warning, info, remove }
   }
   ```

4. **Create `composables/useLoading.ts`:**
   - Global loading state management
   - Shows spinner with message after 2s delay per spec

**Acceptance Criteria:**
- `useRequests()` and `useWorkflows()` manage state reactively via `useState`
- Loading states are tracked and exposed for UI indicators
- Error states are captured and exposed
- State is shared across components using the same composable
- `useToast()` supports all notification variants with auto-dismiss
- All composable functions handle errors gracefully (try/catch with error state update)
