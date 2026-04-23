---
name: sweng-documentation-directory
description: "Use when organizing project docs beyond README, setting up an @documentation/ directory, recording architecture decisions, or fixing context loss between sessions. Fires on 'Como organizo a documentação do projeto?', 'Onde salvo essa decisão arquitetural?', 'Perdi contexto entre sessões', 'Como retomo amanhã de onde parei?'."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara quando o projeto cresce além do README e você precisa de um lugar para: decisões técnicas (ADRs), diagramas de arquitetura, documentação de API, fluxos, ambiente de desenvolvimento, e — o mais importante — **estado atual do projeto para retomar amanhã**.

Também ativa quando o sintoma é "abro o projeto depois de uma semana e não sei onde parei". Perda de contexto entre sessões é o calcanhar de Aquiles de projeto multi-semana — especialmente assistido por AI, onde cada sessão começa com contexto zero se não houver um snapshot persistente.

A estrutura preserva discurso técnico durável (arquitetura, decisões) e discurso efêmero disciplinado (progresso por sessão) — cada um no seu lugar.

## Preconditions

- Projeto tem pelo menos uma semana de desenvolvimento ou foi planejado para durar mais que uma sprint.
- README já existe e está minimamente atualizado (se não, prioriza `sweng-readme-non-rotting` antes).
- Há consenso (mesmo que com você mesmo) de que a documentação precisa ser navegável, não só um dump em um arquivo.
- Se já existe pasta ad-hoc tipo `docs/` ou `notes/`, decida: migrar para `@documentation/` ou manter — sem duplicar.

## Execution Workflow

1. **Crie o esqueleto `@documentation/` na raiz do projeto:**
   ```
   @documentation/
   ├── arquitetura/
   │   ├── diagramas/
   │   └── decisoes-tecnicas.md
   ├── api/
   │   ├── endpoints.md
   │   └── exemplos/
   ├── fluxos/
   │   ├── fluxo-principal.md
   │   └── casos-especiais/
   ├── desenvolvimento/
   │   ├── ambiente.md
   │   ├── testes.md
   │   └── deploy.md
   └── progresso/
       ├── backlog.md
       ├── atual.md
       └── concluido.md
   ```
   Comece com o que é útil agora; não crie subpastas vazias só por simetria. `arquitetura/`, `desenvolvimento/` e `progresso/` são as três pastas mínimas viáveis.
2. **Semeie `progresso/atual.md`** com o estado da última sessão: o que está em andamento, bloqueadores conhecidos, próximo passo, próxima sessão. Esse arquivo é **o principal ponto de prevenção de perda de contexto** — atualize ao final de cada sessão (10min).
3. **Migre decisões técnicas espalhadas** (Slack, Notion, PRs) para `arquitetura/decisoes-tecnicas.md` no formato ADR curto: `## ADR-N: Título\n**Data:** YYYY-MM-DD\n**Status:** Aceito | Substituído | Obsoleto\n**Contexto:** ...\n**Decisão:** ...\n**Consequências:** ...`.
4. **Linke do README.** README tem um bloco `## Documentação` apontando para `@documentation/` com 3–5 links para as seções mais usadas (arquitetura, API, como rodar localmente).
5. **Defina ritmo de atualização:**
   - `progresso/atual.md` → **toda sessão** (no encerramento, 10min).
   - `arquitetura/decisoes-tecnicas.md` → **toda decisão técnica não óbvia** (antes de implementar).
   - `api/`, `fluxos/` → quando a interface muda ou é adicionada.
   - `desenvolvimento/ambiente.md` → quando mudam dependências, setup, ou comandos.
6. **Separe "existe" de "planejado".** Em qualquer arquivo, deixe explícito: `✅ Implementado`, `🚧 Em progresso`, `📋 Planejado`. Documentação que mistura aspiração com realidade alucina.
7. **Revise mensalmente.** Varra `@documentation/` procurando arquivos que não batem mais com o código. Uma hora por mês > três dias de retrabalho.

## Rules: Do

- **`progresso/atual.md` é intocável — atualize.** Se só der para manter uma coisa em dia, mantenha esse.
- **ADRs curtos.** Cinco campos, meia página cada. ADR de 10 páginas ninguém lê; ADR de meia página todo mundo lê.
- **Datas em tudo.** Cada ADR, cada snapshot de progresso, cada doc de API carrega data. Doc sem data é doc suspeito.
- **Diagramas versionados.** PNG ou SVG comitado junto do fonte (Mermaid, PlantUML). Imagem em link externo morre.
- **README linka `@documentation/`.** Sem link, a pasta vira cemitério.
- **Separe existente de planejado.** Use marcadores (`✅ 🚧 📋`) ou seções dedicadas. "Como é" e "como vai ser" são discursos diferentes.
- **Uma decisão = um ADR.** Resiste à tentação de agrupar "três decisões correlatas" em um só ADR — rastreabilidade quebra.

## Rules: Don't

- **Não vomite o Slack inteiro na pasta.** Documentação é curadoria, não arquivo morto. Só entra o que vai ser consultado.
- **Não duplique README.** `@documentation/` é **detalhe**; README é **overview**. Se algo pertence aos dois, vive em `@documentation/` e o README linka.
- **Não escreva documentação aspiracional como se fosse real.** "A API suporta X" quando X não existe ainda é alucinação documental — custa confiança do time.
- **Não versione `progresso/atual.md` tentando manter histórico inline.** Histórico vai para `progresso/concluido.md`; `atual.md` é snapshot presente.
- **Não crie subpasta vazia "para o futuro".** `api/` vazio polui; só crie quando o primeiro arquivo chegar.
- **Não escreva doc para substituir conversa.** Docs persistem o que já foi decidido; não devem ser o lugar onde se descobre/negocia a decisão.

