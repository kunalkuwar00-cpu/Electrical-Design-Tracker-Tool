Electrical Design Project Tracker (updated)
===========================================
RUN: unzip, double-click index.html (Chrome or Edge on Windows). No install, no internet needed.

YOUR EXISTING DATA
- Opens with the same browser storage key as your earlier version, so records saved in the same
  browser/profile load automatically and are migrated in place. Export a backup before replacing files.
- Data is stored in this browser only (localStorage). It is separate per browser profile and device,
  and clearing site data deletes it. Export a backup regularly.

BACKUP / RESTORE (sidebar > Backup & Restore)
- Export Laptop Backup: downloads one .edbackup (JSON) with projects, main tasks, subtasks, owners,
  hours, issues, manual report highlights and Date Change History.
- Import Backup: file is validated first; then you are warned it REPLACES all current data.
  Export a backup of current data first.

CHANGES IN THIS VERSION
Fixed: Person-wise Task List page was broken (functions missing, error on startup); Add Subtask crash;
  stray text above Project Setup; Import Backup button not working (file input was removed from page);
  Overall dashboard did not show workload/subtask details; project target date shown as raw ISO;
  sidebar highlight out of sync when changing page from buttons.
Added: native calendar pickers on all date fields; Project List Actions (Edit/Delete with impact
  warning) + pagination (10/page); Progress % per subtask (editable for In Progress/Under Review/
  Pending Dependency/On Hold; Completed=100, Not Started=0); main-task progress = average of active
  subtasks (Cancelled excluded; no active subtasks = 0%); dashboard cards per spec; hours-based
  workload (planned, actual, remaining, completed, overdue, utilization) in overall AND project view;
  Person-wise list with required columns, person/project/status/search filters, hours summary;
  Reports with period selector, manual highlights kept separate, pending work, risks/dependencies
  (incl. issues); Dependencies & Issues page (dependency tracker + issue log); date-change reason also
  required for Dependency Due Date; history search; default subtask owner (Panel/Field engineer);
  Customer Name, "(quick update)" wording and Issue/Risk fields removed from the UI.

KNOWN LIMITATIONS
- Calendar pickers are the browser's native date control: it shows DD/MM/YYYY when Windows/Chrome
  region is English (UK/India). Tables, reports and history always show DD/MM/YYYY.
- Subtasks completed before this version have no completion date, so they cannot appear in the
  "Week's Highlights" of a dated period until re-marked Completed.
- Data lives in one browser; not shared between people/devices.
- Date-change reason uses a browser prompt box. Not tested in a real Chrome/Edge window (see below).

TESTS ACTUALLY RUN (automated, jsdom simulation of the page; file tests_jsdom.js.txt)
29 checks passed: load without JS errors; add projects; duplicate code blocked; project list/cards;
template load; no sample data leakage; default owners; subtask updates; progress calculation;
date-change history with reason; out-of-period date blocked; person list/ownership/no double count;
workload hours & utilization; project filter and clear; report content; empty report; issues in report;
manual highlights; backup export/restore; bad backup rejected; refresh persistence; project delete.
NOT tested: visual layout/calendar popup in real Chrome/Edge, mobile layout, Test 12 pixel-level regression.


Workload update: Workload is derived from saved Task Management tasks/subtasks and refreshes after task/subtask create, edit, status, owner, or date updates. If planned hours are entered, those hours are distributed across working days in the assigned date range. If no hours estimate is entered, the calendar assumes one full daily capacity (default 8h) on each working day in the assigned timeline so date assignments still appear in capacity planning. Completed/cancelled work is excluded.

Task linking update: Main tasks and subtasks can now link to another main task or subtask in the same project using the "Link to another task" selector. Existing dependency/remarks fields remain available.


