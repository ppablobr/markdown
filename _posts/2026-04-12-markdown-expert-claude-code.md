---
layout: post
title: "Claude Code para quem domina Markdown: o agente que você já sabe como instruir"
date: 2026-04-12 10:00:00 +0000
categories: ia agentes
tags: [claude-code, markdown, agentes, produtividade, conhecimento]
image: https://res.cloudinary.com/dgqhglwo5/image/upload/v1776045756/blog/covers/blog/covers/markdown-expert-claude-code-infografico.jpg
excerpt: "Você não precisa saber programar para trabalhar com agentes de IA. Quem domina Markdown já pensa da forma certa: contexto em texto estruturado, instruções claras, critérios explícitos. O agente precisa exatamente disso."
---

<figure>
  <img src="https://res.cloudinary.com/dgqhglwo5/image/upload/v1776045756/blog/covers/blog/covers/markdown-expert-claude-code-infografico.jpg"
       alt="Infográfico: Claude Code para Markdown Experts — fluxo de trabalho em 3 etapas e 4 casos de uso"
       loading="lazy"
       style="width:100%;border-radius:4px;">
  <figcaption>Fluxo completo: de CLAUDE.md às instruções estruturadas até o agente executar.</figcaption>
</figure>

A narrativa padrão sobre Claude Code coloca ele numa categoria específica: ferramenta para desenvolvedores de software. Terminal, repositórios, testes automatizados, refatoração de código. Se você não escreve Python nem JavaScript, parece que não é pra você.

Essa narrativa está errada.

Quem trabalha com Markdown como ferramenta central de produção, escritores, documentadores, criadores de conteúdo, gestores de conhecimento, pesquisadores, já tem a habilidade mais importante para trabalhar com agentes de IA: sabe estruturar informação em texto. Isso não é detalhe. É o núcleo de tudo.

---

## Por que Markdown é a linguagem dos agentes

Um agente de IA não precisa de código. Precisa de contexto. E contexto, na prática, é texto estruturado.

Quando você escreve um documento Markdown bem organizado, com títulos hierárquicos, listas de itens, blocos de código, links e front matter, você está fazendo exatamente o que um engenheiro de prompts faz para um agente. Está separando o que é instrução do que é dado. Está sinalizando hierarquia com formatação. Está tornando a informação parseável para qualquer sistema que leia texto.

LLMs foram treinados em proporções gigantescas de conteúdo Markdown. Arquivos `.md` no GitHub, documentação técnica, posts em fóruns com formatação Rica, wikis. A estrutura do Markdown é legível por modelos com uma fidelidade muito maior do que texto corrido ou formatos proprietários como `.docx`. Um documento Markdown bem escrito chega ao modelo com sua hierarquia de informação intacta.

O que isso significa na prática: quando você passa para Claude Code um documento Markdown com instruções claras, ele lê essa estrutura e entende o que é regra, o que é exemplo, o que é escopo, o que é restrição. Você não precisa traduzir para "prompt engineering". Você já faz isso quando escreve documentação.

---

## O CLAUDE.md: seu documento de controle

O arquivo `CLAUDE.md` é onde Claude Code busca as instruções persistentes para um projeto. Toda vez que você abre o Claude Code num diretório, ele lê esse arquivo. É o system prompt do agente para aquele contexto específico.

Adivinha o formato? Markdown puro.

Quem tem experiência com Markdown para documentação já sabe instintivamente como estruturar esse arquivo. Você escreve seções com `##` para separar responsabilidades. Usa listas para enumerar regras. Usa blocos de código para mostrar exemplos de comandos. Usa links internos para conectar ao restante da documentação do projeto.

Um `CLAUDE.md` bem escrito para um vault Obsidian poderia ter esta estrutura:

```markdown
## Contexto do projeto

Este é um vault de gestão de conhecimento pessoal no Obsidian.
Estrutura: `notas/`, `projetos/`, `referências/`, `diário/`.
Front matter padrão em todas as notas: `tags`, `criado`, `atualizado`, `status`.

## Convenções de nomenclatura

- Notas: `YYYY-MM-DD-titulo-em-kebab-case.md`
- Projetos: `projeto-nome-do-projeto.md` na pasta `projetos/`
- Nunca altere arquivos em `arquivo/` sem confirmação explícita

## O que você pode fazer sem pedir permissão

- Ler qualquer arquivo do vault
- Criar notas novas em `notas/` seguindo as convenções acima
- Adicionar ou corrigir front matter em arquivos existentes

## O que sempre requer confirmação

- Mover ou renomear arquivos existentes
- Deletar qualquer arquivo
- Alterar a estrutura de pastas
```

