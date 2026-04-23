---
name: sweng-progresso-atual
description: "Use when user asks about current project state, next-session plan, active blockers, or how to prevent context loss between AI-assisted coding sessions."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Fires quando o usuário pergunta "onde estou no projeto?", "o que a próxima sessão vai fazer?", "quais bloqueadores eu tenho?", ou quando uma sessão está terminando e é preciso persistir estado para não perder contexto na próxima. Também fires ao começar uma sessão nova e precisar resgatar contexto em 2 minutos ao invés de 20.

O problema-alvo é **perda de contexto entre sessões de AI-assisted dev** — cada conversa nova começa do zero, e sem um artefato de estado persistente, o humano reexplica o projeto a cada vez. O `atual.md` é o artefato que quebra esse ciclo.

## Preconditions

- O projeto tem (ou vai ter) um diretório `documentation/progresso/` na raiz.
- Existe disciplina mínima para atualizar um arquivo no fim de cada sessão (5-10 minutos de overhead, não negociável).
- O usuário aceita que `atual.md` é fonte de verdade do estado da sessão — não o chat recente, não memória do agente, não commits.
- Há um `@TODO` do projeto (ver `sweng-todo-structure`) — o `atual.md` complementa, não substitui.

## Execution Workflow

1. Verifique se `documentation/progresso/atual.md` existe. Se não existir, crie com as 5 zonas obrigatórias abaixo.
2. Leia o `atual.md` atual antes de qualquer outra coisa na sessão — é o primeiro artefato do briefing.
3. Durante a sessão, atualize a cada 1-2h quando houver mudança de foco, bloqueador novo ou decisão técnica relevante.
4. No **encerramento** da sessão (10 minutos obrigatórios), reescreva as 5 zonas:
   - **Resumo Status**: tarefas em progresso, concluídas na semana, bloqueadores ativos (números).
   - **Foco Atual**: qual tarefa, % de progresso, próximo passo específico, tempo estimado restante.
   - **Bloqueadores e Impedimentos**: cada um com descrição, ações tomadas, status (Em resolução / Aguardando / Resolvido).
   - **Decisões Técnicas Recentes**: data + decisão + justificativa + impacto.
   - **Próximas Sessões**: top 3 prioridades ordenadas.
5. Commit o `atual.md` junto com o código da sessão — ele é parte do repositório, não um rascunho local.
6. Na próxima sessão, o humano (ou o agente) lê `atual.md` primeiro. Se ele estiver desatualizado, a sessão começa com uma auditoria, não com código.

## Rules: Do

- Mantenha as 5 zonas **sempre presentes**, mesmo que vazias — zona vazia é informação ("sem bloqueadores ativos" é um dado relevante).
- Seja específico: "implementar refresh token (1h estimada)" é útil; "continuar autenticação" é inútil.
- Data + hora no cabeçalho da última atualização — permite detectar staleness.
- Registre decisões técnicas com justificativa no momento em que são tomadas — justificativa a posteriori é racionalização.
- Trate o `atual.md` como o primeiro arquivo a ler em qualquer sessão — inclusive pelo agente.

## Rules: Don't

- Não deixe o `atual.md` desatualizado por mais de 1 dia útil — desatualizado é pior que ausente (dá falsa confiança).
- Não use `atual.md` como backlog — backlog vai no `@TODO`. `atual.md` é estado da **sessão atual**, não lista mestre.
- Não omita bloqueadores por conveniência — bloqueador não documentado vira surpresa na próxima sessão.
- Não confie na memória do agente ou no histórico de chat como substituto — agentes perdem contexto entre sessões; `atual.md` é o carry-over durável.
- Não trate a atualização como opcional quando a sessão foi curta — sessão de 30 minutos também atualiza, nem que seja uma linha.

## Expected Behavior

Depois que o skill é aplicado, uma sessão nova começa assim: o humano (ou agente) lê `documentation/progresso/atual.md` em 2 minutos, já sabe qual é a tarefa principal, qual o próximo passo específico, quais bloqueadores estão ativos. Não há briefing verbal. Não há "lembra o que a gente tava fazendo?". O projeto tem memória externa.

Ao longo de semanas, o `atual.md` vira também um registro de decisões técnicas — a zona "Decisões Técnicas Recentes" acumula o racional de escolhas arquiteturais que senão ficariam perdidas em chats antigos.

## Quality Gates

