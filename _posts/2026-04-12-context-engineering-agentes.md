---
layout: post
title: "Engenharia de Contexto: como desenvolver agentes que sabem o que executar"
date: 2026-04-12 10:00:00 -0300
categories: ia agentes
tags: [context-engineering, agentes, llm, processos, boas-praticas]
excerpt: "Você decidiu que o processo está pronto. O agente está configurado. E ainda assim ele erra. O problema quase nunca está no modelo: está no contexto que você deu a ele."
---

Você seguiu as recomendações. Mapeou o processo, definiu as etapas, testou com casos de uso reais. O agente foi configurado. E ainda assim ele toma decisões erradas, ignora informações que estão no prompt, ou repete passos que já foram completados.

O instinto imediato é culpar o modelo. Trocar o GPT-4 pelo Claude, ajustar a temperatura, reescrever o system prompt do zero. Às vezes funciona. Na maioria das vezes, o problema está em outro lugar.

Os dados de produção apontam um padrão consistente: quando agentes falham em ambientes controlados, com processos bem definidos e modelos capazes, o fator mais frequente é o contexto. Especificamente, a ausência de informação certa, no formato certo, no momento certo. Engenharia de contexto é a disciplina que resolve isso, e ela está entre o seu processo bem definido e um agente que opera com confiança.

---

## O processo como matéria-prima do contexto

O [post anterior desta série](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/) trabalha uma distinção fundamental: antes de agentificar qualquer coisa, o processo precisa estar em condições de ser executado sem supervisão constante. As três perguntas diagnósticas giram em torno de um ponto central: você saberia quando o agente errou?

Mas saber quando o processo está pronto para um agente é diferente de saber como dar ao agente as informações que ele precisa para executar. Essa é a fronteira entre design de processo e engenharia de contexto.

Processo define o que o agente deve fazer: as etapas, os critérios de decisão, as saídas esperadas. Contexto define o que o agente sabe no momento em que precisa decidir. Os dois precisam estar alinhados, mas um não substitui o outro.

Um procedimento operacional padrão bem escrito é o primeiro rascunho do system prompt. Mas documento de processo não vira contexto automaticamente. Precisa ser destilado: o que é regra fixa, o que é informação dinâmica, o que o agente precisa recuperar em tempo real, o que é estado acumulado ao longo dos passos. Sem essa destilação, o agente opera com uma versão irrelevante ou incompleta do que foi definido no processo.

---

## O que é engenharia de contexto

[A virada de sistemas que respondem para sistemas que agem](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/a-historia-da-ia-que-levou-aos-agentes/) não aconteceu só por modelos mais capazes. Aconteceu porque ficou possível estruturar o ambiente de informação em que esses modelos operam com precisão cirúrgica.

