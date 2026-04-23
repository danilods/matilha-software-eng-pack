---
name: sweng-analise-tecnica-estrutura
description: "Use when asked to analyze code, architecture, or a technical decision — enforces 5-section analysis structure (context, positives, problems, KISS, recommendations) with evidence per claim."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Fires quando o usuário pede uma análise técnica: "analisa esse código", "revisa essa decisão arquitetural", "o que você acha dessa abordagem?", "code review estrutural dessa PR grande". Fires também quando alguém apresenta uma proposta de stack, ferramenta ou padrão e precisa de avaliação objetiva.

O problema-alvo é análise **desestruturada**: virar só opinião ("achei legal", "parece ok"), ou virar só crítica ("isso tá horrível"), ou virar um parágrafo corrido sem separar benefícios, problemas e recomendações. O skill impõe a estrutura de 5 seções que força evidência por afirmação.

## Preconditions

- Existe código, arquitetura ou decisão técnica concreta para analisar — não uma ideia vaga.
- O usuário aceita que a análise pode revelar problemas que não convenham (ver `sweng-responsabilidade-tecnica` para o princípio de objetividade).
- Contexto mínimo foi confirmado: qual o problema sendo resolvido, quais restrições, quais critérios de sucesso (ver `sweng-pre-analise-clarifying` se contexto está faltando).
- Há acesso ao código/design real, não apenas descrição verbal.

## Execution Workflow

1. **Contexto e Escopo** — abra a análise com 2-3 linhas objetivas: o que está sendo analisado, qual o critério de sucesso assumido, quais premissas foram adotadas.
2. **Aspectos Positivos** — liste benefícios técnicos **mensuráveis** (redução de acoplamento verificável, eliminação de código duplicado, melhoria de performance medida). Evite marketing speak: "moderno", "limpo", "elegante" não são positivos técnicos.
3. **Problemas Identificados** — liste todos os problemas, mesmo os inconvenientes. Cada problema tem: descrição + evidência (linha de código, cenário que falha, métrica) + impacto. Ordene por severidade.
4. **Avaliação KISS** — cobrir 4 perguntas obrigatórias:
   - Simplicidade: é a solução mais simples que atende?
   - Reutilização: já existe algo similar no stack/projeto?
   - Responsabilidade: cada componente tem uma função clara?
   - Alternativas simples: existe abordagem mais direta?
5. **Recomendações** — 3 sub-seções fixas: **Implementar** (o que fazer), **Evitar** (o que NÃO fazer), **Alternativas** (opções mais simples/eficientes se existirem).
6. Finalize com status de 1 linha: `✅ Viável` | `⚠️ Viável com ressalvas` | `❌ Não recomendado` + resumo em uma frase.

## Rules: Do

- Cada afirmação positiva ou negativa tem **evidência explícita**: linha de código citada, cenário específico que falha, métrica que suporta a alegação.
- Mantenha as 5 seções + status final **sempre presentes**, mesmo que alguma fique curta — seção vazia é dado ("sem problemas críticos identificados" é diferente de não ter a seção).
- Ordene problemas por severidade (bloqueador → crítico → sério → cosmético), não por ordem de descoberta.
- Priorize problemas de design sobre estilo — acoplamento forte importa mais que naming inconsistente.
- Use a seção **Alternativas** para sugerir a abordagem mais simples, mesmo que seja "não faça nada".

## Rules: Don't

- Não suavize problemas por conveniência política ou proximidade com o autor — "responsabilidade técnica" inclui dizer coisas desconfortáveis.
- Não misture análise com implementação — este skill é diagnóstico. Código novo sai de outro fluxo.
- Não use buzzwords técnicos sem substância: "escalável", "flexível", "robusto" sem evidência são ruído.
- Não pule a avaliação KISS — mesmo quando a solução parece obviamente boa, as 4 perguntas revelam sobreengenharia invisível.
- Não termine sem status explícito — análise sem veredicto deixa o leitor sem decisão possível.

## Expected Behavior

Depois da análise, o leitor tem:
1. Lista acionável de o que fazer (Implementar) e o que não fazer (Evitar).
2. Evidência concreta para cada alegação — pode contestar ponto a ponto, não apenas "não concordo".
3. Status binário+1 que informa se segue, ajusta ou volta à prancheta.

A análise vira documento citável em PR, RFC ou ADR — não vira "conversa" que se perde no chat.

## Quality Gates

- As 5 seções obrigatórias estão presentes na ordem: Contexto → Positivos → Problemas → KISS → Recomendações.
- Cada item da seção "Problemas Identificados" tem evidência citada (linha, comando, métrica, cenário).
- A seção KISS cobre as 4 perguntas obrigatórias (Simplicidade, Reutilização, Responsabilidade, Alternativas Simples) — não apenas uma delas.
- Recomendações têm as 3 sub-seções: Implementar, Evitar, Alternativas.
- Análise termina com status explícito (`✅` | `⚠️` | `❌`) + resumo em 1 frase.

## Companion Integration

