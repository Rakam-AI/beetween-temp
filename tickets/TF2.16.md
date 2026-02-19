# TF2.16 - Implement Frontend Unit & Component Tests


**Depends on:** TF2.1, TF2.2, TF2.3, TF1.6
**Blocks:** TF2.17

**Context:**
Frontend testing ensures composables, services, utilities, and components work correctly. Tests should cover business logic, state management, and UI rendering.

Reference: All implementation tickets for expected behaviors

**Objective:**
Implement unit and component tests achieving good coverage on critical paths.

**Instructions:**

1. **Set up testing infrastructure:**
   - Install and configure Vitest (Nuxt's recommended test runner)
   - Install `@vue/test-utils` for component testing
   - Install `@nuxt/test-utils` for Nuxt-specific testing
   - Configure test setup with mock environment

2. **Unit tests for types and utilities (`tests/unit/`):**
   - `jwt.test.ts`: Test JWT decode utility
   - `validation.test.ts`: Test all form validation functions (required, email, URL, number range, etc.)

3. **Unit tests for composables:**
   - `useAuth.test.ts`: Test role checks, permission computations
   - `useRequests.test.ts`: Test state management, error handling, filter application
   - `useWorkflows.test.ts`: Test CRUD operations, status changes
   - `useToast.test.ts`: Test toast lifecycle (add, auto-remove)
   - `usePermissions.test.ts`: Test full permission matrix against all role/status combinations
   - `useFormValidation.test.ts`: Test on-blur and on-submit validation logic

4. **Unit tests for services:**
   - `MockRequestService.test.ts`: Test all mock service methods, state transitions, history generation
   - `MockWorkflowService.test.ts`: Test CRUD, status transitions, validation
   - `ApiRequestService.test.ts`: Test API calls with mocked fetch (correct URLs, params, headers)
   - `ApiWorkflowService.test.ts`: Test API calls with mocked fetch
   - `ServiceFactory.test.ts`: Test factory returns correct implementation based on env

5. **Component tests (`tests/components/`):**
   - `UiBadge.test.ts`: Test status badge renders correct colors
   - `UiModal.test.ts`: Test open/close, escape key, backdrop click
   - `WorkflowProgressBar.test.ts`: Test step rendering, current step highlighting, color coding
   - `ActionHistoryTimeline.test.ts`: Test entry rendering for each action type
   - `RequestActions.test.ts`: Test button visibility based on role/status (critical)
   - `RequestCard.test.ts`: Test card info display

6. **Test the permission matrix thoroughly:**
   - For each role (CREATOR, VALIDATOR_CURRENT, VALIDATOR_OTHER, ADMIN)
   - For each status (DRAFT, PENDING, APPROVED, REJECTED, ARCHIVED)
   - Verify correct button visibility

**Acceptance Criteria:**
- Vitest runs successfully with `npm run test`
- Composable tests cover all public methods and computed properties
- Service tests verify correct API calls and response mapping
- Permission matrix is tested for all role/status combinations
- Form validation tests cover all validator functions
- Component tests verify rendering and user interactions
- All tests pass with no flaky behavior
- Coverage report generated (target >70% on critical paths)
