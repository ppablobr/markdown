---
layout: post
title: "Context Engineering: a evolução além do Prompt Engineering"
date: 2026-04-11 10:00:00 +0000
categories: ia
tags: [context-engineering, prompt-engineering, llm, agentes, boas-praticas]
excerpt: "Prompt engineering e context engineering parecem a mesma coisa, mas não são. Entenda a diferença — e por que isso importa cada vez mais conforme os sistemas de IA ficam mais complexos."
---

Se você trabalha com LLMs há algum tempo, já conhece bem o ritual do prompt engineering: testar formulações diferentes, ajustar o tom das instruções, descobrir que uma vírgula no lugar certo muda completamente a resposta. É um trabalho artesanal, e funciona.

Mas conforme os sistemas de IA ficam mais sofisticados — agentes autônomos, pipelines multi-etapa, aplicações com memória e ferramentas — o prompt engineering começa a mostrar seus limites. É aí que entra o **context engineering**.

## O que é prompt engineering (e onde ele para)

Prompt engineering é a prática de formular as instruções certas para obter a resposta certa de um modelo. O foco está na **mensagem que você envia**: a clareza do pedido, o estilo da instrução, exemplos de few-shot, cadeia de raciocínio (chain-of-thought) e técnicas similares.

É extremamente útil. Mas o escopo é limitado: você está otimizando uma entrada isolada para uma saída específica.

Um exemplo clássico:

```
Você é um especialista em SQL. Responda apenas com a query, sem explicação.
Dado o schema abaixo, escreva uma query que retorna os 10 produtos mais vendidos no último mês.
Schema: [...]
```

Isso funciona muito bem para tarefas pontuais. O problema aparece quando você sai do modo "pergunta-resposta" e entra em sistemas mais dinâmicos — onde o modelo toma decisões, chama ferramentas, mantém conversas longas, ou opera em loops de raciocínio.

## O que é context engineering

Context engineering é a disciplina de **gerenciar e estruturar tudo o que entra na janela de contexto do modelo** — não só o prompt em si, mas o conjunto completo de informações que definem o que o modelo "sabe" naquele momento.

Isso inclui:

- **System prompt**: as instruções e persona do sistema
- **Histórico de conversa**: o que foi dito antes
- **Memória recuperada**: informações relevantes trazidas de fontes externas (RAG)
- **Resultados de ferramentas**: output de funções, APIs e buscas que o agente executou
- **Estado do agente**: em que passo do fluxo estamos, o que já foi feito
- **Dados adicionais**: documentos, schemas, exemplos contextuais

Se prompt engineering é escrever uma boa mensagem, context engineering é **arquitetar o ambiente de informação inteiro** em que o modelo opera.

## A diferença na prática

Imagine um agente que ajuda usuários a analisar relatórios financeiros. Com um prompt bem escrito, você consegue boas respostas para documentos simples.

Mas e quando:
- O relatório tem 200 páginas e não cabe na janela de contexto?
- O agente precisa lembrar de análises anteriores do mesmo cliente?
- Ele usa ferramentas para buscar dados de mercado em tempo real?
- Precisa manter o raciocínio coerente ao longo de 15 passos?

Aqui, o problema não é mais "como formular o prompt". É: **quais informações colocar no contexto, em que ordem, com que formato, em que momento**.

Decisões típicas de context engineering:

- Quais trechos do documento incluir (chunking + retrieval)?
- Como sumarizar o histórico sem perder o que é relevante?
- Quando "limpar" o contexto para evitar contaminação de informação antiga?
- Qual a ordem ideal para apresentar system prompt, dados e histórico?
- Como serializar o estado do agente de forma que o modelo entenda onde está?

## Comparação lado a lado

| | Prompt Engineering | Context Engineering |
|---|---|---|
| **Foco** | A mensagem enviada | Todo o conteúdo da janela de contexto |
| **Escopo** | Interações pontuais | Sistemas dinâmicos e agentes |
| **Ferramentas** | Phrasing, few-shot, CoT | RAG, memória, estado, orquestração |
| **Complexidade** | Baixa a média | Média a alta |
| **Quando usar** | Chatbots simples, geração de texto | Agentes, pipelines, apps com memória |

Para casos simples — resumir um texto, gerar código a partir de uma descrição, responder uma pergunta — prompt engineering é suficiente e mais rápido de implementar.

Para sistemas mais complexos, você inevitavelmente vai se deparar com problemas que só fazem sentido como questões de context engineering: o modelo "esqueceu" algo que estava no início da conversa, ficou confuso por informação contraditória no contexto, ou perdeu coerência depois de várias etapas.

## Quando context engineering faz a diferença

Um caso concreto: um agente de suporte que usa ferramentas para consultar tickets, base de conhecimento e histórico do cliente. O system prompt pode estar impecável — mas se o contexto não for bem gerenciado, os problemas aparecem de outras formas:

- Resultados de ferramenta mal formatados confundem o modelo sobre o estado atual
- Histórico de conversa longo faz o modelo "perder o fio" da tarefa principal
- Informações contraditórias (ticket aberto X base de conhecimento desatualizada) geram respostas erradas

Context engineering aqui significa: decidir o que incluir de cada ferramenta, formatar os resultados de forma consistente, sumarizar o histórico quando ele fica longo, e garantir que as informações mais relevantes estejam próximas do final da janela (onde os modelos tendem a focar mais).

## Como começar a pensar assim

Algumas mudanças práticas para incorporar no seu fluxo:

1. **Audite o contexto completo**: imprima o contexto que o modelo recebe e pergunte — isso faz sentido? Está organizado? Tem informação demais ou de menos?
2. **Estruture o conteúdo**: headers, separadores, rótulos claros ajudam o modelo a navegar informações complexas
3. **Pense em relevância, não em volume**: mais informação não é sempre melhor — contexto irrelevante pode prejudicar a qualidade
4. **Gerencie o histórico ativamente**: em conversas longas, considere sumarização ou sliding window em vez de empilhar tudo
5. **Itere com dados**: mude uma coisa por vez e meça o impacto — context engineering também é empírico

## Conclusão

Prompt engineering é uma habilidade fundamental e não vai a lugar nenhum. Mas conforme os sistemas de IA ficam mais ambiciosos, a capacidade de **arquitetar o contexto** se torna tão importante quanto formular boas instruções.

A pergunta que um prompt engineer faz é: *"Como eu devo formular esse pedido?"* A pergunta que um context engineer faz é: *"O que o modelo precisa saber para responder bem?"*

As duas perguntas importam. Mas quanto mais complexo o sistema, mais a segunda começa a dominar.
