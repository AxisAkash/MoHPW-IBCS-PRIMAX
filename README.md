# MoHPW Operation & Monitoring System for Cases

A polished, frontend-only demonstration of a government case monitoring workspace for the Ministry of Housing and Public Works. All records are fictional demo data and the app intentionally does not claim backend security, persistence, identity verification, or production readiness.

## Run locally

Requirements: Node.js 18 or newer.

```text
npm start
```

Open `http://localhost:4173` in a browser. The same command is used for development because the app is deliberately dependency-light and serves static files through the included Node server.

Demo sign-in accepts any non-empty email and password. The default landing role is Case Handler; use the role selector on the login screen to preview Case Handler, Supervisor, or System Administrator visibility.

## Included workflows

- Login with role preview and bilingual UI toggle
- Operational dashboard with demo KPI cards, workload chart, deadlines, activity, notices, and escalation indicators
- Case register with search, filters, saved view, sorting, column visibility, selection, bulk actions, export, pagination, and empty/loading/error/permission states
- Guided case intake with step validation, draft save, attachments, and review-before-submit
- Case workspace with summary, timeline, tasks, deadlines, documents, notes, communications, and audit history
- Workflow/task queue with task actions and escalation cues
- Document centre with upload validation, metadata, preview modal, versions, confidentiality, and verification state
- Reports with filters, charts, table view, printable/export-ready actions
- Notifications, user/role management, and system settings
- Responsive layouts for desktop, tablet, and mobile task review

## Frontend-demo limitations and integration points

The app stores state in memory only. Refreshing the browser resets the demonstration state. A production implementation should add:

1. SSO/OIDC or government identity integration, session management, MFA, and server-side authorization.
2. A case, document, task, notification, audit, organization, and user API with server-side validation and optimistic concurrency.
3. Encrypted object storage with malware scanning, retention policy, immutable audit records, and document access checks.
4. Server-side pagination, filtering, full-text search, exports, report aggregation, and print/PDF generation.
5. Localization files for complete Bangla coverage and a formal accessibility test plan with screen-reader and assistive-technology testing.

## Project guide

- `index.html` — application shell and SVG icon sprite
- `src/app.js` — demo data, reusable render helpers, routing, and interactions
- `src/styles.css` — design tokens, component styles, and responsive behavior
- `docs/PRODUCT_SPEC.md` — information architecture, design tokens, component inventory, layouts, responsive rules, roles, data model, validation, and accessibility checklist

No real government logo, seal, personal data, legal matter, or official statistic is used.
