# Workflow: Publicar Novo Post

## Objetivo

Escrever, ilustrar e publicar um post no blog Jekyll de ponta a ponta — pesquisa, escrita, referências, link building interno, infográfico, Cloudinary e push.

## Quando usar

- Pedro pede um novo post ("escreve sobre X", "cria um artigo sobre Y")
- Pedro fornece um tema e quer o post completo publicado

Para rascunhos prontos fornecidos pelo Pedro, usar `new-post-from-draft.md`.

## Responsável

Root agent (Claude) — orquestra todas as etapas. Não delega e esquece: cada etapa retorna ao root agent antes de avançar.

---

## Sequência de execução

### Etapa 1 — Escrita (subagente ai-agent-editor)

**Executor:** `ai-agent-editor` via `Agent` tool  
**Input:** tema ou briefing do Pedro  
**O que faz:**

1. WebSearch: mínimo 4-6 fontes de qualidade (estudos, artigos técnicos, casos reais)
2. Lê `_posts/` para descobrir posts existentes e selecionar links internos relevantes
3. Escreve o post completo com fontes inline e links internos
4. Salva em `_posts/YYYY-MM-DD-slug.md` com front matter completo
5. Retorna handoff estruturado ao root agent (ver formato abaixo)

**Formato do handoff que o subagente deve retornar:**

```
HANDOFF:
- file_path: _posts/YYYY-MM-DD-slug.md
- article_type: debate | technical | tutorial | timeline
- h2_sections: ["Seção 1", "Seção 2", ...]
- key_concepts: ["Conceito A", "Conceito B", "Conceito C"]
- cover_brief: descrição em 1-2 frases do conceito visual central do artigo
```

**Se o subagente não retornar o handoff estruturado:** root agent lê o arquivo criado, identifica o tipo pelas seções H2 e extrai os conceitos principais do conteúdo.

---

### Etapa 2 — Infográfico (skill imagen-prompt-architect)

**Executor:** Root agent via `Skill` tool → `imagen-prompt-architect`  
**Input:** handoff da Etapa 1  
**O que faz:**

1. Invoca `imagen-prompt-architect` em **modo infográfico** com os dados estruturais do post
2. Script salva a imagem em `assets/images/YYYYMMDD_HHMMSS_<slug>_infografico.png`

**Após o script retornar (root agent executa):**

3. Upload para Cloudinary:
   ```
   mcp__cloudinary__cloudinary_upload_file(
     source: "/caminho/absoluto/assets/images/YYYYMMDD_..._infografico.png",
     folder: "blog/covers",
     public_id: "blog/covers/<slug>-infografico",
     tags: ["blog", "<slug>", "infografico"]
   )
   ```
4. Captura `secure_url` retornada pelo Cloudinary
5. Deleta o arquivo local: `Bash("rm /caminho/absoluto/assets/images/YYYYMMDD_..._infografico.png")`
6. Insere `<figure>` no arquivo do post (ver posição abaixo)
7. Atualiza o campo `image:` no front matter com a URL do Cloudinary

**Posição do `<figure>` no post:**

```
[parágrafos de introdução — 2 a 4 parágrafos antes do primeiro ---]

<figure>
  <img src="[cloudinary secure_url]"
       alt="[descrição do infográfico]"
       loading="lazy"
       style="width:100%;border-radius:4px;">
  <figcaption>[legenda curta]</figcaption>
</figure>

## [Primeiro heading H2]
```

**Nota crítica sobre URLs:** Use sempre a URL absoluta do Cloudinary (`https://res.cloudinary.com/...`), nunca `relative_url` do Jekyll. Imagens geradas localmente e não commitadas no repo quebram com `relative_url` no GitHub Pages.

---

### Etapa 3 — Revisão e aprovação

**Executor:** Root agent  
**O que faz:**

1. Apresenta ao Pedro: título, excerpt, link do arquivo criado, preview da imagem (URL Cloudinary)
2. Pergunta: "Quer ajustar algum argumento, fonte, link ou imagem antes de publicar?"
3. Aguarda confirmação antes de prosseguir

---

### Etapa 4 — Publicação

**Executor:** Root agent  
**Preferência:** GitHub MCP (`mcp__github__create_or_update_file` ou `mcp__github__push_files`)  
**Fallback:** `git add` + `git commit` + `git push` via Bash  

Após push:
- GitHub Actions dispara automaticamente (`.github/workflows/jekyll.yml`)
- URL final: `https://ppablobr.github.io/markdown/<categories>/<YYYY>/<MM>/<DD>/<slug>/`
- Informa Pedro com a URL completa

---

## Checklist de qualidade antes do push

- [ ] Pesquisa realizada: mínimo 4-6 fontes de qualidade
- [ ] Todas as afirmações factuais com dados têm link inline
- [ ] Posts existentes linkados internamente (mínimo 1)
- [ ] Nome do arquivo: `YYYY-MM-DD-slug.md`
- [ ] Front matter completo: layout, title, date, categories, tags, excerpt, image
- [ ] Infográfico gerado, enviado ao Cloudinary e embutido no body com `<figure>`
- [ ] URL da imagem é do Cloudinary (não path local ou relative_url)
- [ ] Nenhum travessão "—" no corpo do texto
- [ ] Nenhuma construção "não é X, é Y" e variações
- [ ] Post aprovado pelo Pedro antes do push

---

## Edge cases

**Script de geração de imagem falha:**  
Verificar se `GEMINI_API_KEY` está em `.env`. Como fallback, tentar via `mcp__gemini-image__gemini_generate_image`. Se persistir, publicar sem imagem e documentar o problema.

**Cloudinary upload falha:**  
Usar a imagem local com URL relativa como fallback temporário e informar Pedro.

**Subagente não encontra fontes relevantes no WebSearch:**  
Pesquisar com queries mais amplas. Mínimo 2 fontes. Documentar a limitação no post ("dados disponíveis até [data]").
