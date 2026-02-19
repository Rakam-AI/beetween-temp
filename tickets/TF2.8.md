# TF2.8 - Implement Request Editor/Creation View


**Depends on:** TF1.5, TF1.6, TF1.7, TF2.2, TF2.6, TF2.7
**Blocks:** TF2.9, TF2.13

**Context:**
The request editor is a dynamic multi-tab form that adapts to the selected workflow. Fields can be standard (fixed) or additional (defined by workflow). Field editability depends on request status and workflow step configuration (hidden/read-only fields).

Reference:
- `UI_Demand Edition (2).png` for visual layout
- `UI_TechnicalSpecification V1 (FR).md` > "Gestion des demandes > Vue editeur"
- `UI_Technical Specification V1 (FR) - IMPLEMENTATION_DETAILS.md` > "Edition Dynamique"

**Objective:**
Implement the request creation and editing form with dynamic field rendering based on workflow configuration.

**Instructions:**

1. **Implement `pages/requests/new.vue` and `pages/requests/[id].vue`:**
   - Share a common `RequestEditor.vue` component
   - Create mode: Start by selecting a workflow
   - Edit mode: Load existing request

2. **Create `components/request/RequestEditor.vue`:**

   - **Header:** "Retour" back button

   - **Workflow progress bar** at top (reuse `WorkflowProgressBar.vue`)
     - Shows current position in validation workflow
     - Current step highlighted in orange

   - **Tab navigation:**
     - **General**: Basic info (workflow selector, job title, country, city, language, mission description, profile description)
     - **Entreprise**: Company-related fields
     - **Contact**: Contact information
     - **Contrat**: Contract details (type, form, duration, salary range, benefits)
     - **Le poste**: Position details (department, level, positions count, location)
     - **Profil**: Candidate profile requirements
     - **Candidature**: Application details
     - Tabs show red dot indicator if they contain validation errors

   - **Workflow selector** (first field in General tab):
     - Dropdown: "Selectionner un workflow"
     - Only shows PUBLISHED workflows
     - Only modifiable when request is in DRAFT status
     - Selecting a workflow dynamically updates visible fields and additional fields

   - **Standard fields per tab** (matching the mockup):
     - General: Titre du poste, Pays, Ville, Langue principale, Description de la mission, Profil recherche
     - Contrat: Type de contrat, Forme de contrat, Nombre de postes, Salaire min/max, Avantages, Date de debut
     - (Other tabs populated from BusinessInfo fields)

   - **Additional fields section:**
     - Render workflow's additional field definitions as form inputs
     - Dynamic rendering based on FieldType:
       - TEXT → text input
       - NUMBER → number input
       - DATE → date picker
       - DROPDOWN → select with options
       - CHECKBOX → checkbox

3. **Dynamic field visibility and editability:**
   - If request is DRAFT: All non-hidden fields are editable by creator
   - If request is PENDING:
     - Creator: Read-only (all fields disabled, grayed at 50% opacity)
     - Validator at current step: **Read-only** (validators can approve/reject/comment but NOT edit fields)
     - Others: Read-only
   - Hidden fields (from workflow step config): Completely invisible (no DOM element)
   - If many fields hidden, show: "[X champs masques selon votre role]"

4. **Footer actions:**
   - **Sauvegarder brouillon** (Save draft): Visible for creator in DRAFT
     - Saves without full validation
     - Toast: "Brouillon sauvegarde"
   - **Soumettre pour validation** (Submit): Visible for creator in DRAFT
     - Validates all required fields first
     - If validation fails: scroll to first error, show all errors
     - If valid: Show confirmation, transition to PENDING
     - Toast: "Demande soumise avec succes"

5. **Optimistic locking (version field):**
   - When saving, send the `version` field from the loaded request
   - If the server returns 409 Conflict, show: "Cette demande a ete modifiee par un autre utilisateur. Veuillez recharger."
   - Provide a "Recharger" button to reload the request

6. **Duplicate button:**
   - Visible on REJECTED requests for the creator only
   - Button: "Dupliquer" - creates a new DRAFT pre-filled with the rejected request's data
   - Navigates to the new request in edit mode after duplication

7. **History section:**
   - Show request history timeline below the form (using `UiTimeline` component)
   - Each entry shows: action type icon, actor name, timestamp, comment/reason if any
   - Field changes shown as diff (old value → new value) for UPDATE entries

8. **Client-side validation:**
   - On blur: validate field format (email, URL, date, number ranges, min/max length)
   - On submit: validate all required fields are filled
   - Error display: Red text below field, red border, red dot on tab

**Acceptance Criteria:**
- Editor layout matches `UI_Demand Edition (2).png` mockup
- Workflow selector loads published workflows only
- Workflow selection dynamically adjusts visible fields
- Tab navigation works with error indicators
- All standard and additional fields render correctly
- Field editability respects status and workflow step config
- Hidden fields are completely invisible
- Non-editable fields are grayed out with tooltip
- Save draft works without full validation
- Submit validates all required fields before transitioning
- Validation errors show inline and on tabs
- Scroll to first error on submit failure
