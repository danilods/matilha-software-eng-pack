# matilha-software-eng-pack

> **You lead. Agents hunt.**
> Matilha companion pack — software engineering skills from Danilo-experience.

15 skills distilled from Danilo's curated engineering rules — not a literature summary, but methodology documented from day-to-day practice across multi-week AI-assisted software projects. Auto-activates when user intent touches code quality, commits, documentation, task tracking, or critical code analysis.

## What makes this pack different

Unlike the Weinschenk/Krug/Tan/Anthropic-derived packs, this one is **opinions-based from practice**. Source is Danilo's own rules directory — 7 discipline documents refined across projects. The skills encode decisions that survived contact with reality.

## What this pack covers

- **Código e princípios** (4 skills): KISS anti-overengineering, RORO pattern (Return Object / Receive Object), Pythonic idioms, naming that reveals intent.
- **Documentação viva** (3 skills): README that doesn't rot, CHANGELOG discipline, `@documentation/` directory for context preservation.
- **Rastreabilidade** (2 skills): atomic + semantic commits, session checklists (start/during/end).
- **Gestão de tarefas** (3 skills): `@TODO.md` structure + priorization, `progresso/atual.md`, task definition + estimation anti-patterns.
- **Análise crítica** (3 skills): technical analysis structure, pre-analysis clarifying, technical responsibility (objectivity + critical thinking + what NOT to do).

## Install

```bash
/plugin marketplace add danilods/matilha-software-eng-pack
/plugin install matilha-software-eng-pack@matilha-software-eng-pack
```

**Install at user scope** for methodology-everywhere availability. See matilha core companions-contract.md for scope guidance.

## Relationship to matilha core

Companion to [matilha](https://github.com/danilods/matilha-skills). When matilha is installed, its `matilha-compose` gateway detects this pack via plugin-namespace (`matilha-*-pack`) and enriches brainstorming sessions whenever user intent touches software-engineering concerns — code quality, commits, docs, tasks, or analysis.

## Overlap disclosures

- `sweng-kiss-antidote-overengineering` **Complementa** `matilha-harness-pack:harness-code-is-free` — harness treats code as cheap because AI writes fast; sweng-kiss treats simplicity as disciplined human-architectural-value. Different angles of the same concern.
- `sweng-pre-analise-clarifying` **Complementa** `matilha:matilha-plan` — matilha-plan is for new features (spec authoring); sweng-pre-analise is for reviewing existing code/architecture (diagnose before judge).

## Source attribution

- **Danilo de Sousa** — curated engineering rules in `/Memory/docs/rules/` (7 documents, ~2,095 lines). The source is opinions-from-practice, not literature.

## Skills inventory

See [docs/skills-inventory.md](docs/skills-inventory.md).

## Contributing

Follows the matilha [skill-authoring-guide.md](https://github.com/danilods/matilha-skills/blob/main/docs/matilha/skill-authoring-guide.md) and [pack-authors.md](https://github.com/danilods/matilha-skills/blob/main/docs/matilha/pack-authors.md).

## License

MIT — see [LICENSE](LICENSE).