- **Requer** `sweng-pre-analise-clarifying` quando o contexto ainda não foi confirmado — análise sem compreensão é review prematura.
- **Aplica** `sweng-responsabilidade-tecnica` — os 3 princípios (objetividade, pensamento crítico, responsabilidade) são a base ética; este skill é a forma estrutural.
- **Complementa** `sweng-kiss-antidote-overengineering` — a seção KISS da análise é o ponto onde sobreengenharia é nomeada e desafiada.
- **Pares com** `matilha:matilha-review` (planejado, Wave 3c) — matilha-review é o orchestrator de qualidade de fase 50; este skill é a lente estrutural dentro dele.
- Fases metodológicas: 50 (review), 20-30 (plan authoring, onde decisões arquiteturais são tomadas).

## Output Artifacts

- Documento de análise em markdown com as 5 seções + status, commitável em `documentation/reviews/` ou colável em PR.
- Opcional: ADR (Architectural Decision Record) quando a análise resulta em decisão arquitetural — mesma estrutura, formato ADR.

## Example Constraint Language

- **Must**: 5 seções na ordem correta; evidência por afirmação; status final com emoji + frase-resumo; 4 perguntas KISS cobertas.
- **Should**: ordenar problemas por severidade; priorizar design sobre estilo; linkar linha de código específica quando disponível.
- **May**: formato exato dos bullets, uso de sub-listas, inclusão de tabelas comparativas quando útil.

## Troubleshooting

- **"Minha análise virou só crítica"**: você esqueceu a seção Positivos. Mesmo código ruim geralmente tem algum trade-off legítimo — identifique qual, senão a análise perde credibilidade e vira agenda pessoal.
- **"Minha análise virou só elogio"**: você esqueceu a responsabilidade técnica. Se não listou **nenhum** problema, releia a seção KISS — quase sempre há simplificação possível.
- **"Não sei se é viável ou não"**: o status `⚠️ Viável com ressalvas` existe exatamente para isso. Liste as ressalvas na seção Recomendações → Implementar como pré-requisitos.
- **"A análise ficou longa demais"**: 5 seções, cada uma com 3-5 bullets objetivos, cabe em 1-2 páginas. Se está passando, você está escrevendo ensaio — enxugue para evidências + recomendações, corte prosa explicativa.
- **"O autor do código se ofendeu"**: evidências-citadas funcionam como distanciamento — a análise ataca o código, não a pessoa. Se ainda assim há fricção, o problema é cultural, não de análise; ver `sweng-responsabilidade-tecnica`.

## Concrete Example

Análise de proposta "vamos usar cache distribuído Redis Cluster":

```markdown
## Análise Técnica: Cache Distribuído Redis Cluster
**Contexto**: Proposta para melhorar p95 das queries de /api/v1/products de 350ms para <100ms. Load atual 1.2k rpm, crescimento 15%/mês.

### Aspectos Positivos
- Redis Cluster é maduro, operado em produção em empresas comparáveis; decisões operacionais bem documentadas.
- Resolve classe de queries repetidas: 73% do tráfego do endpoint é leitura dos mesmos 500 SKUs.

### Problemas Identificados
1. **Invalidação**: proposta não especifica estratégia de invalidação. Risco de servir estoque desatualizado (bloqueador de negócio).
2. **Complexidade operacional**: Redis Cluster adiciona sharding, failover, monitoring. Equipe de 3 devs, ninguém tem experiência prévia.
3. **Custo**: 3 nós c/ réplicas = +$340/mês. Query atual roda em banco já pago.

### Avaliação KISS
- Simplicidade: solução alta-complexidade; considerou-se cache local (Caffeine/in-memory) primeiro? Não mencionado.
- Reutilização: stack já tem PostgreSQL com suporte a materialized views. Foi avaliado?
- Responsabilidade: adiciona responsabilidade de gestão de estado distribuído ao time — sem dono claro.
- Alternativas Simples: (1) cache local com TTL 60s; (2) materialized view com refresh a cada 5min; (3) otimizar query (índice composto ausente em SKU+warehouse).

### Recomendações
**Implementar**: adicionar índice composto `(sku, warehouse_id)` — teste local reduz p95 para 140ms sem infraestrutura nova.
**Evitar**: introduzir Redis Cluster antes de medir materialized view + índice.
**Alternativas**: se após índice p95 ainda >100ms, cache local com Caffeine (single line de config) é próximo passo; Redis Cluster só se multi-instância virar necessidade.

**Status**: ❌ Não recomendado na forma proposta. Alternativa mais simples (índice composto) resolve o problema medido sem custo operacional.
```

## Sources

- `[[docs/rules/Análise Crítica e Técnica Responsável]]` — seção "### 4. ESTRUTURA DA ANÁLISE" (4.1 Resumo Executivo, 4.2 Pontos Positivos, 4.3 Problemas, 4.4 Análise KISS, 4.5 Recomendações).
- Distillation: a obrigação de evidência por afirmação e a ordenação por severidade são amplificações explícitas do princípio "Fatos sobre opiniões" da seção "## Princípios da Análise".
