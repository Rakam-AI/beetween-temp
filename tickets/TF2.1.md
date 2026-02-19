# TF2.1 - Define TypeScript Domain Models & Interfaces


**Depends on:** TF1.1
**Blocks:** TF2.2, TF2.3, TF1.6

**Context:**
The frontend needs strict TypeScript types mirroring the backend domain models and API DTOs. These types are shared across services, composables, and components.

Reference:
- `API_Technical Specification V1.md` > "DTOs communs" and "Modeles de donnees"
- `Agent_Technical Specification V1.0.md` > All domain model descriptions

**Objective:**
Define all TypeScript interfaces, types, and enums needed for the DAR application.

**Instructions:**

1. **Create `types/workflow.ts`:**
   ```typescript
   export type PublicationStatus = 'DRAFT' | 'PUBLISHED' | 'UNPUBLISHED'
   export type ValidationMode = 'ONE' | 'ALL'
   export type FieldType = 'TEXT' | 'NUMBER' | 'DATE' | 'DROPDOWN' | 'CHECKBOX' | 'FILE'
   export type ActorType = 'ROLE' | 'USER'

   export interface ActorReference {
     type: ActorType
     id: string
     displayName?: string
   }

   export interface WorkflowStep {
     name: string
     description?: string
     icon?: string
     validationMode: ValidationMode
     validators: ActorReference[]
     commentators: ActorReference[]
     readOnlyViewers: ActorReference[]
     requiredFields: string[]
     hiddenFields: string[]
     readOnlyFields: string[]
   }

   export interface AdditionalFieldDefinition {
     name: string
     label: string
     type: FieldType
     options?: string[]
     defaultValue?: string
     helpText?: string
   }

   export interface Workflow {
     id: string
     name: string
     description?: string
     publicationStatus: PublicationStatus
     steps: WorkflowStep[]
     additionalFields: AdditionalFieldDefinition[]
     globalRequiredFields: string[]
     activeRequestsCount: number
     createdAt: string
     updatedAt: string
   }

   export interface WorkflowInput {
     name: string
     description?: string
     steps: WorkflowStep[]
     additionalFields?: AdditionalFieldDefinition[]
     globalRequiredFields?: string[]
   }

   export interface WorkflowFilters {
     publicationStatus?: PublicationStatus
     search?: string
     page?: number
     size?: number
   }
   ```

2. **Create `types/request.ts`:**
   ```typescript
   export type RequestStatus = 'DRAFT' | 'PENDING' | 'APPROVED' | 'REJECTED' | 'ARCHIVED' | 'ABANDONED'
   export type HistoryActionType = 'CREATE' | 'UPDATE' | 'SUBMIT' | 'APPROVE' | 'REJECT' | 'COMMENT' | 'ABANDON' | 'ARCHIVE'
   export type ContractType = 'CDI' | 'CDD' | 'STAGE' | 'ALTERNANCE' | 'FREELANCE' | 'INTERIM' | 'APPRENTISSAGE' | 'VIE' | 'THESE_CIFRE' | 'PORTAGE_SALARIAL'

   export interface FieldChange {
     fieldName: string
     oldValue?: string
     newValue?: string
   }

   export interface HistoryEntry {
     actionType: HistoryActionType
     timestamp: string
     userId: string
     userName: string
     userRole: string
     stepName?: string
     comment?: string
     reason?: string
     fieldChanges?: FieldChange[]
   }

   export interface BusinessInfo {
     jobTitle: string
     contractType?: ContractType
     contractForm?: string
     positions?: number
     location?: string
     department?: string
     level?: string
     salaryMin?: number
     salaryMax?: number
     benefits?: string
     startDate?: string
     description?: string
     profileDescription?: string
   }

   export interface Request {
     id: string
     workflowId: string
     workflowName: string
     status: RequestStatus
     currentStepIndex: number
     businessInfo: BusinessInfo
     additionalFieldValues: Record<string, string>
     history: HistoryEntry[]
     pendingValidators?: string[]
     companyId: string
     createdBy: string
     createdByName: string
     createdAt: string
     updatedAt: string
     version: number
   }

   export interface RequestListItem {
     id: string
     jobTitle: string
     workflowName: string
     status: RequestStatus
     currentStepIndex: number
     totalSteps: number
     stepNames: string[]
     companyId: string
     createdBy: string
     createdByName: string
     createdAt: string
     contractType?: ContractType
     department?: string
     level?: string
   }

   export interface RequestInput {
     id?: string
     workflowId: string
     businessInfo: Partial<BusinessInfo>
     additionalFieldValues?: Record<string, string>
     version?: number
   }

   export interface RequestFilters {
     status?: RequestStatus
     workflowId?: string
     createdBy?: string
     search?: string
     dateFrom?: string
     dateTo?: string
     page?: number
     size?: number
     sortBy?: string
     sortDirection?: 'ASC' | 'DESC'
   }
   ```

3. **Create `types/common.ts`:**
   ```typescript
   export interface PaginatedResponse<T> {
     data: T[]
     pagination: PaginationInfo
   }

   export interface PaginationInfo {
     page: number
     size: number
     totalCount: number
     totalPages: number
     hasNext: boolean
   }

   export interface ApiResponse<T> {
     data: T
     message?: string
     pagination?: PaginationInfo
     errors?: ApiError[]
   }

   export interface ApiError {
     code: string
     message: string
     field?: string
   }
   ```

4. **Create `types/statistics.ts`:**
   ```typescript
   export interface DashboardStats {
     totalProcessed: number
     inProgress: number
     approvalRate: number
     averageValidationTime: number
   }

   export interface StatusDistribution {
     approved: number
     rejected: number
     pending: number
   }

   export interface DepartmentStats {
     department: string
     count: number
   }

   export interface StepValidationTime {
     stepName: string
     averageDays: number
   }
   ```

5. **Create `types/index.ts`** re-exporting all types.

**Acceptance Criteria:**
- All types match the backend API DTOs from the API specification
- TypeScript strict mode passes with no errors
- Enums are defined as union types (not TS enums) for better JSON compatibility
- All optional fields are properly marked with `?`
- Types are exported and importable from `~/types`
