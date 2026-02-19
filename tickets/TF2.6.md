# TF2.6 - Implement Workflow Step Editor Component


**Depends on:** TF1.5, TF2.5
**Blocks:** TF2.8 (request editor needs step config awareness)

**Context:**
Each workflow step has a detailed configuration panel for validators, commentators, validation mode, and field constraints. This is shown as an expandable section within the workflow editor.

Reference:
- `UI_WorkFlow StepEdition.png` for visual layout
- `UI_TechnicalSpecification V1 (FR).md` > "Edition des etapes de validation"

**Objective:**
Implement the step editor component used within the workflow editor.

**Instructions:**

1. **Create `components/workflow/WorkflowStepEditor.vue`:**
   - Props: `step: WorkflowStep`, `index: number`, `isExpanded: boolean`
   - Emits: `update:step`, `delete`, `toggle`

2. **Collapsed view:**
   - Step name with colored circle badge
   - Validator count
   - Drag handle
   - Expand toggle button
   - Delete button

3. **Expanded view (additional section below collapsed header):**

   - **Description** (optional): Textarea
     - Placeholder: "Description de cette etape de validation"

   - **Mode de validation**: Dropdown select
     - "Un seul validateur suffit (ONE)"
     - "Tous les validateurs doivent approuver (ALL)"

   - **Validateurs** (required, at least one):
     - List of assigned validators showing name/role badges
     - "Ajouter un validateur..." link/button
     - Search autocomplete for users/groups:
       - Type selector: Utilisateur / Groupe
       - Search input with autocomplete results
       - Selection adds to validator list
     - Each validator has a remove (X) button

   - **Commentateurs** (optional):
     - Same UI as validators but optional
     - "Ajouter un commentateur..." link/button
     - Same search autocomplete pattern

   - **Champs obligatoires pour cette etape** (Required fields for this step):
     - Checkbox grid of standard fields:
       - Titre du poste, Departement, Salaire, Profil recherche, Niveau d'experience
       - Type de contrat, Localisation, Description, Date de debut
     - These are mandatory specifically at this step

   - **Champs masques pour cette etape** (Hidden fields for this step):
     - Same checkbox grid
     - Fields selected here will not be visible during this step
     - A field cannot be both required AND hidden (mutual exclusion validation)

   - **Champs en lecture seule pour cette etape** (Read-only fields for this step):
     - Same checkbox grid
     - Fields selected here will be visible but non-editable at this step
     - A field cannot be both hidden AND read-only (mutual exclusion validation)

   - **Observateurs en lecture seule** (Read-only observers):
     - Optional list of users/roles who can view the request at this step but cannot take any action
     - Same search autocomplete UI as validators/commentators
     - "Ajouter un observateur..." link/button

4. **Validation:**
   - At least one validator must be assigned
   - Step name must not be blank
   - No field can be both in requiredFields and hiddenFields
   - No field can be both in hiddenFields and readOnlyFields

5. **Animations:**
   - Smooth expand/collapse transition
   - Fade-in for search results

**Acceptance Criteria:**
- Step editor layout matches `UI_WorkFlow StepEdition.png` mockup
- Validation mode selector works correctly (ONE/ALL)
- Validators can be added via search autocomplete and removed
- Commentators can be added and removed
- Required field checkboxes save correctly
- Hidden field checkboxes save correctly
- Mutual exclusion between required and hidden fields is enforced
- At least one validator is required (show error if none)
- Expand/collapse animation is smooth
- All changes emit properly to parent workflow editor
