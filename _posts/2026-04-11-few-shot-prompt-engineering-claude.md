---
layout: post
title: "Few-Shot Prompt Engineering com Claude: Guia Prático com Python"
date: 2026-04-11 10:00:00 +0000
categories: tutorial prompt-engineering
tags: [few-shot, prompt-engineering, claude, python, anthropic-api]
excerpt: "Aprenda a usar few-shot examples para guiar o Claude a produzir exatamente o formato e o estilo que você precisa — com exemplos de código Python prontos para rodar."
---

Quando você precisa que um modelo de linguagem siga um padrão específico de resposta, o jeito mais rápido e eficaz é mostrar exemplos antes de fazer a pergunta. Essa técnica se chama **few-shot prompting** — e ela é uma das ferramentas mais poderosas da engenharia de prompt.

Neste tutorial você vai aprender como funciona, quando usar, e como implementar usando a API da Anthropic em Python.

---

## O que é few-shot prompting?

A ideia é simples: em vez de apenas descrever o que você quer, você **demonstra** com pares de entrada/saída antes de apresentar o caso real.

```
# Estrutura básica

[Exemplo 1]
Entrada: X
Saída: Y

[Exemplo 2]
Entrada: A
Saída: B

[Caso real]
Entrada: C
Saída: ?
```

O modelo aprende o padrão dos exemplos e aplica ao novo caso. Não há treinamento — é tudo na janela de contexto.

---

## Instalação e configuração

Primeiro, instale o SDK da Anthropic:

```bash
pip install anthropic
```

Configure sua chave de API como variável de ambiente:

```bash
export ANTHROPIC_API_KEY="sua-chave-aqui"
```

---

## Exemplo 1 — Classificação de sentimento com formato fixo

Imagine que você precisa classificar reviews de produtos e quer sempre receber a resposta em um formato JSON específico. Com few-shot, você garante isso:

```python
import anthropic
import json

client = anthropic.Anthropic()

# Exemplos que ensinam o formato desejado
few_shot_examples = """
Review: "O produto chegou rápido e funcionou perfeitamente."
Resultado: {"sentimento": "positivo", "confiança": "alta", "aspectos": ["entrega", "qualidade"]}

Review: "Demorou 3 semanas para chegar e veio com defeito."
Resultado: {"sentimento": "negativo", "confiança": "alta", "aspectos": ["entrega", "produto"]}

Review: "É ok, nada extraordinário pelo preço."
Resultado: {"sentimento": "neutro", "confiança": "média", "aspectos": ["custo-benefício"]}
"""

def classificar_review(review: str) -> dict:
    prompt = f"""{few_shot_examples}
Review: "{review}"
Resultado:"""

    response = client.messages.create(
        model="claude-opus-4-5",
        max_tokens=200,
        messages=[
            {"role": "user", "content": prompt}
        ]
    )
    
    resultado_texto = response.content[0].text.strip()
    return json.loads(resultado_texto)


# Teste
review = "A câmera tira fotos incríveis, mas a bateria dura muito pouco."
resultado = classificar_review(review)
print(resultado)
# {"sentimento": "misto", "confiança": "alta", "aspectos": ["câmera", "bateria"]}
```

O Claude entende que deve retornar exatamente aquele formato JSON — sem explicação extra, sem texto antes ou depois.

---

## Exemplo 2 — Transformação de texto com estilo consistente

Few-shot é excelente para tarefas de reescrita onde o estilo importa tanto quanto o conteúdo:

```python
import anthropic

client = anthropic.Anthropic()

SYSTEM_PROMPT = """Você é um especialista em comunicação técnica.
Sua tarefa é transformar textos técnicos em linguagem acessível para leigos.
Siga exatamente o padrão dos exemplos fornecidos."""

FEW_SHOT = [
    {
        "role": "user",
        "content": "Técnico: O modelo utiliza atenção multi-cabeça para capturar dependências de longo alcance na sequência de entrada."
    },
    {
        "role": "assistant", 
        "content": "Simples: O modelo consegue 'prestar atenção' em várias partes do texto ao mesmo tempo, o que ajuda a entender frases longas e complexas."
    },
    {
        "role": "user",
        "content": "Técnico: O processo de fine-tuning ajusta os pesos do modelo em um conjunto de dados específico do domínio."
    },
    {
        "role": "assistant",
        "content": "Simples: É como dar uma especialização ao modelo — você o treina com exemplos do seu setor para que ele fique mais preciso naquele assunto."
    }
]

def simplificar_texto(texto_tecnico: str) -> str:
    mensagens = FEW_SHOT + [
        {
            "role": "user",
            "content": f"Técnico: {texto_tecnico}"
        }
    ]
    
    response = client.messages.create(
        model="claude-opus-4-5",
        max_tokens=300,
        system=SYSTEM_PROMPT,
        messages=mensagens
    )
    
    return response.content[0].text.strip()


# Teste
texto = "O pipeline de RAG recupera documentos relevantes via busca semântica e os injeta no contexto do LLM."
print(simplificar_texto(texto))
# Simples: O sistema busca os documentos mais úteis para a pergunta e os passa para o modelo, 
# como se você entregasse as páginas certas de um livro para alguém responder uma questão.
```

