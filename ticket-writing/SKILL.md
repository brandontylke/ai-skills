---
name: ticket-writing
description: >-
  Interactively guide users through writing well-structured tickets including
  bug reports, feature requests, user stories, and technical tasks following
  Agile/Scrum conventions. Use when the user wants to write a ticket, create an
  issue, draft a story, file a bug, or request a feature.
---

# Ticket Writing

Interactively guide users through creating clear, actionable tickets. Use the AskQuestion tool to gather information step-by-step, then produce a formatted ticket.

## Interactive Workflow

### Step 1: Determine Ticket Type

Use AskQuestion to ask:

```
Prompt: "What type of ticket are you creating?"
Options:
  - Bug report
  - Feature request
  - User story
  - Technical task
```

### Step 2: Gather Context by Type

Based on the ticket type, run the appropriate interview below. Use AskQuestion where options are known, and ask conversationally for open-ended fields. Gather **all** fields before generating the ticket.

---

#### Bug Report Interview

1. **Title** (conversational): "Describe the bug in one sentence."
2. **Severity** (AskQuestion):
   - Critical — system down, data loss
   - Major — core feature broken, no workaround
   - Minor — feature impaired, workaround exists
   - Trivial — cosmetic, typo
3. **Environment** (conversational): "What environment did this occur in? (e.g. production, staging, local dev, OS, browser)"
4. **Steps to reproduce** (conversational): "Walk me through the exact steps to reproduce this."
5. **Expected behavior** (conversational): "What should have happened?"
6. **Actual behavior** (conversational): "What actually happened?"
7. **Supporting evidence** (conversational): "Do you have logs, screenshots, or error messages? Paste or describe them."

#### Feature Request Interview

1. **Title** (conversational): "Summarize the feature in one sentence."
2. **Problem statement** (conversational): "What problem does this solve? Who is affected?"
3. **Proposed solution** (conversational): "Describe your ideal solution."
4. **Priority** (AskQuestion):
   - Must have — blocking a release or critical path
   - Should have — important but not blocking
   - Nice to have — improves experience
5. **Acceptance criteria** (conversational): "How will we know this is done? List testable conditions."
6. **Alternatives considered** (conversational): "Are there other approaches you've thought about?"

#### User Story Interview

1. **Role** (conversational): "Who is the user? (e.g. admin, customer, API consumer)"
2. **Goal** (conversational): "What does this user want to do?"
3. **Benefit** (conversational): "Why do they want to do it? What value does it deliver?"
4. **Acceptance criteria** (conversational): "List the conditions that must be true for this story to be accepted."
5. **Story points estimate** (AskQuestion):
   - 1 — trivial (simple text change or some other quick change)
   - 2 — small (small functional changes. things you would have to verify)
   - 3 — medium (common tasks that make you think)
   - 5 — large (more complex, tests need written, sleuthing is involved. the solution isn't quite clear or may have aspects which requires research)
   - 8 — very large (near the realm of breaking down the ticket into smaller ones)
   - 13 — epic-sized, consider splitting
6. **Dependencies** (conversational): "Does this depend on or block other work?"

#### Technical Task Interview

1. **Title** (conversational): "Summarize the task in one sentence."
2. **Category** (AskQuestion):
   - Refactor
   - Infrastructure / DevOps
   - Performance
   - Security
   - Tech debt
   - Documentation
3. **Description** (conversational): "Describe the work to be done."
4. **Motivation** (conversational): "Why is this needed now?"
5. **Acceptance criteria** (conversational): "How will we verify this is complete?"
6. **Risks or caveats** (conversational): "Any risks, rollback plans, or things to watch out for?"

---

### Step 3: Review and Refine

After gathering all inputs, present the draft ticket using the appropriate template from [templates.md](templates.md).

Ask the user:

```
Prompt: "How does this look?"
Options:
  - Looks good — finalize it
  - I want to edit a section
  - Start over
```

If editing, ask which section to change and re-gather that field.

### Step 4: Sufficiency Review

After the user approves the draft, perform a holistic review of the **entire** ticket before finalizing. The goal: **if this ticket were assigned to you, would you have enough information to start working on it without chasing down the author for clarification?**

Evaluate the ticket against these questions silently:

1. **Is the "what" clear?** — Can you state in one sentence exactly what needs to happen?
2. **Is the "why" clear?** — Do you understand the motivation or user impact?
3. **Is the scope bounded?** — Are there implicit assumptions, undefined terms, or ambiguous phrases that could lead to different interpretations?
4. **Are edge cases or constraints mentioned?** — For bugs: can you actually reproduce it from what's written? For features/stories: do you know what's in scope and what's out?
5. **Are the acceptance criteria verifiable?** — Could you demo or test each criterion without guessing what "done" means?

If **any** of these fail, do **not** finalize. Instead:

- Tell the user which areas are thin and **why** they need more detail (be specific — quote the vague part).
- Ask targeted follow-up questions to fill the gaps (conversational, not multiple-choice).
- After receiving answers, weave the new detail into the ticket and re-present the updated draft.
- Repeat this review loop until the ticket passes all five checks.

Only proceed to Step 5 once the ticket would be actionable by someone with no prior context.

---

### Step 5: Output

Produce the final ticket in markdown. Offer:

```
Prompt: "What would you like to do with the ticket?"
Options:
  - Copy to clipboard (print the final markdown)
  - Save to file
  - Create another ticket
```

---

## Quality Guardrails

Before finalizing, silently validate the ticket against these rules. If any fail, prompt the user to fix the gap:

| Rule | Check |
|------|-------|
| Title is specific | Not vague like "Fix bug" or "Update thing" |
| Acceptance criteria exist | At least one testable criterion |
| No assumptions | Steps/description don't skip context |
| Actionable | A developer unfamiliar with the codebase could pick this up |
| Appropriately scoped | Not trying to do too many things in one ticket |

If the title is vague, suggest 2-3 improved alternatives for the user to pick from.

---

## Additional Resources

- For ticket templates, see [templates.md](templates.md)
- For good and bad ticket examples, see [examples.md](examples.md)
