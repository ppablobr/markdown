---
layout: post
title: "Por Que Você Deveria Parar de Criar Agentes e Começar a Criar Skills"
date: 2026-04-12 10:00:00 +0000
categories: blog pessoal
tags: [agentes, skills, arquitetura, llm, automação]
excerpt: "A maioria dos agentes que as pessoas constroem são skills mal implementadas: processos isolados, com overhead de coordenação, contexto duplicado e custo desnecessário. A pergunta certa antes de criar um agente novo é: isso precisa ser independente?"
image: https://res.cloudinary.com/dgqhglwo5/image/upload/v1776047006/blog/covers/blog/covers/pare-de-criar-agentes-comece-com-skills.jpg
---

<figure>
  <img src="https://res.cloudinary.com/dgqhglwo5/image/upload/v1776047006/blog/covers/blog/covers/pare-de-criar-agentes-comece-com-skills.jpg"
       alt="Agente central com skills encaixáveis vs cluster de agentes fragmentados"
       loading="lazy"
       style="width:100%;border-radius:4px;">
  <figcaption>Um agente com skills compostas vs vários agentes desconectados: a diferença de custo e clareza arquitetural.</figcaption>
</figure>

Você tem um agente para gerar commits. Um para escrever documentação. Um para publicar posts no blog. Um para revisar PRs. Cada um com seu próprio system prompt, seu próprio conjunto de ferramentas, sua própria lógica de loop.

Parece uma arquitetura robusta. Na prática, você construiu um sistema frágil, caro e difícil de manter, onde cada peça nova que você adiciona aumenta o custo de orquestração sem necessariamente melhorar o resultado.

O problema não é que você criou agentes. É que você criou agentes para coisas que deveriam ser skills.

## O custo real de cada agente novo

Quando você cria um agente, você cria um processo independente com seu próprio loop de execução. Esse agente precisa de contexto para funcionar: quem ele é, o que pode fazer, quais ferramentas tem acesso, quais limites respeitam. Esse contexto tem custo em tokens.

Além disso, quando o agente pai precisa delegar para esse agente, ocorre um handoff. O agente pai precisa serializar o estado relevante, passar para o filho, esperar a resposta, e reintegrar o resultado no seu próprio contexto. Cada handoff é latência e tokens adicionais.

