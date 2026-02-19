# TF2.12 - Implement Statistics Dashboard View


**Depends on:** TF1.5, TF1.6, TF1.7, TF2.2
**Blocks:** None

**Context:**
The statistics dashboard provides managers and admins with analytics about recruitment request processing including KPIs, charts, and trends.

Reference:
- `UI_Stats.png` for visual layout
- `UI_Technical Specification V1 (FR) - IMPLEMENTATION_DETAILS.md` > "Tableaux de Bord et Visualisation"

**Objective:**
Implement the statistics dashboard with KPI cards and data visualizations.

**Instructions:**

1. **Implement `pages/statistics.vue`:**

   - **Header:**
     - Title: "Statistiques"
     - Search input
     - Date filter: "Cree avant" date picker for filtering by time period

2. **KPI cards row** (4 cards):
   - **Demandes traitees**: Total count (e.g., 72) - neutral color
   - **En cours**: Active requests count (e.g., 15) - blue
   - **Taux d'approbation**: Approval percentage (e.g., 66%) - green
   - **Temps moyen de validation**: Average days (e.g., 9.2j) - orange

3. **Charts section** (use a charting library like Chart.js, ApexCharts, or vue-chartjs):

   - **Repartition par statut** (Status distribution):
     - Donut/pie chart
     - Segments: Validees (green), En attente (blue/orange), Rejetees (red)
     - Percentage labels

   - **Evolution mensuelle** (Monthly evolution):
     - Line chart showing request volume trends over months
     - Multiple series possible (created, approved, rejected)

   - **Demandes par departement** (Requests by department):
     - Horizontal bar chart
     - One bar per department showing request count

   - **Temps moyen de validation par etape** (Average validation time by step):
     - Vertical bar chart
     - One bar per workflow step showing average days (e.g., 1.5j, 1.8j, 2.2j)

   - **Tendances hebdomadaires** (Weekly trends - last 3 weeks):
     - Multi-line chart
     - Tracking multiple metrics over the last 3 weeks

4. **Chart layout:**
   - Responsive grid: 2 columns for large screens, 1 for mobile
   - First row: Donut + Line chart
   - Second row: Bar charts
   - Third row: Weekly trends (full width)

5. **Data source:**
   - For mock mode: Generate realistic sample statistics data
   - For API mode: Call statistics endpoints (or compute from request list data)

**Acceptance Criteria:**
- Statistics layout matches `UI_Stats.png` mockup
- All 4 KPI cards display with correct values and colors
- Donut chart shows status distribution with percentages
- Line chart shows monthly trends
- Bar charts show department and step validation data
- Charts are responsive and resize properly
- Date filter adjusts all statistics
- Loading state shows while fetching data
- Charts animate on load
- Data excludes archived requests from active statistics
