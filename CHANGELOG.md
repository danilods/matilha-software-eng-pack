# Changelog

## [0.3.0] — 2026-04-26 — Wave 5h: deterministic trigger skill

### Added

- **`matilha-software-eng-trigger` skill** — independent activation surface for software-engineering domain. Keyword-rich description (refactor, technical debt, KISS, DRY, TDD, coverage, context loss, clean code, naming, backlog, code review, pull request, YAGNI, atomic commits, changelog discipline, etc.) ensures pack skills enter the conversation whenever the domain appears in user prompts.
- Complements `matilha-skills`'s routing table (`skills/matilha-compose/routing-table.md`); together they form Wave 5h's Maximum Deterministic Activation surface.

### Notes

- Fully additive: existing 15 skills untouched. Pack continues to work standalone or with `matilha-skills` (Matilha core).
- Trigger is a routing surface, not a craft skill — emits a compact domain acknowledgment and hands off to the most relevant pack skill via the Skill tool.
- No behavior change when pack is uninstalled: trigger emits a `/matilha-install` nudge and yields to default flow.

## [0.1.0] — 2026-04-23 — Wave 5f: initial release

### Added

- 15 skills distilled from Danilo-experience engineering rules:
  - **Princípios de código** (4): `sweng-kiss-antidote-overengineering`, `sweng-roro-pattern`, `sweng-pythonic-idioms`, `sweng-nomenclatura-clareza`.
  - **Documentação viva** (3): `sweng-readme-non-rotting`, `sweng-changelog-discipline`, `sweng-documentation-directory`.
  - **Rastreabilidade** (2): `sweng-commits-atomicos-semanticos`, `sweng-session-checklists`.
  - **Gestão de tarefas** (3): `sweng-todo-structure`, `sweng-progresso-atual`, `sweng-task-definition-estimativas`.
  - **Análise crítica** (3): `sweng-analise-tecnica-estrutura`, `sweng-pre-analise-clarifying`, `sweng-responsabilidade-tecnica`.

- `.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json` — plugin manifest + marketplace registration (owner + metadata + plugins[] schema).
- `README.md` — pack overview with install instructions, companion relationship, overlap disclosures.
- `docs/source-distillation-workflow.md` — workflow for distilling Danilo-experience rules into skill bodies (different from wiki-ingestion-workflow used by other packs).
- `docs/skills-inventory.md` — full inventory with triggering intents and key principles per skill.

### Overlap disclosures

- `sweng-kiss-antidote-overengineering` **Complementa** `matilha-harness-pack:harness-code-is-free` — harness treats code as cheap (AI writes fast); sweng treats simplicity as human-architectural-value discipline.
- `sweng-pre-analise-clarifying` **Complementa** `matilha:matilha-plan` — matilha-plan for new features (spec authoring); sweng-pre-analise for reviewing existing code (diagnose before judge).

### Notes

- Pack shipped following matilha skill-authoring-guide + Wave 5e validator conventions.
- Unlike Weinschenk/Krug/Tan/Anthropic-derived packs, this is opinions-from-practice rather than literature-ingestion.
- Source authority: Danilo de Sousa's curated rules directory (7 documents).
