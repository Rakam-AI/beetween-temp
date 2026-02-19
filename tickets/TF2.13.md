# TF2.13 - Implement Error Handling & Form Validation System


**Depends on:** TF1.5, TF2.8
**Blocks:** None (quality enhancement)

**Context:**
The application needs a comprehensive error handling system covering client-side validation, backend error responses, and user feedback through toasts and modals.

Reference:
- `UI_TechnicalSpecification V1 (FR).md` > "Gestion des Erreurs et Validations" (full section)

**Objective:**
Implement the complete error handling and validation system as specified.

**Instructions:**

1. **Create `composables/useFormValidation.ts`:**
   ```typescript
   export const useFormValidation = () => {
     const errors = ref<Record<string, string>>({})
     const touched = ref<Record<string, boolean>>({})

     // Validators
     function validateRequired(field: string, value: any, label: string): boolean { ... }
     function validateEmail(field: string, value: string): boolean { ... }
     function validateUrl(field: string, value: string): boolean { ... }
     function validateMinLength(field: string, value: string, min: number): boolean { ... }
     function validateMaxLength(field: string, value: string, max: number): boolean { ... }
     function validateNumberRange(field: string, value: number, min?: number, max?: number): boolean { ... }
     function validateDate(field: string, value: string): boolean { ... }

     // On blur handler
     function onBlur(field: string, value: any, rules: ValidationRule[]): void { ... }

     // Full form validation
     function validateAll(fields: FormField[]): boolean { ... }

     // Scroll to first error
     function scrollToFirstError(): void { ... }

     // Clear errors
     function clearError(field: string): void { ... }
     function clearAll(): void { ... }

     return { errors, touched, onBlur, validateAll, scrollToFirstError, clearError, clearAll }
   }
   ```

2. **Implement on-blur validation:**
   - Validate field format on blur event
   - Show inline error immediately below the field
   - Error styling: Red text (#EF4444), red border, error icon

3. **Implement on-submit validation:**
   - Check ALL required fields (not just first)
   - Display all error messages simultaneously
   - Scroll to the first field with an error
   - Show red dot indicator on tabs containing errors
   - Show global validation toast: "Veuillez corriger les erreurs avant de soumettre"

4. **Create `composables/useErrorHandler.ts`:**
   - Global error handler for API errors:
     ```typescript
     function handleError(error: unknown): void {
       if (error instanceof ApiValidationError) {
         // Map field errors to form errors
         toast.error('Erreur de validation')
       } else if (error instanceof ApiPermissionError) {
         toast.error("Vous n'avez pas les permissions pour cette action")
       } else if (error instanceof ApiNotFoundError) {
         toast.error("La demande n'existe plus ou a ete supprimee")
         navigateTo('/requests')
       } else if (error instanceof ApiConflictError) {
         // Trigger concurrent edit modal (TF2.15)
       } else if (error instanceof ApiServerError) {
         toast.error("Une erreur serveur s'est produite. Veuillez reessayer ou contacter le support")
       }
     }
     ```

5. **Backend error message mapping** (per spec):
   - 400: Display backend's specific message
   - 401: "Votre session a expire, veuillez vous reconnecter"
   - 403: "Vous n'avez pas les permissions pour cette action"
   - 404: "La demande n'existe plus ou a ete supprimee"
   - 409: "Cette demande a ete modifiee par quelqu'un d'autre. Veuillez recharger"
   - 500: "Une erreur serveur s'est produite. Veuillez reessayer ou contacter le support"

6. **Temporal feedback** (per spec):
   - Save draft: Toast "Brouillon sauvegarde" (auto-hide 3s)
   - Submit: Toast "Demande soumise avec succes"
   - Approve: Toast "Demande approuvee"
   - Reject: Toast "Demande rejetee"
   - Loading: Spinner with "Chargement en cours..." if > 2s
   - Network error: Toast "Probleme de connexion, reessai en cours..."

**Acceptance Criteria:**
- On-blur validation shows inline errors immediately
- On-submit validation checks all fields and shows all errors
- Scroll to first error works correctly
- Tab error indicators (red dots) show when tab has errors
- All backend error codes are mapped to French messages
- Success toasts auto-dismiss after 3s
- Error toasts auto-dismiss after 5s
- Loading spinner appears after 2s delay
- Form validation composable is reusable across all forms