=== v4: WORKLOAD REBUILT TO MATCH THE REFERENCE PHOTO ===
Bug found in the previous build: the old dashboard code wrote its table into the element with id "workload",
which is the Workload page itself, so the Workload page was overwritten whenever the dashboard refreshed.
Fixed (old writer disabled; Person-wise Workload stays removed from the Dashboard).
Workload tab now has: From Today / This Week / This Month / Next Month / Custom range with prev/next arrows;
multi-select project dropdown ("N Projects"); Tags; Assignee; Search; Export CSV; settings (daily capacity per person,
holidays, no-estimate rule). Grid: NAME column with arrow + avatar (greyed when no work in range), week headers
(Mon date) with day letters, narrow weekend columns, green = assigned, red = above capacity, yellow = holiday column,
blue-grey = weekend, TOTAL column; arrow or "Show tasks" expands the tasks behind each person's hours.
Task Management link: Estimated Hours, Owner, Start, Target and the new Tags field (subtask form) drive the grid;
any change refreshes it. Task-linking feature from your file is untouched.
Tests (jsdom, tests_workload_v4_jsdom.js.txt): 12 checks passed. Not checked in a real browser.

=== v5: Workload table simplified (as per reference photo) ===
Removed: coloured initials/avatars, expand arrows, task rows, "Show tasks" button. The table now shows only
Name | days (hours per day) | TOTAL, with green (assigned), red (over 8 h/day), yellow (holiday), blue-grey (weekend).
Hours per day come from Estimated Hours entered in Task Management (spread over Start-Target working days).
Default changed: activities WITHOUT Estimated Hours are no longer counted (they inflated totals). To count them as
full days, tick the option under the gear icon.
Tests: 10 jsdom checks passed. Not checked in a real browser.

=== v6: approved Workload layout ===
Removed: Tags filter, Daily capacity setting (limit is fixed at 8 h/day for everyone). Kept: Holidays (button on Workload bar).
Workload table: names on one line; each day column shows weekday letter + date with the hours directly below; weekend
columns narrow; green assigned / red over 8 h / yellow holiday / grey weekend; Total column.
Click an hours cell -> opens that activity in Task Management (highlighted). If several activities share that day, a
picker list appears first.
Theme: button at bottom-right switches Black / White theme for the whole tool (remembered). The black theme is made by
inverting the page colours, so some shades may look slightly different from the white theme.
Tests (jsdom): 13 checks passed. Not checked in a real browser.

=== v7: Workload layout fixed, themes, Settings tab ===
Root cause of "looks different / colours not changing" in v4-v6: the Workload styling was placed where the page
builder discards it, so only plain unstyled HTML was shown. Now styling is in the main stylesheet.
- Workload table rebuilt to the approved layout (names on one line, weekday letter + date, hours below,
  green/red/yellow/grey blocks, sticky Name and Total columns). Click an hours cell -> opens that activity in
  Task Management (picker if several activities on that day).
- Real White and Black themes for the whole tool (no colour inversion trick).
- New "Settings" tab (sidebar): theme, accent colour, text size; Workload colours (assigned, over limit, holiday,
  weekend); daily limit (hours), default range, row height, column width, show/hide weekend columns; Reset.
  Saved in this browser (localStorage) and applied immediately.
Note: the date pickers show dates in your Windows region format (DD/MM/YYYY if region is English UK/India).
Tests: real headless Chromium run (tests_browser_e2e.js.txt): 12 checks passed incl. colours, Settings, limit,
weekends, theme persistence after reload, click-through, picker. Screenshots in preview_*.png use sample data.
Known: one older v2 check (dashboard workload table) no longer applies because that table was removed on request.

=== v8: Task linking, working-day durations, auto date shifting, overload tools ===
NOTE: the previous file contained NO task-linking code (its README described a feature that was not in the file). Linking is built here.

TASK MANAGEMENT
- Every main task / subtask has a number (No.). Subtask table now has: No., Days (working days between start and
  target, weekends and holidays excluded), Linked From, Linked To. Main task header shows #, days and links.
