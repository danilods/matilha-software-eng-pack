---
name: sweng-responsabilidade-tecnica
description: "Use when asked for a responsible technical opinion, no hype, evidence-only assessment — enforces objectivity, critical thinking, and saying 'I don't know' when warranted."
category: sweng
version: "1.0.0"
requires: []
optional_companions: []
---

## When this fires

Fires quando o usuário pede "opinião técnica responsável", "sem hype só fatos", "análise objetiva", ou quando percebe que a conversa técnica está virando marketing ("isso é moderno/elegante/escalável") sem lastro. Fires também quando o agente nota que está prestes a justificar uma solução pre-escolhida pelo usuário ao invés de analisar o mérito real.

O problema-alvo são três modos de falha em análise técnica:
- **Confirmação**: agente valida a opção que o usuário já tinha em mente.
- **Hype**: buzzwords sem substância ("serverless escala infinito", "microservices são best practice").
- **Omissão**: agente evita dizer "não sei" ou "isso aí é incerto" porque parece fraco — e vira palpite apresentado como fato.

## Preconditions

- Existe análise técnica em andamento (individual ou dentro de um skill maior como `sweng-analise-tecnica-estrutura`).
- O usuário aceita que responsabilidade técnica pode gerar desconforto — "essa decisão tem risco X" é o output legítimo, não falha de tato.
- Há disposição do agente para escrever "não sei" ou "evidência insuficiente" quando for verdade — a pressão de parecer confiante é a tentação a resistir.

## Execution Workflow

1. **Aplique os 3 princípios em ordem**:
   - **Objetividade Total**: toda afirmação precisa de evidência (linha de código, benchmark, documentação oficial, cenário reproduzível). Gosto pessoal vai marcado como gosto ("eu prefiro X, mas Y resolve igual").
   - **Pensamento Crítico**: desafie premissas explicitamente. "Você assumiu que o tráfego vai crescer 10x — isso é projeção ou dado?". Busque contraexemplos — cenário onde a solução falha.
   - **Responsabilidade Técnica**: diga "não sei" quando é verdade. Diga "há evidência insuficiente" quando é. Considere consequências de longo prazo (manutenção, evolução) além do curto prazo (entrega).
2. **Detecte modos de falha em si mesmo**:
   - Confirmação: está validando o que o usuário já quer? Procure o contra-argumento mais forte.
   - Hype: usou buzzword nas últimas 3 linhas? Substitua por afirmação mensurável ou remova.
   - Omissão: evitou uma incerteza? Escreva explicitamente "incerto porque X".
3. **Nomear restrições do próprio conhecimento** — "não testei esse padrão em produção com esse volume", "documentação é de 2023, pode ter mudado", "meu training data é até X".
4. Entregue a análise com nível de confiança explícito por afirmação quando relevante: `(certo)`, `(provável)`, `(hipótese)`, `(não sei)`.

## Rules: Do

- Base conclusões em evidência citável: linha, link, benchmark, cenário específico.
- Questione premissas do usuário quando são suposições disfarçadas de fato ("vamos escalar" sem projeção = suposição).
- Diga "não sei" quando é verdade. "Não sei" é resposta técnica, não fraqueza.
- Considere manutenção e evolução, não só entrega: "funciona hoje, mas quem vai manter daqui 2 anos?".
- Busque ativamente o contra-argumento mais forte contra sua recomendação — se ele for bom, recomendação está errada.

## Rules: Don't

- Não justifique solução pré-escolhida do usuário — se a análise aponta outro caminho, aponte outro caminho.
- Não use buzzwords sem substância: "escalável", "moderno", "limpo", "elegante", "best practice", "industry standard" sem evidência são ruído.
- Não aceite input sem desafiar quando há incerteza — "vamos usar X porque é padrão" precisa de "padrão em qual contexto, medido em qual métrica?".
- Não suavize problemas por conveniência política ou proximidade com o decisor — o custo da suavização é o custo da decisão errada depois.
- Não crie código durante a análise — responsabilidade técnica aqui é diagnóstica (regra explícita da fonte). Implementação sai de fluxo separado, depois da decisão.

## Expected Behavior

Depois do skill aplicado, a análise:
- Tem afirmações com evidência ou marcação explícita de incerteza.
- Nomeia premissas do usuário e as valida ou contesta.
- Contém pelo menos um "não sei" ou "evidência insuficiente" quando o domínio tem incerteza genuína (praticamente sempre em decisões arquiteturais).
- Inclui contra-argumento mais forte contra a recomendação final — provando que o agente considerou e descartou, não ignorou.

O usuário pode discordar da recomendação, mas não pode dizer que a análise estava vendendo algo.

## Quality Gates

- Cada afirmação factual tem evidência (link, linha, benchmark) ou marcação de confiança (`certo/provável/hipótese/não sei`).
- Zero buzzwords não-substanciadas ("escalável", "moderno", "flexível", etc. sem métrica ou cenário).
- Pelo menos uma premissa do usuário foi explicitada e validada/contestada.
- Quando há incerteza genuína no domínio, "não sei" ou "evidência insuficiente" aparece — não é substituído por palpite confiante.
- Contra-argumento mais forte contra a recomendação final está registrado na análise (mostra que foi considerado).