Perceba como os exemplos no histórico de mensagens ensinam não só o formato (`Simples:`), mas também o tom e o nível de detalhe.

---

## Exemplo 3 — Extração estruturada de dados

Few-shot brilha especialmente em tarefas de extração, onde você precisa de consistência absoluta:

```python
import anthropic
import json
from typing import Optional

client = anthropic.Anthropic()

def extrair_dados_vaga(descricao_vaga: str) -> dict:
    prompt = """Extraia as informações estruturadas das descrições de vagas abaixo.

---
Vaga: "Procuramos Engenheiro de ML com 3+ anos em Python e TensorFlow. Remoto. Salário: R$ 12.000 a R$ 18.000."
JSON:
{
  "cargo": "Engenheiro de ML",
  "experiencia_anos": 3,
  "tecnologias": ["Python", "TensorFlow"],
  "modalidade": "remoto",
  "salario_min": 12000,
  "salario_max": 18000
}

---
Vaga: "Analista de Dados Pleno para trabalho híbrido em SP. Necessário SQL e Power BI. Salário a combinar."
JSON:
{
  "cargo": "Analista de Dados Pleno",
  "experiencia_anos": null,
  "tecnologias": ["SQL", "Power BI"],
  "modalidade": "híbrido",
  "salario_min": null,
  "salario_max": null
}

---
Vaga: \""""  + descricao_vaga + """\"
JSON:"""

    response = client.messages.create(
        model="claude-opus-4-5",
        max_tokens=400,
        messages=[{"role": "user", "content": prompt}]
    )
    
    json_texto = response.content[0].text.strip()
    return json.loads(json_texto)


# Teste
vaga = "Buscamos Tech Lead Backend com 5+ anos em Go e Kubernetes. 100% remoto. Faixa salarial: R$ 25.000 - R$ 35.000."
dados = extrair_dados_vaga(vaga)
print(json.dumps(dados, indent=2, ensure_ascii=False))
```

---

## Boas práticas

**Quantos exemplos usar?**
- 2–3 exemplos costumam ser suficientes para tarefas simples
- 5–10 para tarefas com variações importantes
- Mais de 10 raramente traz ganho proporcional ao custo de tokens

**Qual ordem colocar os exemplos?**
Coloque o exemplo mais similar ao caso real por último — é o que terá maior influência na resposta.

**Exemplos devem ser representativos**
Inclua exemplos que cubram os casos-limite. Se você só mostrar casos fáceis, o modelo pode travar nos difíceis.

**Separe exemplos do caso real**
Use delimitadores claros (`---`, `###`, ou marcadores explícitos como `Exemplo 1:`) para que o modelo não confunda exemplos com a pergunta real.

---

## Quando few-shot não é suficiente

Few-shot resolve muito, mas existem casos onde outras abordagens se saem melhor:

| Situação | Alternativa |
|---|---|
| Tarefa muito complexa | Chain-of-thought (peça raciocínio passo a passo) |
| Muitos exemplos necessários | Fine-tuning |
| Exemplos variam por usuário | Injeção dinâmica via RAG |
| Precisa de consistência extrema | System prompt detalhado + few-shot |

---

## Conclusão

Few-shot prompting é a forma mais direta de ensinar ao modelo exatamente o que você quer — sem configuração, sem treinamento, apenas exemplos bem escolhidos no contexto. Em Python, a API da Anthropic torna isso trivial de implementar, seja no campo `messages` como um histórico de conversa, seja como texto inline no prompt.

Comece com 2–3 exemplos, observe o comportamento e ajuste. Na maioria dos casos, isso é tudo que você precisa para alcançar respostas consistentes e bem formatadas.
