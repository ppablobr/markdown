---
layout: post
title: "Codex CLI: o agente de terminal da OpenAI e como ele fecha o triângulo dos agentes"
date: 2026-04-14 08:00:00 +0000
categories: ia agentes
tags: [codex-cli, openai, agentes, terminal, cli, context-engineering, claude-code, gemini-cli]
excerpt: "OpenAI lançou o Codex CLI e agora temos três agentes de terminal maduros no mercado. Claude Code, Gemini CLI e Codex CLI — cada um com uma filosofia diferente de execução, contexto e autonomia. Entender as diferenças muda como você monta sua stack de agentes."
---

Em 2025, a OpenAI lançou o Codex CLI com uma proposta simples: um agente de terminal leve, open source, construído em Rust, com os modelos o3 e o4-mini disponíveis diretamente no shell. Sem interface gráfica, sem plataforma extra. Você instala com `npm`, abre o terminal e começa.

Com isso, o mapa dos agentes de terminal ficou completo. Temos agora três players maduros: [Claude Code da Anthropic](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/claude-code-agente-terminal/), [Gemini CLI do Google](https://ppablobr.github.io/markdown/ia/agentes/2026/04/13/gemini-cli-agente-terminal-google/), e o Codex CLI da OpenAI. Cada um com uma filosofia distinta de execução, autonomia e segurança.

Este post cobre o que torna o Codex CLI diferente dos outros dois, como funciona o modelo de sandbox que é sua principal aposta de segurança, e como decidir qual dos três faz mais sentido para o seu fluxo de trabalho.

---

## O que é o Codex CLI na prática

O Codex CLI é um agente de coding que roda localmente no terminal. Ele lê arquivos, executa comandos, escreve código, observa os resultados e itera, sem precisar de uma interface intermediária. O loop é o mesmo que descrevi nos posts anteriores desta série: perceber, raciocinar, agir, observar.

O que diferencia o Codex CLI na superfície é a interface. Ele tem uma TUI (Terminal User Interface) interativa em tela cheia, onde você acompanha as ações do agente em tempo real enquanto ele trabalha. O modo Agent é o padrão: ao iniciar, o CLI já está pronto para ler o repositório, planejar mudanças e executar.

Para instalar:

```bash
npm install -g @openai/codex
codex
```

No macOS, a alternativa via Homebrew também funciona:

```bash
brew install --cask codex
```

A partir daí, você está em sessão interativa. O agente tem acesso ao diretório atual, pode ler e modificar arquivos, executar comandos shell, e retomar sessões anteriores com:

```bash
codex resume --last
# ou
codex resume <SESSION_ID>
```

Persistência de sessão é um diferencial prático. Se você parou uma tarefa longa no meio, pode retomá-la exatamente de onde parou.

---

## O modelo de sandbox: segurança por padrão

O Codex CLI foi construído com uma camada de segurança que os outros CLIs não têm nativamente: um sistema de sandbox configurável que define o que o agente pode e não pode fazer no seu sistema.

A segurança opera em dois níveis independentes que trabalham juntos:

**Sandbox mode** controla o que o agente pode fazer tecnicamente: onde ele pode escrever, se pode acessar a rede, quais comandos pode executar.

**Approval policy** determina quando o agente precisa perguntar antes de agir: antes de sair do sandbox, antes de usar a rede, antes de executar comandos fora de um conjunto confiável.

O modo padrão é `workspace-write`: o agente pode ler arquivos de qualquer lugar, mas só escreve dentro do diretório de trabalho atual, e executa comandos locais sem acesso à rede. É um ponto de equilíbrio entre autonomia e segurança que funciona bem para a maioria dos fluxos de desenvolvimento.

No macOS, o sandboxing usa o framework nativo Seatbelt. No Windows, usa o Windows Sandbox em PowerShell ou o sandbox Linux no WSL2. Não é uma simulação: o agente opera em um ambiente genuinamente restrito.

Para automação em CI, o Codex CLI roda em modo não-interativo por padrão com sandbox read-only:

```bash
codex exec "analisa os arquivos de configuração e lista as dependências desatualizadas"
```

A instrução é: use `danger-full-access` apenas em ambientes isolados e controlados, como um runner de CI dedicado ou container descartável. Nunca em desenvolvimento local com acesso irrestrito.

Esse design responde a uma crítica legítima aos agentes de terminal: quando um agente tem acesso total ao seu sistema, você está apostando na qualidade do modelo para não errar em momentos críticos. O Codex CLI coloca a fronteira de segurança na arquitetura, não só na instrução.

---

## Três modos de aprovação, uma escolha de autonomia

O Codex CLI tem três modos de aprovação que você configura conforme o nível de confiança que quer dar ao agente:

**`suggest`**: o agente propõe ações mas não executa nada sem confirmação explícita. Modo mais conservador, útil para exploração e aprendizado.

**`auto-edit`**: o agente pode editar arquivos livremente, mas precisa de aprovação para executar comandos shell. Bom para tasks de código puro onde você não quer que o agente rode comandos arbitrários.

**`full-auto`**: o agente executa tudo dentro dos limites do sandbox configurado, sem parar para confirmar. É o modo onde o Codex CLI funciona como agente autônomo de verdade.

Na prática, `full-auto` dentro de um sandbox `workspace-write` é o ponto ideal para a maioria dos fluxos: autonomia real, mas limitada ao diretório do projeto e sem acesso à rede.

```bash
# modo full-auto com sandbox padrão
codex --approval-mode full-auto

# modo mais cauteloso, confirma cada ação
codex --approval-mode suggest
```

Essa granularidade importa. Como discuti no [post sobre engenharia de contexto](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/), agentes falham quando a arquitetura em volta deles não foi pensada. Poder configurar exatamente o quanto de autonomia o agente tem é parte dessa arquitetura.

---

## Performance e escolhas de modelo

O Codex CLI roda sobre os modelos o3 e o4-mini da OpenAI. Isso tem implicações diretas na performance em tarefas de código.

Benchmarks de comparação entre os três agentes mostram um quadro interessante. No SWE-bench, Claude Code com Opus 4.6 marca ~80,9%, enquanto o Codex CLI com modelos recentes fica em torno de 80%. A diferença é pequena em tarefas de código puro.

O diferencial do Codex CLI aparece no Terminal-Bench, benchmark focado em tarefas nativas de terminal como scripting, administração de sistema e DevOps: 77,3% contra 65,4% do Claude Code. O Codex CLI lida melhor com tarefas que envolvem comandos de shell, arquivos de configuração de sistema e automação de pipeline.

Em eficiência de tokens, o Codex CLI usa aproximadamente 4x menos tokens por tarefa que o Claude Code. Para quem tem preocupação com custo de API em uso intensivo, essa diferença é relevante.

A janela de contexto padrão é de 200k tokens, menor que o 1 milhão do Claude Code e do Gemini CLI. Para projetos muito grandes, isso impõe limitações reais no que o agente consegue raciocinar em uma única sessão.

---

## MCP e integração com o ecossistema

O Codex CLI suporta o Model Context Protocol, o que significa que ele pode se conectar ao mesmo ecossistema de ferramentas que Claude Code e Gemini CLI usam. Você configura servidores MCP e o agente ganha acesso a ferramentas externas: APIs, bancos de dados, sistemas de CI/CD, o que você precisar.

Há uma capacidade adicional que vai além do suporte básico a MCP: o Codex CLI pode ser exposto como um servidor MCP para outros agentes. Isso abre a possibilidade de usar o Codex CLI como subagente dentro de um pipeline orquestrado via [OpenAI Agents SDK](https://developers.openai.com/codex/guides/agents-sdk).

Na prática, isso significa:

```bash
# Codex CLI como servidor MCP para outro agente
codex serve --mcp-port 3000
```

Um agente orquestrador pode então delegar tarefas específicas de código para o Codex CLI como uma ferramenta especializada, enquanto mantém controle do fluxo maior. Para quem está construindo pipelines de múltiplos agentes, essa é uma capacidade importante que os outros CLIs não têm nativamente.

Para workfows de subagentes, o Codex CLI só cria subagentes quando você pede explicitamente. Não é um comportamento automático. Isso é intencional: controle previsível sobre o que o agente faz.

---

## Comparando os três agentes de terminal

Com os três agentes no mercado, a pergunta mais frequente é: qual usar? A resposta honesta é que depende do que você está tentando fazer.

| | Claude Code | Gemini CLI | Codex CLI |
|---|---|---|---|
| Contexto | 1M tokens | 1M tokens | 200k tokens |
| Modelo base | Claude Opus/Sonnet | Gemini 2.5 Pro | o3 / o4-mini |
| Open Source | Sim | Sim (Apache 2.0) | Sim (Apache 2.0) |
| Sandbox nativo | Limitado | Limitado | Sim (Seatbelt/WSL2) |
| Google Search nativo | Via MCP | Sim | Via MCP |
| Subagente / MCP server | Não | Não | Sim |
| Terminal-Bench | 65,4% | N/A | 77,3% |
| SWE-Bench | ~80,9% | N/A | ~80% |
| Custo base | Assinatura | Gratuito (limites) | Assinatura |

**Claude Code** continua sendo a melhor opção para tarefas que demandam raciocínio profundo sobre codebases complexos, refatoração arquitetural e debugging de lógica de negócio intrincada. A janela de 1 milhão de tokens e a qualidade do raciocínio do Opus são difíceis de bater para esse perfil de tarefa.

**Gemini CLI** é a porta de entrada mais acessível: gratuito, com Google Search nativo, e com janela de 1 milhão de tokens. Para equipes explorando o que agentes de terminal podem fazer antes de comprometer budget, é o ponto de partida natural.

**Codex CLI** se destaca em tarefas de terminal nativas, DevOps, automação de pipeline, e qualquer workflow onde segurança por sandbox seja um requisito. O suporte a ser exposto como servidor MCP também o torna mais adequado para arquiteturas de múltiplos agentes.

---

## Podem ser usados juntos?

Sim, e essa é uma das perguntas mais relevantes à medida que workflows de agentes ficam mais complexos.

Os três CLIs suportam MCP, o que significa que podem compartilhar o mesmo ecossistema de ferramentas. Você pode ter um agente orquestrador que delega tarefas específicas para cada CLI conforme a especialidade:

- Codex CLI como subagente para scripts de DevOps e automação de terminal
- Claude Code para análise profunda de código e refatoração
- Gemini CLI para pesquisa e tarefas que precisam de informação atualizada da web

Isso se encaixa no conceito de [skills antes de agentes](https://ppablobr.github.io/markdown/blog/pessoal/2026/04/12/pare-de-criar-agentes-comece-com-skills/): cada ferramenta funciona melhor quando tem um papel bem definido na arquitetura, não quando tenta resolver tudo.

A questão prática de usar os três em conjunto é custo e complexidade operacional. Cada agente consome tokens de APIs diferentes. Gerenciar três contextos distintos numa pipeline exige cuidado com o que cada agente sabe e o que cada um faz. Mas para casos de uso específicos, a composição vale o overhead.

---

## O que o triângulo completo significa para você

Com Claude Code, Gemini CLI e Codex CLI no mercado, o espaço de agentes de terminal atingiu uma maturidade que não existia há dois anos. Cada ferramenta representa uma aposta diferente sobre o que importa mais num agente: profundidade de raciocínio, integração com ecossistema, ou segurança e composabilidade.

A pergunta que o [debate sobre agentes vs. otimização de processos](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/) coloca ainda é válida aqui: a ferramenta não resolve um processo mal definido. Codex CLI com sandbox impecável em cima de uma tarefa mal especificada vai produzir um resultado mal especificado com mais segurança.

O que mudou é que agora você tem opções reais para cada camada do problema. A [história da IA que levou até aqui](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/a-historia-da-ia-que-levou-aos-agentes/) é, em parte, a história de como chegamos a ter agentes de terminal capazes o suficiente para que a escolha entre eles seja uma decisão de arquitetura, não de disponibilidade.

Escolha com base no que você está construindo, não no nome da empresa por trás da ferramenta.

---

*Esta é a terceira parte da série sobre agentes de terminal. Os posts anteriores cobriram [Claude Code](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/claude-code-agente-terminal/) e [Gemini CLI](https://ppablobr.github.io/markdown/ia/agentes/2026/04/13/gemini-cli-agente-terminal-google/). O próximo post da série vai entrar em estratégias práticas de context engineering para agentes que operam por períodos longos.*
