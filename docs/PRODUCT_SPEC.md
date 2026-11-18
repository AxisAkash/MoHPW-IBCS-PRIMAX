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
Identity
Navy          #102A43   sidebar, login panel, headings
Navy 2        #173D5C   active navigation surface
Navy 3        #0B1F33   overlay scrim base
Blue          #2563EB   primary actions, links, focus
Blue dark     #1D4ED8   primary action hover
Blue soft     #E8F0FE   hover / highlighted surfaces
Teal          #0F766E   selected accents, notes, success-adjacent cues
Teal soft     #E3F4F1   note and demo-label backgrounds

Neutral
Canvas        #F4F7FA   page background
Surface       #FFFFFF   panels, tables, forms, cards
Surface alt   #F1F5F9   toolbars, filters, subtle fills
Surface hover #F8FAFC   row and control hover
Border        #D9E2EC   dividers and card borders
Border strong #B8C6D4   emphasis borders, input hover
Control border#7D8B9A   form-control outlines; >=3:1 on white and canvas
Text          #162331   primary text
Muted         #5E6B7A   secondary text; >=5:1 on white
Muted 2       #6B7887   tertiary text

Status
Success       #15803D / bg #E9F7EE / border #B7E2C4
Warning       #B45309 / bg #FFF6E5 / border #F0D49B
Danger        #B42318 / bg #FEF0EE / border #EFB9B3
Info          #1D4ED8 / bg #EDF3FF / border #C4D6FB
Neutral       #4E5D6C / bg #F1F5F9 / border #D9E2EC

Chart
Primary       #2563EB   registered series
Secondary     #9AB9EC   closed series
Tertiary      #91A7BF   distribution segment
Quaternary    #D6DCE4   distribution remainder
```

Font stack: `Inter` (400–800) with `Noto Sans Bengali` (400–800) for Bangla, then `Segoe UI`, Arial, sans-serif. Both families are requested from Google Fonts with `display=swap` and preconnects; the sandbox has no access to that host, so web-font rendering is not verified there.

Type scale: 11 / 12 / 13 / 14 / 16 / 18px. No body text is smaller than 11px.
Spacing scale: 4 / 8 / 12 / 16 / 20 / 24 / 32 / 40 / 48px.
Radius scale: 4 / 6 / 10 / 14px. Pills are used only for status dots, the brand mark is 6px, and panels are 10px.
Elevation scale: `xs` (1px hairline), `sm` (2px subtle), `md` (8px floating), `lg` (18px overlay). Panels, metric cards, and workflow cards carry a subtle `xs` shadow; the login card and dialogs use `lg`, toasts use `md`, and the mobile drawer uses `lg`.

Focus treatment: a 2px solid outline with a 2px offset on every keyboard-focused control; inputs additionally receive a 4px soft halo. Horizontal scroll containers reserve padding so the ring is not clipped.

Motion: `fast` 120ms and `base` 180ms, both eased, applied only to hover/transition states; all animation collapses under `prefers-reduced-motion`.

## 4. Component inventory

Shell, Sidebar, Topbar, Breadcrumbs, PageHeader, Button, IconButton, Input, Select, DateField, Checkbox, Toggle, Badge, Alert, Card, MetricCard, Tabs, DataTable, Pagination, FilterBar, EmptyState, LoadingState, ErrorState, PermissionState, Timeline, ProgressSteps, TaskRow, DocumentRow, UploadDropzone, PreviewModal, Toast, ConfirmDialog, ChartBars, ChartLine, ChartDonut, Drawer, and Footer.

## 5. Page layouts

Every page uses a centered, fluid content frame with a page header and one or more white panels. Dashboard uses a 5-card metric row, two-column operational content area, and an activity/deadlines row. Case list uses a filter panel followed by a table. Registration uses a two-column form with a vertical step rail and a review step. Case details uses a prominent identity header, summary cards, tabs, then a two-column workspace. Documents use a table plus preview side panel. Reports pair compact filters with one or two charts and an export-ready table.

## 6. Responsive behavior

- **1520px and below:** the last table column (Action / Preview) is pinned to the right edge with a soft inset shadow so row actions stay reachable while the wide table scrolls.
- **1280px and below:** the five-card metric row becomes three columns and dashboard chart rows stack into a single column.
- **1180px and below:** the persistent rail narrows to 214px, content padding drops to 22px, and the filter grid becomes three columns with the search field spanning the row.
- **820px and below:** the rail becomes an off-canvas drawer (z-index 40) with a scrim (z-index 39) above the bottom navigation (z-index 25). The top bar stays sticky, page headers wrap, the settings navigation scrolls horizontally, forms become single-column, and case detail stacks into one column. Buttons, pagination, tabs, navigation items, and inputs use 44px touch targets; input text is 16px to avoid mobile zoom. The drawer stops above the bottom navigation so its footer is never clipped, and toasts are raised above it. Safe-area insets are applied to content, drawer footer, bottom navigation, and toasts.
- **560px and below:** the compact language switch replaces the toggle, metrics and filters become two columns, head actions wrap below the title, timelines reflow to two columns, review rows and notifications stack, and the case header tightens.

Tables stay horizontally scrollable at narrow widths rather than switching to cards. The case register keeps the case ID, title, status, priority, next deadline, and row action visible within the scrollable table.

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

- Semantic landmarks are used: a banner header, navigation aside, main content region, dialog role, and a skip link to the application root.
- Form controls have visible labels; required fields and validation errors are shown in text, and invalid controls get a danger border and background.
- Keyboard focus is never removed: a 2px outline with 2px offset is applied globally, inputs add a 4px halo, and horizontally scrollable containers reserve padding so rings are not clipped. Dialogs autofocus their close button and return focus to the trigger on close.
- Status is conveyed with text plus a badge treatment, never color alone. Priority is written out as a word.
- Table headers are `<th>` elements with ghost-style sort buttons; selection checkboxes expose an aria-label containing the case ID.
- Dialogs have a labelled title, a close button, Escape support, and a scrim that closes on click.
- Motion is limited to short hover/transition states and honors `prefers-reduced-motion`.
- Contrast targets WCAG 2.1 AA: primary text `#162331`, muted `#5E6B7A` (about 5.4:1 on white), and form-control borders `#7D8B9A` (about 3.5:1 on white) exceed the 3:1 non-text threshold. No body text is below 11px.
- Mobile controls are at least 44px tall and form inputs use 16px text; the drawer footer clears the bottom navigation; toasts do not cover it.
- The page has no horizontal overflow at 375, 390, 430, 768, 1024, 1366, or 1920px; tables scroll inside their own containers. Layout remains usable at 200% zoom without hiding primary actions.
