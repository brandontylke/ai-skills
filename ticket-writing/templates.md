# Ticket Templates

## Bug Report

```markdown
## [BUG] <Title>

**Severity:** <Critical | Major | Minor | Trivial>
**Environment:** <environment details>
**Reported by:** <name/role>
**Date:** <YYYY-MM-DD>

### Description
<One-sentence summary of the defect.>

### Steps to Reproduce
1. <Step 1>
2. <Step 2>
3. <Step 3>

### Expected Behavior
<What should happen.>

### Actual Behavior
<What actually happens.>

### Supporting Evidence
<Logs, screenshots, error messages, or stack traces.>

### Acceptance Criteria
- [ ] <Condition that confirms the fix>
```

---

## Feature Request

```markdown
## [FEATURE] <Title>

**Priority:** <Must have | Should have | Nice to have>
**Requested by:** <name/role>
**Date:** <YYYY-MM-DD>

### Problem Statement
<Who is affected and what problem does this solve?>

### Proposed Solution
<Description of the desired feature.>

### Acceptance Criteria
- [ ] <Testable condition 1>
- [ ] <Testable condition 2>

### Alternatives Considered
<Other approaches and why they were not chosen.>
```

---

## User Story

```markdown
## [STORY] <Title>

**Story Points:** <1 | 2 | 3 | 5 | 8 | 13>
**Sprint:** <sprint name or backlog>
**Date:** <YYYY-MM-DD>

### User Story
As a **<role>**, I want **<goal>** so that **<benefit>**.

### Acceptance Criteria
- [ ] Given <precondition>, when <action>, then <result>
- [ ] Given <precondition>, when <action>, then <result>

### Dependencies
<List any tickets this depends on or blocks.>

### Notes
<Additional context, design links, or open questions.>
```

---

## Technical Task

```markdown
## [TASK] <Title>

**Category:** <Refactor | Infrastructure | Performance | Security | Tech Debt | Documentation>
**Date:** <YYYY-MM-DD>

### Description
<What needs to be done.>

### Motivation
<Why this work is needed now.>

### Acceptance Criteria
- [ ] <Verifiable condition 1>
- [ ] <Verifiable condition 2>

### Risks & Caveats
<Known risks, rollback plans, or things to watch for.>
```
