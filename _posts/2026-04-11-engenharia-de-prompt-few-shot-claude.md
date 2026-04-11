---
layout: post
title: "Engenharia de Prompt com Few-Shot Examples no Claude: Tutorial Passo a Passo"
date: 2026-04-11 10:00:00 -0300
categories: [blog, inteligencia-artificial]
tags: [claude, anthropic, prompt-engineering, few-shot, python, api, llm]
---

Few-shot prompting é uma das técnicas mais poderosas de engenharia de prompt: em vez de apenas descrever o que você quer, você **mostra** ao modelo com exemplos concretos. Neste tutorial, você vai aprender a usar essa técnica com o Claude via API da Anthropic usando Python.

## O que é Few-Shot Prompting?

Quando você instrui um modelo de linguagem, existem três abordagens básicas:

- **Zero-shot**: sem nenhum exemplo — você descreve a tarefa e confia que o modelo entende
- **One-shot**: um único exemplo para guiar o modelo
- **Few-shot**: múltiplos exemplos (geralmente 2–8) que estabelecem um padrão claro

A lógica é simples: exemplos valem mais que definições. Em vez de dizer *"classifique o sentimento como positivo ou negativo"*, você mostra o que isso significa na prática.

## Pré-requisitos

Instale o SDK da Anthropic:

```bash
pip install anthropic
```

Configure sua chave de API:

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

---

## Passo 1: Estrutura básica de uma chamada à API

Antes de entrar nos few-shot examples, veja como funciona uma chamada básica:

```python
import anthropic

client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-opus-4-5",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Qual é a capital do Brasil?"}
    ]
)

print(response.content[0].text)
```

O objeto `messages` é uma lista de turnos de conversa. Cada turno tem um `role` (`user` ou `assistant`) e um `content`.

---

## Passo 2: Few-shot com exemplos no system prompt

A maneira mais simples de usar few-shot é incluir os exemplos diretamente no `system` prompt:

```python
import anthropic

client = anthropic.Anthropic()

system_prompt = """Você é um classificador de sentimentos para avaliações de produtos.
Classifique cada avaliação como POSITIVO, NEGATIVO ou NEUTRO.

Exemplos:

Avaliação: "Produto chegou rápido e funciona perfeitamente!"
Sentimento: POSITIVO

Avaliação: "Veio com defeito e o suporte não resolveu."
Sentimento: NEGATIVO

Avaliação: "Produto ok, nada de especial."
Sentimento: NEUTRO

Avaliação: "Não esperava tanta qualidade pelo preço. Recomendo!"
Sentimento: POSITIVO

Avaliação: "Demorou 3 semanas para chegar e a embalagem estava amassada."
Sentimento: NEGATIVO

Responda apenas com a palavra: POSITIVO, NEGATIVO ou NEUTRO."""

def classify_sentiment(review: str) -> str:
    response = client.messages.create(
        model="claude-opus-4-5",
        max_tokens=10,
        system=system_prompt,
        messages=[
            {"role": "user", "content": f"Avaliação: \"{review}\""}
        ]
    )
    return response.content[0].text.strip()

# Teste
reviews = [
    "Superou minhas expectativas, produto incrível!",
    "Péssima experiência, nunca mais compro.",
    "Entregou o que prometeu, sem mais nem menos.",
]

for review in reviews:
    sentiment = classify_sentiment(review)
    print(f"[{sentiment}] {review}")
```

Saída esperada:

```
[POSITIVO] Superou minhas expectativas, produto incrível!
[NEGATIVO] Péssima experiência, nunca mais compro.
[NEUTRO] Entregou o que prometeu, sem mais nem menos.
```

---

## Passo 3: Few-shot com alternância de turnos (multi-turn)

Uma abordagem mais robusta é simular a conversa usando turnos `user`/`assistant` alternados. Isso usa o formato nativo do modelo:

