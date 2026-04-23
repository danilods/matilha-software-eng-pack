---
name: sweng-readme-non-rotting
description: "Use when writing or reviewing a README, or asking 'esse README vai sobreviver 6 meses?' — filtra o que DEVE estar (overview, install, usage) e o que NÃO deve (roadmap detalhado, status temporário, decisões em flux)."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Dispara quando alguém (você, um agente, ou um contributor) vai escrever
ou atualizar um README. Sinais típicos:

- "Como escrevo o README deste projeto?"
- "O README ficou desatualizado — rewrite ou update?"
- "Este README vai sobreviver 6 meses sem virar mentira?"
- "Posso colocar o roadmap aqui?"
- "Onde documento as decisões técnicas?"
- README que começa com uma lista de TODOs e termina com "Em
  desenvolvimento".
- README que lista feature toggle interna e screenshot da sprint
  passada.

A regra: **README é conteúdo que não envelhece rápido**. Overview,
instalação, uso básico, arquitetura simplificada, links para doc
detalhada. Status temporário, roadmap granular e decisões em flux vão
pra outro lugar (CHANGELOG, `docs/`, `progresso/atual.md`).

## Preconditions

- Projeto tem um `README.md` (ou vai ter).
- Há pelo menos uma primeira versão rodando ou spec concreta do
  básico — README sem produto é wishlist, não README.
- Existem (ou vão existir) outros artefatos de documentação:
  `CHANGELOG.md`, diretório `docs/`, ou equivalente — o README aponta
  pra eles, não reimplementa.

## Execution Workflow

1. Liste o que você pensou em colocar no README. Separa em duas
   colunas:
   - **Vive bem 6-12 meses**: título, descrição do projeto, instalação,
     uso básico, arquitetura simplificada, links pra doc detalhada.
   - **Apodrece rápido**: roadmap com datas, status temporário
     ("atualmente trabalhando em X"), decisões em discussão, screenshots
     de UI pré-release, lista de contributors, números de issues em
     aberto.
2. O que apodrece rápido **sai do README**. Destinos canônicos:
   - Roadmap detalhado → `docs/progresso/backlog.md` ou GitHub
     Projects.
   - Status atual → `docs/progresso/atual.md` ou PR/issue trackers.
   - Decisões técnicas em discussão → RFCs/ADRs em `docs/decisions/`.
   - Changelog → `CHANGELOG.md` (referenciado no README).
3. Escreve o README com o conteúdo obrigatório:
   - **Título + descrição curta** (1-2 frases — o que é, pra quem).
   - **Instalação/configuração** (copy-pasteable, testado).
   - **Uso básico** (1-2 exemplos mínimos, funcionais).
   - **Arquitetura simplificada** (parágrafo ou diagrama curto).
   - **Links pra doc detalhada**, `CHANGELOG.md`, contribuição.
4. Aplica o teste dos 6 meses: "se eu voltar aqui em 6 meses e não
   tocar em nada, o README ainda está correto?". Se "não", é porque
   meteu conteúdo que apodrece — move ou remove.
5. Aplica o teste do fresco: "alguém que chega hoje no projeto
   consegue rodar o setup em 10 min só com este README?". Se "não",
   falta conteúdo obrigatório — adiciona.
6. Atualiza o README quando: nova feature que muda instalação/uso
   básico; mudança de arquitetura; novo link de doc detalhada. Não
   atualiza pra "registrar progresso" — isso é CHANGELOG ou
   `atual.md`.

## Rules: Do

- Conteúdo obrigatório: título, descrição, instalação, uso básico,
  arquitetura simplificada, links pra doc detalhada.
- Mantém conciso. README é porta de entrada, não manual completo.
- Linka pra documentação detalhada em vez de inliná-la. README
  aponta, `docs/` explica.
- Exemplos de uso básicos **testados** — se copy-paste quebra, README
  mentiu. Ideal: exemplos rodam em CI.
- Atualiza a cada nova funcionalidade que **muda instalação ou uso
  básico**. Feature interna sem impacto externo não mexe no README.
- Separa claramente o que existe do que está planejado. Se
  mencionar futuro, deixa explícito ("planejado, não implementado").

## Rules: Don't

- Não põe roadmap detalhado com datas. Datas envelhecem em 2 semanas;
  README vira mentira; ninguém confia mais.
