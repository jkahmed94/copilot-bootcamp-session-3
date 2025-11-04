# Epics and Stories - TODO App Upgrade

Source: Derived from `docs/prd-todo.md` (MVP and Post-MVP requirements). Story titles with acceptance criteria.

## Stories

### MVP Stories (Titles Only)
* Add optional due date field
* Add priority field with default P3
* Ignore invalid due date inputs
* Default missing priority to P3
* Create task with new fields
* Edit task fields (title, description, due date, priority)
* Delete task
* Toggle task completion state
* Show completed tasks only in All view
* Implement All filter view
* Implement Today filter view
* Implement Overdue filter view
* Search tasks by title
* Search tasks by description
* Sort tasks by due date ascending
* Place undated tasks after dated tasks
* Persist tasks locally
* Load tasks from local storage on app start
* Update functional requirements with new fields
* Document filtering behavior

### Post-MVP Stories (Titles Only)
* Highlight overdue tasks visually
* Sort overdue tasks first
* Then sort by priority P1→P2→P3
* Then sort by due date ascending
* Then list undated tasks last
* Add color-coded priority badges
* Add Completed-only view
* Add keyboard navigation support
* Add ARIA improvements
* Abstract storage layer for future backend
* Prepare data model for multi-device sync

## Acceptance Criteria

This section aggregates acceptance criteria for quick reference (original criteria remain inline under each story below).

### MVP Acceptance Criteria
* Due date field accepts ISO YYYY-MM-DD; optional; displays consistently.
* Priority select offers P1/P2/P3; defaults to P3 when omitted/invalid.
* Invalid due date strings ignored (task stored without dueDate, no crash).
* Missing/invalid priority stored as P3; never blank in list.
* Task creation requires non-empty title; other fields optional; task appears immediately.
* Editing updates fields; clearing due date removes it; timestamp refreshed if used.
* Deletion removes task from all views/search; no orphan references.
* Completion toggle persists and visually differentiates completed tasks.
* Completed tasks only appear in All view; toggling repositions task.
* All view shows every task; Today shows incomplete tasks due today; Overdue shows incomplete tasks with past due dates.
* Search is case-insensitive across title/description; respects active filter; clearing restores list.
* Sorting (MVP) places dated tasks first by ascending due date; undated tasks follow preserving creation order.
* Local persistence (backend or local) survives refresh without duplicates.
* Initial load hydrates from stored collection; defaults applied, corrupt entries skipped.
* Docs updated with new fields, validation rules, and filter behaviors.

### Post-MVP Acceptance Criteria
* Overdue incomplete tasks visually highlighted (red) with accessible contrast; highlight removed when resolved.
* Advanced sorting precedence: overdue first; then P1→P2→P3; then due date asc; undated last.
* Priority badges colored (P1 red, P2 orange, P3 gray) with accessible labels.
* Completed-only view lists only completed tasks; search narrows within that view.
* Keyboard navigation reaches tabs/tasks/actions; standard key interactions work; visible focus.
* ARIA roles for tabs, badges announce priority, list semantics correct.
* Storage abstraction allows repository swap without component changes.
* Data model sync-ready: stable IDs, createdAt, updatedAt maintained; update operations modify updatedAt.

## Technical Requirements (Summary)

For full detail see section at end of document; summary:
* Unified Task schema (add `priority`, future `updated_at`).
* Backend schema adjustments for `priority` (MVP) and `updated_at` (Post-MVP).
* Validation: enforce title; normalize/ignore invalid due_date; default priority.
* Frontend utilities: mapping (`mapTask`), filtering (All/Today/Overdue), searching, sorting (MVP & advanced comparator Post-MVP).
* Components to add: `FilterBar`, priority select in `TaskForm`, optional badge in `TaskList` (Post-MVP).
* Accessibility upgrades (Post-MVP): tab roles, keyboard navigation, aria-labels.
* Storage abstraction (`taskRepository`) Post-MVP.
* Testing coverage: priority defaults, invalid dates, filter logic, sorting precedence, visual overdue state.


## MVP Epics

### Epic: Task Data Model Expansion
#### Story: Add optional due date field
##### Acceptance Criteria
* User can supply a date in ISO YYYY-MM-DD format.
* Field may be left blank; task still saves successfully.
* Saved due date displays consistently in list and detail views.
#### Story: Add priority field with default P3
##### Acceptance Criteria
* Priority select offers P1, P2, P3.
* If user does not choose a priority, task stored as P3.
* Editing preserves chosen priority unless explicitly changed.
#### Story: Ignore invalid due date inputs
##### Acceptance Criteria
* Non-ISO or impossible dates (e.g., 2025-13-40) are not saved.
* Invalid entry results in task stored without dueDate (no error modal required).
* UI does not crash on invalid input.
#### Story: Default missing priority to P3
##### Acceptance Criteria
* Any task created without a priority persists as P3.
* Invalid priority values fallback to P3.
* No blank priority values appear in the list.

