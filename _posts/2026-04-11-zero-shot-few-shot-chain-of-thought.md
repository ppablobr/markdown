---
layout: post
title: "Do Zero-Shot ao Chain of Thought: a evolução de um único prompt"
date: 2026-04-11 14:00:00 -0300
categories: ia
tags: [prompt-engineering, chain-of-thought, few-shot, zero-shot, llm]
excerpt: "Usando sempre o mesmo problema de desconto percentual, veja como zero-shot, few-shot e Chain of Thought produzem resultados radicalmente diferentes — e por quê isso importa."
---

Tem uma forma simples de ver o impacto real das técnicas de engenharia de prompt: pegar um único problema e submetê-lo de três formas diferentes, observando como a resposta muda. É o que vamos fazer aqui.

O problema é este:

> **Uma loja aplicou 20% de desconto em um produto. Em seguida, aplicou mais 15% de desconto sobre o novo preço. Qual foi o desconto total percentual sobre o preço original?**

Parece simples. Mas o resultado vai surpreender — especialmente quando você ver o que o modelo faz sem orientação.

---

## 1. Zero-Shot: só a pergunta

No zero-shot, você não fornece exemplos nem instrui o modelo sobre como raciocinar. Você simplesmente pergunta.

**Prompt:**

```
Uma loja aplicou 20% de desconto em um produto. Em seguida, aplicou mais 15%
de desconto sobre o novo preço. Qual foi o desconto total percentual sobre o
preço original?
```

**Resposta típica do modelo:**

```
O desconto total foi de 35% sobre o preço original (20% + 15%).
```

Errado. A resposta correta é **32%**, não 35%.

O modelo fez a soma ingênua dos percentuais. É um erro clássico — e completamente compreensível, porque sem orientação o modelo segue o caminho mais provável estatisticamente: "dois descontos, some-os". Ele não foi instruído a raciocinar; ele apenas associou padrões.

O zero-shot funciona bem para tarefas diretas onde o caminho até a resposta é único e óbvio. Para problemas que envolvem múltiplos passos ou armadilhas cognitivas, ele falha com frequência.

---

## 2. Few-Shot: exemplos guiam o formato

No few-shot, você fornece pares de entrada e saída antes de apresentar o problema real. O modelo aprende o padrão — tanto o formato quanto (idealmente) o raciocínio implícito nos exemplos.

**Prompt:**

```
Calcule o desconto total quando dois descontos são aplicados em sequência.

---
Produto com preço R$ 100,00.
Primeiro desconto: 10%
Novo preço: R$ 90,00
Segundo desconto: 10% sobre R$ 90,00
Novo preço: R$ 81,00
Desconto total: R$ 19,00 sobre R$ 100,00 = 19%

---
Produto com preço R$ 200,00.
Primeiro desconto: 25%
Novo preço: R$ 150,00
Segundo desconto: 20% sobre R$ 150,00
Novo preço: R$ 120,00
Desconto total: R$ 80,00 sobre R$ 200,00 = 40%

---
Produto com preço R$ 100,00.
Primeiro desconto: 20%
Segundo desconto: 15% sobre o novo preço.
Qual o desconto total percentual?
```

**Resposta do modelo:**

```
Novo preço após 20%: R$ 80,00
Novo preço após 15% sobre R$ 80,00: R$ 68,00
Desconto total: R$ 32,00 sobre R$ 100,00 = 32%
```

Agora sim. O modelo acertou.

Por quê? Porque os exemplos demonstraram, implicitamente, como resolver: calcular passo a passo com valores concretos, não somar percentuais. O modelo viu o padrão nos exemplos e replicou.

O few-shot é poderoso especialmente quando você quer **consistência de formato** — extrações JSON, classificações com rótulos fixos, reescritas com estilo definido. Os exemplos servem de molde.

Mas há um limite: o modelo aprendeu o *padrão de resposta*, não necessariamente o *raciocínio*. Se o problema mudar um pouco, ele pode falhar de novo. É aqui que entra o Chain of Thought.

---

