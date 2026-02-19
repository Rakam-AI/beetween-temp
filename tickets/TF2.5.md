# TF2.5 - Implement Workflow Editor View


**Depends on:** TF1.5, TF1.6, TF1.7, TF2.2, TF2.4
**Blocks:** TF2.6

**Context:**
The workflow editor allows admins to create and modify workflows including general information, publication status, validation steps ordering, and additional field definitions.

Reference:
- `UI_Workflow Edition.png` for visual layout
- `UI_TechnicalSpecification V1 (FR).md` > "Administration des workflows > Creation, modification et suppression"

**Objective:**
Implement the workflow editor page for both create and edit modes.

**Instructions:**

1. **Implement `pages/workflows/[id].vue` and `pages/workflows/new.vue`:**
   - Share a common `WorkflowEditor.vue` component
   - Edit mode: Load existing workflow by ID
   - Create mode: Start with empty workflow template

2. **Create `components/workflow/WorkflowEditor.vue`:**

   - **Header:** "Editeur de workflow" with back button

   - **General information section:**
     - **Nom du workflow**: Text input (required)
     - **Description**: Textarea (optional)

   - **Publication toggle:**
     - Toggle switch: "Publier le workflow pour le rendre utilisable"
     - Only active workflows (PUBLISHED) are selectable for new requests
     - Cannot publish if any step has zero validators (show validation error)

   - **Validation steps section:**
     - Title: "Etapes de validation" with subtitle "Reorganisez les etapes par glisser-deposer"
     - "+ Ajouter une etape" button
     - Ordered list of steps showing:
       - Drag handle (6-dot icon) for reordering
       - Step name
       - Validator count badge
       - Expand/collapse toggle
       - Delete button (trash icon)
     - Drag-and-drop reordering (use a library like `vuedraggable` or `@vueuse/integrations`)
     - Clicking a step or expand toggle opens the step editor (TF2.6)

   - **Additional fields section:**
     - List of defined additional fields with name, type, and delete button
     - "+ Ajouter un champ" button opening a field editor modal:
       - Field name (required)
       - Field label (required)
       - Field type dropdown: TEXT, NUMBER, DATE, DROPDOWN, CHECKBOX
       - Conditional options list (for DROPDOWN type)
       - Default value (optional)
     - Edit existing fields inline or via modal

   - **Global required fields section:**
     - Checkbox grid of standard fields that are globally required across all steps

3. **Footer actions:**
   - **Annuler** (Cancel): Navigate back to list with unsaved changes warning
   - **Previsualiser** (Preview): Show read-only preview of the workflow
   - **Sauvegarder** (Save): Validate and save, show success toast

4. **Validation rules before save:**
   - Workflow name is required and non-blank
   - At least one step exists
   - Each step has at least one validator (enforced by step editor)
   - Dropdown additional fields have at least one option

5. **Read-only mode:**
   - If workflow is PUBLISHED, show a warning banner:
     "Ce workflow est publie. Depubliez-le d'abord pour pouvoir le modifier."
   - If workflow is PUBLISHED **and** has active requests:
     "Ce workflow est actuellement utilise par X demande(s). Vous devez d'abord le depublier et attendre la fin des demandes actives."
   - Disable all inputs for any PUBLISHED workflow (not just those with active requests)

**Acceptance Criteria:**
- Editor layout matches `UI_Workflow Edition.png` mockup
- Create and edit modes work correctly
- Drag-and-drop step reordering works smoothly
- Steps can be added and removed
- Additional fields can be added, edited, and removed
- Publication toggle validates before publishing
- Read-only mode prevents editing active workflows
- Cancel shows unsaved changes warning
- Save validates and persists (via composable)
- Success/error toasts display appropriately
