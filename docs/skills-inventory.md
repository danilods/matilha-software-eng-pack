# matilha-software-eng-pack — skills inventory

15 skills organized in 5 families.

## Family 1 — Princípios de código (4 skills)

| Skill | Triggering intent | Source rule |
|---|---|---|
| `sweng-kiss-antidote-overengineering` | "Esse código parece over-engineered?" / "Preciso dessa abstração?" | KISS rule |
| `sweng-roro-pattern` | "Função com muitos parâmetros" / "Como estruturar o return?" | Diretrizes Python — RORO section |
| `sweng-pythonic-idioms` | "Code review Python" / "Comprehension ou loop?" | Diretrizes Python — estruturas de controle |
| `sweng-nomenclatura-clareza` | "Nome dessa variável/função não tá bom" / "Como nomear X?" | Diretrizes Python — convenções nomenclatura |

## Family 2 — Documentação viva (3 skills)

| Skill | Triggering intent | Source rule |
|---|---|---|
| `sweng-readme-non-rotting` | "Como escrevo um README?" / "Este README vai sobreviver?" | Documentação rule — README diretrizes |
| `sweng-changelog-discipline` | "Como versionar?" / "Entry no CHANGELOG" | Documentação rule — CHANGELOG diretrizes |
| `sweng-documentation-directory` | "Como organizar docs do projeto?" / "Perda de contexto entre sessões" | Documentação rule — @documentation + prevenção de perda de contexto |

## Family 3 — Rastreabilidade (2 skills)

| Skill | Triggering intent | Source rule |
|---|---|---|
| `sweng-commits-atomicos-semanticos` | "Como commitar isso?" / "Mensagem de commit" | Commits rule |
| `sweng-session-checklists` | "Começando sessão" / "Terminando sessão" / "Como continuar amanhã?" | Gestão Tarefas rule — checklists início/fim |

## Family 4 — Gestão de tarefas (3 skills)

| Skill | Triggering intent | Source rule |
|---|---|---|
| `sweng-todo-structure` | "@TODO do projeto" / "Backlog" / "Priorizar tarefas" | Gestão Tarefas + Planejamento rules |
| `sweng-progresso-atual` | "Estado atual do projeto" / "Próxima sessão" / "Bloqueadores" | Gestão Tarefas — arquivo progresso/atual.md |
| `sweng-task-definition-estimativas` | "Como definir essa tarefa?" / "Quanto tempo leva?" / "Tarefa bem definida" | Gestão Tarefas — padrões de qualidade |

## Family 5 — Análise crítica (3 skills)

| Skill | Triggering intent | Source rule |
|---|---|---|
| `sweng-analise-tecnica-estrutura` | "Analisa esse código/arquitetura" / "Code review estrutural" | Análise Crítica rule — estrutura de análise |
| `sweng-pre-analise-clarifying` | "Antes de analisar X, confirme comigo" | Análise Crítica rule — compreensão profunda |
| `sweng-responsabilidade-tecnica` | "Opinião técnica responsável" / "Sem hype, só fatos" | Análise Crítica rule — princípios de análise |

## Overlap disclosures

- `sweng-kiss-antidote-overengineering` **Complementa** `matilha-harness-pack:harness-code-is-free` — harness treats code as cheap because AI writes fast; sweng-kiss treats simplicity as human-architectural-value discipline.
- `sweng-pre-analise-clarifying` **Complementa** `matilha:matilha-plan` — matilha-plan is for new features (spec authoring); sweng-pre-analise is for reviewing existing code/architecture (diagnose before judge).

## Source distillation

See [source-distillation-workflow.md](source-distillation-workflow.md) — workflow for distilling Danilo-experience rules into skill bodies (differs from wiki-ingestion used by other matilha packs).
