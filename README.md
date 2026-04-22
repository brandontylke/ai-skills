# AI Skills

A collection of agent skills that extend your AI assistant with structured, reusable workflows.

## Skills

### [Ticket Writing](ticket-writing/)

An interactive skill that guides you through writing clear, actionable tickets following Agile/Scrum conventions. Rather than generating a ticket from a vague prompt, it runs a structured interview — asking targeted questions based on ticket type (bug report, feature request, user story, or technical task) — then assembles the answers into a well-formatted ticket with acceptance criteria, context, and metadata.

### [URL Summary](url-summary/)

A personal URL collector and research summarizer. Drop URLs into a dated collection managed by your agent, then ask for summaries over any time period. The agent fetches each page, groups URLs by topic, and ranks themes by frequency — helping you spot trends and keep track of what you've been reading without hoarding browser tabs.

## Installation

Copy or symlink any skill directory to a supported location:

| Location | Scope |
|----------|-------|
| `~/.cursor/skills/<skill>/` | Available across all your projects |
| `~/.claude/skills/<skill>/` | Available across all your projects |