Isso não é código. É documentação. Qualquer pessoa que escreve documentação técnica ou mantém um wiki reconhece essa estrutura imediatamente.

A lógica é a mesma da [engenharia de contexto](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/): o que você coloca no `CLAUDE.md` define o que o agente sabe antes da primeira mensagem. Regras fixas, convenções do projeto, limites de atuação. Quanto mais preciso e bem estruturado esse arquivo, menos você precisa repetir instruções a cada sessão.

---

## Como dar tarefas ao agente: objetivos, escopo, critério de sucesso

A diferença entre uma sessão produtiva com Claude Code e uma sessão frustrante está quase sempre na qualidade da instrução inicial. Não na capacidade do modelo.

Um bom pedido tem três componentes. Objetivo claro do que deve ser feito. Escopo explícito do que está em jogo e do que está fora do limite. Critério de sucesso que permite verificar o resultado.

Quem escreve especificações técnicas, tickets de produto ou procedimentos operacionais já conhece esse formato. É o mesmo que você usa para delegar trabalho para um colaborador que não tem contexto implícito.

Exemplos concretos:

**Ruim:**
> Organiza minhas notas.

**Bom:**
> Leia todos os arquivos `.md` em `notas/raw/`. Para cada arquivo que não tenha front matter, adicione o bloco com os campos `tags: []`, `criado: YYYY-MM-DD` (use a data de criação do arquivo), `status: rascunho`. Não altere o conteúdo do corpo do documento. Ao final, liste quais arquivos foram modificados.

A diferença não é só de clareza. É de executabilidade. O segundo pedido tem objetivo (adicionar front matter), escopo (apenas `notas/raw/`, apenas arquivos sem front matter, não tocar no corpo), e critério de sucesso (lista de arquivos modificados para conferência).

Isso é Markdown thinking aplicado a delegação. Você decompõe a tarefa, torna os limites explícitos, define o output esperado.

---

## Casos de uso práticos para quem trabalha com texto

Estes são fluxos reais, não conceitos abstratos. Cada um pode ser executado por alguém que sabe Markdown e tem Claude Code instalado, sem nenhum conhecimento de programação.

**Padronizar front matter em massa**

Você tem 200 notas Obsidian com front matter inconsistente. Alguns arquivos usam `tags`, outros usam `tag`, outros não têm nenhum. Você escreve a especificação do front matter padrão num parágrafo e pede ao Claude Code para aplicar em todos os arquivos de uma pasta, relatando exceções.

**Gerar índices e tabelas de conteúdo**

Você tem uma pasta de referências ou de artigos e quer um arquivo `README.md` com uma tabela linkando todos os documentos, com título, data e tags extraídos do front matter. Claude Code lê todos os arquivos, extrai os campos, gera a tabela em Markdown e salva o arquivo.

**Reformatar documentos para um padrão**

Você recebeu 50 relatórios exportados de uma ferramenta externa com uma estrutura inconsistente. Você descreve a estrutura alvo em Markdown, mostra um exemplo de como o documento deve ficar, e pede para o agente aplicar a transformação em todos os arquivos de uma pasta.

**Validar estrutura de vault**

Você define as regras do seu vault num arquivo de texto: toda nota deve ter front matter com `status`, todo projeto deve ter uma seção `## Próximos passos`, todo arquivo em `arquivo/` deve ter `status: arquivado`. O agente varre o vault, lista as violações, e você decide o que corrigir.

**Criar templates a partir de exemplos**

Você tem três notas de reunião bem escritas que seguem um padrão que você gosta. Você passa os três arquivos para o Claude Code e pede para ele gerar um template Markdown que capture a estrutura comum, com placeholders claros para as partes variáveis.

**Extrair e consolidar informação**

Você tem diário de bordo de projeto em arquivos diários separados por data. Você pede ao Claude Code para extrair todas as linhas que começam com `- [ ]` (tarefas abertas) e `- [x]` (tarefas concluídas) dos últimos 30 dias, consolidar num único arquivo com seções por semana, e calcular a proporção de tarefas concluídas por semana.

---

## Workflows em texto: descreva o processo, deixe o agente executar

Uma das aplicações mais poderosas para quem trabalha com gestão de conhecimento é transformar workflows repetitivos em instruções persistentes.

A lógica é simples. Se você faz o mesmo processo toda semana, como processar notas brutas, publicar um post, ou preparar um relatório semanal, você pode escrever esse processo em Markdown e deixar o Claude Code executar cada vez que precisar.

Um workflow de publicação de post poderia ser um arquivo `workflows/publicar-post.md` com este conteúdo:

