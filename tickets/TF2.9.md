# TF2.9 - Implement Request Details View


**Depends on:** TF1.5, TF1.6, TF1.7, TF2.2, TF2.7, TF2.8
**Blocks:** TF2.10, TF2.11

**Context:**
The request details view shows all information about a request including position details, company info, history, and metadata. It provides action buttons for validation operations based on the user's role and the request's current state.

Reference:
- `UI_Demand Details.png` for details tab layout
- `UI_Demand Details ActionHistory.png` for history tab layout
- `UI_TechnicalSpecification V1 (FR).md` > "Gestion des demandes > Vue editeur" (details sections)

**Objective:**
Implement the request details page with all tabs and the right information panel.

**Instructions:**

1. **Implement `pages/requests/[id].vue`:**
   - Determine mode: If DRAFT and user is creator → show editor (TF2.8). Otherwise → show details view.

2. **Create `components/request/RequestDetails.vue`:**

   - **Header section:**
     - Back button ("Detail de la demande")
     - Status badges (e.g., "Multi", "En attente")
     - Job title (large, bold): e.g., "Developpeur Full Stack"
     - Contract summary: e.g., "CDI - Senior - Technologies"
     - Action buttons row (right-aligned): Commenter, Rejeter, Approuver (see TF2.11)

   - **Workflow progress bar:**
     - 5-stage progress visualization with step dates
     - Steps: Soumission → Step 1 → Step 2 → ... → Validation finale
     - Each step shows completion date if passed
     - Current step highlighted

   - **Tab navigation:**
     - **Details**: Position information
     - **Entreprise**: Company information
     - **Historique (N)**: Action history with count badge (see TF2.10)
     - **Informations**: Additional metadata

3. **Details tab content:**
   - **Informations du poste:**
     - Display all BusinessInfo fields in a structured layout:
       - Type de contrat, Forme de contrat, Postes, Localisation
       - Domaine, Niveau, Salaire (range), Remuneration/Avantages
     - Key-value pair display with labels on left, values on right

   - **Description du poste:**
     - Rich text display of the position description

   - **Profil recherche:**
     - Rich text display of the desired candidate profile

   - **Additional fields:**
     - Display workflow-defined additional field values

4. **Right information panel** (sidebar):
   - **Cree le**: Creation date
   - **Cree par**: Creator name and role
   - **Workflow**: Workflow name
   - **Etape actuelle**: Current step name
   - **Validateurs en attente**: List of pending validators with status badges ("En attente")

5. **Informations tab:**
   - Technical metadata (ID, version, dates)
   - Workflow details summary

**Acceptance Criteria:**
- Details layout matches `UI_Demand Details.png` mockup
- All BusinessInfo fields are displayed correctly
- Workflow progress bar shows correct step completion
- Right sidebar shows current status and pending validators
- Tab navigation works with badge count for history
- Additional fields from workflow are displayed
- View adapts based on user permissions (action buttons visibility)
- Loading state handled with skeleton/spinner
- 404 handling if request not found
