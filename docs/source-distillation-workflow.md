# Source distillation workflow — matilha-software-eng-pack

This pack's source is NOT wiki literature. It's Danilo-experience: curated engineering rules (7 documents in `/Memory/docs/rules/`, ~2,095 lines) written from day-to-day practice across multi-week AI-assisted software projects. The distillation workflow differs from wiki-ingestion-workflow used by other matilha packs.

## Key difference from wiki-ingestion

Wiki ingestion: source book → wiki concept page (paraphrase layer 1) → skill body (paraphrase layer 2). 3 layers of remove.

Source distillation: Danilo's rules → skill body. 2 layers — because the rules ARE already the distilled version of Danilo's experience. The skill body adds the "how to apply" translation but preserves the rule's voice.

This is why this pack is opinions-from-practice rather than literature summary. Users install it to get Danilo's way of working, not a generic textbook.

## 5-step distillation

### Step 1 — Read the rule document end to end

Each rule file covers ONE topic (KISS, Python quality, commits, docs, tasks, analysis, planning). Read the whole file first to understand the voice and the decisions made.

### Step 2 — Identify the actionable principles

Each rule has:
- **Fundamental principles** (what the rule claims)
- **Practical examples** (how it manifests in code/process)
- **Warning signs** (when the rule is being violated)

Map each principle to one candidate skill. Aim for 2-4 skills per rule document.

### Step 3 — Frame the skill for matilha activation

Each skill needs:
- A `description` that starts with "Use when" and names concrete user intents (e.g., "Use when writing or reviewing a README", "Use when tempted to add a configuration option")
- Triggering phrases the user might type: "Como escrevo um README?", "Esse código parece over-engineered?", "Como commitar essa mudança?"
- Integration with matilha compose routing (should be discoverable via intent classification)

### Step 4 — Preserve voice; translate to skill body

Unlike wiki-paraphrase (which rewrites completely to avoid verbatim), rule distillation **preserves Danilo's voice** where possible — the decisions are his, the examples are his, the framing is his. The skill body adds:
- ONE concrete example applied to a realistic software-construction scenario
- Companion integration section (how this skill relates to other matilha packs/core skills)
- "Quality gates" that make the rule auditable (validator + self-review)

### Step 5 — Validate + cross-check uniqueness

Each skill must pass:
- Frontmatter schema: `name`, `description` starting with "Use when", `category: sweng`, `version: 1.0.0`, `requires: []`, `optional_companions: []`
- 12 required body sections + `## Sources` (13 total)
- Body length 150–300 lines (target ~180)
- Description word-overlap < 80% with other skills in the pack (avoid activation collisions)

Cross-pack check: compare against matilha-harness-pack, matilha-sysdesign-pack. Overlap is allowed if COMPLEMENTARY (disclose via `Complementa matilha-<pack>:<slug> at <angle>`). Two known overlaps pre-identified:
- `sweng-kiss-antidote-overengineering` vs `matilha-harness-pack:harness-code-is-free`
- `sweng-pre-analise-clarifying` vs `matilha:matilha-plan`

## Source attribution

Rule files consumed:
- `Princípios KISS - Keep It Simple, Stupid.md`
- `Diretrizes de Qualidade de Código Python.md`
- `Documentação Completa do Projeto.md`
- `Gerenciamento de Commits e Rastreamento de Código.md`
- `Gestão de Tarefas e Acompanhamento de Execução.md`
- `Análise Crítica e Técnica Responsável.md`
- `# Planejamento Completo do Projeto.md`

Each skill body's `## Sources` section wikilinks back to the rule file that sourced it.
