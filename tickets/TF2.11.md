# TF2.11 - Implement Request Validation Actions & Modals


**Depends on:** TF1.5, TF1.6, TF2.9
**Blocks:** None

**Context:**
Validation actions (approve, reject, comment, submit, archive) are shown as buttons whose visibility depends on the user's role, the request status, and the current workflow step. Each critical action requires a confirmation modal.

Reference:
- `UI_TechnicalSpecification V1 (FR).md` > "Vue details - Actions de Validation" and "Matrice de Permissions et d'Actions"
- `UI_Technical Specification V1 (FR) - IMPLEMENTATION_DETAILS.md` > "Gestion des Erreurs > Confirmations Modales"

**Objective:**
Implement all validation action buttons and their associated confirmation modals with role-based visibility.

**Instructions:**

1. **Create `components/request/RequestActions.vue`:**
   - Props: `request: Request`, `workflowStep: WorkflowStep`
   - Uses `useAuth()` and `useRequests()` composables

2. **Action buttons (in order per spec):**

   - **Sauvegarder brouillon** (Save draft):
     - Visible: Creator only, request in DRAFT
     - Action: Save current form state
     - Toast: "Brouillon sauvegarde" (auto-hide 3s)

   - **Soumettre pour validation** (Submit):
     - Visible: Creator only, request in DRAFT, all required fields filled
     - Disabled with tooltip if required fields missing: "Certains champs obligatoires ne sont pas remplis"
     - Action: Show confirmation modal → call `submitRequest()`
     - Toast: "Demande soumise avec succes"

   - **Approuver** (Approve):
     - Visible: Validators of the current step only, request is PENDING
     - Style: Green button (primary action)
     - Modal content:
       - Title: "Confirmer l'approbation"
       - Message: "Confirmez-vous l'approbation de cette demande?"
       - Optional comment field: "Commentaire d'approbation (optionnel)"
       - Buttons: Annuler / Approuver
     - After approval:
       - ONE mode: Automatic step advance
       - ALL mode: Wait for other validators
     - Toast: "Demande approuvee"

   - **Rejeter** (Reject):
     - Visible: Validators of the current step only, request is PENDING
     - Style: Red/danger button
     - Modal content:
       - Title: "Confirmer le rejet"
       - Message: "Confirmez-vous le rejet? Cette action est definitive"
       - **Raison du rejet** (required): Textarea
       - **Commentaires** (optional): Textarea
       - Buttons: Annuler / Rejeter
     - Toast: "Demande rejetee"

   - **Commenter** (Comment):
     - Visible: Validators + commentators of current step
     - **Note**: Per the spec, the creator can comment only if they are also a commentator at the current step (not automatically). However, UI spec says creator can always comment — align with backend behavior (creator excluded unless explicitly in commentators list)
     - Modal content:
       - Comment textarea (required)
       - Buttons: Annuler / Envoyer
     - Toast: "Commentaire ajoute"

   - **Dupliquer** (Duplicate):
     - Visible: Creator only, request in REJECTED status
     - Action: Call `duplicateRequest()`, navigate to new request editor
     - Toast: "Demande dupliquee. Vous pouvez modifier et soumettre a nouveau."

   - **Archiver** (Archive):
     - Visible:
       - Creator in DRAFT → "Abandonner" (abandon)
       - Creator or admin for APPROVED/REJECTED → "Archiver"
     - Not visible for PENDING requests
     - Modal (DRAFT): "Etes-vous sur? Ce brouillon ne pourra plus etre recupere"
     - Modal (APPROVED/REJECTED): "Confirmer l'archivage de cette demande?"
     - Buttons: Annuler / Confirmer

3. **Disabled button states:**
   - When a button is not applicable but would normally be visible, show it disabled
   - Tooltip on hover explaining why: e.g., "Vous n'etes pas validateur de cette etape"

4. **Determine validator status:**
   - Extract current user from `useAuth()`
   - Check if user matches any validator in the current step (by USER id or ROLE)
   - Check if user matches any commentator in the current step

**Acceptance Criteria:**
- Approve button only visible to current step validators
- Reject button only visible to current step validators
- Comment button visible to validators + commentators + creator
- Submit button only visible to creator in DRAFT
- Archive button follows the permission matrix exactly
- Confirmation modals show before every critical action
- Reject modal requires a reason (cannot submit without)
- Approval modal has optional comment field
- Success toasts display after each action
- Disabled buttons show tooltip explanation
- ONE/ALL validation mode is handled correctly on approval
