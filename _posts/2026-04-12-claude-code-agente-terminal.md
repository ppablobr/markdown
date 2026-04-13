---
layout: post
title: "Claude Code como agente de terminal: o que muda quando a IA executa, não só responde"
date: 2026-04-12 08:00:00 +0000
categories: ia agentes
tags: [claude-code, agentes, terminal, cli, context-engineering, ferramentas]
excerpt: "Claude Code não é um assistente de chat com superpoderes. É um agente que lê arquivos, executa comandos, abre PRs e corrige erros em loop. Entender essa diferença muda como você trabalha com ele."
---

Você abre o terminal, digita `claude` e começa a conversar. Parece um chat. Mas o que está acontecendo por baixo é fundamentalmente diferente de uma janela de chat com um LLM.

Claude Code não espera você copiar e colar código. Ele lê seus arquivos diretamente. Executa comandos. Roda testes. Observa o output. Decide o próximo passo. E repete esse ciclo até a tarefa estar feita ou até você dizer para parar.

Isso importa para como você usa a ferramenta. Uma conversa de chat pede clareza e especificidade na pergunta. Um agente de terminal pede objetivo claro, limites definidos e um ambiente que não vai explodir se ele tomar uma decisão errada.

---

## O loop que o torna diferente

Todo agente de IA moderno opera numa estrutura básica: percebe o ambiente, raciocina sobre o que fazer, age, observa o resultado da ação, e recomeça. O que distingue Claude Code de um LLM numa janela de chat é que esse loop acontece com ferramentas reais conectadas ao seu ambiente de desenvolvimento.

As ferramentas disponíveis por padrão incluem leitura e escrita de arquivos, execução de comandos bash, busca por padrões em código (equivalente a um grep sofisticado), navegação de diretórios e acesso a contexto do repositório git. Quando você pede "refatora essa função para aceitar configuração via arquivo YAML", ele não escreve o código e te entrega. Ele lê o arquivo atual, escreve a nova versão, roda os testes, lê o output, corrige o que quebrou, e só então te pergunta se está satisfatório.

Isso tem um corolário importante: erros custam mais. Num chat, uma resposta ruim é jogada fora. Num agente com ferramentas, uma decisão ruim pode sobrescrever arquivos, executar comandos indesejados, ou criar um estado intermediário difícil de reverter. Controle de versão ativo antes de qualquer sessão longa com Claude Code não é paranoia, é protocolo básico.

---

## Como estruturar tarefas para um agente de terminal

A diferença entre uma sessão produtiva e uma sessão frustrante com Claude Code tem menos a ver com o modelo e mais com como a tarefa é apresentada.

**Objetivos vagos produzem iterações longas.** "Melhora esse código" dispara uma série de perguntas ou, pior, uma série de mudanças que não eram o que você queria. "Extrai a lógica de autenticação desta função para um módulo separado em `src/auth/`, mantendo a assinatura pública existente e todos os testes passando" é executável diretamente.

**Escopo implícito virou escopo do agente.** Quando você pede para corrigir um bug, Claude Code vai seguir os imports, ler os arquivos relacionados, e possivelmente tocar em código que você não pretendia alterar. Isso pode ser útil ou pode ser um problema. Definir explicitamente o que está fora do escopo ("não altere os arquivos de configuração nem as interfaces públicas") economiza revisões.

**Estado do repositório importa mais do que parece.** O agente usa o estado atual dos arquivos como parte do seu contexto. Um repositório com mudanças não commitadas misturadas com código estável é um contexto confuso. Commits limpos por feature, antes de uma sessão de trabalho com Claude Code, produzem resultados mais previsíveis.

---

## Padrões que funcionam na prática

A seguir, três padrões de uso que aparecem com frequência em times que já integraram Claude Code no workflow.

### Exploração de codebase desconhecida

Entrar num repositório novo e entender como as peças se encaixam é uma das tarefas que consome mais tempo de onboarding. Claude Code resolve isso com uma instrução direta:

```bash
claude "Leia a estrutura deste repositório e me explica: qual é o fluxo de uma requisição HTTP desde o ponto de entrada até a resposta? Quais são os principais módulos e como eles se comunicam?"
```

O agente vai navegar os diretórios, ler os arquivos relevantes, seguir os imports, e construir uma explicação fundamentada no código real, não em suposições. Em repositórios de tamanho médio, isso produz um mapa útil em minutos.

### Ciclo de desenvolvimento com testes

O padrão mais produtivo para desenvolvimento com Claude Code é ancorar cada tarefa nos testes:

```bash
claude "Implementa a função `parseConfig` em src/config.ts seguindo a especificação no arquivo SPEC.md. Os testes já existem em src/config.test.ts. Faz os testes passarem sem modificar os arquivos de teste."
```

Com essa instrução, o agente tem critério de sucesso verificável. Ele vai escrever a implementação, rodar os testes, ler os erros, ajustar o código, e repetir até os testes passarem. O que você recebe no final é código que demonstravelmente funciona conforme especificado.

Isso é um caso de [context engineering aplicado](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/): os testes são a especificação executável. O agente não precisa inferir o que "correto" significa porque tem um oráculo objetivo.

### Revisão e padronização

Para times que precisam manter consistência de código:

```bash
claude "Revisa todos os arquivos em src/handlers/ e identifica: quais não seguem o padrão de tratamento de erro definido em src/errors/base.ts? Lista os arquivos e a discrepância específica em cada um. Não modifica nada ainda."
```

A instrução "não modifica nada ainda" é importante. Separar a fase de diagnóstico da fase de execução permite revisar o plano antes de aplicar mudanças em vários arquivos simultaneamente.

