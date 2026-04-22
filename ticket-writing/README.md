# Ticket Writing Skill

A skill that interactively guides you through writing clear, actionable tickets following Agile/Scrum conventions.

## What It Does

Instead of generating a ticket from a vague prompt, this skill runs a structured interview — asking targeted questions based on ticket type, then assembling the answers into a well-formatted ticket with acceptance criteria, context, and appropriate metadata.

## Supported Ticket Types

| Type | Use When |
|------|----------|
| Bug report | Something is broken and needs fixing |
| Feature request | You want new functionality added |
| User story | You're describing work from a user's perspective |
| Technical task | Refactors, infra work, performance, security, or tech debt |

## How to Use

Trigger the skill by asking the agent to help you write a ticket, file a bug, create a story, or draft a feature request. The agent will:

1. Ask what type of ticket you're creating
2. Walk you through type-specific questions (severity, steps to reproduce, acceptance criteria, etc.)
3. Present a formatted draft for your review
4. Let you edit sections or finalize the output

## Installation

Copy or symlink this directory to one of the supported skill locations:

```
# Personal (available in all projects)
cp -r . ~/.cursor/skills/ticket-writing/
cp -r . ~/.claude/skills/ticket-writing/
```

## File Structure

| File | Purpose |
|------|---------|
| `SKILL.md` | Main instructions and interactive workflow |
| `templates.md` | Fill-in-the-blank templates for each ticket type |
| `examples.md` | Good and bad examples for quality calibration |
