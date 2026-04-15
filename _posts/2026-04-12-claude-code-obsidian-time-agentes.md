---
layout: post
title: "Claude Code + Obsidian: montando um time de agentes com memória persistente"
date: 2026-04-12 10:00:00 +0000
categories: blog pessoal
tags: [claude-code, obsidian, agentes, ia, automacao]
excerpt: "A maioria dos times de agentes falha porque cada sessão começa do zero. Obsidian resolve o problema de memória; Claude Code resolve o problema de execução. Juntos, formam uma infraestrutura de agentes que aprende."
image: https://res.cloudinary.com/dgqhglwo5/image/upload/v1776086753/blog/covers/blog/covers/claude-code-obsidian-time-agentes.jpg
---

A maioria dos times de agentes não falha por falta de poder computacional. Falha por falta de memória.

Você configura três agentes especializados: um pesquisa, um escreve, um publica. Cada um funciona bem em isolamento. Mas quando precisam trabalhar juntos, o contexto some. O agente de escrita não sabe o que o pesquisador encontrou. O agente de publicação não sabe para qual público o conteúdo foi escrito. Cada sessão começa do zero.

O problema é estrutural. LLMs são stateless por design. Cada chamada à API é uma lousa em branco. E a maioria das arquiteturas de agentes não resolve isso: empurra contexto via prompt, adiciona um banco de vetores como camada de memória, ou simplesmente ignora o problema e torce para que a janela de contexto seja grande o suficiente.

Existe uma combinação mais simples e mais durável: Obsidian como cérebro persistente do time, Claude Code como o executor que coordena agentes especializados.

<figure>
  <img src="https://res.cloudinary.com/dgqhglwo5/image/upload/v1776086753/blog/covers/blog/covers/claude-code-obsidian-time-agentes.jpg"
       alt="Claude Code conectado ao Obsidian vault — terminal executor à esquerda, grafo de notas à direita"
       loading="lazy"
       style="width:100%;border-radius:4px;">
  <figcaption>Terminal executor e vault de memória: dois sistemas distintos, uma infraestrutura coesa.</figcaption>
</figure>

---

## O que Karpathy está dizendo sobre memória e agentes