Andrej Karpathy definiu o conceito em junho de 2025: ["context engineering is the art of filling the context window with just the right information"](https://x.com/karpathy/status/1937902205765607626). A ênfase está em "just the right" porque mais informação não é mais precisão. Informação errada, excessiva ou mal posicionada na janela de contexto degrada o desempenho do modelo tanto quanto informação ausente.

O contexto de um agente tem cinco camadas distintas:

1. **System prompt**: instruções fixas, persona, regras de comportamento, restrições
2. **Histórico de conversa**: o que foi dito e feito nos turnos anteriores
3. **Memória recuperada via RAG**: informação relevante trazida dinamicamente de bases de conhecimento externas
4. **Resultados de ferramentas**: output de APIs, buscas, execuções de código, chamadas a sistemas externos
5. **Estado do agente**: em que passo do fluxo está, o que já foi executado, o que está pendente

Prompt engineering trabalha com a primeira camada. Context engineering é a arquitetura das cinco. A diferença aparece quando o agente começa a usar ferramentas, acumular histórico, ou operar em fluxos de múltiplos passos. Nesse ponto, um system prompt bem escrito é necessário, mas insuficiente.

---

## Q&A: as dúvidas mais comuns na prática

### Se o meu processo está bem definido, o contexto vai ficar bom automaticamente?

Não. Processo define etapas e critérios de decisão; contexto é o que o agente consegue acessar no momento em que precisa decidir. Um processo pode estar documentado em 40 páginas. O agente vai operar com o que couber na janela de contexto, no formato em que você estruturou.

A maioria dos erros de agentes em produção acontece exatamente aqui: o processo estava correto, mas o agente não tinha as informações certas disponíveis na hora certa. Ou tinha informação demais, o que é igualmente problemático.

### Qual a diferença prática entre escrever um bom system prompt e fazer context engineering?

Um system prompt bem escrito é uma das partes do contexto, mas context engineering é a arquitetura toda. Quando o agente usa ferramentas, mantém memória entre passos ou opera em fluxos longos, o system prompt sozinho não resolve.

Você precisa decidir o que o agente recupera do RAG, como os resultados das ferramentas são formatados antes de entrar na janela, como o histórico é sumarizado quando fica longo demais, e como o agente sabe em que passo do fluxo está. A diferença é entre escrever uma boa instrução e projetar um ambiente de informação completo.

### Como detectar que o contexto está mal estruturado antes de ir a produção?

A técnica mais direta é o audit de contexto: imprimir exatamente o que o modelo recebe antes de cada decisão e ler como se você fosse o modelo. Você consegue responder bem com essas informações? Está claro o que se espera como saída? A informação mais importante está posicionada para ser bem processada?

Isso importa porque modelos têm viés posicional documentado. Liu et al. (2023) demonstraram no paper ["Lost in the Middle"](https://arxiv.org/abs/2307.03172) que LLMs processam melhor a informação no início e no final da janela de contexto do que a do meio. Informação crítica enterrada no centro do contexto tem performance degradada.

Além do audit, testar com casos de borda: o que acontece quando o histórico fica longo, quando uma ferramenta retorna erro, quando dois documentos contradizem um ao outro?

### Context engineering funciona diferente para agentes autônomos versus chatbots simples?

Sim, e a diferença é significativa. Em um chatbot, o contexto é relativamente estático: system prompt mais histórico de conversa. Em um agente autônomo, o contexto cresce e muda a cada passo: o agente chama ferramentas, acumula resultados, atualiza seu estado interno.

Isso cria riscos específicos. Simon Willison descreve o padrão como normalização do desvio em sua [análise de 2025 sobre LLMs em produção](https://simonwillison.net/2025/Dec/31/the-year-in-llms/): pequenos erros no contexto se acumulam silenciosamente ao longo dos passos, até que o agente está operando com premissas erradas sem que ninguém perceba. O loop que transformou $127 em $47.000 de custo em quatro semanas, documentado pelo [ZenML em 1.200 deployments de produção](https://www.zenml.io/blog/what-1200-production-deployments-reveal-about-llmops-in-2025), é o exemplo mais caro desse padrão.

---

## Três armadilhas comuns de contexto em produção

### Contexto por volume

"Mais informação é mais seguro" é um instinto natural, mas funciona contra você. O paper "Lost in the Middle" de Liu et al. (2023) demonstrou o viés posicional: a informação no início e no final da janela é processada melhor do que a do meio. Encher o contexto com documentação potencialmente relevante não aumenta a precisão, dilui ela.

A regra prática: inclua o que o agente precisa saber para esta decisão específica, não tudo que pode ser relevante algum dia.

### Estado implícito

O agente executa passos em sequência, mas você não modelou explicitamente em que passo ele está. O resultado: o modelo infere o estado a partir do histórico, e infere errado em casos ambíguos. Passo 2 vira passo 3, ação já executada é repetida, condição de parada é ignorada.

A solução é tornar o estado explícito no contexto a cada passo: "você está no passo 3 de 5, já executou X e Y, o próximo passo é Z". O guia de boas práticas de construção de agentes da Anthropic ([Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)) descreve estado implícito como uma das fontes mais comuns de falhas em fluxos multi-etapa.

### Memória sem gerenciamento

Em conversas longas ou agentes que operam por horas, o histórico cresce sem controle. A maioria dos frameworks simplesmente empilha tudo, o que eventualmente briga com o limite da janela de contexto ou degrada a qualidade das respostas.

Três estratégias funcionam: sliding window (descarta o histórico mais antigo conforme o contexto cresce), sumarização periódica (condensa turnos anteriores em um resumo compacto), ou memória hierárquica (resumo estruturado mais detalhe dos passos recentes). Sem gerenciamento ativo, o agente que funcionava bem nos primeiros 10 passos começa a errar no passo 30.

---

## Quatro perguntas antes de projetar o contexto

Antes de escrever uma linha de código de agente, responder:

**1. O que o agente precisa saber para tomar cada decisão do fluxo?**
Mapeie por passo, não genericamente. "Precisa saber sobre o cliente" é vago. "No passo 2, precisa saber o histórico de compras dos últimos 90 dias e o segmento de risco atual" é acionável.

**2. O que deve ser fixo no system prompt versus o que precisa ser recuperado dinamicamente?**
Regras de comportamento e restrições são fixas. Informação sobre entidades específicas, dados que mudam, contexto de execução atual: recuperar via RAG ou injetar dinamicamente.

**3. Como o agente vai saber em que passo está e o que já foi feito?**
Estado deve ser explícito. Defina o formato de representação do estado antes de escrever o código.

**4. Como você vai detectar quando o contexto saiu dos trilhos?**
Métricas de qualidade de saída, não só de execução bem-sucedida. O loop do ZenML rodava com sucesso técnico enquanto explodia o orçamento. Execução e qualidade são coisas diferentes.

Se você consegue responder essas quatro perguntas com especificidade, o contexto do seu agente já tem uma arquitetura. Se as respostas forem vagas ("depende", "vamos ver em produção"), o agente vai herdar essa ambiguidade e operar com ela.

---

## Fechando a cadeia

O [post sobre processos](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/) termina com uma pergunta: no processo que você quer automatizar, você saberia quando o agente errou? Engenharia de contexto é a resposta estrutural a essa pergunta.

Quando o contexto está bem projetado, erros ficam visíveis porque o agente opera dentro de limites definidos e seu estado é observável. Quando está mal projetado, o agente pode estar errando silenciosamente há semanas, com confiança e velocidade total.

A linha que separa esses dois cenários é, quase sempre, o quanto de cuidado foi colocado em decidir o que entra na janela de contexto, em que ordem, em que formato, e como isso muda a cada passo do fluxo.

Processo define o que fazer. Contexto define o que o agente sabe quando precisa fazer. Os dois juntos, e bem projetados, são o que torna um agente confiável em produção.

---

*Este post faz parte de uma série sobre desenvolvimento prático de agentes. Você pode começar pelo [começo da história da IA](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/a-historia-da-ia-que-levou-aos-agentes/) ou pelo [debate sobre processos antes de agentes](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/).*