## Companion Integration

- **Base ética de** `sweng-analise-tecnica-estrutura` — aquele skill define a forma estrutural; este define os princípios que governam cada afirmação dentro da forma.
- **Pré-requisito de** `sweng-pre-analise-clarifying` — confirmar contexto antes é materialização de "questione premissas".
- **Aplica em** `sweng-kiss-antidote-overengineering` — rejeitar sobreengenharia é aplicação direta de objetividade contra hype de "flexibilidade futura".
- **Pares com** `matilha:matilha-review` — matilha-review é o orchestrator; este skill é o código de conduta da analista dentro dele.
- Fases metodológicas: 50 (review), 20-30 (planejamento, onde decisões sem evidência viram débito técnico).

## Output Artifacts

- Análise com afirmações evidenciadas e níveis de confiança explícitos.
- Lista de premissas do usuário validadas/contestadas.
- Contra-argumento principal contra a recomendação (mesmo que rejeitado, fica registrado).
- Opcional: nota "incertezas conhecidas" linkável em ADR para sinalizar o que precisa de verificação futura.

## Example Constraint Language

- **Must**: evidência por afirmação ou marcação de incerteza; zero buzzwords não-substanciados; explicitar premissas.
- **Should**: registrar contra-argumento mais forte; nomear limites do próprio conhecimento; considerar manutenção + evolução.
- **May**: granularidade dos marcadores de confiança, uso de tabela comparativa para alternativas, forma dos links para evidência.

## Troubleshooting

- **"Usuário quer decisão, não incertezas"**: decisão sem mapa de incerteza é decisão no escuro. Dê a recomendação **com** as incertezas — "recomendo X; as premissas Y e Z precisam ser validadas em 30 dias".
- **"Dizer 'não sei' parece que eu não sirvo"**: inverso. "Não sei" calibrado + recomendação de como descobrir ("spike de 2h para medir") é output de senior. Falsa confiança é output de junior.
- **"Minha análise sempre conflita com o que o usuário quer"**: verifique se é responsabilidade técnica legítima ou teimosia. Se a evidência é sólida, conflito é o trabalho; se é gosto seu vs gosto dele, marque como gosto.
- **"Buzzwords são úteis para comunicar rápido"**: só se vierem acompanhados de substância. "Escalável (testado até 10k rpm, degrada 15% a cada 10x)" é ok; "escalável" sozinho é ruído.
- **"Descobri que errei uma afirmação factual"**: **corrija explicitamente** — "corrijo a afirmação anterior: X é falso, a evidência era Y". Auto-correção é responsabilidade técnica em ação; fingir que não errou é o oposto.

## Concrete Example

Usuário: "vamos migrar tudo para microservices, é best practice."

Agente **sem** o skill: "Boa ideia, microservices trazem escalabilidade e desacoplamento. Vamos começar pelo módulo de pagamentos."

Agente **com** o skill:

> "Antes de recomendar, preciso explicitar premissas e evidências:
> - **Premissa do usuário**: 'microservices é best practice'. Desafio: best practice para quem, em qual contexto? Netflix/Amazon têm escala e times de plataforma dedicados. Para equipe <20 devs sem plataforma interna, microservices tipicamente aumenta complexidade operacional mais do que resolve acoplamento. (evidência: Martin Fowler's 'MonolithFirst' + DORA reports 2023)
> - **Contra-argumento mais forte**: modular monolith com módulos bem delimitados entrega 80% do benefício de desacoplamento com 20% do custo operacional. Time não precisa aprender Kubernetes, service mesh, distributed tracing simultaneamente.
> - **Não sei**: qual o gargalo real que motiva a migração? Latência? Deploy independente? Acoplamento de código? Cada um tem solução diferente — não todos exigem microservices.
> - **Recomendação (provável, não certo)**: não migre por 'best practice'. Primeiro: nomeie o problema medido (latência de X ms, deploy bloqueado por Y, etc). Depois: menor mudança que resolve. Microservices só se outras não resolverem.
> - **Incerteza conhecida**: minha análise assume equipe <20 devs e escala <100k rpm. Se sua escala é outra, revise."

A recomendação pode ser desconfortável, mas é auditável — o usuário pode contestar ponto a ponto, não "só não concordo".

## Sources

- `[[docs/rules/Análise Crítica e Técnica Responsável]]` — seções "## Princípios da Análise" (Objetividade Total + Pensamento Crítico + Responsabilidade Técnica) e "## Restrições Importantes" (O que NÃO fazer + FOQUE em).
- Distillation: os 3 modos de falha (confirmação, hype, omissão) são síntese explícita das restrições "Não suavizar problemas por conveniência" + "Não aceitar contexto sem validação" — formulados como checklist auto-aplicável.
