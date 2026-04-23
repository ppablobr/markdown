---
layout: post
title: "Project Glasswing: Como a Anthropic Está Usando IA para Defender o Software Crítico do Mundo"
date: 2026-04-23 10:00:00 +0000
categories: ia seguranca
tags: [anthropic, glasswing, ciberseguranca, claude, vulnerabilidades, zero-day]
excerpt: "A Anthropic lançou o Project Glasswing com o modelo Claude Mythos Preview para encontrar e corrigir vulnerabilidades zero-day em sistemas críticos. Entenda o que muda para a segurança digital."
image: ""
---

Em 7 de abril de 2026, a Anthropic fez dois anúncios simultâneos que raramente aparecem juntos: apresentou um modelo de IA mais capaz do que qualquer coisa que a empresa tinha lançado antes, e declarou que não vai disponibilizá-lo ao público geral. O motivo é direto. O Claude Mythos Preview é capaz de identificar e explorar vulnerabilidades zero-day em sistemas operacionais e browsers com uma eficiência que supera todos os hackers humanos, exceto os mais especializados do planeta. Tornar isso disponível indiscriminadamente seria, nas palavras implícitas da própria decisão, irresponsável.

O Project Glasswing é a estrutura construída em torno dessa capacidade. Doze organizações foram selecionadas como parceiras de lançamento: Amazon Web Services, Anthropic, Apple, Broadcom, Cisco, CrowdStrike, Google, JPMorganChase, Linux Foundation, Microsoft, NVIDIA e Palo Alto Networks. Outras [40 organizações responsáveis por infraestrutura de software crítico](https://www.infosecurity-magazine.com/news/anthropic-launch-project-glasswing/) também receberam acesso. O objetivo declarado é usar o Mythos Preview para encontrar e corrigir vulnerabilidades antes que atacantes as descubram por conta própria.

A Anthropic comprometeu $100 milhões em créditos de uso do modelo para essa iniciativa, mais $4 milhões em doações diretas: $2,5 milhões para Alpha-Omega e OpenSSF via Linux Foundation, e $1,5 milhão para a Apache Software Foundation. A lógica é equipar os mantenedores de software open source com recursos para responder a um cenário em que a detecção de vulnerabilidades ficou dramaticamente mais rápida.

## O que o Claude Mythos Preview consegue fazer

Os resultados divulgados pela Anthropic são concretos. Nas semanas que antecederam o lançamento, o Mythos Preview identificou [milhares de vulnerabilidades zero-day](https://www.anthropic.com/glasswing) em todos os principais sistemas operacionais e browsers. Muitas eram críticas. Algumas tinham décadas.

O caso mais citado é uma vulnerabilidade de execução remota de código no FreeBSD com 17 anos de existência, catalogada como CVE-2026-4747. O modelo a identificou e explorou de forma totalmente autônoma, sem intervenção humana além da instrução inicial. A falha permite que um atacante não autenticado obtenha acesso root ao servidor.

Outro número que circulou na cobertura especializada: no benchmark Firefox 147 exploit, o Mythos produziu [181 exploits funcionais contra 2 do Claude Opus 4.6](https://foreignpolicy.com/2026/04/20/claude-mythos-preview-anthropic-project-glasswing-cybersecurity-ai-hacking-danger/). Uma diferença de 90x entre modelos da mesma empresa em intervalos de meses. Isso não é uma evolução gradual de capacidade. É um salto qualitativo.

A Anthropic [reconhece publicamente](https://red.anthropic.com/2026/mythos-preview/) que os avanços que tornam o Mythos melhor em identificar vulnerabilidades são os mesmos que o tornariam perigoso nas mãos erradas. A distinção entre encontrar uma falha para corrigi-la e encontrá-la para explorá-la não existe no nível do modelo. Existe apenas no nível do acesso.

## Como o Glasswing funciona na prática

A estrutura operacional do Glasswing é baseada em acesso controlado. A Anthropic não disponibilizou o Claude Mythos Preview na API pública nem via Claude.ai. O modelo é acessado exclusivamente pelos parceiros autorizados, em ambientes específicos, para tarefas de segurança defensiva: varredura de sistemas próprios, análise de software mantido pelos parceiros e contribuição para correções no ecossistema open source.

A lógica é de vantagem temporal. Defensores com acesso ao Mythos Preview conseguem encontrar e corrigir vulnerabilidades mais rápido do que atacantes que ainda dependem de métodos manuais ou de modelos menos capazes. O investimento de [$100 milhões em créditos de uso](https://venturebeat.com/technology/anthropic-says-its-most-powerful-ai-cyber-model-is-too-dangerous-to-release/) e $4 milhões em doações a projetos open source de segurança serve para reduzir a barreira de adoção entre os parceiros e financiar melhorias em software de infraestrutura que está fora do escopo comercial direto das empresas participantes.

A Anthropic também anunciou que planeja incorporar salvaguardas desenvolvidas no contexto do Mythos Preview ao próximo modelo Claude Opus, tornando-as disponíveis mais amplamente em um modelo com perfil de risco diferente. O Glasswing é, em parte, um laboratório de onde sairão as regras de segurança para os modelos seguintes.

## O incidente: acesso não autorizado ao modelo mais restrito da Anthropic

Em 21 de abril de 2026, [reportagens do TechCrunch e Bloomberg](https://techcrunch.com/2026/04/21/unauthorized-group-has-gained-access-to-anthropics-exclusive-cyber-tool-mythos-report-claims/) revelaram que um pequeno grupo de usuários não autorizados havia acessado o Claude Mythos Preview. O método foi simples: o grupo, articulado via um canal privado no Discord dedicado a rastrear modelos de IA não lançados, deduziu o endereço do modelo a partir das convenções de nomenclatura de URLs que a Anthropic usa em outros modelos.

A dedução foi auxiliada por informações obtidas em uma brecha de dados na empresa de recrutamento Mercor Inc., combinadas com credenciais de pelo menos um funcionário de um contratado terceirizado com acesso autorizado. O grupo compartilhou capturas de tela e demonstrações ao vivo com veículos de imprensa antes da resposta oficial da Anthropic.

A Anthropic confirmou estar investigando o incidente e afirmou não ter evidências de que o acesso não autorizado afetou sistemas centrais da empresa além do ambiente do fornecedor. O acesso ao Mythos Preview foi encerrado para as credenciais comprometidas.

A ironia é direta: um modelo criado para identificar falhas de segurança ficou exposto por uma falha de controle de acesso. O incidente não invalida a proposta do Glasswing, mas demonstra que a segurança de uma iniciativa como essa depende tanto de processos operacionais dos parceiros quanto das capacidades técnicas do modelo. Um modelo "não disponível ao público" que opera via API ainda tem uma superfície de acesso que inclui todos os contratados e sistemas dos parceiros.

## O paradoxo ético de um modelo ofensivo para fins defensivos

O núcleo do debate em torno do Glasswing é técnico e filosófico ao mesmo tempo. As capacidades que tornam o Claude Mythos Preview útil para defensores, identificar e explorar vulnerabilidades de forma autônoma, são exatamente as capacidades que o tornam perigoso em contextos não controlados.

[Bruce Schneier](https://www.schneier.com/blog/archives/2026/04/on-anthropics-mythos-preview-and-project-glasswing.html), um dos pesquisadores mais respeitados em segurança digital, reconheceu que o Glasswing é "em muitos aspectos, exatamente o tipo de divulgação responsável que pesquisadores de segurança pedem há anos". Ao mesmo tempo, levantou questões estruturais: cinquenta organizações, por mais bem escolhidas que sejam, não substituem a expertise distribuída de toda a comunidade de pesquisa. E uma empresa privada está tomando decisões unilaterais sobre quais pedaços da infraestrutura global crítica são defendidos primeiro e quais precisam esperar.

[A preocupação com governos e atores estatais](https://stateofsurveillance.org/news/anthropic-mythos-project-glasswing-zero-day-ai-surveillance-2026/) é estrutural. Enquanto a Anthropic limita acesso ao Mythos para garantir uso defensivo, nada impede que governos com recursos desenvolvam modelos equivalentes sem as mesmas restrições. A assimetria entre quem tem acesso ao modelo da Anthropic e quem pode construir alternativas independentes é real, mas temporária.

Esse dilema não é exclusivo da IA. Vulnerabilidades zero-day são compradas e vendidas em mercados cinzas há décadas. Ferramentas de pen testing legítimas são usadas por equipes de red team e por atacantes com os mesmos binários. O que o Mythos muda é a escala e a velocidade: o que antes exigia uma equipe de especialistas experientes pode agora ser executado de forma autônoma em paralelo em centenas de sistemas.

A questão central não é se a Anthropic deveria ou não ter construído o Mythos. A capacidade emergiu de um modelo de propósito geral sem que a empresa a buscasse especificamente. A questão real é como a indústria como um todo vai responder quando múltiplos atores com capacidades equivalentes estiverem no campo, com diferentes conjuntos de restrições e motivações.

## O que o Glasswing revela sobre o momento atual da IA

O Project Glasswing é relevante além da segurança cibernética porque documenta publicamente algo que a maioria das discussões sobre IA ainda trata como hipotético: modelos de propósito geral cruzando para capacidade ofensiva autônoma de nível especialista.

Esse padrão vai surgir em outros domínios. A pergunta que o Glasswing coloca para além do setor de segurança é: quando uma capacidade emergente é considerada poderosa demais para distribuição ampla, quem decide quem tem acesso, por quais critérios e com qual supervisão independente?

A Anthropic optou por um modelo de consórcio fechado com empresas de grande porte como parceiros primários. Essa escolha favorece velocidade de adoção e controle de distribuição, mas concentra benefícios imediatos em organizações que já têm recursos. Projetos open source críticos, governos de países menores e pesquisadores independentes ficam fora do primeiro círculo de acesso. [Análises de centros de pesquisa de segurança](https://www.iansresearch.com/resources/all-blogs/post/security-blog/2026/04/19/anthropic's--project-glasswing--exposes-the-next-challenge-for-vulnerability-management) apontam que apenas 1% das vulnerabilidades identificadas pelo Mythos foram corrigidas até o momento, o que sugere que o gargalo não é a detecção. É a capacidade de resposta dos times de desenvolvimento.

Para quem acompanha o desenvolvimento de agentes autônomos, o Glasswing conecta-se diretamente ao debate mais amplo sobre autonomia e supervisão humana em sistemas de IA. O post sobre [Claude Code como agente de terminal](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/claude-code-agente-terminal/) discute o custo de erros em agentes com ferramentas reais, um princípio que se aplica aqui com consequências muito maiores. O artigo sobre [Claude Opus 4.7](https://ppablobr.github.io/markdown/ia/agentes/2026/04/16/claude-opus-4-7-lancamento/) também documenta que o Opus 4.7 recebeu salvaguardas específicas para uso em automações de segurança, o que indica que o aprendizado do Glasswing já está influenciando modelos com acesso público.

O Project Glasswing não resolve o problema da segurança digital com IA. Ele documenta que o problema chegou a um novo nível de urgência, e que a resposta ainda está sendo construída em tempo real.

---

**Fontes consultadas:**
- [Anthropic: Project Glasswing](https://www.anthropic.com/project/glasswing)
- [Anthropic: Claude Mythos Preview (red.anthropic.com)](https://red.anthropic.com/2026/mythos-preview/)
- [TechCrunch: Anthropic debuts preview of powerful new AI model Mythos](https://techcrunch.com/2026/04/07/anthropic-mythos-ai-model-preview-security/)
- [TechCrunch: Unauthorized group has gained access to Anthropic's Mythos](https://techcrunch.com/2026/04/21/unauthorized-group-has-gained-access-to-anthropics-exclusive-cyber-tool-mythos-report-claims/)
- [VentureBeat: Anthropic says its most powerful AI cyber model is too dangerous to release publicly](https://venturebeat.com/technology/anthropic-says-its-most-powerful-ai-cyber-model-is-too-dangerous-to-release)
- [Fortune: Anthropic is giving some firms early access to Claude Mythos](https://fortune.com/2026/04/07/anthropic-claude-mythos-model-project-glasswing-cybersecurity/)
- [Foreign Policy: Anthropic's Claude Mythos Preview Changes Cyber Calculus](https://foreignpolicy.com/2026/04/20/claude-mythos-preview-anthropic-project-glasswing-cybersecurity-ai-hacking-danger/)
- [Schneier on Security: On Anthropic's Mythos Preview and Project Glasswing](https://www.schneier.com/blog/archives/2026/04/on-anthropics-mythos-preview-and-project-glasswing.html)
- [The Hacker News: Anthropic's Claude Mythos Finds Thousands of Zero-Day Flaws](https://thehackernews.com/2026/04/anthropics-claude-mythos-finds.html)
- [Infosecurity Magazine: Anthropic Launches Project Glasswing](https://www.infosecurity-magazine.com/news/anthropic-launch-project-glasswing/)
- [IANS Research: Project Glasswing Exposes the Next Challenge for Vulnerability Management](https://www.iansresearch.com/resources/all-blogs/post/security-blog/2026/04/19/anthropic's--project-glasswing--exposes-the-next-challenge-for-vulnerability-management)
- [State of Surveillance: An AI Found Zero-Days in Every Major OS](https://stateofsurveillance.org/news/anthropic-mythos-project-glasswing-zero-day-ai-surveillance-2026/)
- [Bloomberg: Anthropic's Mythos AI Model Is Being Accessed by Unauthorized Users](https://www.bloomberg.com/news/articles/2026-04-21/anthropic-s-mythos-model-is-being-accessed-by-unauthorized-users)