Andrej Karpathy articulou com clareza a mudança em curso. No framework que ele chama de [Software 3.0](https://www.latent.space/p/s3), LLMs não são mais chatbots. São o processo kernel de um novo sistema operacional. "O LLM vira o CPU e o shell, sua janela de contexto vira RAM, sistemas de recuperação agem como o sistema de arquivos, e agentes funcionam como aplicações de longa duração."

Mas a frase que mais importa aqui veio mais tarde. Em 2026, Karpathy descreveu uma arquitetura que chama de [LLM Knowledge Base](https://venturebeat.com/data/karpathy-shares-llm-knowledge-base-architecture-that-bypasses-rag-with-an): uma biblioteca de arquivos Markdown mantida pelo próprio LLM. O modelo não consulta RAG. Ele compila, organiza, revisa e interliga notas como um bibliotecário de pesquisa em tempo integral.

O formato Markdown foi uma escolha deliberada. Compacto, legível por humanos e por LLMs, versionável com git, rastreável. Cada afirmação pode ser encontrada em um arquivo específico. Nada de caixas-pretas de embeddings.

A infraestrutura que Karpathy descreve existe há anos num produto que a maioria dos desenvolvedores já tem instalado: Obsidian.

---

## Obsidian como memória persistente do time

[Obsidian](https://obsidian.md) é um editor de notas local que trabalha com arquivos Markdown comuns em uma pasta, chamada vault. Sem banco de dados proprietário, sem sincronização obrigatória para a nuvem, sem formato de lock-in. Seus arquivos são arquivos.

Isso o torna ideal como camada de memória para um time de agentes porque:

**Leitura e escrita simples.** Claude Code lê e escreve arquivos Markdown nativamente. Não precisa de plugin, SDK, ou API intermediária. Um agente pode criar `pesquisa-concorrentes.md`, salvar os resultados, e o próximo agente lê o arquivo no início da sua tarefa.

**Estrutura é opcional mas poderosa.** Você pode ter notas planas ou organizar com YAML frontmatter, links internos `[[wiki-style]]`, tags, e pastas hierárquicas. O Dataview transforma o vault num banco de dados consultável via queries SQL-like, o que permite que um agente orquestrador faça perguntas como "quais tarefas têm status: pendente nesta semana".

**Contexto acumula entre sessões.** Um vault bem mantido cresce. Cada execução de agente pode depositar o que aprendeu. A próxima sessão começa mais rica do que a anterior. É o oposto do stateless.

**Visibilidade humana.** Você consegue abrir qualquer nota, editar, corrigir, ou deletar. O agente não opera numa caixa-preta. O Canvas do Obsidian permite visualizar fluxos inteiros de agentes como um mapa visual, útil para depurar ou apresentar o pipeline para um stakeholder.

A estrutura básica de um vault para times de agentes segue um padrão simples:

```
vault/
├── agents/
│   ├── research-agent.md      # instruções e contexto do agente pesquisador
│   ├── writer-agent.md        # instruções e contexto do agente escritor
│   └── publisher-agent.md     # instruções do agente publicador
├── memory/
│   ├── decisions.md           # decisões arquiteturais e editoriais
│   ├── style-guide.md         # voz, tom, preferências
│   └── session-log.md         # o que foi feito em cada sessão
├── inbox/
│   └── raw-research.md        # material bruto a processar
└── output/
    └── drafts/                # rascunhos prontos para revisão
```

---

## Claude Code como coordenador de agentes

Claude Code resolve a outra metade do problema: execução com ferramentas reais.

Como discutido em detalhe no post [Claude Code como agente de terminal](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/claude-code-agente-terminal/), ele opera num loop de percepção, raciocínio, ação e observação. Lê arquivos, executa comandos, abre PRs, corrige erros sem supervisão constante. Isso o torna naturalmente adequado para coordenar agentes especializados que trabalham com artefatos em disco.

A partir da versão com [Agent Teams](https://code.claude.com/docs/en/agent-teams), o Claude Code suporta um modelo onde uma sessão atua como "team lead" e coordena instâncias independentes como "teammates". Cada instância tem sua própria janela de contexto, trabalha em paralelo, e reporta de volta ao coordenador. O que antes exigia frameworks externos (LangGraph, AutoGen) agora é primitiva nativa.

Para times mais simples, a Agent tool interna funciona sem Agent Teams: o orquestrador invoca subagentes via `Task` tool, passa contexto relevante como argumento, e recebe o resultado como string. O subagente não precisa ser uma instância separada do Claude Code, pode ser um agente especializado definido como skill, com instruções específicas para um domínio.

O padrão que funciona bem é separar responsabilidades por arquivo de instrução no vault:

```yaml
# vault/agents/research-agent.md
---
agent: researcher
tools: [WebSearch, Read, Write]
output_path: vault/inbox/
---

Você é um pesquisador especializado em IA aplicada.
Quando receber um tópico, pesquise 4-6 fontes confiáveis,
extraia os argumentos principais, e salve um relatório estruturado
em vault/inbox/YYYY-MM-DD-[topico].md.

Formato obrigatório:
- Fonte: [URL]
- Argumento central: ...
- Dados ou evidências: ...
- Posição que defende ou contra-argumenta: ...
```

Claude Code lê esse arquivo no início de cada sessão do agente pesquisador. O contexto não está no prompt em linha, está persistido no vault.

---

## Montando a integração na prática

O ponto de entrada é conectar Claude Code ao seu vault Obsidian via MCP (Model Context Protocol) ou simplesmente via leitura direta de arquivos.

A abordagem mais direta é a mais confiável: aponte Claude Code para o vault como diretório de trabalho, ou passe o caminho absoluto do vault como contexto inicial.

```bash
# Inicia Claude Code com o vault como diretório de contexto
cd ~/obsidian/meu-vault
claude "leia vault/agents/orchestrator.md e execute o pipeline de pesquisa sobre [tópico]"
```

Para integração via MCP, o plugin [Claudian para Obsidian](https://github.com/YishenTu/claudian) embute Claude Code diretamente no vault, permitindo que o agente leia e escreva notas enquanto o usuário as visualiza no editor. O repositório [obsidian-mind](https://github.com/breferrari/obsidian-mind) oferece um vault pré-estruturado com hooks, comandos e prompts de subagentes prontos para Claude Code, Codex CLI e Gemini CLI.

Uma CLAUDE.md no root do vault é o ponto de configuração central. Ela instrui o agente sobre a estrutura do vault, como ler as instruções dos subagentes, e as convenções de nomeação de arquivos:

```markdown
# CLAUDE.md — Vault de Agentes

## Estrutura
- vault/agents/: instruções de cada agente especializado
- vault/memory/: contexto persistente entre sessões
- vault/inbox/: material bruto aguardando processamento
- vault/output/: artefatos finalizados

## Convenções
- Sempre registre decisões importantes em vault/memory/decisions.md
- Arquivos de pesquisa: YYYY-MM-DD-[topico]-research.md
- Rascunhos: YYYY-MM-DD-[topico]-draft.md

## Antes de iniciar qualquer tarefa
1. Leia vault/memory/session-log.md (últimas 3 entradas)
2. Verifique vault/memory/decisions.md para contexto de decisões anteriores
3. Confirme o objetivo com o usuário antes de executar
```

---

## Exemplo real: pipeline de pesquisa, escrita e publicação

Este blog opera exatamente com esse padrão. O pipeline completo, quando executado via Claude Code com vault Obsidian como memória, funciona assim:

**Sessão 1: Pesquisa**

O agente pesquisador recebe um tópico, faz 4-6 buscas, extrai dados e argumentos, e salva um relatório estruturado no vault:

```
vault/inbox/2026-04-12-claude-code-obsidian-research.md
```

O arquivo contém fontes, argumentos centrais, posições divergentes e dados específicos. Tudo com URLs rastreáveis.

**Sessão 2: Escrita**

O agente escritor começa lendo o relatório de pesquisa da sessão anterior. Ele também lê `vault/memory/style-guide.md` para carregar as preferências editoriais: tom, estrutura preferida, restrições de escrita. Produz um rascunho salvo em:

```
vault/output/drafts/2026-04-12-claude-code-obsidian-draft.md
```

**Sessão 3: Publicação**

O agente publicador lê o rascunho aprovado, verifica o front matter, gera o slug correto, e publica via GitHub MCP. Registra em `vault/memory/session-log.md`:

```markdown
## 2026-04-12
- [x] Pesquisa: claude-code-obsidian — 5 fontes, Karpathy LLM KB e obsidian-mind
- [x] Rascunho: revisado, 1450 palavras
- [x] Publicado: _posts/2026-04-12-claude-code-obsidian-time-agentes.md
- Decisões: tom mais técnico que tutorial, evitar passo-a-passo detalhado de instalação
```

Na próxima vez que qualquer agente iniciar uma sessão, ele lê esse log e sabe exatamente o que foi feito, como, e por quê. Sem re-explicar. Sem perder contexto.

O padrão é discutido em mais detalhe no post sobre [engenharia de contexto para agentes](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/): o que o agente sabe no momento da decisão determina a qualidade do output, e o vault é o mecanismo que garante que esse "saber" acumula em vez de evaporar.

---

## O que isso resolve de verdade

A vantagem dessa arquitetura não é sofisticação técnica. É durabilidade.

Projetos de agentes falham quando o contexto fica dependente de um único prompt longo mantido na cabeça do desenvolvedor. Quando o desenvolvedor sai de férias, a sessão expira, ou o projeto é retomado depois de três semanas, o sistema degrada.

Um vault Obsidian bem mantido é o oposto disso. Você pode abrir qualquer nota, entender o que estava acontecendo, e retomar de onde parou. O agente pode fazer o mesmo. Claude Code lê o `session-log.md`, carrega as decisões relevantes do `decisions.md`, consulta as instruções do agente específico, e executa com contexto completo.

Essa arquitetura também escala de forma incremental. Você começa com um único agente e um vault simples. Com o tempo, adiciona agentes especializados para domínios novos, cada um com seu próprio arquivo de instruções no vault. A complexidade fica visível, editável, e auditável em vez de estar dispersa em prompts, variáveis de ambiente, e histórico de chat.

Para quem está avaliando se faz sentido construir times de agentes, o post [Por que você deveria parar de criar agentes e começar a criar skills](https://ppablobr.github.io/markdown/blog/pessoal/2026/04/12/pare-de-criar-agentes-comece-com-skills/) oferece o contraponto necessário: nem todo problema precisa de um agente independente. Mas quando precisa, a memória persistente não é um detalhe de implementação. É o que diferencia um time de agentes que funciona de um que você abandona depois de uma semana.
