# Product Requirements Document (PRD) - TODO App Upgrade (Due Dates, Priority & Filters)

## 1. Overview
We are upgrading the existing basic TODO app (currently supporting title, description, completion state, CRUD, and search) to improve task organization and urgency signaling. The enhancement introduces optional due dates, a simple priority level, and focused filter views so users can quickly identify what needs attention today and what is overdue. This MVP intentionally avoids backend complexity and advanced features to remain a lean teaching tool. Requirements are sourced from the 09/16 requirements meeting transcript and 09/17 Slack confirmation.

Primary goals:
- Provide lightweight task triage (due date + priority) without feature bloat.
- Maintain simplicity: local-only persistence, minimal UI changes beyond added fields & filters.
- Establish a foundation for future improvements (visual urgency indicators, advanced sorting, badges).

---

## 2. MVP Scope
(In scope for initial release)
- Data Model Fields:
  - title (required)
  - description (existing, optional)
  - completed (boolean, existing)
  - dueDate (optional ISO YYYY-MM-DD; invalid formats ignored / treated as absent)
  - priority (enum: P1 | P2 | P3; default P3)
- User Actions (all persisted locally):
  - Create task (with new fields)
  - Edit task (including priority & dueDate)
  - Delete task
  - Toggle completion
  - Search tasks by title or description (existing behavior retained)
- Filters (tabbed or segmented control):
  - All: shows all tasks (completed + incomplete)
  - Today: shows incomplete tasks whose dueDate equals current local date
  - Overdue: shows incomplete tasks with dueDate earlier than current date
- Sorting (retain current simple behavior during MVP):
  - By due date ascending (soonest first), then creation date
  - Undated tasks appear after dated tasks (as per existing doc intent)
- Validation Rules:
  - Invalid dueDate input (non-ISO or impossible date) is ignored (field considered unset)
  - Missing priority defaults to P3
- Storage:
  - Local-only (e.g., browser localStorage or in-memory until refresh strategy is defined)
- Minimal UI Adjustments:
  - Form fields for due date (date picker or text input) and priority (select/dropdown)
  - Filter navigation (All / Today / Overdue)
- Documentation & Stories:
  - Update functional requirements doc
  - Provide acceptance criteria for new filters, data model, and validation behavior

---

## 3. Post-MVP Scope
(Planned enhancements after MVP delivery)
- Visual overdue highlighting (e.g., red accent or background) for overdue tasks
- Advanced composite sorting:
  - Overdue first
  - Then priority (P1 → P2 → P3)
  - Then due date ascending
  - Then undated tasks last
- Color-coded priority badges (Red P1, Orange P2, Gray P3)
- Additional filtering modes (e.g., Completed-only tab) if needed
- Accessibility improvements (enhanced keyboard navigation, ARIA refinements)
- Possible data persistence abstraction layer (preparing for future backend integration)

---

## 4. Out of Scope
(Explicitly excluded now; may be revisited later)
- Notifications / reminders
- Recurring tasks
- Multi-user accounts / authentication
- External/server storage (cloud DB, API integration)
- Advanced keyboard navigation beyond default browser behavior
- Complex accessibility enhancements (will revisit in Post-MVP)
- Priority-based automation (e.g., auto-escalation or reminders)

---

## 5. User Stories & Acceptance Criteria (MVP)
1. As a user, I can add a task with a title, optional description, optional due date, and optional priority so I can capture work with minimal friction.
   - Acceptance: Title required; priority defaults to P3 if not chosen; invalid due date ignored.
2. As a user, I can edit a task’s title, description, due date, and priority to keep information current.
   - Acceptance: Changes persist locally; removing dueDate sets it to absent.
3. As a user, I can mark a task complete or incomplete to reflect progress.
   - Acceptance: Completed tasks remain visible only in All filter.
4. As a user, I can view tasks filtered by Today to focus on what must be done now.
   - Acceptance: Only incomplete tasks with dueDate = current date appear.
5. As a user, I can view tasks filtered by Overdue to see what requires urgency.
   - Acceptance: Only incomplete tasks with dueDate < current date appear.
6. As a user, I can search tasks by keyword to quickly locate items.
   - Acceptance: Matches title OR description substring (case-insensitive suggested).
7. As a user, I can rely on consistent sorting so urgent items appear earlier by date.
   - Acceptance: Dated tasks sorted by due date ascending; undated tasks after dated.

---

## 6. Data Model
```
Task {
  id: string;          // unique identifier
  title: string;       // required
  description?: string;// optional
  completed: boolean;  // default false
  priority: 'P1' | 'P2' | 'P3'; // default 'P3'
  dueDate?: string;    // ISO YYYY-MM-DD, optional; invalid excluded
  createdAt: string;   // ISO timestamp (existing or new)
  updatedAt?: string;  // ISO timestamp for edits
}
```
Validation:
- `title`: non-empty string
- `priority`: if absent or invalid → 'P3'
- `dueDate`: must match /^\d{4}-\d{2}-\d{2}$/ and be a real calendar date; else ignore

---

## 7. UX / UI Notes (MVP)
- Use existing styling baseline; add date input and priority select with minimal footprint.
- Filters as horizontal tabs or buttons (All, Today, Overdue).
- No color-coded badges yet (deferred to Post-MVP).
- Responsive: maintain mobile usability (stacked layout, accessible touch targets ≥ 44px).

---

## 8. Non-Functional Requirements
- Performance: Instant client-side filtering (no network calls).
- Reliability: Local persistence should survive page refresh (if localStorage chosen).
- Accessibility: Basic semantic HTML; advanced navigation deferred.
- Maintainability: Clear separation of filtering logic, sorting logic, and data model transformations.

---

## 9. Risks & Mitigations
- Risk: Ambiguity around date handling/time zones.
  - Mitigation: Treat all dates as local date (no time component) for MVP.
- Risk: Sorting expectations may shift once priority & overdue visual cues added.
  - Mitigation: Document roadmap sorting change early (this PRD) to reduce confusion.
- Risk: Users may expect completed tasks in Today/Overdue.
  - Mitigation: Explicitly document filter exclusion behavior.

---

## 10. Success Metrics
- Adoption: >= 80% of created tasks use either dueDate or priority within first test cohort.
- Task Completion: Users complete at least 50% of tasks they mark with dueDate (indicates meaningful usage).
- Usability Feedback: Qualitative — clarity of filters rated "clear" or better in >70% feedback responses.

---

## 11. Future Considerations (Beyond Post-MVP List)
- Bulk edit / bulk complete actions
- Tagging / categorization
- Calendar view
- Integration with backend API for multi-device sync

---

## 12. Assumptions
- Single-user context is sufficient for all current workshops.
- Local time zone is acceptable for interpreting "Today" and "Overdue" (no UTC complexity).
- Browser environment supports basic date input or polyfill (no legacy IE support required).

---

## 13. Open Questions
- Should search span priority label (e.g., searching "P1")? Currently: no.
- Should undated tasks appear in Today if created today? Currently: no (needs dueDate match).
- Should we add a Completed filter later? Deferred.

---

## 14. Approval
Sign-off stakeholders (from artifacts): Client 1, Client 2, Consultant.

---

(End of PRD)
