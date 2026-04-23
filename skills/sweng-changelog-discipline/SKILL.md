---
name: sweng-changelog-discipline
description: "Use when adding a CHANGELOG entry, cutting a release, deciding major vs minor vs patch, or auditing a project's versioning history. Fires on 'Como versiono isso?', 'Que entry coloco no CHANGELOG?', 'Isso é breaking change?', 'Quando bumpar a versão?'."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara quando você está prestes a cortar um release e precisa escrever (ou revisar) uma entrada no `CHANGELOG.md`, ou quando não sabe se uma mudança pede major, minor ou patch. Também ativa quando alguém pergunta "posso publicar sem atualizar o CHANGELOG?" — a resposta é não, e esta skill explica por quê e como fazer rápido.

O CHANGELOG é um contrato com quem usa o projeto (usuário externo, time interno, o próprio você daqui a três meses). Commits são o diário técnico; o CHANGELOG é a tradução para o consumidor. Uma versão sem entry é uma versão invisível — quem vai atualizar não sabe o que mudou, se deve se preocupar, se pode ir direto.

Não espere perfeição. Entry imperfeito supera entry inexistente. Incremente continuamente, revise na release cut.

## Preconditions

- Existe um `CHANGELOG.md` na raiz do projeto. Se não existe, criar é o primeiro passo (cabeçalho + seção `[Unreleased]`).
- O projeto tem uma noção de versão (semver: `MAJOR.MINOR.PATCH`). Se ainda não versiona, comece em `0.1.0`.
- As mudanças que você quer registrar já estão commitadas (ou pelo menos stageadas). Entry no CHANGELOG sem commit correspondente gera descoordenação.
- Para breaking changes, confirme que a chamada de major é intencional — breaking quebra contrato com quem usa.

## Execution Workflow

1. **Identifique a seção de destino.** Se estiver desenvolvendo, toda entry nova vai em `## [Unreleased]` no topo. Se estiver cortando release, renomeie `[Unreleased]` para `[X.Y.Z] — YYYY-MM-DD` e crie um novo `[Unreleased]` vazio acima.
2. **Classifique a mudança em um dos quatro buckets:**
   - `### Added` — funcionalidade nova, endpoint novo, flag nova
   - `### Changed` — comportamento alterado de algo que já existia
   - `### Fixed` — bug corrigido
   - `### Removed` — feature/endpoint/flag removido (breaking se era público)
   - (Opcional, quando aplicável: `### Deprecated`, `### Security`.)
3. **Escreva a entry focada no consumidor.** Pergunta de filtro: "Alguém que atualiza para esta versão precisa saber disso?". Se sim, entra; se não, fica só no commit log. Exemplo bom: `Adiciona flag \`--dry-run\` ao comando \`deploy\` (#142)`. Exemplo ruim: `refatora função interna de parsing` (isso é commit, não CHANGELOG).
4. **Linke issue/PR.** Toda entry relevante carrega `(#123)` ou `(PR #45)`. Rastreabilidade é metade do valor do CHANGELOG.
5. **Decida o bump de versão** (ao cortar release):
   - Só `Added` / `Fixed` sem breaking → **patch** (`0.4.0 → 0.4.1`).
   - `Added` novo sem breaking → **minor** (`0.4.1 → 0.5.0`).
   - Qualquer `Removed` público ou `Changed` que quebra API/comportamento esperado → **major** (`0.5.0 → 1.0.0`, ou no estágio `0.x`: `0.5.0 → 0.6.0` — trate `0.x` minor como major, é a convenção).
6. **Adicione notas de migração quando major.** Se alguém precisa mudar código para atualizar, explique como em 2–5 linhas embaixo da entry de `### Removed` ou `### Changed`.
7. **Commit separado para o bump de release.** `chore(release): v0.5.0` — commit dedicado que toca `CHANGELOG.md` + arquivo de versão (`package.json`, `pyproject.toml`, `Cargo.toml`). Tag depois desse commit.

## Rules: Do

