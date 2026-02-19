# TF1.7 - Set Up Routing, Layouts & Page Skeletons


**Depends on:** TF1.1, TF1.5
**Blocks:** TF2.4, TF2.7, TF2.8, TF2.9, TF2.12

**Context:**
The application has a sidebar navigation layout with main content area. Navigation includes: Dashboard (Tableau de bord), My Requests (Mes demandes), Workflows (Modeles), Statistics (Statistiques), and Settings (Parametres).

Reference:
- `UI_Dashboard.png` for sidebar layout reference
- `UI_TechnicalSpecification V1 (FR).md` > "Modules UI"

**Objective:**
Create the application layout with sidebar navigation and set up file-based routing with skeleton pages.

**Instructions:**

1. **Create `layouts/default.vue`:**
   - Left sidebar with:
     - Application logo at top
     - Search bar ("Rechercher...")
     - Navigation sections:
       - **GESTION**: Tableau de bord, Mes demandes, Modeles
       - **ADMINISTRATION**: Statistiques, Parametres
     - Active route highlighting
     - Collapsible sidebar for smaller screens
   - Main content area taking remaining width
   - Top bar with user info/avatar (from `useAuth()`)

2. **Create page files for file-based routing:**
   ```
   pages/
   ├── index.vue                    # Dashboard (Tableau de bord)
   ├── requests/
   │   ├── index.vue                # Request list (Mes demandes)
   │   ├── new.vue                  # New request creation
   │   └── [id].vue                 # Request details/edit
   ├── workflows/
   │   ├── index.vue                # Workflow list (Modeles)
   │   ├── new.vue                  # New workflow creation
   │   └── [id].vue                 # Workflow edit
   ├── statistics.vue               # Statistics dashboard
   └── settings.vue                 # Settings page
   ```

3. **Each page skeleton should contain:**
   - Page title/header
   - Breadcrumb navigation
   - Loading state placeholder
   - A comment indicating which ticket will implement the full view

4. **Create `middleware/auth.global.ts`:**
   - Protect all routes except any public pages
   - Redirect unauthenticated users

5. **Create `middleware/admin.ts`:**
   - Route-level guard for admin-only pages (workflows, settings)
   - Check `useAuth().isAdmin`

6. **Create `components/layout/AppSidebar.vue`:**
   - Responsive sidebar component
   - Navigation items with icons
   - Section grouping (GESTION / ADMINISTRATION)
   - Active link styling

7. **Create `components/layout/AppTopBar.vue`:**
   - User avatar and name
   - Optional breadcrumb
   - Mobile menu toggle

**Acceptance Criteria:**
- Sidebar layout matches the mockup structure
- All routes are accessible via sidebar navigation
- Active route is visually highlighted in sidebar
- Page transitions work smoothly
- Auth middleware redirects unauthenticated users
- Admin middleware blocks non-admin users from admin pages
- Layout is responsive (sidebar collapses on mobile)
- Page skeletons render with proper titles