## 3. Chain of Thought: instruindo o raciocínio

No Chain of Thought (CoT), você instrui explicitamente o modelo a **pensar em voz alta** antes de concluir. Em vez de ir direto à resposta, ele descreve cada etapa do raciocínio.

**Prompt:**

```
Uma loja aplicou 20% de desconto em um produto. Em seguida, aplicou mais 15%
de desconto sobre o novo preço. Qual foi o desconto total percentual sobre o
preço original?

Pense passo a passo antes de responder.
```

**Resposta do modelo:**

```
Vou resolver passo a passo.

Passo 1: Após o primeiro desconto de 20%, o produto fica com 80% do preço original.
  Fator: 1 - 0,20 = 0,80

Passo 2: Sobre esse novo preço, aplica-se 15% de desconto. O produto fica com
  85% do valor pós-desconto.
  Fator: 1 - 0,15 = 0,85

Passo 3: Multiplico os dois fatores para encontrar o percentual restante do
  preço original.
  0,80 × 0,85 = 0,68

Passo 4: O preço final é 68% do original. Portanto, o desconto total é:
  1 - 0,68 = 0,32 = 32%

O desconto total percentual sobre o preço original foi de 32%, não 35%.
```

Correto e completo. E perceba o detalhe final: o modelo ele mesmo reconhece e rejeita a resposta errada mais óbvia (35%), o que mostra que o raciocínio foi efetivo — não apenas uma formatação.

---

## Por que o CoT funciona?

O Chain of Thought força o modelo a **decompor o problema** antes de responder. Ao processar cada etapa intermediária no contexto, ele reduz as chances de "pular" para uma conclusão errada.

É análogo ao que acontece quando você resolve um problema matemático no papel versus de cabeça: escrever os passos intermediários reduz erros porque cada etapa ancora a próxima.

Pesquisas da Google e da Anthropic mostram que o CoT melhora substancialmente o desempenho em tarefas de raciocínio, especialmente em modelos maiores. Em modelos menores o ganho é menor — o raciocínio em cadeia requer capacidade de seguir instruções com precisão.

---

## As três técnicas lado a lado

| Técnica | Prompt | Resultado no nosso exemplo | Quando usar |
|---|---|---|---|
| **Zero-Shot** | Só a pergunta | 35% (errado) | Tarefas simples e diretas |
| **Few-Shot** | Pergunta + exemplos | 32% (correto) | Quando o formato importa; padrões repetitivos |
| **Chain of Thought** | Pergunta + "pense passo a passo" | 32% (correto, com raciocínio explícito) | Raciocínio multietapas, lógica, matemática |

---

## Combinando as três

Na prática, as técnicas não são excludentes. O melhor resultado frequentemente vem da combinação: **few-shot com Chain of Thought nos exemplos**.

```
Calcule o desconto total, pensando passo a passo.

---
Primeiro desconto: 10%, Segundo desconto: 10%

Passo 1: Fator após 10% = 1 - 0,10 = 0,90
Passo 2: Fator após mais 10% = 1 - 0,10 = 0,90
Passo 3: Fator combinado = 0,90 × 0,90 = 0,81
Desconto total = 1 - 0,81 = 19%

---
Primeiro desconto: 20%, Segundo desconto: 15%

Passe a raciocinar:
```

Aqui os exemplos ensinam ao modelo tanto o formato quanto o caminho de raciocínio correto. É a abordagem mais robusta para problemas complexos que precisam de saída previsível.

---

## Conclusão

Um único problema. Três prompts. Três comportamentos diferentes.

Zero-shot é rápido mas frágil. Few-shot garante consistência de formato e pode ensinar padrões de solução. Chain of Thought desbloqueia raciocínio estruturado — especialmente útil quando a tarefa tem múltiplos passos ou armadilhas que o caminho mais óbvio leva ao erro.

A boa notícia: você não precisa escolher apenas uma. Combine as técnicas conforme a complexidade da tarefa, e use o CoT sempre que a resposta correta depender de raciocínio intermediário — não só de reconhecimento de padrão.
