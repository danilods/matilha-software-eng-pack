---
name: matilha-software-eng-trigger
description: "Use when the user mentions refactoring, technical debt, KISS, DRY, TDD, test coverage, context loss, clean code, naming conventions, backlog grooming, code review, pull request, commit discipline, YAGNI, or any topic related to software engineering practices, team discipline, and day-to-day coding decisions. Fires independently of compose to ensure matilha-software-eng-pack skills activate whenever software engineering domain appears."
category: software-eng-pack
version: "1.0.0"
---

## When this fires

User prompt mentions any keyword from matilha-software-eng-pack's domain: refactoring, technical debt, KISS, DRY, TDD, test coverage, context loss, clean code, naming conventions, backlog grooming, code review, pull request, commit discipline, YAGNI.

This skill fires independently of `matilha:matilha-compose` and `CLAUDE.md` activation rules — its keyword-rich description is the activation surface for the Skill tool's matcher. It guarantees that whenever the user prompt touches the software-engineering practice domain, at least one matilha-software-eng-pack skill enters the conversation.

## Execution Workflow

1. **Pack presence check.** Inspect the ambient skill list for skills in the `matilha-software-eng-pack:*` namespace. Examples: `matilha-software-eng-pack:sweng-kiss-antidote-overengineering`, `matilha-software-eng-pack:sweng-commits-atomicos-semanticos`, `matilha-software-eng-pack:sweng-todo-structure`.

2. **Pack installed path.** If ≥1 skill from `matilha-software-eng-pack` is visible:
   - Identify the user's sub-intent within the engineering-practice domain (commit hygiene, naming, refactor scope, KISS guard, README rot, task definition, technical-responsibility framing, etc.).
   - Emit a compact domain acknowledgment (≤2 lines — no full sigil, that belongs to compose). Example: `Software engineering practice detected. Pulling matilha-software-eng-pack guidance for <sub-intent>.`
   - Invoke the most relevant pack skill via the Skill tool. Prefer one specific skill over listing many.

3. **Pack not installed path.** If no `matilha-software-eng-pack` skills are visible:
   - Emit: "Software Engineering skills not installed. Run `/matilha-install` and select `software-eng` to add 15 engineering practice skills."
   - Proceed with default flow (`matilha:matilha-compose` if visible, otherwise `superpowers:brainstorming`).

## Rules

- Do NOT emit the full compose sigil. The sigil belongs to compose; this skill emits a compact pack-specific acknowledgment.
- Do NOT block on pack absence — emit the nudge and continue with the default flow.
- Prefer invoking a specific pack skill over listing all skills.
- This trigger is a routing surface, not a craft skill — hand off to the chosen pack skill quickly.

## Why this exists

Wave 5h adds deterministic activation surfaces to compensate for probabilistic Skill-tool matching. Compose's routing table covers the central path; this trigger covers prompts that bypass compose (e.g., when CLAUDE.md is absent, or when a domain keyword appears mid-conversation without a compose-classifiable phase). Together they form the Maximum Activation guarantee for matilha-software-eng-pack.
