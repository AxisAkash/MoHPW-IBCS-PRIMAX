# MoHPW case monitoring frontend specification

## 1. Information architecture

The product is organized around the daily operational loop: understand workload, inspect cases, progress tasks, manage evidence, and report upward.

```text
Workspace
├─ Dashboard
├─ Cases
│  ├─ All cases
│  ├─ Register a case
│  ├─ My cases
│  └─ Saved views
├─ Work queue
├─ Documents
├─ Calendar & deadlines
├─ Reports
├─ Notifications
└─ Administration
   ├─ Users & roles
   ├─ Organizations & courts
   ├─ Audit trail
   └─ System settings
Help & manuals
```

The case workspace is the primary object. Each case can be reviewed through Summary, Timeline, Tasks, Documents, Notes, Communications, Related cases, and Audit tabs.

## 2. Navigation structure

Desktop uses persistent left navigation and a compact top bar with global search, language toggle, notifications, and profile. Tablet collapses the rail to a compact icon-and-label drawer. Mobile uses a top bar plus a bottom navigation for Dashboard, Cases, Work queue, and More. Breadcrumbs appear above every internal page. Primary actions are placed at the page-header right; destructive actions require confirmation.

## 3. Design tokens

```text
Navy       #0F2A43   identity, navigation, headings
Blue       #1D4ED8   primary actions, links, focus rings
Teal       #0F766E   selected states, supporting highlights
Canvas     #F5F7FA   page background
Surface    #FFFFFF   cards, tables, forms
Border     #D6DCE4   dividers and input outlines
Text       #17202A   primary text
Muted      #667085   secondary text
Success    #15803D   completed / verified
Warning    #B45309   due soon / attention
Danger     #B42318   overdue / error / destructive
```

Typography uses a system sans stack with `Noto Sans Bengali` first for Bangla fallback. Spacing is 4px-based. Surfaces use 6–10px corners, not decorative pills. Shadows are reserved for the login card and modal. Focus ring is a 3px blue outline with a 2px white offset.

## 4. Component inventory

Shell, Sidebar, Topbar, Breadcrumbs, PageHeader, Button, IconButton, Input, Select, DateField, Checkbox, Toggle, Badge, Alert, Card, MetricCard, Tabs, DataTable, Pagination, FilterBar, EmptyState, LoadingState, ErrorState, PermissionState, Timeline, ProgressSteps, TaskRow, DocumentRow, UploadDropzone, PreviewModal, Toast, ConfirmDialog, ChartBars, ChartLine, ChartDonut, Drawer, and Footer.

## 5. Page layouts

Every page uses a centered, fluid content frame with a page header and one or more white panels. Dashboard uses a 5-card metric row, two-column operational content area, and an activity/deadlines row. Case list uses a filter panel followed by a table. Registration uses a two-column form with a vertical step rail and a review step. Case details uses a prominent identity header, summary cards, tabs, then a two-column workspace. Documents use a table plus preview side panel. Reports pair compact filters with one or two charts and an export-ready table.

## 6. Responsive behavior

At 1100px the left rail narrows and multi-column grids reduce. At 820px the rail becomes a drawer and page headers wrap. At 640px tables become horizontally scrollable with sticky first columns or switch to stacked cards where the content is more readable. Filter panels become an explicit “Filters” drawer. Touch targets remain at least 44px high. Mobile keeps the case ID, title, status, priority, next deadline, and primary action visible first.

## 7. Role-based visibility

| Capability | Case handler | Supervisor | System administrator |
|---|---:|---:|---:|
| View assigned cases | yes | yes | yes |
| Register / update case | yes | yes | yes |
| Assign or escalate work | no | yes | yes |
| View organization-wide reports | limited | yes | yes |
| Manage users and roles | no | no | yes |
| Change system settings | no | no | yes |
| View audit trail | own actions | organization | all |
| Export data | limited | yes | yes |

The demo keeps navigation visible to communicate the full product architecture, while permission-denied states demonstrate how restricted views should behave.

## 8. Sample data model

```js
Case { id, title, titleBn, organization, court, category, priority, status,
       assignee, filingDate, lastAction, nextDeadline, risk, updatedAt,
       summary, timeline[], tasks[], documents[], notes[], communications[], audit[] }
Task { id, caseId, title, owner, dueDate, status, priority, stage, escalation }
Document { id, caseId, name, category, type, size, uploadedAt, uploadedBy,
           version, confidentiality, verification, required, history[] }
User { id, name, nameBn, email, organization, role, status, lastActive, permissions[] }
Notification { id, title, body, type, createdAt, read, caseId }
```

All values are fictional and visibly labeled as demonstration data.

## 9. Validation rules

- Email must contain a local part and domain; password is required for the demo.
- Case title, organization, category, filing date, priority, and responsible person are required.
- Filing date cannot be after today in a real integration; the demo flags missing values and keeps dates fictional.
- Attachments must be PDF, DOCX, XLSX, JPG, or PNG and no larger than 10MB in the frontend demo.
- Review step must be completed before submission; saving a draft is always allowed.
- Destructive actions require confirmation; bulk actions require at least one selected row.
- Report period requires a start and end date with start <= end.

## 10. Accessibility checks

- Semantic landmarks: banner, navigation, main, footer, dialog.
- All form controls have visible labels and inline error text.
- Keyboard focus is visible and never removed; skip link is available.
- Status is conveyed with text plus a visual treatment, never color alone.
- Table headers use `scope=col`; selection controls expose accessible names.
- Dialogs have a labelled title, close button, and Escape support.
- Motion is minimal and honors `prefers-reduced-motion`.
- Contrast is designed around WCAG 2.1 AA targets; muted text remains above 4.5:1 on white where used for body copy.
- Layout remains usable at 200% zoom and on narrow widths without hiding primary actions.