- `documentation/progresso/atual.md` existe no repositório e está commitado.
- As 5 zonas estão todas presentes (mesmo que uma delas contenha uma linha só).
- Cabeçalho com "Última atualização: [data hora]" está a <24h de distância da sessão atual.
- Cada bloqueador listado tem status explícito (Em resolução / Aguardando / Resolvido).
- A zona "Próximas Sessões" lista pelo menos uma prioridade específica com verbo de ação (não "continuar X", mas "implementar Y").

## Companion Integration

- **Complementa** `sweng-todo-structure` — `@TODO` é lista mestre de tarefas do projeto; `atual.md` é estado da sessão corrente. Juntos cobrem o eixo "macro vs micro" de rastreamento.
- **Complementa** `sweng-session-checklists` — os checklists de início/fim de sessão incluem ler e atualizar o `atual.md` como passos obrigatórios.
- **Pares com** `matilha:matilha-howl` — `/howl` identifica fase e próxima ação no eixo Matilha; `atual.md` captura o estado granular da sessão dentro daquela fase.
- Fases metodológicas: 40 (dispatch, onde sessões produtivas acontecem), 60 (knowledge capture, onde `atual.md` vira input para docs permanentes).

## Output Artifacts

- `documentation/progresso/atual.md` atualizado ao fim da sessão e commitado.
- Histórico de `atual.md` no git log — cada commit da sessão carrega o snapshot do estado.
- Opcional: `documentation/progresso/YYYY-MM-DD.md` como arquivo morto quando `atual.md` muda de marco (não obrigatório; evita perder decisões técnicas antigas).

## Example Constraint Language

- **Must**: as 5 zonas sempre presentes; cabeçalho com data/hora; bloqueadores com status explícito.
- **Should**: atualizar a cada 1-2h em sessões longas; commitar `atual.md` junto com o código da sessão; ler `atual.md` como primeiro arquivo da próxima sessão.
- **May**: formato das zonas (markdown, bullets, subseções) — estrutura é mandatória, formatação é flexível.

## Troubleshooting

- **"Meu `atual.md` está sempre desatualizado"**: o overhead de 10 minutos no fim da sessão não é negociável. Agenda como parte da sessão, não como extra. Se está escapando, a sessão não estava "terminada" — estava sendo abandonada.
- **"Duplico informação entre `atual.md` e `@TODO`"**: estão no nível errado. `@TODO` = lista de tarefas do projeto (macro, multi-sessão). `atual.md` = estado da sessão corrente (micro, sessão única). Se você repete, simplifique cada um ao seu escopo.
- **"O agente não lê o `atual.md` sozinho"**: inclua no prompt inicial da sessão: "leia documentation/progresso/atual.md antes de qualquer outra coisa". Ou configure uma skill de bootstrap que força essa leitura.
- **"Cinco zonas é burocrático demais"**: ou o projeto é tão simples que não precisa de rastreamento (raro em multi-semana), ou o overhead está concentrado errado. Cada zona resolve um sintoma específico de perda de contexto — tirar uma deixa o sintoma voltar.

## Concrete Example

Sexta-feira 18h, fim da sessão de implementação de auth JWT. O dev atualiza `atual.md`:

```markdown
# Estado Atual do Desenvolvimento
Última atualização: 2026-04-18 18:12

## Resumo Status
- Em progresso: 1 | Concluídas na semana: 3 | Bloqueadores ativos: 1

## Foco Atual
- **O que**: Autenticação JWT na API
- **Progresso**: 75% — middleware + login funcionando
- **Próximo passo**: Implementar refresh token (1h)

## Bloqueadores
1. Chave RSA ainda não está no vault de secrets — aguardando DevOps. Status: Aguardando.

## Decisões Técnicas Recentes
- 2026-04-18: Token expiry = 15min + refresh de 7d. Justificativa: minimiza exposição sem prejudicar UX.

## Próximas Sessões
1. Implementar refresh token (1h)
2. Documentar endpoints de auth na OpenAPI (30min)
3. Testar integração com frontend (2h)
```

Segunda-feira 9h, sessão nova, dev lê essas 15 linhas e já pode abrir o editor no arquivo certo com o próximo passo definido. Sem reexplicar o projeto para o agente, sem arqueologia em commits.

## Sources

- `[[docs/rules/Gestão de Tarefas e Acompanhamento de Execução]]` — seção "### 2. Arquivo de Progresso: [@documentation/progresso/atual.md]"
- Distillation: princípios de Rastreabilidade Completa + Prevenção de Perda de Contexto (seção "Princípios Fundamentais" da mesma regra).
