---
layout: post
title: "Agentes de IA ou processos melhores? A pergunta que ninguém quer responder"
date: 2026-04-11 12:00:00 +0000
categories: ia agentes
tags: [agentes, automacao, processos, llm, roi]
excerpt: "Implementar agentes de IA em processos ruins é a forma mais eficiente de escalar o caos. Mas quando isso é verdade, e quando é só desculpa para adiar o que precisa ser feito?"
---

Em fevereiro de 2024, a Klarna publicou um dos press releases mais citados da história recente de IA. Seu assistente virtual [gerenciava dois terços das conversas de suporte ao cliente](https://www.klarna.com/international/press/klarna-ai-assistant-handles-two-thirds-of-customer-service-chats-in-its-first-month/) logo no primeiro mês: 2,3 milhões de interações, equivalente ao trabalho de 700 funcionários. O tempo médio de resolução caiu de 11 minutos para menos de 2. A projeção era de $40 milhões em lucro adicional em 2024.

Pouco mais de um ano depois, o CEO Sebastian Siemiatkowski [admitiu publicamente que foi longe demais](https://www.entrepreneur.com/business-news/klarna-ceo-reverses-course-by-hiring-more-humans-not-ai/491396). A satisfação dos clientes tinha caído. A qualidade do atendimento piorou. A empresa voltou a contratar humanos.

O que aconteceu entre esses dois momentos é o problema mais honesto de 2025 para qualquer time pensando em agentes de IA.

---

## O argumento para agir agora

Quem defende implementar agentes sem esperar tem argumentos sólidos.

O custo dos LLMs caiu mais de 90% em dois anos. A capacidade técnica cresce rápido: segundo [análise de Simon Willison sobre 2025](https://simonwillison.net/2025/Dec/31/the-year-in-llms/), a duração das tarefas que LLMs completam de forma autônoma dobra a cada 7 meses, chegando a janelas de até 5 horas em tarefas estruturadas. Esperar o momento certo pode significar chegar tarde.

Há também um argumento pragmático: processos nunca estão prontos. Toda organização tem fluxos ineficientes, decisões não documentadas, exceções que viram regra informal. Se a condição para usar IA for processo perfeito, a condição nunca vai ser satisfeita.

E existem casos reais de sucesso que vão além do marketing. O primeiro capítulo da Klarna é um deles. Outros aparecem em aplicações mais delimitadas: triagem de documentos, geração de rascunhos, pesquisa estruturada, monitoramento de logs. Onde o escopo é claro e o erro tem custo baixo, agentes entregam valor imediato.

---

## O argumento para consertar primeiro

Mark Graban, referência em lean manufacturing, resumiu o problema central em [um post direto sobre IA e processos ruins](https://www.leanblog.org/2026/04/ai-automate-bad-process-performance-reviews/): "faster waste is still waste".

A frase vem do trabalho de Deming, mas poderia ter sido escrita ontem. Automatizar um processo disfuncional não o melhora. Torna o problema mais rápido, mais barato por unidade e muito mais difícil de perceber.

No contexto de LLMs, isso se traduz em algo concreto: agentes dependem de objetivos claros, dados confiáveis e critérios de sucesso verificáveis. Se o processo humano opera na base da intuição e do "depende do caso", o agente vai herdar exatamente essa ambiguidade. Ele vai executar com confiança o caminho errado.

Um dado de 1.200 deployments de LLM em produção coletados pelo [ZenML em 2025](https://www.zenml.io/blog/what-1200-production-deployments-reveal-about-llmops-in-2025) ilustra o problema: os custos de um cliente explodiram de $127 por semana para $47.000 em quatro semanas, causados por loops infinitos entre agentes. O sistema da Zalando registrou cerca de 10% de taxa de erro mesmo utilizando Claude Sonnet. Em ambos os casos, o problema estava na arquitetura e no design do processo, não no modelo em si.

---

## O que os dados dizem

O [relatório State of AI do McKinsey de 2025](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai) entrevistou centenas de empresas e encontrou um padrão consistente: 55% das empresas com alto desempenho em IA redesenham processos end-to-end antes de implantar modelos. Isso é quase três vezes a taxa das empresas com resultados mediocres.

O dado mais revelador: 80% das organizações simplesmente colocam IA em cima dos processos existentes, sem repensar o fluxo. Apenas 21% redesenharam algum fluxo de trabalho antes da implementação. O resultado, segundo o McKinsey, é impacto isolado e fragmentado, longe de transformar resultados de negócio.

O Gartner vai além. Uma previsão publicada em 2025 indica que [mais de 40% dos projetos de IA agêntica serão cancelados até o fim de 2027](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027). As razões apontadas: custos escalantes, valor de negócio pouco claro e controles de risco inadequados. Três falhas que têm muito mais a ver com fundação do que com tecnologia.

---

## Três perguntas antes de decidir

O debate entre "agentes agora" e "processos primeiro" é frequentemente mal formulado. A decisão correta depende do processo específico, e algumas perguntas ajudam a calibrar isso:

**1. Você consegue descrever o processo em etapas verificáveis?**

Se a resposta for "depende do caso" ou "a pessoa experiente sabe o que fazer", o agente vai ampliar essa ambiguidade. Processos executados na intuição são os mais difíceis de agentificar com confiabilidade. Antes do agente, precisa existir uma especificação que um humano novo também conseguiria seguir.

**2. Você tem como detectar quando o agente erra?**

Um dos maiores riscos em sistemas autônomos é o que Willison chama de normalização do desvio: erros pequenos se acumulam sem que ninguém perceba, até que o impacto é grande. Sem métricas claras de qualidade de saída, não há como distinguir um agente funcionando de um agente falhando silenciosamente.

**3. O gargalo é de execução ou de definição?**

Se o processo é lento porque as pessoas são lentas em tarefas repetitivas, agentes ajudam. Se é lento porque os dados estão fragmentados, as regras são contraditórias ou as decisões mudam conforme o contexto, agentes herdam esse problema e o amplificam. A velocidade vai na direção errada mais rápido.

Se as três respostas forem positivas, agentes fazem sentido agora. Se uma ou mais forem negativas, o investimento mais inteligente vai para o processo.

---

## O segundo capítulo da Klarna

A Klarna não falhou por implementar IA. O primeiro capítulo foi tecnicamente impressionante.

O problema estava na premissa implícita de que velocidade de resolução era o mesmo que qualidade de atendimento. O agente otimizou a métrica errada. E otimizou bem, o que tornou o erro mais difícil de ver até ele acumular.

Esse é o padrão que aparece nos dados do McKinsey, do Gartner e do ZenML: a falha de agentes em produção raramente é técnica. Ela vem de objetivos mal definidos, processos que não estavam prontos para operar sem supervisão constante, e ausência de instrumentação para detectar quando as coisas começam a sair dos trilhos.

Implementar agentes onde o processo já funciona bem o suficiente para você saber quando o agente está errando: essa é a condição que os casos de sucesso têm em comum. Não é um processo perfeito. É um processo com critérios de sucesso verificáveis, dados razoáveis e tolerância a erro compatível com o que a automação vai produzir.

A pergunta para o seu time não é "agentes ou processos". É: no processo que você quer automatizar, você saberia quando o agente errou?

Se a resposta for não, comece pelo processo.

---

*Se esse tema é relevante para o que você está construindo, o [próximo post da série vai entrar em context engineering](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/): como projetar o contexto que agentes precisam para tomar decisões confiáveis.*