- Links: button "Linked From" (subtask) or "Links" (main task) opens a list; tick the tasks that must finish first
  (finish-to-start). Loops are blocked. Linked To is calculated automatically. A warning mark shows when a task starts
  before its linked task finishes; on saving a link you are offered to move it after the linked task.
- Auto date shifting: change a target date -> after the reason prompt you are shown which linked tasks will move
  (by the same number of WORKING days) and can accept or move only this task. Also available as the "Move" button
  (main task) / arrow button (subtask): moves the task by N working days and everything linked after it.
  Completed/Cancelled tasks are never moved. Each moved date is written to Date Change History with your reason.
- Main task dates now follow its subtasks (earliest start, latest target).
WORKLOAD
- One row per person across ALL selected projects (hours of every project added together). Hover a cell to see the
  split by project; tick "Project split" to show A1 / A2 hours under the person.
- Red banner lists who is over the daily limit, which days and which projects; click a name to focus that person.
- Click a red cell: list of the activities that day with Open and "Move by days" (linked tasks follow).
TESTS: 16 real-browser checks passed (links, loop block, working-day duration, cascade shift, main roll-up,
history, overload banner across two projects, project split, move to resolve overload).
LIMITS: links are within one project; links are finish-to-start only (no lag); the Load Template does not create
links automatically; moving a task that would leave the project period shows a warning but is allowed.

=== v10: roles, Project Review, Project Leads, redesigned Task Management ===
WHO CAN DO WHAT
- Admin (Kunal Kuwar): everything — projects, tasks, subtasks, dates, links, holidays, issues, announcements,
  Backup & Restore, Team & Access, Settings (colours/theme).
- Team Leaders and Managers: view everything; update Project Review; assign Daily Tasks to their team;
  update their own subtasks.
- Members: view everything; update status, progress %, actual hours and remarks of their OWN subtasks
  (Dashboard or Task Management); close Daily Tasks assigned to them.
- Date Change History: everyone can view (menu only). Backup & Restore, Team & Access, Settings: admin only.
INTERFACE: menu opens below the top bar with a back arrow; logo in the centre returns to the Dashboard;
profile circle (e.g. KK) shows name, designation, department, reporting to, access and team.
DASHBOARD tiles: Daily Tasks, Task Management, Project List, Workload, Project Review, Project Setup,
Dependencies & Issues. Overall Project Dashboard adds Design Projects and Installation Projects, removes
Projects at Risk, and has a Period filter: Overall / Month / Quarter / Year (financial year April–March).
Subtask Workload: Project Completion column removed.
TASK MANAGEMENT: main tasks in aligned columns (No., Main Task, Status, Progress, Start, Target, Days, Delay,
Subtasks, Linked From, Actions); subtasks edited directly in the row (name, owner, status, dates, hours,
dependency); Move / Edit / Delete are small icon buttons on one line.
PROJECT REVIEW (was Reports): weekly status per project — Weekly Status, Current Week Tasks, Pending Tasks,
Dependency to Complete. "Fill from task data" suggests lines from saved subtasks; "Copy last week";
History per project; "Copy Week Summary" for the weekly mail. Auto Summary from Task Data kept below.
PROJECT LEADS: Project Setup has FACE and PACE fields. Project List shows "Projects per Project Lead"
(count by stage, running, total) and "Project Allocation" (Lead, FACE, PACE, Panel/Field engineers) with a
person filter.
TEAM & ACCESS: Access Role, Designation, Department, Reports To, Task Owner Name; "Add Several People"
creates many logins at once (one per line: Full name, Access role, Designation, Reports-to Login ID).
LIMIT: still a local version (one browser on one computer). For 22 people on their own laptops, a shared
server/database is needed so everyone sees the same data.
TESTS: 35 real-browser checks passed (roles, permissions, period metrics, leads summary, review, aligned rows,
inline edits, profile, daily tasks, reload, workload grid, no page errors).