### Epic: Task Creation and Editing
#### Story: Create task with new fields
##### Acceptance Criteria
* Title required; attempt to save empty title prevents creation.
* Optional fields (description, dueDate, priority) can be omitted.
* Task appears immediately in All view after creation.
#### Story: Edit task fields (title, description, due date, priority)
##### Acceptance Criteria
* Changes persist on save and reflect in filters/search instantly.
* Removing due date (clear action) results in task having no dueDate.
* Updated timestamp (if used) is refreshed.
#### Story: Delete task
##### Acceptance Criteria
* Deleted task no longer appears in any filter or search results.
* Local storage reflects deletion after refresh.
* No orphan references remain.

### Epic: Task Completion Management
#### Story: Toggle task completion state
##### Acceptance Criteria
* User can mark a task complete/incomplete from list and/or detail.
* Completed state persists across refresh.
* Visual style differentiates completed vs incomplete (e.g., strikethrough or gray per UI guidelines).
#### Story: Show completed tasks only in All view
##### Acceptance Criteria
* Completed tasks hidden from Today and Overdue.
* Completed tasks remain visible in All after page refresh.
* Toggling a completed task back to incomplete re-evaluates filter placement.

### Epic: Filtering Views
#### Story: Implement All filter view
##### Acceptance Criteria
* Displays all tasks regardless of completion or dueDate.
* Default view on initial load.
* Switching away and back restores full list.
#### Story: Implement Today filter view
##### Acceptance Criteria
* Shows only incomplete tasks with dueDate equal to current local date.
* Updates dynamically at midnight (on reload) to reflect new day.
* Excludes tasks without dueDate.
#### Story: Implement Overdue filter view
##### Acceptance Criteria
* Shows only incomplete tasks with dueDate earlier than current date.
* Excludes completed tasks and tasks without dueDate.
* Refresh or state change (editing dueDate) updates list immediately.

### Epic: Search Functionality
#### Story: Search tasks by title
##### Acceptance Criteria
* Case-insensitive substring match on title.
* Works within currently selected filter (does not override filter rules).
* Clearing search restores pre-search filtered list.
#### Story: Search tasks by description
##### Acceptance Criteria
* Case-insensitive substring match on description.
* Combined with title search (single input) returning union of matches.
* No results message displayed when empty.

### Epic: Basic Sorting
#### Story: Sort tasks by due date ascending
##### Acceptance Criteria
* Tasks with due dates appear before undated tasks.
* Among tasks with due dates, earliest date appears first.
* Sorting updates after edits to dueDate.
#### Story: Place undated tasks after dated tasks
##### Acceptance Criteria
* Tasks lacking dueDate grouped at end of list.
* Relative order among undated tasks determined by creation timestamp.
* Adding a dueDate moves task into dated section correctly.

### Epic: Local Persistence
#### Story: Persist tasks locally
##### Acceptance Criteria
* Tasks remain after browser refresh.
* Data structure includes new fields (priority, dueDate) when present.
* No duplicate tasks created on reload.
#### Story: Load tasks from local storage on app start
##### Acceptance Criteria
* Initial render populates list from saved collection.
* Missing fields (priority) default correctly without errors.
* Corrupt entries skipped gracefully.

### Epic: Documentation Updates
#### Story: Update functional requirements with new fields
##### Acceptance Criteria
* `functional-requirements.md` lists dueDate & priority definitions.
* Document reflects validation rules (invalid dueDate ignored, default priority).
* PR merged with review approval.
#### Story: Document filtering behavior
##### Acceptance Criteria
* Filters (All, Today, Overdue) described with inclusion/exclusion logic.
* Completed task visibility rules captured.
* Sorting description matches MVP behavior.

## Post-MVP Epics

### Epic: Visual Urgency Indicators
#### Story: Highlight overdue tasks visually
##### Acceptance Criteria
* Overdue incomplete tasks display distinct red highlight per design.
* Highlight removed when task marked complete or dueDate edited to future/today.
* Meets contrast accessibility (WCAG AA for text on background).