```python
import anthropic

client = anthropic.Anthropic()

def build_few_shot_messages(examples: list[dict], new_input: str) -> list[dict]:
    """
    Constrói a lista de mensagens com few-shot examples.
    
    Args:
        examples: lista de dicts com chaves 'input' e 'output'
        new_input: o input novo que queremos classificar
    
    Returns:
        lista de mensagens no formato da API
    """
    messages = []
    
    for example in examples:
        messages.append({"role": "user", "content": example["input"]})
        messages.append({"role": "assistant", "content": example["output"]})
    
    # Adiciona o novo input ao final
    messages.append({"role": "user", "content": new_input})
    
    return messages


# Exemplos de extração de entidades
examples = [
    {
        "input": "Texto: 'Pedro Oliveira trabalha na Anthropic em São Paulo.'\nExtraia: pessoa, empresa, cidade",
        "output": '{"pessoa": "Pedro Oliveira", "empresa": "Anthropic", "cidade": "São Paulo"}'
    },
    {
        "input": "Texto: 'Maria Silva é CEO da Nubank no Rio de Janeiro.'\nExtraia: pessoa, empresa, cidade",
        "output": '{"pessoa": "Maria Silva", "empresa": "Nubank", "cidade": "Rio de Janeiro"}'
    },
    {
        "input": "Texto: 'Carlos Mendes fundou a iFood em Osasco.'\nExtraia: pessoa, empresa, cidade",
        "output": '{"pessoa": "Carlos Mendes", "empresa": "iFood", "cidade": "Osasco"}'
    },
]

new_text = "Texto: 'Ana Costa dirige a Stone em Curitiba.'\nExtraia: pessoa, empresa, cidade"

messages = build_few_shot_messages(examples, new_text)

response = client.messages.create(
    model="claude-opus-4-5",
    max_tokens=100,
    system="Você extrai entidades de textos e retorna JSON. Siga exatamente o formato dos exemplos.",
    messages=messages
)

print(response.content[0].text)
# Saída: {"pessoa": "Ana Costa", "empresa": "Stone", "cidade": "Curitiba"}
```

A vantagem desta abordagem: o modelo vê exatamente o padrão de resposta que você espera, já no formato de conversa que ele foi treinado.

---

## Passo 4: Few-shot para formatação de saída estruturada

Few-shot é especialmente útil quando você quer uma saída com formato rigoroso — como Markdown, tabelas ou JSON com campos específicos:

```python
import anthropic
import json

client = anthropic.Anthropic()

SYSTEM = """Você transforma descrições informais de bugs em relatórios estruturados.
Siga exatamente o formato JSON dos exemplos."""

examples = [
    {
        "input": "o botão de login não funciona no celular",
        "output": json.dumps({
            "titulo": "Botão de login inoperante em dispositivos móveis",
            "severidade": "alta",
            "componente": "autenticação",
            "passos_reproducao": [
                "Abrir o app em dispositivo móvel",
                "Navegar até a tela de login",
                "Tocar no botão 'Entrar'"
            ],
            "resultado_esperado": "Usuário é autenticado e redirecionado",
            "resultado_atual": "Nenhuma ação ocorre ao tocar no botão"
        }, ensure_ascii=False, indent=2)
    },
    {
        "input": "às vezes a página de checkout trava e perde o carrinho",
        "output": json.dumps({
            "titulo": "Travamento intermitente na página de checkout com perda de carrinho",
            "severidade": "crítica",
            "componente": "checkout",
            "passos_reproducao": [
                "Adicionar itens ao carrinho",
                "Ir para a página de checkout",
                "Aguardar ou interagir com a página"
            ],
            "resultado_esperado": "Checkout é concluído normalmente",
            "resultado_atual": "Página trava e carrinho é esvaziado"
        }, ensure_ascii=False, indent=2)
    }
]

def create_bug_report(description: str) -> dict:
    messages = []
    for ex in examples:
        messages.append({"role": "user", "content": ex["input"]})
        messages.append({"role": "assistant", "content": ex["output"]})
    messages.append({"role": "user", "content": description})
    
    response = client.messages.create(
        model="claude-opus-4-5",
        max_tokens=500,
        system=SYSTEM,
        messages=messages
    )
    
    return json.loads(response.content[0].text)

# Teste
bug = "o filtro de busca ignora acentos e não encontra produtos com ç ou ã"
report = create_bug_report(bug)
print(json.dumps(report, ensure_ascii=False, indent=2))
```

---

## Passo 5: Escolhendo e refinando seus exemplos

A qualidade dos seus few-shot examples impacta diretamente o resultado. Siga estas diretrizes:

### Diversidade nos exemplos