- **Entry por release, não por commit.** CHANGELOG agrega mudanças por versão; o commit log é que detalha commit por commit.
- **Ordem decrescente.** Versão mais recente no topo. Quem atualiza lê de cima pra baixo e para quando reconhece a versão anterior dele.
- **Datas em ISO (`YYYY-MM-DD`).** Sem ambiguidade entre `05/10` (maio ou outubro?).
- **`[Unreleased]` sempre presente.** Funciona como buffer — entries vão sendo adicionadas durante o desenvolvimento; na hora do release, você renomeia.
- **Voz do consumidor, imperativo ou presente.** "Adiciona X", "Corrige Y", "Remove Z". Não "adicionei", não "feature X was added".
- **Linke (#N).** Permite a quem lê ir ao commit/PR/issue e aprofundar.
- **Semver no estágio 0.x:** bump minor conta como major (a API ainda não é estável, mas você ainda quer sinalizar quebra).
- **Breaking changes destacados.** Em negrito ou com marcador `**BREAKING:**` no início da entry.
- **Edite imperfeição depois.** Entry ruim hoje > entry perfeito daqui a uma semana.

## Rules: Don't

- **Não commite só no git e pule o CHANGELOG** em mudanças relevantes para o usuário. O histórico do git é para o time; CHANGELOG é para quem consome.
- **Não misture buckets.** Uma entry `Added` que também muda comportamento antigo vira duas entries — uma em `Added` e outra em `Changed`.
- **Não descreva o "como".** `Refatora classe X usando visitor pattern` é ruim: detalhe técnico irrelevante para consumidor. O "o quê" e o "por quê do impacto" são o que importa.
- **Não apague entries antigas.** Histórico é imutável. Errou? Adicione nota de correção na próxima versão.
- **Não bumpe major silenciosamente.** Major sem seção `### Removed` ou `### Changed` destacada é má sinalização.
- **Não espere o release para começar o CHANGELOG.** Adicione em `[Unreleased]` conforme desenvolve — na hora do cut, quase não há trabalho.

## Expected Behavior

Quando a skill roda, o entregável é **uma ou mais entries adicionadas ao CHANGELOG** (na seção certa, com link para issue/PR) e, se for release cut, um commit dedicado de release com bump de versão coerente com as mudanças.

Output típico: diff do `CHANGELOG.md` + recomendação clara de bump (`patch`, `minor`, `major`), com justificativa em uma linha. Se houver breaking, a skill também escreve a nota de migração.

A skill **não publica** — ela prepara o contrato. Tag, push, publish no npm/PyPI/registry ficam com o operador humano.

## Quality Gates

- [ ] `CHANGELOG.md` tem seção `[Unreleased]` viva no topo?
- [ ] Toda entry está em um dos buckets canônicos (Added/Changed/Fixed/Removed/Deprecated/Security)?
- [ ] Cada entry relevante tem link `(#N)` para commit/PR/issue?
- [ ] Versões estão em ordem decrescente, com data ISO?
- [ ] Breaking changes destacados e com nota de migração?
- [ ] Bump de versão é coerente com o conteúdo (patch para fix-only, minor para added, major para removed/breaking)?
- [ ] Commit de release é atômico (só `CHANGELOG.md` + arquivo de versão, nada mais)?

## Companion Integration

- **Complementa `sweng-commits-atomicos-semanticos`:** commits contam o "como" granular; CHANGELOG conta o "o quê" agregado por release. Uma mudança grande vira N commits atômicos + 1 entry no CHANGELOG.
- **Complementa `sweng-readme-non-rotting`:** o README aponta para o CHANGELOG quando o leitor quer histórico. Link no README → CHANGELOG é padrão.
- **Delegação para `matilha-hunt`/`matilha-gather`:** quando matilha está coordenando waves, o gather de uma wave produz o CHANGELOG entry da versão que aquela wave ship.
- **Se `matilha-den` presente:** entries grandes de CHANGELOG podem virar capítulos em `docs/den/` com detalhes técnicos.

## Output Artifacts

- `CHANGELOG.md` atualizado (diff esperado: uma ou mais linhas adicionadas em `[Unreleased]`, ou renomeação para versão datada).
- Commit de release separado (quando aplicável): `chore(release): vX.Y.Z` tocando `CHANGELOG.md` + arquivo de versão.
- Sugestão de tag: `vX.Y.Z` (a criar após o commit).

## Example Constraint Language

Quando você está orientando um agente a registrar mudanças, use frases como:

- "Antes de cortar o release, adiciona uma entry no `[Unreleased]` do CHANGELOG em `### Changed` linkando #142."
- "Essa mudança remove o endpoint `/v1/legacy` — isso é breaking. Bump é major; adiciona entry em `### Removed` com nota de migração explicando como migrar para `/v2/items`."
- "Não mexe no CHANGELOG neste commit — ele é refactor interno. Só os commits que afetam usuário entram lá."

## Troubleshooting

- **"Esqueci de adicionar entries durante o desenvolvimento, e agora tem 40 commits para ler."** Na hora do cut, passe pelos commits em ordem e destile para entries. Leva 15min, melhor que pular. Próxima vez, adicione conforme desenvolve.
- **"Não sei se é minor ou major."** Pergunta de filtro: alguém que atualiza precisa mudar código ou config? Se sim, major. Se não, minor.
- **"Duas entries parecem redundantes."** Consolide em uma; CHANGELOG não é commit log.
- **"Breaking change pequeno, mas preciso lançar."** Pode. Só marque como breaking, explique a migração, e bumpe major (ou minor em estágio `0.x`). O tamanho da quebra não muda a classificação — quebra é quebra.
- **"Projeto greenfield, ainda não publicado."** Comece em `0.1.0`, CHANGELOG com `[Unreleased]` desde o commit 1. Barato agora, caro depois.

## CLI shortcut (optional)

Se `matilha` CLI disponível:
```bash
matilha changelog add --type added --pr 142 "Adiciona flag --dry-run ao deploy"
matilha changelog release --bump minor   # renomeia [Unreleased] → [0.5.0] e cria [Unreleased] vazio
```

Equivalente manual (sempre funciona, não requer CLI):
```bash
$EDITOR CHANGELOG.md
# edita [Unreleased], commit separado para o release
git add CHANGELOG.md package.json
git commit -m "chore(release): v0.5.0"
git tag v0.5.0
```

## Sources

- [[rules/Documentação Completa do Projeto]] — seção "## Diretrizes para CHANGELOG.md"
- Convenção externa referenciada: [Keep a Changelog](https://keepachangelog.com/), [SemVer](https://semver.org/) (a regra acima preserva a semântica de ambas, adaptada à voz do projeto).
