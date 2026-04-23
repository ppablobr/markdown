---
layout: post
title: "Project Glasswing: Como a Anthropic Está Usando IA para Defender o Software Crítico do Mundo"
date: 2026-04-23 10:00:00 +0000
categories: ia seguranca
tags: [anthropic, glasswing, ciberseguranca, claude, vulnerabilidades, zero-day]
excerpt: "A Anthropic lançou o Project Glasswing com o modelo Claude Mythos Preview para encontrar e corrigir vulnerabilidades zero-day em sistemas críticos. Entenda o que muda para a segurança digital."
image: ""
---

Em 7 de abril de 2026, a Anthropic anunciou o [Project Glasswing](https://www.anthropic.com/glasswing): uma iniciativa para usar sua IA mais poderosa na defesa do software crítico do mundo. O modelo central é o Claude Mythos Preview, descrito pela própria empresa como capaz de identificar e explorar vulnerabilidades de forma autônoma em sistemas operacionais, browsers e infraestrutura de rede. O resultado preliminar, divulgado no lançamento, foi de milhares de vulnerabilidades zero-day encontradas antes que qualquer atacante pudesse usá-las.

A iniciativa reúne 12 parceiros de lançamento: Amazon Web Services, Anthropic, Apple, Broadcom, Cisco, CrowdStrike, Google, JPMorganChase, Linux Foundation, Microsoft, NVIDIA e Palo Alto Networks. Outras [40 organizações responsáveis por infraestrutura crítica](https://www.infosecurity-magazine.com/news/anthropic-launch-project-glasswing/) também receberam acesso. O investimento comprometido pela Anthropic chega a $100 milhões em créditos de uso e $4 milhões em doações para organizações de segurança open source.

O anúncio teve impacto imediato, mas também gerou controvérsias. O modelo não ficou restrito por muito tempo a quem devia: em poucos dias após o lançamento, um grupo não autorizado conseguiu acesso ao Claude Mythos Preview. Esse incidente, enquanto a Anthropic ainda divulgava os benefícios da iniciativa, resume com precisão o dilema central do Glasswing.

## O que o Claude Mythos Preview consegue fazer

O Claude Mythos Preview não é um modelo de propósito geral com um plugin de segurança. Segundo a documentação publicada em [red.anthropic.com](https://red.anthropic.com/2026/mythos-preview/), o modelo consegue identificar vulnerabilidades zero-day, escrever código de exploração funcional e encadear múltiplas falhas em ataques compostos.

Um exemplo concreto divulgado pela Anthropic ilustra a capacidade: o modelo identificou de forma autônoma e depois explorou uma vulnerabilidade de execução remota de código de 17 anos no FreeBSD, classificada como CVE-2026-4747. A falha permite que qualquer usuário não autenticado obtenha acesso root completo a um servidor rodando NFS. O modelo não apenas encontrou a brecha, mas construiu o exploit funcional.

Outros resultados divulgados incluem a descoberta de uma vulnerabilidade de 27 anos no OpenBSD, sistema usado para firewalls e infraestrutura crítica, e de uma falha de 16 anos no FFmpeg. No caso do Firefox, [o Mythos foi capaz de transformar vulnerabilidades identificadas em 181 ataques utilizáveis](https://foreignpolicy.com/2026/04/20/claude-mythos-preview-anthropic-project-glasswing-cybersecurity-ai-hacking-danger/). O modelo anterior da Anthropic aplicado à mesma tarefa produziu dois.

A diferença de escala não é incremental. É qualitativa.

## Como o Glasswing funciona na prática

A estrutura operacional do Glasswing é baseada em acesso controlado. A Anthropic não disponibilizou o Claude Mythos Preview na API pública nem via Claude.ai. O modelo é acessado exclusivamente pelos parceiros autorizados, em ambientes específicos, para tarefas de segurança defensiva.

Os parceiros usam o modelo para auditar seus próprios sistemas: identificar falhas antes que sejam descobertas por atacantes, priorizar correções por severidade e desenvolver patches. A lógica é de vantagem temporal: defensores com acesso ao Mythos conseguem encontrar e corrigir vulnerabilidades mais rápido do que atacantes que ainda dependem de métodos manuais ou de modelos menos capazes.

O investimento de [$100 milhões em créditos de uso](https://venturebeat.com/technology/anthropic-says-its-most-powerful-ai-cyber-model-is-too-dangerous-to-release/) e $4 milhões em doações a projetos open source de segurança serve para reduzir a barreira de adoção entre os parceiros e financiar melhorias em software de infraestrutura que está fora do escopo comercial direto das empresas participantes.

A estratégia reconhece uma realidade do setor: vulnerabilidades em sistemas como o kernel Linux ou em componentes de rede legados existem há anos, mas correção ativa requer investimento que organizações sem fins lucrativos ou projetos de voluntários raramente conseguem sustentar.

## O incidente: o modelo restrito que não ficou restrito

Em algum momento nos dias seguintes ao anúncio, um grupo ganhou acesso ao Claude Mythos Preview sem autorização. A investigação posterior revelou o mecanismo: o grupo deduziu o endpoint do modelo com base nos padrões de nomenclatura que a Anthropic usa para outros modelos, acessou por meio de um portal de terceiros contratados e usou ferramentas de reconhecimento de rede para confirmar o acesso.

A Anthropic confirmou a investigação em nota pública: ["estamos investigando um relatório afirmando acesso não autorizado ao Claude Mythos Preview por meio de um de nossos ambientes de fornecedores terceirizados"](https://techcrunch.com/2026/04/21/unauthorized-group-has-gained-access-to-anthropics-exclusive-cyber-tool-mythos-report-claims/). A empresa disse não ter evidências de que seus sistemas principais foram comprometidos além do ambiente do fornecedor.

O grupo compartilhou capturas de tela e demonstrações ao vivo com veículos como a Bloomberg, o que tornou o incidente verificável por jornalistas antes da resposta oficial. Segundo relatos, o acesso teria ocorrido no mesmo dia do anúncio público.

A ironia é imediata: um modelo criado para identificar falhas de segurança ficou exposto por uma falha de controle de acesso. O incidente não invalida a proposta do Glasswing, mas demonstra que a segurança da iniciativa depende tanto de processos operacionais quanto das capacidades técnicas do modelo.

## O paradoxo ético de um modelo ofensivo para fins defensivos

O núcleo do debate em torno do Glasswing é técnico e filosófico ao mesmo tempo. As capacidades que tornam o Claude Mythos Preview útil para defensores, como identificar e explorar vulnerabilidades de forma autônoma, são exatamente as capacidades que o tornam perigoso nas mãos erradas.

[Bruce Schneier](https://www.schneier.com/blog/archives/2026/04/on-anthropics-mythos-preview-and-project-glasswing.html), um dos pesquisadores mais respeitados em segurança digital, caracterizou o anúncio como "uma jogada de PR da Anthropic" e observou que veículos de mídia repetiram os argumentos da empresa sem apuração crítica adequada. Schneier também apontou que a empresa de segurança Aisle conseguiu replicar parte das vulnerabilidades usando modelos públicos mais antigos e mais baratos, o que levanta perguntas sobre a singularidade real das capacidades do Mythos. Sua conclusão mais relevante: "essa vantagem provavelmente vai diminuir" à medida que modelos comparáveis se tornarem acessíveis.

[A preocupação com governos e atores estatais](https://stateofsurveillance.org/news/anthropic-mythos-project-glasswing-zero-day-ai-surveillance-2026/) é estrutural. Enquanto a Anthropic limita acesso ao Mythos para garantir uso defensivo, nada impede que governos com recursos desenvolvam modelos equivalentes sem as mesmas restrições. A assimetria entre quem tem acesso ao modelo da Anthropic e quem pode construir alternativas independentes é real, mas temporária.

Esse dilema não é exclusivo da IA. Vulnerabilidades zero-day são compradas e vendidas em mercados cinzas há décadas. Ferramentas de pen testing legítimas são usadas por equipes de red team e por atacantes com os mesmos binários. O que o Mythos muda é a escala e a velocidade: o que antes exigia uma equipe de especialistas experientes pode agora ser executado de forma autônoma em paralelo em centenas de sistemas.

A questão central não é se a Anthropic deveria ou não ter construído o Mythos. A capacidade emergiu de um modelo de propósito geral sem que a empresa a buscasse especificamente, conforme descrito na documentação do lançamento. A questão real é como a indústria como um todo vai responder quando múltiplos atores com capacidades equivalentes estiverem no campo, sem o mesmo conjunto de restrições.

## O que o Glasswing revela sobre o momento atual da IA

O Project Glasswing é relevante além da segurança cibernética porque documenta publicamente algo que a maioria das discussões sobre IA ainda trata como hipotético: modelos de propósito geral cruzando para capacidade ofensiva autônoma de nível especialista.

Esse padrão, que aparece aqui na forma de exploits de vulnerabilidades, vai surgir em outros domínios. A pergunta que o Glasswing coloca para além do setor de segurança é: quando uma capacidade emergente é poderosa demais para ser distribuída amplamente, quem decide quem tem acesso, por quais critérios e com qual supervisão?

A Anthropic optou por um modelo de consórcio fechado com empresas de grande porte como parceiros primários. Essa escolha favorece velocidade de adoção e controle de distribuição, mas concentra benefícios imediatos em organizações que já têm recursos. Projetos open source críticos, governos de países menores e pesquisadores independentes ficam fora do primeiro círculo de acesso.

Para quem acompanha o desenvolvimento de agentes autônomos, o Glasswing conecta-se diretamente ao debate mais amplo sobre autonomia e supervisão humana em sistemas de IA. O post sobre [Claude Code como agente de terminal](https://ppablobr.github.io/markdown/ia/agentes/2026/04/12/claude-code-agente-terminal/) discute o custo de erros em agentes com ferramentas reais, um princípio que se aplica aqui com consequências muito maiores. E o artigo sobre [a história da IA que levou aos agentes](https://ppablobr.github.io/markdown/ia/agentes/2026/04/11/a-historia-da-ia-que-levou-aos-agentes/) contextualiza por que a chegada a esse ponto não foi acidental, mas o resultado de décadas de progressão técnica acelerada.

O Project Glasswing não resolve o problema da segurança digital com IA. Ele documenta que o problema chegou a um novo nível de urgência, e que a resposta ainda está sendo construída.

---

**Fontes consultadas:**
- [Anthropic: Project Glasswing](https://www.anthropic.com/project/glasswing)
- [Anthropic: Claude Mythos Preview (red.anthropic.com)](https://red.anthropic.com/2026/mythos-preview/)
- [TechCrunch: Anthropic debuts preview of powerful new AI model Mythos](https://techcrunch.com/2026/04/07/anthropic-mythos-ai-model-preview-security/)
- [TechCrunch: Unauthorized group has gained access to Anthropic's Mythos](https://techcrunch.com/2026/04/21/unauthorized-group-has-gained-access-to-anthropics-exclusive-cyber-tool-mythos-report-claims/)
- [VentureBeat: Anthropic says its most powerful AI cyber model is too dangerous to release publicly](https://venturebeat.com/technology/anthropic-says-its-most-powerful-ai-cyber-model-is-too-dangerous-to-release)
- [Foreign Policy: Anthropic's Claude Mythos Preview Changes Cyber Calculus](https://foreignpolicy.com/2026/04/20/claude-mythos-preview-anthropic-project-glasswing-cybersecurity-ai-hacking-danger/)
- [Schneier on Security: On Anthropic's Mythos Preview and Project Glasswing](https://www.schneier.com/blog/archives/2026/04/on-anthropics-mythos-preview-and-project-glasswing.html)
- [The Hacker News: Anthropic's Claude Mythos Finds Thousands of Zero-Day Flaws](https://thehackernews.com/2026/04/anthropics-claude-mythos-finds.html)
- [Infosecurity Magazine: Anthropic Launches Project Glasswing](https://www.infosecurity-magazine.com/news/anthropic-launch-project-glasswing/)
- [State of Surveillance: An AI Found Zero-Days in Every Major OS](https://stateofsurveillance.org/news/anthropic-mythos-project-glasswing-zero-day-ai-surveillance-2026/)
- [The Register: Anthropic's Project Glasswing CVE count is still guesswork](https://www.theregister.com/2026/04/15/project_glasswing_cves/)
- [Bloomberg: Anthropic's Mythos AI Model Is Being Accessed by Unauthorized Users](https://www.bloomberg.com/news/articles/2026-04-21/anthropic-s-mythos-model-is-being-accessed-by-unauthorized-users)