### Epic: Advanced Sorting Logic
#### Story: Sort overdue tasks first
##### Acceptance Criteria
* All overdue tasks precede non-overdue tasks regardless of priority.
* Completed overdue tasks (if shown in All) appear after incomplete overdue tasks.
#### Story: Then sort by priority P1→P2→P3
##### Acceptance Criteria
* Within non-overdue (or overdue group when multiple), tasks ordered P1 before P2 before P3.
* Priority change reorders list immediately.
#### Story: Then sort by due date ascending
##### Acceptance Criteria
* Tasks sharing priority ordered by earliest due date.
* Date edits reposition tasks correctly.
#### Story: Then list undated tasks last
##### Acceptance Criteria
* All undated tasks appear after dated tasks.
* Order among undated tasks stable by creation timestamp.

### Epic: Priority Visual Design
#### Story: Add color-coded priority badges
##### Acceptance Criteria
* P1 badge: red; P2: orange; P3: gray (per design palette).
* Badges include accessible text (e.g., aria-label "Priority P1").
* Badge updates when priority edited.

### Epic: Extended Filtering
#### Story: Add Completed-only view
##### Acceptance Criteria
* Shows only tasks with completed = true.
* Switching away returns previous filtered context.
* Search still narrows within completed set.

### Epic: Accessibility Enhancements
#### Story: Add keyboard navigation support
##### Acceptance Criteria
* Tab order reaches filter controls, task items, and action buttons logically.
* Enter/Space toggles completion; Escape dismisses dialogs (if any).
* Focus outline visible on all interactive elements.
#### Story: Add ARIA improvements
##### Acceptance Criteria
* Filter controls have role="tab" with proper aria-selected state.
* Priority badges expose text alternatives.
* Task list announced as list with correct item counts.

### Epic: Persistence Layer Evolution
#### Story: Abstract storage layer for future backend
##### Acceptance Criteria
* Storage operations routed through a single interface/service.
* Unit tests mock storage interface successfully.
* Swapping implementation (e.g., to API) requires no component rewrites.
#### Story: Prepare data model for multi-device sync
##### Acceptance Criteria
* Model includes stable unique IDs and timestamps (createdAt, updatedAt).
* Update operations produce updatedAt changes.
* Documentation notes sync-ready fields.

## Technical Requirements

The following technical requirements translate the acceptance criteria into concrete implementation directives for the existing codebase (React frontend in `packages/frontend` and Express/SQLite backend in `packages/backend`).

### Global / Architecture
- Introduce a unified Task shape (frontend & backend):
  - id: number (from SQLite) — expose as number consistently.
  - title: string (required, non-empty)
  - description: string | ''
  - due_date: string | null (ISO YYYY-MM-DD; null when absent)
  - priority: 'P1' | 'P2' | 'P3' (default 'P3')
  - completed: boolean (frontend) / 0|1 (backend persistence)
  - created_at: timestamp (backend) — surfaced to frontend for future sorting extensions.
  - updated_at: timestamp (added Post-MVP; plan column for future sync).
- Backend: extend SQLite schema (`app.js`) adding columns `priority TEXT DEFAULT 'P3'` and later `updated_at TIMESTAMP` (Post-MVP), keeping migration inline for simplicity (re-run on bootstrap).
- Frontend: create a central `taskApi.js` module to abstract fetch calls; prepare for future local storage fallback or sync layer.

### Data Validation & Normalization
- Backend validation (POST/PUT):
  - Reject empty title with 400 (already implemented).
  - If `priority` missing or invalid: set to 'P3'.
  - If `due_date` provided: ensure matches /^\d{4}-\d{2}-\d{2}$/ and forms a valid date; else store NULL.
- Frontend form (`TaskForm.js`): add priority select; on submit normalize `due_date` to YYYY-MM-DD; do not send invalid date strings.
- Ensure all fetch responses convert numeric `completed` (0/1) to boolean for in-memory operations.

### Persistence & State
- Current implementation calls backend for every operation; MVP requires local persistence survival only if backend present — clarify: keep server-driven storage (already implemented) and optionally add localStorage caching layer (deferred unless explicitly required). Technical requirement: no duplicates after reload due to consistent server list fetch.
- Add helper `mapTask(raw)` => converts backend row into UI object: `{ ...raw, completed: raw.completed === 1 }`.

### Priority Field Implementation
- Backend: update INSERT & UPDATE statements to include `priority`.
- Backend endpoints: include `priority` in SELECT queries (currently selects * so auto-included after schema change).
- Frontend: `TaskForm` add `<TextField select>` or `<Select>` for priority; default value 'P3'.
- Display: `TaskList` optionally shows priority badge Post-MVP; for MVP no visual badge required.

