# TF1.2 - Configure µFrontend Architecture & Host Communication


**Depends on:** TF1.1
**Blocks:** TF1.3, TF2.14

**Context:**
The DAR UI runs as a µFrontend embedded in a host application via iFrame or Web Components. Communication with the host application happens through the `sendMessage` API. The host application manages primary navigation, authentication context, and user session.

Reference: `UI_TechnicalSpecification V1 (FR).md` > "Objectifs" and "Contraintes techniques > Architecture"

**Objective:**
Set up the µFrontend integration layer so the DAR application can communicate with its host application and receive authentication/navigation context.

**Instructions:**

1. **Create `plugins/host-communication.ts`:**
   - Set up `window.addEventListener('message', ...)` to receive messages from the host
   - Define message types:
     ```typescript
     type HostMessage =
       | { type: 'AUTH_TOKEN'; payload: { token: string; refreshToken?: string } }
       | { type: 'USER_CONTEXT'; payload: { userId: string; companyId: string; roles: string[] } }
       | { type: 'NAVIGATE'; payload: { route: string } }
       | { type: 'THEME'; payload: { theme: 'light' | 'dark' } }
     ```
   - Implement `sendMessageToHost(message: MicrofrontendMessage)` utility:
     ```typescript
     type MicrofrontendMessage =
       | { type: 'READY' }
       | { type: 'NAVIGATE_REQUEST'; payload: { route: string } }
       | { type: 'TOKEN_EXPIRED' }
       | { type: 'ERROR'; payload: { message: string; code?: string } }
     ```
   - Use `window.parent.postMessage()` for iFrame communication
   - Validate message origin for security

2. **Create `composables/useHostCommunication.ts`:**
   - Expose reactive state: `hostConnected`, `hostTheme`
   - Expose methods: `sendToHost()`, `requestNavigation()`
   - Handle standalone mode (no host) gracefully for local development

3. **Configure Nuxt for iFrame embedding:**
   - Remove `X-Frame-Options` headers in dev mode
   - Configure CORS appropriately in `nuxt.config.ts`

4. **Create a standalone fallback:**
   - When running outside of iFrame (development mode), the app should function normally
   - Log a warning: "Running in standalone mode - host communication disabled"
   - Provide local auth fallback (connects to TF1.3)

**Acceptance Criteria:**
- Host communication plugin initializes on app start
- Messages from host are received and processed correctly
- `sendMessageToHost()` sends properly formatted messages to parent window
- App works in standalone mode (no iFrame) without errors
- Message origin validation prevents cross-origin attacks
- TypeScript types cover all message formats
