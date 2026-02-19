# TF2.14 - Implement Permission-Based UI Rendering


**Depends on:** TF1.3, TF1.2, TF2.9, TF2.11
**Blocks:** None (quality enhancement)

**Context:**
The UI must adapt dynamically based on the user's role, the request status, and the workflow step configuration. This includes showing/hiding action buttons, enabling/disabling form fields, and completely hiding fields configured as hidden.

Reference:
- `UI_TechnicalSpecification V1 (FR).md` > "Matrice de Permissions et d'Actions" and "Affichage des Champs Non-Editables"

**Objective:**
Implement the complete permission-based rendering system across all views.

**Instructions:**

1. **Create `composables/usePermissions.ts`:**
   ```typescript
   export const usePermissions = (request: Ref<Request | null>, workflow: Ref<Workflow | null>) => {
     const { user, isAdmin, hasRole } = useAuth()

     const isCreator = computed(() => request.value?.createdBy === user.value?.userId)
     const currentStep = computed(() => {
       if (!workflow.value || !request.value) return null
       return workflow.value.steps[request.value.currentStepIndex - 1]
     })

     const isValidatorAtCurrentStep = computed(() => {
       if (!currentStep.value || !user.value) return false
       return currentStep.value.validators.some(v =>
         (v.type === 'USER' && v.id === user.value!.userId) ||
         (v.type === 'ROLE' && user.value!.roles.includes(v.id))
       )
     })

     const isCommentatorAtCurrentStep = computed(() => {
       if (!currentStep.value || !user.value) return false
       return currentStep.value.commentators.some(c =>
         (c.type === 'USER' && c.id === user.value!.userId) ||
         (c.type === 'ROLE' && user.value!.roles.includes(c.id))
       )
     })

     const isObserverAtCurrentStep = computed(() => {
       if (!currentStep.value?.readOnlyViewers || !user.value) return false
       return currentStep.value.readOnlyViewers.some(o =>
         (o.type === 'USER' && o.id === user.value!.userId) ||
         (o.type === 'ROLE' && user.value!.roles.includes(o.id))
       )
     })

     // Action permissions
     const canEdit = computed(() => { /* Creator + DRAFT, or validator at current step */ })
     const canSubmit = computed(() => { /* Creator + DRAFT */ })
     const canApprove = computed(() => { /* Validator at current step + PENDING + NOT creator (creator cannot validate own request) */ })
     const canReject = computed(() => { /* Validator at current step + PENDING + NOT creator (creator cannot reject own request) */ })
     const canComment = computed(() => { /* Validator OR commentator OR creator */ })
     const canArchive = computed(() => { /* Creator+DRAFT, or Creator/Admin+APPROVED/REJECTED */ })

     // Field permissions
     const isFieldVisible = (fieldName: string): boolean => { /* Check hidden fields */ }
     const isFieldEditable = (fieldName: string): boolean => { /* Check read-only + status */ }
     const hiddenFieldCount = computed(() => { /* Count of hidden fields for indicator */ })

     // Creator cannot validate/reject their own request
     const isCreatorValidator = computed(() => isCreator.value && isValidatorAtCurrentStep.value)

     return {
       isCreator, isValidatorAtCurrentStep, isCommentatorAtCurrentStep, isObserverAtCurrentStep, isCreatorValidator,
       canEdit, canSubmit, canApprove, canReject, canComment, canArchive,
       isFieldVisible, isFieldEditable, hiddenFieldCount
     }
   }
   ```

2. **Implement permission matrix** (from spec):

   | Role | Status | Read | Edit | Validate | Comment | Archive |
   |------|--------|------|------|----------|---------|---------|
   | Creator | DRAFT | Yes | Yes | - | No | Yes (abandon) |
   | Creator | PENDING | Yes | No | - | No | No |
   | Creator | APPROVED/REJECTED | Yes | No | - | No | No |
   | Validator (current step) | PENDING | Yes | No | Yes | Yes | No |
   | Validator (other step) | Any | Yes | No | No | No | No |
   | Observer (current step) | PENDING | Yes | No | No | No | No |
   | Admin | Any | Yes | Yes | Yes | Yes | Yes |

3. **Non-editable field rendering:**
   - Disabled appearance: 50% opacity, grayed out
   - Cannot be modified (pointer-events: none)
   - Tooltip on hover: "Ce champ n'est pas editable a ce stade du workflow"

4. **Hidden field rendering:**
   - Completely invisible (not rendered in DOM)
   - If many fields hidden, show indicator: "[X champs masques selon votre role]"

5. **Create `components/ui/PermissionGate.vue`:**
   ```vue
   <template>
     <slot v-if="allowed" />
     <UiTooltip v-else-if="showDisabled" :text="reason">
       <slot name="disabled" />
     </UiTooltip>
   </template>
   ```
   - Wrapper component for permission-gated UI sections

**Acceptance Criteria:**
- Action buttons match the permission matrix exactly
- Non-editable fields appear grayed out with tooltips
- Hidden fields are completely invisible (no DOM element)
- Admin role overrides grant full access
- Validator matching works for both USER and ROLE actor types
- Hidden field count indicator shows when fields are hidden
- Permission checks are reactive (update when user/request/step changes)
- `usePermissions()` composable is reusable across all request views