### Filtering (All / Today / Overdue)
- Frontend: implement derived view state; add a `FilterBar` component with three buttons/tabs.
- Logic:
  - Today: `task.due_date === formatToday()` AND `!task.completed`.
  - Overdue: `task.due_date < formatToday()` AND `!task.completed`.
  - All: no restrictions.
- Date comparison uses local date (no time). Provide utility `getTodayISO()`.
- When due_date edited, recompute filtered lists immediately via state update.

### Search Integration
- Single search input filtering currently loaded (and pre-filtered) tasks: case-insensitive match `title.includesIgnoreCase(q) || description.includesIgnoreCase(q)`.
- Provide utility `matchesQuery(task, q)`; avoid re-fetching server for search (client-side only for MVP).

### Sorting (MVP)
- Apply sorting after filtering & search: 
  1. Tasks with due_date (non-null) first.
  2. Within dated tasks: ascending due_date string (lexicographic works if ISO).
  3. Undated tasks: preserve creation order (server already returns ordered by due_date IS NULL, due_date ASC, created_at ASC — reuse server ordering to minimize client logic).
- Post-MVP advanced sorting will shift logic to client (or new ORDER BY clause) using composite priority & overdue precedence.

### Completion Toggle
- Preserve existing PATCH endpoint usage.
- After toggle: re-fetch list OR optimistically update `completed` state and re-run filter/sort pipeline.
- Ensure completed tasks excluded from Today/Overdue views by filter predicate.

### Invalid Due Date Handling
- Frontend: if user clears date input, send `due_date: null`.
- Backend: treat invalid date string as NULL; never return malformed date to frontend.
- Add unit test verifying invalid date results in null persisted field.

### Documentation & Traceability
- Link each epic/story to technical utility/function names once implemented (e.g., `filterTasks()`, `sortTasksMvp()`).
- Maintain a `TECH_REQUIREMENTS.md` (optional future follow-up) if complexity grows.

### Performance & Resilience
- Keep task list in memory; operations O(n) acceptable given small teaching scope.
- Error handling: show error banner (already implemented) for fetch failures; no silent failures.

### Accessibility (Post-MVP)
- Assign role="tablist" to filter container; each button role="tab" with `aria-selected`.
- Priority badge: add `aria-label="Priority P1"` etc.
- Keyboard: implement arrow key navigation in tab list (Post-MVP) following WAI Tabs pattern.

### Advanced Sorting (Post-MVP)
- New ORDER precedence algorithm:
  1. Overdue (incomplete) tasks.
  2. Non-overdue tasks grouped by priority (P1, P2, P3).
  3. Within same priority: due_date ascending.
  4. Undated tasks last (preserve creation order).
- Option: implement in backend SQL with CASE expressions or client JS comparator. Choose client for clarity in teaching; document comparator function `compareTasksAdvanced(a,b)`.

### Overdue Highlight (Post-MVP)
- Add style class `.task--overdue` applied when `due_date < today && !completed`.
- Provide CSS variables for color referencing design palette; ensure AA contrast.

### Storage Abstraction (Post-MVP)
- Create `taskRepository` interface: `list(), create(task), update(id, task), patch(id, partial), remove(id)`.
- Initial implementation wraps `fetch`; later alternative wraps localStorage or remote API without component changes.

### Sync Readiness (Post-MVP)
- Add `updated_at` column; on UPDATE/PATCH set `updated_at = CURRENT_TIMESTAMP`.
- Frontend: display may remain hidden initially; ensure data retained in model.

### Testing Requirements
- Backend tests: extend to cover priority default, invalid due_date handling, and sorting order (dated before undated).
- Frontend tests: add filter behavior tests for Today/Overdue, search combination with filters, and priority default when omitted.
- Post-MVP: tests for advanced sorting precedence and overdue highlight CSS class presence.

### Migration Plan
- Because DB is in-memory (ephemeral), simply modify schema creation SQL; no migration scripts needed.
- If persistent storage introduced later, add versioned migrations.

### Security / Input Hardening
- Sanitize title/description length (e.g., max 255 chars) server-side to avoid oversized payloads.
- Reject non-string JSON types for title/description/priority with 400.

### Open Technical Questions
- Should we move search to backend query parameters? (Currently client-only, acceptable for MVP.)
- Will local-only requirement supersede backend usage in teaching environment? (Clarify if backend kept for demonstration.)
- Priority storage type: keep TEXT vs ENUM (SQLite lacks native ENUM; TEXT acceptable).

