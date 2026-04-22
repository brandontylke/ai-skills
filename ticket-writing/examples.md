# Ticket Examples

Good and bad examples to calibrate ticket quality.

---

## Bug Report

### Bad

```
Title: Login broken
Description: Login doesn't work. Please fix.
```

**Why it's bad:** No severity, no steps to reproduce, no environment, no expected vs actual behavior. A developer cannot act on this.

### Good

```markdown
## [BUG] Login fails with 500 error when using SSO on Firefox

**Severity:** Major
**Environment:** Production, Firefox 128, macOS 15

### Steps to Reproduce
1. Navigate to /login
2. Click "Sign in with SSO"
3. Complete SAML authentication on IdP
4. Redirected back to /auth/callback

### Expected Behavior
User is authenticated and redirected to the dashboard.

### Actual Behavior
Server returns HTTP 500. Error in logs:
`NullPointerException at AuthService.java:142 — samlResponse.getNameID() is null`

### Acceptance Criteria
- [ ] SSO login succeeds on Firefox without 500 error
- [ ] NameID null case is handled gracefully
```

---

## User Story

### Bad

```
Title: User dashboard
Description: We need a dashboard for users.
```

**Why it's bad:** No role, no goal, no benefit, no acceptance criteria. "Dashboard" could mean anything.

### Good

```markdown
## [STORY] Account overview dashboard for subscribers

**Story Points:** 5

### User Story
As a **subscriber**, I want **a dashboard showing my billing history and current plan** so that **I can track spending without contacting support**.

### Acceptance Criteria
- [ ] Given a logged-in subscriber, when they visit /dashboard, then they see their current plan name and renewal date
- [ ] Given a subscriber with past invoices, when they view billing history, then invoices are listed newest-first with amount and status
- [ ] Given a subscriber on a free trial, when they view the dashboard, then a banner shows days remaining
```

---

## Feature Request

### Bad

```
Title: Add export
Description: Users want to export stuff.
```

**Why it's bad:** Export what? In what format? Who asked? No acceptance criteria.

### Good

```markdown
## [FEATURE] Export transaction history as CSV

**Priority:** Should have

### Problem Statement
Finance team members manually copy transaction data into spreadsheets for monthly reconciliation, costing ~2 hours per report.

### Proposed Solution
Add a "Download CSV" button to the transactions page that exports filtered results with columns: date, description, amount, category, status.

### Acceptance Criteria
- [ ] CSV includes all columns visible in the current table view
- [ ] Active filters are applied to the export
- [ ] Files over 10k rows are streamed to avoid timeouts
- [ ] Filename follows pattern: transactions_YYYY-MM-DD.csv

### Alternatives Considered
- PDF export — rejected; finance team needs editable data
- API endpoint — considered for phase 2; UI button solves the immediate need
```

---

## Technical Task

### Bad

```
Title: Fix performance
Description: The app is slow, speed it up.
```

**Why it's bad:** No specifics on what's slow, no measurements, no acceptance criteria.

### Good

```markdown
## [TASK] Add database index to speed up order search

**Category:** Performance

### Description
Add a composite index on `orders(customer_id, created_at)` to eliminate full table scans on the order search endpoint.

### Motivation
The GET /orders?customer_id=X endpoint averages 4.2s at p95 (target: <500ms). EXPLAIN shows a sequential scan on the 12M-row orders table.

### Acceptance Criteria
- [ ] Composite index exists on orders(customer_id, created_at DESC)
- [ ] GET /orders p95 latency drops below 500ms
- [ ] Migration is reversible

### Risks & Caveats
- Index creation on a 12M-row table may lock writes for ~30s; run during off-peak hours or use CREATE INDEX CONCURRENTLY (Postgres).
```