```python
# Ruim: exemplos muito parecidos
bad_examples = [
    {"input": "Ótimo produto!", "output": "POSITIVO"},
    {"input": "Produto excelente!", "output": "POSITIVO"},
    {"input": "Muito bom!", "output": "POSITIVO"},
]

# Bom: exemplos variados que cobrem o espaço do problema
good_examples = [
    {"input": "Ótimo produto!", "output": "POSITIVO"},
    {"input": "Péssimo, não funciona.", "output": "NEGATIVO"},
    {"input": "Chegou no prazo.", "output": "NEUTRO"},
    # Casos edge importantes:
    {"input": "O produto é bom, mas o frete foi lento.", "output": "NEUTRO"},
    {"input": "Esperava mais pelo preço.", "output": "NEGATIVO"},
]
```

### Ordem dos exemplos

Coloque os exemplos mais representativos no início. O modelo dá mais peso ao padrão geral do que ao último exemplo, mas em casos ambíguos o exemplo mais recente pode ter mais influência.

### Número de exemplos

```python
# Teste com diferentes quantidades
def test_few_shot_count(examples_pool, test_cases, counts=[1, 2, 3, 5]):
    results = {}
    for n in counts:
        examples = examples_pool[:n]
        correct = 0
        for test in test_cases:
            # ... faz a chamada e verifica
            pass
        results[n] = correct / len(test_cases)
    return results
```

Geralmente 3–5 exemplos já são suficientes. Mais de 8 raramente traz ganhos significativos e aumenta o custo de tokens.

---

## Passo 6: Combinando few-shot com chain-of-thought

Para tarefas complexas, mostre o raciocínio nos exemplos — não só a resposta final:

```python
import anthropic

client = anthropic.Anthropic()

SYSTEM = """Você resolve problemas de lógica. Mostre seu raciocínio passo a passo antes da resposta final."""

examples = [
    {
        "input": "Se todos os gatos são felinos, e Mimi é um gato, Mimi é um felino?",
        "output": """Raciocínio:
1. Premissa 1: Todos os gatos são felinos (universal)
2. Premissa 2: Mimi é um gato (particular)
3. Aplicando modus ponens: se Mimi pertence ao conjunto 'gatos', e todos os gatos pertencem a 'felinos', então Mimi pertence a 'felinos'

Resposta: Sim, Mimi é um felino."""
    },
    {
        "input": "João é mais alto que Maria. Maria é mais alta que Carlos. Quem é o mais baixo?",
        "output": """Raciocínio:
1. Relação 1: João > Maria (altura)
2. Relação 2: Maria > Carlos (altura)
3. Por transitividade: João > Maria > Carlos
4. O mais baixo é quem está no final da ordem

Resposta: Carlos é o mais baixo."""
    }
]

def solve_with_cot(problem: str) -> str:
    messages = []
    for ex in examples:
        messages.append({"role": "user", "content": ex["input"]})
        messages.append({"role": "assistant", "content": ex["output"]})
    messages.append({"role": "user", "content": problem})
    
    response = client.messages.create(
        model="claude-opus-4-5",
        max_tokens=400,
        system=SYSTEM,
        messages=messages
    )
    return response.content[0].text

problem = "Ana, Bia e Carla têm idades diferentes. Ana é mais velha que Bia. Carla é mais nova que Bia. Ordene as três da mais velha para a mais nova."
print(solve_with_cot(problem))
```

---

## Resumo das boas práticas

| Técnica | Quando usar |
|---|---|
| Few-shot no system prompt | Tarefa simples, formato fixo |
| Few-shot multi-turn | Quando o formato de conversa importa |
| 3–5 exemplos | Maioria dos casos |
| Exemplos diversos | Sempre — evite exemplos redundantes |
| CoT nos exemplos | Tarefas de raciocínio, matemática, lógica |
| JSON nos exemplos | Quando precisar de saída estruturada |

A engenharia de prompt com few-shot examples é uma habilidade iterativa: comece com 3 exemplos bem escolhidos, teste nos seus casos de uso reais, e ajuste conforme necessário. O modelo aprende o padrão dos seus exemplos — então a qualidade deles determina a qualidade da saída.

---

*Quer se aprofundar? O próximo passo natural é experimentar [prompt caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) para reduzir custos quando você usa os mesmos few-shot examples repetidamente.*