---

## MCP e ferramentas externas: ampliando o ambiente do agente

O Model Context Protocol é o mecanismo pelo qual Claude Code se conecta a sistemas externos além do sistema de arquivos local. Com servidores MCP configurados, o agente pode interagir com APIs, bancos de dados, ferramentas de CI/CD, e qualquer sistema que tenha um servidor MCP disponível.

A lógica é direta: um servidor MCP expõe um conjunto de ferramentas com nomes e descrições em linguagem natural. O agente decide quando e como usar cada ferramenta baseado na tarefa em andamento. Do ponto de vista do agente, a ferramenta "busca no banco de dados de clientes" funciona da mesma forma que a ferramenta "lê arquivo do sistema de arquivos": é uma ação disponível com um resultado observável.

Para configurar um servidor MCP no Claude Code, você adiciona a entrada no arquivo de configuração global (`~/.claude.json`) ou no arquivo de projeto (`.claude/settings.json`):

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "seu_token_aqui"
      }
    }
  }
}
```

Com o servidor do GitHub configurado, o agente pode criar issues, abrir PRs, listar commits e verificar o status de workflows diretamente da sessão de terminal, sem você precisar navegar para a interface web.

A relação entre MCP e context engineering é direta: cada ferramenta MCP é uma fonte de informação dinâmica que pode entrar no contexto do agente. O que entra, quando entra e em que formato são decisões de arquitetura, não defaults automáticos. Um servidor MCP mal configurado que retorna payloads gigantes em cada chamada vai degradar o contexto do agente da mesma forma que [um contexto cheio de informação irrelevante](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/).

---

## O arquivo CLAUDE.md: contexto persistente entre sessões

Uma das características mais práticas do Claude Code é o arquivo `CLAUDE.md` na raiz do repositório. Qualquer instrução nesse arquivo é automaticamente incluída no contexto do agente em toda sessão naquele projeto.

Isso resolve um problema concreto: sem memória persistente entre sessões, você repetiria as mesmas instruções de contexto toda vez que iniciasse uma nova sessão. Com `CLAUDE.md`, essas instruções ficam no repositório e se aplicam automaticamente.

Um `CLAUDE.md` útil tipicamente inclui:

```markdown
## Comandos essenciais
- `npm test` para rodar a suite de testes
- `npm run lint` para checar estilo de código
- `npm run build` para build de produção

## Padrões de código
- Todas as funções públicas precisam de JSDoc
- Tratamento de erros segue o padrão em src/errors/base.ts
- Nunca commitar diretamente em main

## Contexto do projeto
- Este é um serviço de processamento de pagamentos
- Dados de PII nunca devem ser logados
- Rate limiting está implementado no middleware, não nos handlers
```

O efeito prático é que o agente já sabe como rodar os testes, qual é o padrão de erro e quais são as restrições do domínio sem você precisar explicar a cada sessão. Isso é [context engineering aplicado ao nível de projeto](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/): informação relevante disponível no momento certo, sem sobrecarga.

---

## Limites reais e onde o agente erra

Claude Code funciona bem quando o objetivo é claro, o ambiente é estável e existe um critério de sucesso verificável. Onde ele tende a produzir resultados ruins:

**Tarefas de design de alto nível sem especificação.** "Projeta a arquitetura do módulo de autenticação" produz uma proposta plausível mas não necessariamente adequada ao seu contexto. Para decisões de arquitetura, use o agente para explorar e documentar o que já existe, não para decidir o que não existe ainda.

**Refatorações amplas sem testes.** Quando não há critério de sucesso automatizado, o agente não tem como saber se o resultado de uma refatoração grande está correto. A tendência é produzir código que compila mas que pode ter mudado comportamento em casos não cobertos.

**Ambientes com side effects perigosos.** Se o agente tem acesso a comandos que afetam produção, bancos de dados reais ou sistemas externos sem reversibilidade, o custo de um erro sobe dramaticamente. A regra dos [deployments de LLM em produção](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/) se aplica aqui também: você precisa saber quando ele está errando, antes que o erro acumule.

---

## Um workflow concreto para começar

Para quem está começando a usar Claude Code de forma estruturada:

1. Inicialize o repositório com um `CLAUDE.md` descrevendo os comandos de desenvolvimento e as restrições do projeto.
2. Antes de qualquer sessão longa, garanta que o repositório está em estado limpo (sem mudanças não commitadas que não sejam intencionais).
3. Formule tarefas com objetivo, escopo e critério de sucesso. "Implementa X em Y, os testes estão em Z, não altera A nem B" é o formato.
4. Para tarefas que afetam múltiplos arquivos, peça primeiro um plano antes de executar: "antes de modificar qualquer coisa, descreve o que você pretende fazer e quais arquivos serão afetados".
5. Monitore o output dos testes em cada iteração. Se os testes não estão passando depois de algumas tentativas, pare e avalie se o problema é de especificação, não de execução.

O que separa times que extraem valor real do Claude Code daqueles que ficam frustrados não é o modelo. É o quanto de cuidado foi colocado em estruturar o ambiente e as tarefas antes de deixar o agente executar, que é a mesma lógica do [debate entre agentes e otimização de processos](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/): a ferramenta amplifica o que está lá.

---

*Este post faz parte de uma série sobre desenvolvimento prático de agentes de IA. Você pode seguir para o post sobre [engenharia de contexto](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/) para entender a arquitetura de informação por trás de agentes confiáveis, ou voltar para o [debate sobre quando usar agentes](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/) se a decisão ainda está em aberto.*