- Não coloca status temporário ("em desenvolvimento", "atualmente
  trabalhando em X"). Esse tipo de informação vai pra
  `docs/progresso/atual.md` ou tracker.
- Não lista decisões técnicas em flux. Decisões vão pra ADRs em
  `docs/decisions/`. README menciona as estáveis, no máximo.
- Não cola screenshot de UI que muda em todo sprint. Se colocar, usa
  imagem versionada e sabe que vai precisar atualizar.
- Não duplica conteúdo do CHANGELOG. README linka; CHANGELOG lista.
- Não documenta "alucinação" (feature que não existe ainda). Se
  mencionar feature planejada, marca explicitamente como planejada.
- Não deixa exemplo que não roda. Exemplo quebrado é pior que exemplo
  ausente.

## Expected Behavior

Após aplicar a skill, o README fica estável: atualiza raramente e
cada atualização é justificada por mudança real de API/instalação.
Contribuidores novos conseguem rodar o setup em minutos; o
mantenedor não perde ciclos reescrevendo README toda semana.

Conteúdo volátil migra pra onde faz sentido: `CHANGELOG.md` absorve
o histórico; `docs/progresso/atual.md` absorve o estado de
desenvolvimento; `docs/decisions/` absorve ADRs; trackers externos
absorvem roadmap. Cada pedaço vive onde o ciclo de vida dele
combina.

## Quality Gates

- README tem título, descrição (1-2 frases), instalação, uso básico,
  arquitetura simplificada, links pra doc detalhada.
- Nenhum item com data específica (prazos, sprints, versões com
  "previsto pra").
- Nenhum status temporário ("em desenvolvimento", "TODO", "WIP") no
  corpo principal — se necessário, parágrafo curto marcado como
  "Status" no final, linkando pra `atual.md`.
- Exemplo de uso básico é copy-pasteable e testado (pode ser
  validado em CI com doctest/pytest-examples).
- Features planejadas marcadas explicitamente como planejadas,
  nunca misturadas com features existentes.
- Links internos (`docs/`, `CHANGELOG.md`) existem — nenhum 404.

## Companion Integration

Pareia com `sweng-nomenclatura-clareza` — README que não envelhece
depende de código com nomes auto-documentados. Se você precisa
explicar cada função no README, ou os nomes do código estão ruins
ou o README está com o escopo errado.

Complementa `matilha-harness-pack:harness-docs-as-system-of-record` e
`matilha-harness-pack:harness-agents-md-as-index` — harness-pack
estabelece docs como fonte de verdade e índice navegável por
agentes; `sweng-readme-non-rotting` especifica o subset do README
que deve ficar estável para que o system of record tenha um ponto
de entrada que não mente.

Em phase 60 (captura de conhecimento) roda junto com `matilha:matilha-den`;
em phase 70 (artefatos reutilizáveis) pareia com `matilha:matilha-pack`
que escreve READMEs de packs com este mesmo filtro.

## Output Artifacts

- `README.md` com estrutura canônica (título, descrição, install,
  usage, arquitetura, links).
- `CHANGELOG.md` referenciado e atualizado separadamente.
- `docs/progresso/atual.md` (ou equivalente) onde o status temporário
  de fato vive.
- `docs/decisions/` para ADRs, se o projeto tem decisões que valem
  registrar.
- Opcional: doctest em CI garantindo que o "uso básico" não apodreça.

## Example Constraint Language

- Use "must" para: incluir título, descrição, instalação, uso básico
  e arquitetura simplificada; testar o exemplo de uso básico;
  marcar features planejadas como planejadas.
- Use "should" para: linkar ao invés de inlinar documentação
  detalhada; atualizar README a cada mudança de instalação/uso
  básico; referenciar `CHANGELOG.md` no topo ou na seção final.
- Use "may" para: incluir badges (CI, versão, licença) no topo;
  adicionar seção "Troubleshooting" ou "FAQ" curta; incluir seção
  "Contributing" com link pra guia separado.

## Troubleshooting

- **"Quero registrar o progresso do sprint no README"**: não vai pro
  README. Cria `docs/progresso/atual.md` e referencia no README com
  um link. O README diz "o que é"; `atual.md` diz "onde está hoje".
- **"Temos um roadmap ambicioso, quero mostrar no README"**: linka
  pro roadmap em `docs/` ou GitHub Projects. README menciona escopo
  atual ("o que já faz"), não promete futuro granular.
- **"O exemplo quebrou depois de 3 meses"**: coloca o exemplo em CI
  (doctest, pytest-examples, ou script separado testado). Se o
  exemplo é testado, ele envelhece com o código.
- **"README ficou enorme"**: sintoma de inlinar doc detalhada. Extrai
  pra `docs/` e deixa link. README é porta de entrada, não manual.
- **"O agente escreveu um README com screenshots e roadmap"**:
  esperado. Agentes copiam padrão de repos populares que frequentemente
  têm README que apodrece. Você filtra antes de commitar.
- **"Tenho várias decisões técnicas importantes"**: cria ADRs em
  `docs/decisions/` (1 arquivo por decisão). README só linka pro
  índice de ADRs.

## Concrete Example

README anti-pattern (apodrece em semanas):

    # MyApp
    MyApp is a cool new tool we're building!

    ### Status
    Currently in active development. Working on feature X this sprint.
    Planning to ship v0.5 by end of May 2026.

    ### Roadmap
    - [ ] Feature A (planned for April 2026)
    - [ ] Feature B (planned for May 2026)
    - [x] Feature Z (done!)

    ### Latest updates
    2026-03-15: Fixed auth bug
    2026-03-10: Added new dashboard

Problemas: roadmap com datas, status temporário no corpo, changelog
inline. Em 3 meses, 80% do README vai estar errado.

README que não envelhece:

    # MyApp
    Task queue server for distributed workers with retry and backpressure.

    ### Install
        pip install myapp

    ### Usage
        from myapp import Queue
        q = Queue(url="...")
        q.enqueue(my_task, args=(...))

    ### Architecture
    Producer clients enqueue tasks over HTTP; workers poll a Redis-backed
    queue; retries use exponential backoff. See docs/architecture.md.

    ### More
    - CHANGELOG.md — release history
    - docs/ — detailed documentation
    - docs/decisions/ — architecture decision records
    - docs/progresso/atual.md — current development status

Esse README sobrevive meses sem atualização. Quando `pip install` mudar,
a linha muda. Quando a arquitetura mudar de fato, o parágrafo muda.
Roadmap, status, changelog vivem nos seus arquivos próprios.

## Sources

- [[docs/rules/Documentação Completa do Projeto]]
- Destilado das seções "Diretrizes para README.md", "Processo de
  Atualização" e "Prevenção de Perda de Contexto" do rule file de
  Danilo. Preserva o conteúdo obrigatório canônico (título,
  instalação, uso, arquitetura, links) e o princípio "separar
  claramente o que existe do que está planejado" — que é exatamente
  o que faz um README não envelhecer.
