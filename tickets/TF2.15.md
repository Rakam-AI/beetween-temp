# TF2.15 - Implement Concurrent Edit Handling


**Depends on:** TF1.5, TF2.3, TF2.8, TF2.13
**Blocks:** None

**Context:**
When two users edit the same request simultaneously, the backend returns HTTP 409 Conflict. The frontend must detect this and offer the user options to resolve the conflict.

Reference:
- `UI_TechnicalSpecification V1 (FR).md` > "Edition Concurrente"

**Objective:**
Implement the concurrent edit detection and resolution UI.

**Instructions:**

1. **Create `components/request/ConflictResolutionModal.vue`:**
   - Trigger: Shown when API returns HTTP 409 on save/update
   - Modal content:
     - Title: "Modification concurrente detectee"
     - Message: "Cette demande a ete modifiee par un autre utilisateur"
     - Three action buttons:
       - **Recharger** (Reload): Fetches the latest version, discards local changes
       - **Voir les differences** (View diff): Shows a comparison of local vs server changes
       - **Garder mes changements** (Keep my changes): Retries the save with force flag (warning: last write wins)

2. **Implement diff view:**
   - Side-by-side comparison of changed fields
   - Highlight differences in red (removed) and green (added)
   - Show field name, server value, local value

3. **Version tracking:**
   - The `Request.version` field is used for optimistic locking
   - Include version in every update request
   - If 409 is returned, the version has changed server-side

4. **Integration with request editor:**
   - Catch `ApiConflictError` in save/update operations
   - Open `ConflictResolutionModal` automatically
   - After resolution, update local state with correct version

5. **Auto-refresh suggestion:**
   - If viewing a request details and a conflict is detected, suggest refreshing
   - Optional: poll for updates periodically (low frequency)

**Acceptance Criteria:**
- 409 errors trigger the conflict resolution modal
- Reload option fetches latest version and updates the form
- Diff view shows all changed fields clearly
- "Keep my changes" retries with updated version
- Version number is tracked and sent with every update
- Modal is non-dismissible (must choose an action)
- Works correctly with the request editor form
