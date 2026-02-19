# TF2.4 - Implement Workflow List View


**Depends on:** TF1.5, TF1.6, TF1.7, TF2.2
**Blocks:** TF2.5

**Context:**
The workflow list is an admin-only view showing all validation workflows for the company with their status, step count, validator count, and number of associated requests.

Reference:
- `UI_Workflow List.png` for visual layout
- `UI_TechnicalSpecification V1 (FR).md` > "Administration des workflows de validation > Liste"

**Objective:**
Implement the workflow list page matching the mockup design.

**Instructions:**

1. **Implement `pages/workflows/index.vue`:**

   - **Header section:**
     - Title: "Circuits de validation"
     - Search bar for filtering by workflow name/description
     - "+ Nouveau workflow" button (navigates to `/workflows/new`)

   - **Workflow table** with columns:
     - **Nom**: Workflow name + description snippet (with workflow icon)
     - **Etapes**: Step count (e.g. "5 etapes")
     - **Validateurs**: Total validator count across all steps (with avatar stack)
     - **Demandes**: Active request count using this workflow
     - **Statut**: Publication status badge:
       - "Publie" → green badge
       - "Brouillon" → gray badge
       - "Desactive" → orange badge
     - **Actions**: Action buttons (edit, delete)

2. **Implement filter functionality:**
   - Filter by status (Publie / Brouillon / Desactive / Tous)
   - Search by name/description (debounced)

3. **Implement row actions:**
   - **Edit**: Navigate to `/workflows/{id}`
   - **Delete**: Show confirmation modal, call `deleteWorkflow(id)`
     - Block deletion if workflow has active requests (show tooltip explaining why)
   - **Quick status toggle**: Allow publish/unpublish directly from list

4. **Handle states:**
   - Loading: Show skeleton/spinner while fetching
   - Empty: Show "Aucun workflow trouve" with create CTA
   - Error: Show error message with retry button

5. **Implement pagination** at bottom of table

6. **Guard access:** Only ADMIN users can see this page (use admin middleware)

**Acceptance Criteria:**
- Table layout matches `UI_Workflow List.png` mockup
- All columns display correct data
- Search filters workflows in real-time (debounced)
- Status badges use correct semantic colors
- Delete shows confirmation modal before executing
- Cannot delete workflows with active requests
- Navigation to edit/create works correctly
- Pagination works correctly
- Non-admin users are redirected away
- Loading and empty states are handled
