# CLAUDE.md

Nuxt 3 µFrontend with TypeScript, Tailwind CSS, and Keycloak authentication.
Read this before writing any code.

---

## Project Structure

```
├── components/       # Reusable UI components — no business logic
├── composables/      # State management and business logic
├── layouts/          # Page layout wrappers
├── middleware/        # Route guards (auth, guest)
├── pages/            # File-based routing
├── plugins/          # Nuxt plugins (client-side only if needed)
├── services/
│   ├── ServiceFactory.ts        # Switches between Mock and API implementations
│   ├── types.ts                 # Service interfaces and shared DTOs
│   └── implementations/        # Mock and API concrete implementations
├── types/            # TypeScript domain types and interfaces
└── data/             # Static mock data (JSON)
```

---

## Architecture Conventions

**Service Factory:** all backend interaction goes through service interfaces — never call `$fetch` directly in components or composables. `ServiceFactory` selects the implementation at runtime via an environment variable (`MOCK` or `REAL`).

**Composables own state and orchestration:** business logic, API calls, and app-wide reactive state live in composables. Components are thin — they render and emit, nothing more.

**State management:** use Nuxt `useState()` for SSR-safe app-wide state, `ref()` / `computed()` for component-local state. No external state library (no Pinia, no Vuex).

**DTO boundary:** API response mapping and transformation happens inside service implementations, not in composables or components.

**Error handling:** services throw errors; composables catch them and surface feedback via `useToast()`. A `401` triggers a token refresh and retry before failing.

**Auth:** Keycloak integration lives in `useAuth()`. Token storage, refresh scheduling, and role checks are handled there — nowhere else.

---

## TypeScript Conventions

- Strict mode — no `any`
- Domain types in `types/` (interfaces and union types, not enums)
- Service contracts defined as interfaces in `services/types.ts`
- All components use `<script setup lang="ts">`
- Props typed explicitly — no implicit `defineProps` without types

---

## Tech Stack

| Concern | Choice |
|---|---|
| Framework | Nuxt 3 |
| Language | TypeScript (strict) |
| Styling | Tailwind CSS (utility-first, custom brand palette) |
| Auth | Keycloak OAuth2/JWT (direct integration via `useAuth`) |
| HTTP | `$fetch` (Nuxt built-in) — inside service implementations only |
| State | Vue 3 composables + `useState` |
| Build | `npm run build` |

---

## Naming Conventions

| What | Convention |
|---|---|
| Components | PascalCase (`AppModal.vue`, `WorkflowCard.vue`) |
| Composables | camelCase prefixed with `use` (`useAuth.ts`, `useRequests.ts`) |
| Services | PascalCase suffixed with `Service` (`ApiWorkflowService.ts`) |
| Types/interfaces | PascalCase (`Workflow`, `RequestStatus`) |
| Status/enum-like unions | `type Foo = 'A' \| 'B'` — not TypeScript `enum` |

---

## Commands

```bash
npm run dev          # dev server
npm run build        # production build
npm run preview      # preview production build locally
npm run typecheck    # TypeScript strict check — run before considering a change done
```

---

## Do Nots

- Never call `$fetch` outside of service implementations
- Never put business logic in components — move it to a composable
- Never manage auth state outside of `useAuth`
- Never use TypeScript `enum` — use union types instead
- Never use `any` — fix the type instead
- Never add Pinia or Vuex — composables with `useState` are the pattern
- Never access `localStorage` directly outside of `useAuth`
