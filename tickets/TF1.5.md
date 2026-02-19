# TF1.5 - Set Up Base UI Components Library


**Depends on:** TF1.1
**Blocks:** TF2.4, TF2.5, TF2.6, TF2.7, TF2.8, TF2.9, TF2.10, TF2.11, TF2.12, TF2.13

**Context:**
The UI follows a consistent design system with semantic colors, reusable components, and micro-interactions. The design must be "premium, intuitive and reactive" per specifications.

Reference:
- `UI_TechnicalSpecification V1 (FR).md` > "Principes de Conception" and "Gestion des Erreurs"
- `UI_Technical Specification V1 (FR) - IMPLEMENTATION_DETAILS.md` > "Fonctionnalités UX et Design"
- All UI mockup PNGs for visual reference

**Objective:**
Create the foundational UI component library used across all views.

**Instructions:**

1. **Create base form components in `components/ui/`:**

   - **`UiButton.vue`**: Primary, secondary, danger, ghost variants. Sizes: sm, md, lg. Loading state with spinner. Disabled state with tooltip support.
   - **`UiInput.vue`**: Text input with label, placeholder, error message slot, disabled/readonly states. Supports `@blur` validation.
   - **`UiTextarea.vue`**: Multi-line input with same features as UiInput.
   - **`UiSelect.vue`**: Dropdown select with label, options, placeholder, error state.
   - **`UiCheckbox.vue`**: Checkbox with label, indeterminate state.
   - **`UiToggle.vue`**: Toggle switch for boolean states (e.g. workflow publish).

2. **Create feedback components in `components/ui/`:**

   - **`UiModal.vue`**: Modal dialog with title, body slot, footer actions. Confirmation variant with "Confirm/Cancel". Close on escape and backdrop click.
   - **`UiToast.vue`**: Toast notification (top-right). Variants: success (green), error (red), warning (orange), info (blue). Auto-hide after configurable duration (default 3s for success, 5s for errors).
   - **`UiToastContainer.vue`**: Container managing multiple toasts.
   - **`UiSpinner.vue`**: Loading spinner with optional message text. Shows "Chargement en cours..." if loading > 2s.
   - **`UiBadge.vue`**: Status badge with semantic colors (Brouillon, En attente, Approuvée, Rejetée, Archivée, Publié).
   - **`UiTooltip.vue`**: Hover tooltip for explaining disabled actions.

3. **Create layout components in `components/ui/`:**

   - **`UiCard.vue`**: Card container with optional header, body, footer slots.
   - **`UiTable.vue`**: Data table with sortable columns, row actions.
   - **`UiPagination.vue`**: Pagination controls (previous, page numbers, next).
   - **`UiTabs.vue`** and **`UiTabPanel.vue`**: Tab navigation with active indicator and optional error dot.
   - **`UiEmptyState.vue`**: Empty state placeholder with icon and message.
   - **`UiSearchBar.vue`**: Search input with debounced filtering.

4. **Create additional form & display components in `components/ui/`:**

   - **`UiDatePicker.vue`**: Date picker input with label, min/max date support, error state. Used for date-type additional fields.
   - **`UiAutocomplete.vue`**: Autocomplete/search input for selecting users (validators, commentators). Fetches suggestions on type. Used in workflow step configuration.
   - **`UiFileUpload.vue`**: File upload input with drag-and-drop, file type validation, size limit display. Used for FILE-type additional fields.
   - **`UiStepper.vue`**: Visual step indicator showing workflow progression (step number, name, status). Highlights current step, shows completed steps in green, pending in gray.
   - **`UiTimeline.vue`**: Vertical timeline component for displaying request history entries (action, actor, date, comment). Each entry shows icon based on action type.

5. **Create `components/ui/UiConfirmModal.vue`:**
   - Specialized modal for confirmation dialogs
   - Props: title, message, confirmLabel, confirmVariant (danger/primary), cancelLabel
   - Used for: approve, reject, archive, delete confirmations
   - Per spec confirmations:
     - Approve: "Confirmez-vous l'approbation de cette demande?"
     - Reject: "Confirmez-vous le rejet? Cette action est definitive"
     - Archive draft: "Etes-vous sur? Ce brouillon ne pourra plus etre recupere"
     - Delete workflow: "Etes-vous sur? Cette action est irreversible"

6. **Define semantic color mappings** in a utility or constants file:
   - Green (#10b981): Validated step/request
   - Blue (#3b82f6): In progress
   - Red (#ef4444): Rejected/Error
   - Orange (#f59e0b): Attention/Action required
   - Gray: Draft/Inactive

**Acceptance Criteria:**
- All base components render correctly with Tailwind CSS styling
- Components accept props for all visual variants
- Modal has keyboard support (Escape to close)
- Toast notifications auto-dismiss
- Badge colors match the semantic color scheme from specs
- Components are generic and reusable (no business logic)
- All components have TypeScript props definitions
