# TF2.3 - Implement API Services (Real Backend Integration)


**Depends on:** TF1.4, TF2.1
**Blocks:** TF2.16 (Backend integration tests)

**Context:**
The API services connect to the real backend REST API. They use the configured `$fetch` client with authentication and map API responses to frontend domain types.

Reference:
- `API_Technical Specification V1.md` > "Endpoints principaux" (all endpoints)
- Backend tickets T2.12, T2.13 for exact endpoint specifications

**Objective:**
Implement ApiRequestService and ApiWorkflowService that call the real backend API.

**Instructions:**

1. **Create `services/api/ApiWorkflowService.ts`:**
   ```typescript
   export class ApiWorkflowService implements IWorkflowService {
     private client = useApiClient()

     async fetchWorkflows(filters?: WorkflowFilters): Promise<PaginatedResponse<Workflow>> {
       const params = new URLSearchParams()
       if (filters?.page) params.set('page', String(filters.page))
       if (filters?.size) params.set('size', String(filters.size))
       if (filters?.publicationStatus) params.set('publicationStatus', filters.publicationStatus)
       const response = await this.client<ApiResponse<Workflow[]>>(`/api/v1/workflows?${params}`)
       return { data: response.data, pagination: response.pagination! }
     }

     async fetchWorkflowById(id: string): Promise<Workflow> {
       const response = await this.client<ApiResponse<Workflow>>(`/api/v1/workflows/${id}`)
       return response.data
     }

     async createWorkflow(input: WorkflowInput): Promise<Workflow> {
       const response = await this.client<ApiResponse<string>>('/api/v1/workflows', {
         method: 'POST',
         body: input
       })
       return this.fetchWorkflowById(response.data)
     }

     async updateWorkflow(id: string, input: WorkflowInput): Promise<Workflow> {
       await this.client(`/api/v1/workflows/${id}`, { method: 'PUT', body: input })
       return this.fetchWorkflowById(id)
     }

     async deleteWorkflow(id: string): Promise<void> {
       await this.client(`/api/v1/workflows/${id}`, { method: 'DELETE' })
     }

     async changeWorkflowStatus(id: string, status: 'PUBLISHED' | 'UNPUBLISHED'): Promise<Workflow> {
       await this.client(`/api/v1/workflows/${id}/status`, {
         method: 'PUT',
         body: { status }
       })
       return this.fetchWorkflowById(id)
     }
   }
   ```

2. **Create `services/api/ApiRequestService.ts`:**
   - Map to all 9 backend endpoints:
     - `POST /api/v1/requests` → `createRequest()`
     - `PUT /api/v1/requests/{id}` → `saveRequest()`
     - `GET /api/v1/requests` → `fetchRequests()` (with query params for filters)
     - `GET /api/v1/requests/{id}` → `fetchRequestById()`
     - `POST /api/v1/requests/{id}/submission` → `submitRequest()`
     - `POST /api/v1/requests/{id}/approval` → `approveStep()`
     - `POST /api/v1/requests/{id}/rejection` → `rejectRequest()`
     - `POST /api/v1/requests/{id}/comment` → `addComment()`
     - `POST /api/v1/requests/{id}/archive` → `archiveRequest()`

3. **Create `services/api/apiClient.ts`:**
   - Wrapper around `$fetch` with:
     - Base URL from `useRuntimeConfig().public.apiBaseUrl`
     - Bearer token injection from `useAuth().token`
     - Error response mapping:
       ```typescript
       function handleApiError(error: FetchError): never {
         switch (error.statusCode) {
           case 400: throw new ApiValidationError(error.data?.errors)
           case 401: /* trigger token refresh or redirect */ break
           case 403: throw new ApiPermissionError()
           case 404: throw new ApiNotFoundError()
           case 409: throw new ApiConflictError(error.data)
           default: throw new ApiServerError()
         }
       }
       ```

4. **Create error classes in `services/api/errors.ts`:**
   ```typescript
   export class ApiValidationError extends Error { constructor(public errors: ApiError[]) { ... } }
   export class ApiPermissionError extends Error { ... }
   export class ApiNotFoundError extends Error { ... }
   export class ApiConflictError extends Error { constructor(public conflictData?: any) { ... } }
   export class ApiServerError extends Error { ... }
   ```

**Acceptance Criteria:**
- All API service methods correctly call the backend endpoints
- Request/response mapping matches the backend API DTOs
- Bearer token is included in every request
- HTTP errors are caught and mapped to typed error classes
- 401 errors trigger token refresh
- 409 errors include conflict data for concurrent edit handling
- Query parameters for filters/pagination are correctly serialized
- TypeScript types match exactly between frontend and backend
