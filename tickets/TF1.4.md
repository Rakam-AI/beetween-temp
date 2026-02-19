# TF1.4 - Set Up Service Factory Pattern & API Client


**Depends on:** TF1.1, TF1.3
**Blocks:** TF2.2, TF2.3

**Context:**
The application uses a Factory pattern to switch between Mock services and real API services via environment variables. This allows development and testing with mock data without a running backend.

Reference: `UI_Technical Specification V1 (FR) - IMPLEMENTATION_DETAILS.md` > "Architecture Technique > Pattern Factory"

**Objective:**
Create the service interfaces, factory, and a configured API client (`$fetch`) with authentication headers.

**Instructions:**

1. **Create service interfaces in `services/interfaces/`:**

   - **`IRequestService.ts`:**
     ```typescript
     export interface IRequestService {
       fetchRequests(filters?: RequestFilters): Promise<PaginatedResponse<RequestListItem>>
       fetchRequestById(id: string): Promise<Request>
       createRequest(workflowId: string): Promise<Request>
       saveRequest(request: RequestInput): Promise<Request>
       submitRequest(requestId: string): Promise<Request>
       approveStep(requestId: string, comment?: string): Promise<Request>
       rejectRequest(requestId: string, reason: string, comment?: string): Promise<Request>
       addComment(requestId: string, comment: string): Promise<Request>
       archiveRequest(requestId: string): Promise<void>
      duplicateRequest(requestId: string): Promise<Request>
     }
     ```

   - **`IWorkflowService.ts`:**
     ```typescript
     export interface IWorkflowService {
       fetchWorkflows(filters?: WorkflowFilters): Promise<PaginatedResponse<Workflow>>
       fetchWorkflowById(id: string): Promise<Workflow>
       createWorkflow(input: WorkflowInput): Promise<Workflow>
       updateWorkflow(id: string, input: WorkflowInput): Promise<Workflow>
       deleteWorkflow(id: string): Promise<void>
       changeWorkflowStatus(id: string, status: 'PUBLISHED' | 'UNPUBLISHED'): Promise<Workflow>
     }
     ```

2. **Create `services/api/apiClient.ts`:**
   - Create a configured `$fetch` instance with:
     - Base URL from runtime config
     - Authorization header with Bearer token from `useAuth()`
     - Default headers (`Content-Type: application/json`)
     - Response interceptor for error handling (401 → token refresh, 403 → permission error, etc.)

3. **Create `services/ServiceFactory.ts`:**
   ```typescript
   export class ServiceFactory {
     private static useMocks(): boolean {
       const config = useRuntimeConfig()
       return config.public.useMockServices === 'true'
     }

     static getRequestService(): IRequestService {
       return this.useMocks()
         ? new MockRequestService()
         : new ApiRequestService()
     }

     static getWorkflowService(): IWorkflowService {
       return this.useMocks()
         ? new MockWorkflowService()
         : new ApiWorkflowService()
     }
   }
   ```

4. **Create `plugins/services.ts`:**
   - Register service factory as a Nuxt plugin providing `$services`
   - Or use `provide/inject` pattern for composable access

**Acceptance Criteria:**
- `IRequestService` and `IWorkflowService` interfaces define all required methods
- `ServiceFactory` returns mock or API implementations based on env variable
- API client automatically injects Bearer token on every request
- API client handles 401 responses by attempting token refresh
- Switching `NUXT_PUBLIC_USE_MOCK_SERVICES` between `true`/`false` toggles service implementations
- TypeScript compilation passes with strict mode
