# TF1.3 - Configure Keycloak Authentication & RBAC


**Depends on:** TF1.1, TF1.2
**Blocks:** TF1.4, TF1.6, TF2.14

**Context:**
Authentication uses Keycloak with the `dar` realm. The `dar-ui` client is public (implicit/PKCE flow). Roles are: ADMIN, MANAGER, RECRUITER, HR_MANAGER. In µFrontend mode, the token may be received from the host application. In standalone mode, the app authenticates directly with Keycloak.

Reference:
- `UI_Technical Specification V1 (FR) - AUTHENTICATION.md` (full document)
- `UI_TechnicalSpecification V1 (FR).md` > "Contraintes techniques > Authentification"

**Objective:**
Implement Keycloak authentication with silent SSO, JWT token management, role extraction, and RBAC utilities.

**Instructions:**

1. **Install `keycloak-js`** dependency

2. **Create `plugins/keycloak.client.ts`:**
   - Initialize Keycloak instance with runtime config (url, realm, clientId)
   - Implement silent SSO check (`onLoad: 'check-sso'`, `silentCheckSsoRedirectUri`)
   - Set up automatic token refresh before expiry
   - On token refresh failure, send `TOKEN_EXPIRED` to host (if in µFrontend mode)
   - Store token in a reactive state accessible to the app

3. **Create `composables/useAuth.ts`:**
   ```typescript
   export const useAuth = () => {
     const user = useState<UserContext | null>('auth-user', () => null)
     const token = useState<string | null>('auth-token', () => null)
     const isAuthenticated = computed(() => !!user.value)

     // Role checks
     const hasRole = (role: string): boolean => { ... }
     const isAdmin = computed(() => hasRole('ADMIN'))
     const isManager = computed(() => hasRole('MANAGER'))
     const isRecruiter = computed(() => hasRole('RECRUITER'))
     const isHRManager = computed(() => hasRole('HR_MANAGER'))

     // Permission checks
     const canCreateRequest = computed(() => isRecruiter.value || isManager.value || isHRManager.value)
     const canManageWorkflows = computed(() => isAdmin.value)
     const canViewWorkflows = computed(() => true) // All authenticated users can view published workflows (needed to create requests)

     return { user, token, isAuthenticated, hasRole, isAdmin, isManager, isRecruiter, isHRManager, canCreateRequest, canManageWorkflows, canViewWorkflows }
   }
   ```

4. **Create `types/auth.ts`:**
   ```typescript
   interface UserContext {
     userId: string
     email: string
     username: string
     companyId: string
     roles: string[]
     firstName?: string
     lastName?: string
   }
   ```

5. **Create `middleware/auth.global.ts`:**
   - Check authentication on every route
   - Redirect to login if not authenticated (standalone mode)
   - Send `TOKEN_EXPIRED` to host if token is invalid (µFrontend mode)

6. **Scope admin middleware correctly:**
   - The `middleware/admin.ts` route middleware should only protect **workflow write routes** (create, edit, delete, publish)
   - **All authenticated users** must be able to read/view the workflow list and workflow detail pages (needed when creating a request to select a workflow)
   - Only ADMIN role can access workflow creation/editing/publishing routes

7. **Handle dual auth modes:**
   - **µFrontend mode**: Receive token from host via `AUTH_TOKEN` message, decode JWT to extract user context
   - **Standalone mode**: Use Keycloak JS adapter directly for login/logout

8. **Create JWT decode utility** (`utils/jwt.ts`):
   - Decode JWT payload without verification (verification done server-side)
   - Extract: `sub`, `email`, `preferred_username`, `realm_access.roles`, `companyId`

**Acceptance Criteria:**
- Keycloak authentication works in standalone mode (redirects to Keycloak login)
- Silent SSO check initializes without full-page redirect
- Token is automatically refreshed before expiry
- User context (roles, companyId) is correctly extracted from JWT
- `useAuth()` composable provides reactive role checks
- Auth middleware blocks unauthenticated access
- µFrontend mode accepts token from host messages
- No hardcoded credentials or tokens in source code