## Expected Behavior

Após a skill rodar, o projeto tem:

1. Pasta `@documentation/` com subestrutura mínima (arquitetura, desenvolvimento, progresso) semeada.
2. `progresso/atual.md` refletindo estado da sessão atual.
3. Pelo menos um ADR registrado se houve decisão técnica recente.
4. README com bloco `## Documentação` linkando `@documentation/`.
5. Ritmo de atualização definido (explícito em `CLAUDE.md` ou `@documentation/README.md`).

Output típico: árvore de arquivos criada, primeiro `progresso/atual.md` semeado com snapshot da sessão corrente, link adicionado no README.

## Quality Gates

- [ ] `@documentation/` existe na raiz?
- [ ] `progresso/atual.md` tem data da última atualização no topo?
- [ ] README tem link claro para `@documentation/`?
- [ ] Existe pelo menos uma subpasta populada de cada categoria que faz sentido para o projeto (`arquitetura/`, `desenvolvimento/`)?
- [ ] Decisões técnicas recentes têm ADR correspondente?
- [ ] Não há documentação aspiracional misturada com documentação real sem marcador?
- [ ] Ritmo de atualização está explícito em algum lugar (CLAUDE.md, docs/README)?

## Companion Integration

- **Complementa `sweng-readme-non-rotting`:** README fica conciso porque detalhe mora em `@documentation/`. Essa simbiose protege os dois.
- **Complementa `sweng-session-checklists`:** o checklist de fim de sessão **escreve** em `progresso/atual.md`. Uma skill define o conteúdo, a outra ritualiza a atualização.
- **Complementa `sweng-todo-structure`:** `@TODO.md` na raiz é planejamento ativo; `progresso/atual.md` é snapshot de execução; `progresso/concluido.md` é histórico. Os três conversam, não competem.
- **Integra com `matilha-den`:** quando matilha-den registra aprendizado de projeto, o lugar é `@documentation/arquitetura/decisoes-tecnicas.md` ou um arquivo em `@documentation/den/`.
- **Integra com `matilha-howl`:** ao iniciar sessão, `matilha-howl` lê `progresso/atual.md` para determinar próxima ação.

## Output Artifacts

- Árvore `@documentation/` criada (ou ampliada).
- `progresso/atual.md` semeado com snapshot da sessão atual.
- `arquitetura/decisoes-tecnicas.md` com ADR-001 registrando a adoção desta estrutura (meta-ADR).
- Bloco `## Documentação` adicionado ao README.
- Commit dedicado: `docs: scaffold @documentation/ structure + progresso/atual.md inicial`.

## Example Constraint Language

- "Antes de começar, crie `@documentation/progresso/atual.md` com data de hoje e o foco desta sessão. Ao encerrar, atualize com 'feito/próximo/bloqueadores'."
- "Essa decisão arquitetural (usar Postgres em vez de SQLite) merece ADR. Registra em `arquitetura/decisoes-tecnicas.md` antes de migrar."
- "Não documenta no README que suportamos X — X está só planejado. Se for aspiracional, vai em `@documentation/progresso/backlog.md` com marcador `📋 Planejado`."

## Troubleshooting

- **"Já tenho `docs/` espalhado e inconsistente."** Crie `@documentation/` novo, migre em ondas (arquitetura primeiro, depois decisões, depois fluxos). Não tente converter tudo em um dia.
- **"Ninguém lê a documentação que eu escrevo."** Sintoma de: (a) não linkada do README, (b) não atualizada (confiança perdida), (c) muito longa. Corrija na ordem.
- **"`progresso/atual.md` vira dump gigante."** Limite a uma tela. O que excede vai para `progresso/concluido.md` (histórico) ou `@documentation/arquitetura/` (decisão durável).
- **"Projeto small — vale a pena?"** Se dura menos de 2 semanas e é solo: README + `progresso/atual.md` basta. Estrutura completa é para projetos de 4+ semanas.
- **"ADRs parecem burocracia."** ADR de meia página em 10min poupa 3 horas de arqueologia em PR daqui a dois meses. Teste por três ADRs e avalie.

## CLI shortcut (optional)

Se `matilha` CLI disponível:
```bash
matilha docs init              # cria árvore @documentation/ mínima
matilha docs adr "Postgres escolhido sobre SQLite"
matilha docs progress update   # abre atual.md com template
```

Equivalente manual:
```bash
mkdir -p @documentation/{arquitetura/diagramas,api/exemplos,fluxos,desenvolvimento,progresso}
touch @documentation/progresso/{atual,backlog,concluido}.md
touch @documentation/arquitetura/decisoes-tecnicas.md
```

## Concrete Example

Projeto em semana 3. Dev reabre após 5 dias. Sem `progresso/atual.md`: abre `git log`, lê últimos 15 commits, tenta reconstruir por onde ia, erra duas suposições, gasta 40min antes de escrever primeira linha útil. Com `progresso/atual.md` atualizado na última sessão: lê 1 tela, identifica `Próximo passo: finalizar refresh token (1h)` e bloqueador `aguardando chave AWS`, decide pular o bloqueador e fazer o próximo passo — 3min de aquecimento, 55min produtivos. Diferença: 40min/sessão × 3 sessões/semana × 8 semanas = ~16h recuperadas por projeto.

## Sources

- [[rules/Documentação Completa do Projeto]] — seções "## Diretório @documentation", "## Prevenção de Perda de Contexto", "## Processo de Atualização"
- Convenção externa: ADRs (Architecture Decision Records), formato curto (inspirado em Michael Nygard) — adaptado à voz do projeto.
