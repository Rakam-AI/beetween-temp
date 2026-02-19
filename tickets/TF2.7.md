# TF2.7 - Implement Dashboard & Request List View


**Depends on:** TF1.5, TF1.6, TF1.7, TF2.2
**Blocks:** TF2.8, TF2.9

**Context:**
The dashboard is the main landing page showing an overview of the user's requests with quick statistics and a list of active demands. Users can create new requests and see which ones need their attention.

Reference:
- `UI_Dashboard.png` for visual layout
- `UI_TechnicalSpecification V1 (FR).md` > "Gestion des demandes > Vue liste"

**Objective:**
Implement the dashboard page and request list with filters, search, and workflow progress visualization.

**Instructions:**

1. **Implement `pages/index.vue` (Dashboard):**

   - **Left panel - Quick actions & stats:**
     - "Nouvelle demande" button to create a new request
     - Statistics summary:
       - En cours: count of PENDING requests
       - Validees: count of APPROVED requests
       - En attente: count requiring user's validation action

   - **Main content area:**
     - **Alert banner** (if applicable): "{N} demande(s) de recrutement necessitent votre validation" (orange/yellow)
     - **Tab navigation:**
       - **Mes demandes**: Requests created by the current user
       - **Mes validations en attente**: Requests where the current user is a validator at the current step and status is PENDING (uses `pendingValidatorId` filter)
     - **Request cards list** showing active/recent requests

2. **Implement `pages/requests/index.vue` (Full request list):**

   - **Header:**
     - Title: "Mes demandes"
     - Search bar (keyword search on title, description, requester)
     - Filter controls:
       - Status filter (Brouillon, En attente, Approuvee, Rejetee, Archivee, Tous)
       - Date range filter
       - Workflow filter (dropdown of available workflows)
       - Requester filter
     - Sorting: by date created, by status

   - **Request cards** (each card shows):
     - Request ID (e.g., "DAR-2024-001") in subtle text
     - Job title (bold) with contract info (e.g., "CDI - Senior - Technologies")
     - Creator name (e.g., "Cree par Marie Leroy")
     - Mini workflow progress breadcrumb:
       - Visual step indicators showing completion status
       - Each step: colored circle/line (green=complete, blue=current, gray=pending)
       - Step names below dots (e.g., "Soumission → RH → Manager → Direction → Validation finale")
       - Tooltip on hover showing step details
     - Current status text: e.g., "En attente de validation au niveau 1"
     - Status badge (colored per semantic scheme)
     - Quick action buttons (Voir, Editer, Soumettre) based on permissions

3. **Create `components/request/RequestCard.vue`:**
   - Reusable card component for request display
   - Shows all info described above
   - Click navigates to request detail page

4. **Create `components/request/WorkflowProgressBar.vue`:**
   - Visual workflow step progress indicator
   - Props: `steps: string[]`, `currentStepIndex: number`, `status: RequestStatus`
   - Steps shown as connected dots with labels
   - Color coding: completed (green), current (blue/orange), future (gray), rejected (red)

5. **Create `components/request/RequestFilters.vue`:**
   - Filter panel component
   - Status chips for quick filtering
   - Collapsible advanced filters

6. **Implement grouping by status** (optional toggle)

7. **Pagination** at the bottom

**Acceptance Criteria:**
- Dashboard layout matches `UI_Dashboard.png` mockup
- Request cards show all required information
- Mini workflow progress bar visualizes step completion correctly
- Search filters requests by keyword (debounced)
- Status filter works correctly
- "Nouvelle demande" navigates to request creation
- Cards link to request detail/edit pages
- Alert banner shows when user has pending validations
- Quick stats reflect current data
- Pagination works correctly
- Empty state is handled gracefully
