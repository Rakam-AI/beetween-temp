# TF1.1 - Initialize Nuxt 3 Project with TypeScript & Tailwind CSS


**Depends on:** Nothing (first frontend ticket)
**Blocks:** All subsequent frontend tickets

**Context:**
The frontend for the Demand Approval Request (DAR) service is a Nuxt 3 application built as a µFrontend. It uses TypeScript for strict typing and Tailwind CSS for styling.

Reference: `UI_TechnicalSpecification V1 (FR).md` > "Contraintes techniques"

**Objective:**
Bootstrap a Nuxt 3 project with TypeScript and Tailwind CSS configured, ready for DAR frontend development.

**Instructions:**

1. **Initialize the Nuxt 3 project:**
   - Create a new Nuxt 3 project (e.g. `dar-ui/`) using `npx nuxi@latest init dar-ui`
   - Ensure TypeScript is enabled by default (`typescript: { strict: true }` in `nuxt.config.ts`)

2. **Configure `nuxt.config.ts`:**
   ```typescript
   export default defineNuxtConfig({
     devtools: { enabled: true },
     typescript: { strict: true },
     modules: ['@nuxtjs/tailwindcss'],
     runtimeConfig: {
       public: {
         apiBaseUrl: process.env.NUXT_PUBLIC_API_BASE_URL || 'http://localhost:8080',
         useMockServices: process.env.NUXT_PUBLIC_USE_MOCK_SERVICES || 'true',
         keycloakUrl: process.env.NUXT_PUBLIC_KEYCLOAK_URL || 'http://localhost:8085',
         keycloakRealm: process.env.NUXT_PUBLIC_KEYCLOAK_REALM || 'dar',
         keycloakClientId: process.env.NUXT_PUBLIC_KEYCLOAK_CLIENT_ID || 'dar-ui'
       }
     },
     app: {
       head: {
         title: 'DAR - Demandes d\'Approbation de Recrutement'
       }
     }
   })
   ```

3. **Install and configure Tailwind CSS:**
   - Install `@nuxtjs/tailwindcss`
   - Create `tailwind.config.ts` with semantic color palette:
     ```typescript
     export default {
       content: [],
       theme: {
         extend: {
           colors: {
             primary: { /* teal/brand colors */ },
             success: { DEFAULT: '#10b981', light: '#d1fae5' },
             warning: { DEFAULT: '#f59e0b', light: '#fef3c7' },
             danger: { DEFAULT: '#ef4444', light: '#fee2e2' },
             info: { DEFAULT: '#3b82f6', light: '#dbeafe' }
           }
         }
       }
     }
     ```

4. **Create base project structure:**
   ```
   dar-ui/
   ├── assets/css/main.css          # Tailwind directives
   ├── components/                   # Shared UI components
   ├── composables/                  # State management composables
   ├── layouts/                      # App layouts
   ├── middleware/                    # Route middleware (auth)
   ├── pages/                        # File-based routing
   ├── plugins/                      # Nuxt plugins
   ├── services/                     # Service interfaces & implementations
   │   ├── interfaces/
   │   ├── mock/
   │   └── api/
   ├── types/                        # TypeScript type definitions
   ├── utils/                        # Utility functions
   ├── nuxt.config.ts
   ├── tailwind.config.ts
   ├── tsconfig.json
   └── package.json
   ```

5. **Create `.env.example`:**
   ```
   NUXT_PUBLIC_API_BASE_URL=http://localhost:8080
   NUXT_PUBLIC_USE_MOCK_SERVICES=true
   NUXT_PUBLIC_KEYCLOAK_URL=http://localhost:8085
   NUXT_PUBLIC_KEYCLOAK_REALM=dar
   NUXT_PUBLIC_KEYCLOAK_CLIENT_ID=dar-ui
   ```

6. **Verify the project starts** with `npm run dev`

**Acceptance Criteria:**
- `npm run dev` starts the Nuxt 3 dev server without errors
- TypeScript strict mode is enabled and working
- Tailwind CSS classes are applied correctly
- Project structure directories are created
- Environment variables are configurable via `.env`
- No linting or type errors on initial setup