Os dados empíricos confirmam isso. Uma [análise comparativa publicada em 2026](https://iterathon.tech/blog/multi-agent-orchestration-economics-single-vs-multi-2026) documentou um caso onde um sistema multi-agente de atendimento ao cliente custava $47.000 por mês em orquestração, contra $22.700 para um agente único equivalente, com uma diferença de apenas 2,1 pontos percentuais de acurácia, e 4,8 segundos adicionais por consulta por causa da coordenação entre agentes.

Uma [revisão sistemática de configurações multi-agente](https://arxiv.org/html/2512.08296v1) encontrou degradação em relação ao baseline de agente único em todos os 28 cenários testados, variando de -4,4% a -35,3%. O overhead de coordenação escala linearmente com o volume, mas os benefícios platô muito antes.

A proliferação de agentes tem custo real. E parte desse custo é invisível até você tentar escalar.

## O que diferencia uma skill de um agente

Um agente é um processo com seu próprio loop de execução: ele percebe o ambiente, decide uma ação, executa, observa o resultado, e repete. Ele opera de forma independente. Tem memória própria. Pode ser pausado e retomado. É o modelo certo para tarefas longas, paralelas, ou que precisam de isolamento por questões de segurança ou confiabilidade.

Uma skill é diferente. Ela é executada no contexto do agente pai. Não tem loop próprio. Não precisa de handoff. Ela injeta conhecimento procedural, scripts, e instruções estruturadas diretamente no contexto do agente que a invocou, e o agente usa esse contexto para executar.

O paper [Agent Skills for Large Language Models](https://arxiv.org/html/2602.12430v3) define bem essa distinção:

> "Ferramentas executam e retornam resultados. Skills preparam o agente para resolver um problema, injetando conhecimento procedural, modificando o contexto de execução, e habilitando divulgação progressiva de informação."

Uma skill de commit, por exemplo, injeta no contexto do agente raiz as instruções de como fazer um bom commit: verificar o diff, formatar a mensagem, respeitar convenções do repositório, rodar os hooks. O agente raiz executa isso diretamente, com suas próprias ferramentas, sem precisar inicializar um segundo processo.

O resultado medido pelo [sistema SAGE](https://arxiv.org/html/2602.12430v3): 8,9% de melhora em completion de tarefas, 26% de redução em passos de interação, e 59% menos tokens gerados, em comparação com arquiteturas que usavam agentes separados para as mesmas capacidades.

## Quando usar cada um

**Use skills quando:**

- A capacidade é reutilizável em diferentes contextos do mesmo agente raiz
- O processo não precisa rodar em paralelo com outros processos
- O estado relevante já está no contexto do agente pai
- A tarefa é bem definida e tem escopo limitado

**Use agentes quando:**

- A tarefa vai rodar em paralelo com outras tarefas (paralelismo real)
- Você precisa de isolamento por segurança ou por confiabilidade
- O processo é longo o suficiente para que o contexto do agente pai não aguente todo o estado necessário
- A tarefa precisa de um loop de execução próprio, independente do agente coordenador

A linha é mais simples do que parece: se a capacidade serve um agente existente, é uma skill. Se ela precisa operar de forma independente, é um agente.

## Como skills funcionam na prática

Considere um fluxo de publicação de post em um blog. Uma abordagem baseada em agentes criaria:

1. Um agente pesquisador
2. Um agente escritor
3. Um agente de geração de imagem
4. Um agente de publicação via GitHub

Cada um com seu contexto, suas ferramentas, sua lógica de inicialização. O agente coordenador precisa fazer handoff entre cada um, serializar o estado, reintegrar os resultados.

Uma abordagem baseada em skills cria, em vez disso:

```
Agente raiz (Claude Code, por exemplo)
  └── skill: web-search (injeta instruções de pesquisa + resultados)
  └── skill: ai-blog-editor (injeta instruções de escrita + template de front matter)
  └── skill: imagen-prompt-architect (injeta instruções de geração de imagem)
  └── skill: publish-post (injeta instruções de publicação via GitHub MCP)
```

O agente raiz mantém o contexto completo do fluxo. Quando invoca uma skill, o contexto da skill é carregado, o agente executa os passos necessários com suas ferramentas já disponíveis, e a skill termina. Sem processo filho. Sem handoff. Sem latência de coordenação.

Cada skill é um arquivo com instruções estruturadas, scripts auxiliares quando necessário, e metadados sobre quando deve ser usada. Ela pode ser versionada junto com o código, reutilizada em outros projetos, e composta com outras skills sem conflito.

Simon Willison descreveu bem a diferença de ergonomia: skills são um [primitivo mais simples e de menor overhead do que MCP ou agentes separados](https://simonw.substack.com/p/agentic-engineering-patterns) para a maioria das capacidades que desenvolvedores querem adicionar a seus agentes.

## A biblioteca de skills como arquitetura central

Se você pensar em skills como "superpoderes" do agente raiz, a arquitetura muda de forma.

Você começa a construir uma biblioteca de skills reutilizáveis. Cada skill é uma capacidade bem definida, testada, documentada. Quando você quer que seu agente saiba fazer algo novo, você adiciona uma skill à biblioteca. O agente raiz permanece estável. As capacidades se expandem incrementalmente.

Isso tem implicações práticas para manutenção. Quando uma skill precisa ser atualizada (por exemplo, quando uma API muda, ou quando você aprende uma forma melhor de fazer commit), você atualiza um arquivo. Todos os contextos que usam essa skill passam a usar a versão atualizada automaticamente.

Com agentes separados, você teria que atualizar cada agente individualmente, retestar a integração, e garantir que os handoffs ainda funcionam. O custo de manutenção cresce com cada agente adicionado.

A Anthropic recomenda explicitamente em seu guia [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) que a complexidade deve seguir necessidade comprovada, não precedê-la. A questão não é se você pode criar dez agentes especializados. É se você deve.

## A provocação

A maioria dos agentes que as pessoas criam são skills mal implementadas.

São processos com system prompt próprio, chamadas de API próprias, lógica de loop própria, para executar tarefas que poderiam perfeitamente ser executadas como capacidades injetadas em um agente já existente.

Isso tem custos: mais tokens por tarefa, mais latência por handoff, mais superfície de falha, mais trabalho de manutenção. E, ironicamente, resultados frequentemente piores do que um agente único bem provisionado.

Antes de criar o próximo agente, faça uma pergunta: essa capacidade precisa operar de forma independente, ou ela serve um agente que já existe?

Se ela serve um agente existente, a resposta certa é uma skill. E a diferença entre fazer isso bem ou mal é, em produção, a diferença entre um sistema que você consegue manter e um que você vai eventualmente jogar fora.

Para entender como o contexto disponível para o agente raiz afeta a qualidade das decisões dele, o post sobre [engenharia de contexto para agentes](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/) cobre os mecanismos concretos. E se você quer ver um agente de terminal operando com esse modelo de skills na prática, o post sobre [Claude Code como agente de terminal](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/claude-code-agente-terminal/) mostra o loop de execução em detalhe.