```markdown
## Publicar novo post

### Entrada
- Arquivo de rascunho em `_drafts/` fornecido pelo usuário

### Passos

1. Ler o rascunho e verificar se tem front matter completo
   - Campos obrigatórios: `title`, `date`, `categories`, `tags`, `excerpt`
   - Se faltar algum campo, perguntar ao usuário antes de continuar

2. Verificar convenções de estilo
   - Nenhum travessão "—" no texto
   - Todos os blocos de código com linguagem especificada
   - Pelo menos um link interno para post relacionado

3. Gerar nome de arquivo no formato `YYYY-MM-DD-slug.md`
   - Slug derivado do título em kebab-case
   - Data a partir do campo `date` no front matter

4. Mover o arquivo para `_posts/` com o nome gerado

5. Reportar o caminho do arquivo criado e listar qualquer problema encontrado
```

Isso é um SOP (procedimento operacional padrão) em Markdown. Quem trabalha com documentação de processos reconhece o formato. A diferença é que agora você entrega esse documento ao Claude Code e diz "executa o workflow de publicar post para este rascunho", e ele segue cada passo.

Este é exatamente o modelo que o post sobre [Claude Code como agente de terminal](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/claude-code-agente-terminal/) descreve: objetivo claro, escopo definido, loop de execução supervisionado. Só que aqui, a pessoa que escreveu o workflow não é um desenvolvedor. É alguém que sabe estruturar processos em texto.

---

## O que o agente faz bem e onde precisa de supervisão

Honestidade sobre limites é parte de trabalhar bem com qualquer ferramenta.

Claude Code opera bem em tarefas que têm estrutura clara, critérios verificáveis e baixo custo de reversão. Transformações em massa de arquivos Markdown se encaixam perfeitamente: o resultado é sempre um arquivo de texto que você pode abrir e conferir, o git mantém histórico de tudo, e erros são visíveis imediatamente.

Algumas zonas que requerem mais atenção:

**Inferências sobre intenção.** Quando a instrução é ambígua, o agente infere. Essa inferência pode ser errada. "Padroniza o estilo das minhas notas" abre espaço para interpretações sobre o que é "padrão" que podem não ser as suas. Especificidade na instrução inicial resolve isso, mas requer que você saiba o que quer antes de pedir.

**Volume de arquivos sem conferência intermediária.** Pedir para processar 500 arquivos de uma vez sem checkpoints intermediários aumenta o risco de um erro se propagar. Melhor pedir em lotes de 20 ou 30 e conferir antes de continuar. Isso vale especialmente quando o agente está modificando arquivos existentes, não criando novos.

**Estrutura de pastas e arquivos.** Claude Code lê e escreve arquivos com facilidade. Mas operações destrutivas, como deletar, mover em massa ou reestruturar pastas, requerem confirmação explícita no `CLAUDE.md` ou no pedido. O arquivo `CLAUDE.md` é o lugar certo para escrever "nunca delete arquivos sem confirmação explícita".

**Decisões editoriais.** O agente pode reformatar, reorganizar e padronizar. Ele não sabe o que você quer dizer. Revisão editorial, escolhas de argumento, coerência de voz ao longo de um longo documento: essas continuam sendo decisões humanas.

O padrão que funciona é usar o agente para as partes mecânicas do trabalho com texto, tudo que é transformação, validação, extração e geração baseada em regras claras, e manter o julgamento editorial e as decisões de conteúdo consigo.

---

## A vantagem do markdown expert

Há um padrão nos workflows de agentes que falham: a instrução é vaga, o escopo é implícito, o critério de sucesso não existe. O modelo fica adivinhando o que você quer e produz algo genérico.

Quem trabalha com Markdown profissionalmente desenvolve um reflexo oposto. Você aprende a tornar estrutura explícita porque sabe que texto não estruturado é texto que não será encontrado, não será mantido, não será entendido por quem vier depois. Esse mesmo reflexo é o que torna a colaboração com agentes mais produtiva.

Você já sabe como escrever um `CLAUDE.md`. Já sabe como especificar um workflow. Já sabe como descrever uma transformação de documento com critério de verificação. Você só precisa aprender que o destinatário dessas instruções agora é um agente que vai executar, não um colega que vai ler.

A diferença é menor do que parece.

---

*Este post faz parte de uma série sobre agentes de IA em produção. Os posts anteriores cobrem [por que agentes não substituem otimização de processos](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/agentes-vs-otimizacao-de-processos/) e [como a engenharia de contexto determina o que o agente executa](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/context-engineering-agentes/).*
