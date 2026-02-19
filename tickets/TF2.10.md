# TF2.10 - Implement Request Action History Timeline


**Depends on:** TF1.5, TF2.9
**Blocks:** None

**Context:**
The history timeline shows a chronological log of all actions performed on a request, with detailed information for each action type (edition with field changes, submissions, approvals, rejections with reasons, comments).

Reference:
- `UI_Demand Details ActionHistory.png` for visual layout
- `UI_TechnicalSpecification V1 (FR).md` > "Vue details - Historique des Actions & Commentaires"

**Objective:**
Implement the action history timeline component shown in the Historique tab.

**Instructions:**

1. **Create `components/request/ActionHistoryTimeline.vue`:**
   - Props: `history: HistoryEntry[]`
   - Vertical timeline layout with connecting line

2. **Each timeline entry displays:**

   - **Action type icon/badge** with semantic color:
     - CREATION: Blue circle
     - SUBMISSION: Blue circle with arrow
     - APPROVAL: Green checkmark
     - REJECTION: Red X
     - COMMENT: Blue speech bubble
     - EDITION: Orange pencil
     - ABANDONMENT: Gray archive
     - ARCHIVAL: Gray archive

   - **Action label and step name:**
     - e.g., "Approbation - Validation Manager"
     - e.g., "Soumission - Soumission"

   - **Date and time:**
     - Formatted: "18 dec. 2024 a 10:15"

   - **User info:**
     - Avatar/initials badge (e.g., "ML", "JD", "SM")
     - Full name and role (e.g., "Marie Leroy, Responsable RH")

   - **Action-specific details:**
     - **EDITION**: List of field changes with "ancien → nouveau" format
       - e.g., "Salaire max: 50 000 € → 60 000 €"
       - e.g., "Date de debut: 01/02/2025 → 15/02/2025"
     - **REJECTION**: Reason text (bold) + optional comments
     - **COMMENT**: Comment text in a styled quote block
     - **APPROVAL**: Optional approval comment
     - **SUBMISSION**: "Demande soumise pour validation"

3. **Timeline visual styling:**
   - Vertical connecting line between entries
   - Alternating or consistent card-style entries
   - Most recent entries at top (default sort)

4. **Filter by action type** (optional toggle/chips):
   - All, Editions, Approbations, Rejets, Commentaires

5. **Pagination:**
   - If more than 20 entries, show "Voir plus" button or pagination
   - Load more entries on demand

**Acceptance Criteria:**
- Timeline layout matches `UI_Demand Details ActionHistory.png` mockup
- All action types display with correct icons and colors
- Edition entries show field change diffs (old → new)
- Rejection entries show mandatory reason
- Comment entries show comment text
- User avatars/initials display correctly
- Dates are formatted in French locale
- Default sort is most recent first
- Action type filtering works
- Pagination handles large histories (20+ entries)
