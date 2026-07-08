# AGENTS.md — gabrielgabateli site

Site pessoal estático de Gabriel Gabateli, estudante de engenharia da computação na FIAP. Minimalista, em português brasileiro, com blog em markdown, canvas ASCII animado e tipografia limpa.

## Stack

| Camada      | Tecnologia                                      |
|-------------|-------------------------------------------------|
| Framework   | Astro v5, output `static`                       |
| Estilo      | Tailwind CSS v3, class-based dark mode, CSS custom properties |
| Conteúdo    | Markdown/MDX (coleção `blog`)                   |
| Tipografia  | Geist (sans) + Geist Mono, Google Fonts         |
| Validação   | Zod (bundled com `astro:content`)               |
| SEO         | `@astrojs/sitemap` (auto gera `sitemap-index.xml`) |
| Transições  | `ViewTransitions` nativo do Astro (SPA-like)    |

## Estrutura de diretórios

```
site/
├── src/
│   ├── content/
│   │   ├── blog/           ← posts .md (coleção Zod-validada)
│   │   └── config.ts       ← schema da coleção blog
│   ├── pages/
│   │   ├── index.astro     ← home: bio + writing (PostList, últimos 5) + projects
│   │   ├── 404.astro       ← página 404 customizada (com AsciiBackground)
│   │   ├── tags.astro      ← listagem de tags com contagem de posts
│   │   └── blog/
│   │       ├── index.astro ← listagem de posts (lista inline) + TagFilter
│   │       └── [slug].astro← rota dinâmica de post individual
│   ├── layouts/
│   │   ├── BaseLayout.astro ← shell HTML: <head>, fontes, dark mode, favicon swap, ViewTransitions, AsciiBackground, max-w-[620px]
│   │   └── PostLayout.astro ← wrapper de post: ProgressBar + header + conteúdo + prev/next + BackToTop
│   ├── components/
│   │   ├── Nav.astro          ← barra superior: brand + links + ThemeToggle
│   │   ├── PostList.astro     ← lista reutilizável de resumos (usada na home)
│   │   ├── TagFilter.astro    ← filtro client-side por tag (pills)
│   │   ├── ThemeToggle.astro  ← botão dark/light mode + favicon swap
│   │   ├── AsciiBackground.astro  ← canvas animado fullscreen com ondas ASCII + cursor
│   │   ├── ProgressBar.astro  ← barra de progresso de leitura (2px, fixa no topo)
│   │   └── BackToTop.astro    ← botão "↑ topo" fixo (aparece ao scrollar)
│   └── styles/
│       └── global.css         ← CSS custom properties (:root + .dark), prose-custom, selection
├── public/
│   ├── 50x.html               ← página de erro 500 standalone (autocontida)
│   ├── robots.txt             ← aponta sitemap gerado pelo Astro
│   ├── favicon-light.svg      ← círculo claro (#ededed, para dark mode)
│   └── favicon-dark.svg       ← círculo escuro (#111, para light mode)
├── astro.config.ts
├── tailwind.config.ts
└── package.json
```

## Design system

### Fontes
- **Sans-serif**: Geist (pesos 400, 500) — corpo, títulos, links
- **Monospace**: Geist Mono (peso 400) — datas, tags, código, labels

### Cores

Todas as cores são definidas como CSS custom properties em `global.css` e referenciadas via classes Tailwind (ex: `text-secondary`, `bg-border`). Os valores hex são declarados nos seletores `:root` (light) e `.dark` (dark).

| Variável CSS        | Classe Tailwind     | Light      | Dark       |
|---------------------|---------------------|------------|------------|
| `--color-bg`        | `bg-bg`             | `#fff`     | `#0a0a0a`  |
| `--color-fg`        | `text-fg`           | `#111`     | `#ededed`  |
| `--color-secondary` | `text-secondary`    | `#555`     | `#aaa`     |
| `--color-tertiary`  | `text-tertiary`     | `#888`     | `#555`     |
| `--color-quaternary`| `text-quaternary`   | `#ccc`     | `#333`     |
| `--color-border`    | `border-border`     | `#ebebeb`  | `#1e1e1e`  |
| `--color-code-bg`   | `bg-code-bg`        | `#f2f2f2`  | `#1a1a1a`  |
| `--color-code-fg`   | `text-code-fg`      | `#333`     | `#ccc`     |
| `--color-prose`     | — (manual em .prose-custom) | `#444` | `#aaa`    |

- **Selection**: `::selection` — fundo `--color-fg`, texto `--color-bg` (invertido)
- **Prose paragraphs** usam `--color-prose` (distinto de `--color-secondary`, ligeiramente mais suave)

### Escala tipográfica

| Uso                     | Tamanho  | Peso | Família     |
|-------------------------|----------|------|-------------|
| Código de erro (404)    | `72px`   | 500  | Geist       |
| Título de post          | `22px`   | 500  | Geist       |
| Título do site / página | `20px`   | 500  | Geist       |
| Corpo, links nav        | `15px`   | 400  | Geist       |
| Descrição de post       | `14px`   | 400  | Geist       |
| Sub-headings             | `13px`   | 500  | Geist       |
| Nav links (mobile)      | `13px`   | 400  | Geist       |
| Brand nav (mobile)      | `14px`   | 500  | Geist       |
| Mono labels, links      | `12px`   | 400  | Geist Mono  |
| Post meta (data/tag/readTime) | `11px` | 400 | Geist Mono |
| Rodapé, tag pills       | `11px`   | 400  | Geist Mono  |
| Label de erro           | `10px`   | 400  | Geist Mono  |

### Dark mode

- **Método**: class-based — toggle `.dark` no `<html>`
- **Persistência**: `localStorage.getItem('theme')`
- **Fallback**: se não houver tema salvo, respeita `prefers-color-scheme: dark`
- **Flicker-free**: script inline no `<head>` do `BaseLayout` aplica a classe antes do primeiro paint
- **View transitions**: script também escuta `astro:before-swap` para transferir `.dark` para o novo documento
- **Toggle**: `ThemeToggle.astro` — botão client-side que alterna a classe, salva em `localStorage`, e atualiza o favicon
- **Favicon dinâmico**: `favicon-dark.svg` (círculo escuro) em light mode, `favicon-light.svg` (círculo claro) em dark mode. Trocado tanto no script inline quanto no `ThemeToggle`

### Prose (conteúdo de posts)

Classes `.prose-custom` definidas manualmente em `global.css` (não usa plugin typography):

- Parágrafos: 15px, line-height 1.8, cor `--color-prose`
- Headings h2: 17px medium, mt-10 mb-3
- Headings h3: 15px medium, mt-6 mb-2
- Links: underline, decoration `--color-quaternary`, hover decoration `--color-fg`
- Código inline (`code:not(pre code)`): mono 12px, px-1.5 py-0.5, rounded, fundo `--color-code-bg`
- Blocos de código (`pre`): mono 12px, p-4, rounded-lg, overflow-x-auto, border `--color-border`, fundo `--color-code-bg`
- Blockquotes: border-l-2, pl-4, itálico, 14px, cor `--color-prose`

## Sistema de conteúdo (blog)

### Schema (Zod, em `src/content/config.ts`)

```
title:         string   (obrigatório)
description:   string   (obrigatório)
date:          string   (obrigatório, formato YYYY-MM)
tag:           string   (obrigatório, uma palavra)
draft:         boolean  (opcional, default false)
```

### Draft gating

O campo `draft: true` exclui o post em **4 locais**:
1. `blog/[slug].astro` → `getStaticPaths()` — não gera página HTML
2. `blog/index.astro` → listagem — não aparece na lista
3. `index.astro` → home — não aparece nos posts recentes (PostList)
4. `tags.astro` → contagem de tags — não é incluído nas estatísticas

Para publicar um draft existente: mude `draft: true` para `draft: false` (ou remova o campo).

### Comportamento de listagem

- Posts ordenados por `date` decrescente em todas as listagens
- Tags extraídas do conjunto único de todos os posts não-draft
- Home mostra os 5 posts mais recentes via `PostList`
- `/blog` renderiza lista inline própria (com atributo `data-post-tag` para filtro), não usa `PostList`
- Sem paginação — todos os posts em uma página
- Sem RSS configurado

### Imagens em posts

Coloque os arquivos em `public/images/` (crie o diretório se necessário) e referencie no markdown como:

```md
![texto alternativo](/images/foto.png)
```

A pasta `public/` é copiada crua para a raiz do `dist/` no build.

### Read time

Calculado em `blog/[slug].astro`: `Math.ceil(post.body.split(/\s+/).length / 200) min read`. Exibido no header do post como terceiro item da linha de meta.

### Navegação prev/next

Em `blog/[slug].astro`, `getStaticPaths` computa o post anterior (mais antigo, `posts[i+1]`) e o próximo (mais novo, `posts[i-1]`) a partir do array ordenado por data decrescente. Props têm o shape:

```ts
prevPost?: { slug: string; title: string } | null
nextPost?: { slug: string; title: string } | null
```

Títulos são truncados a 22 caracteres com `"..."` no `PostLayout`.

## Componentes

### BaseLayout (`src/layouts/BaseLayout.astro`)

**Propósito**: Shell HTML de todas as páginas. Fornece `<html>`, `<head>`, fontes, meta tags (OG + Twitter Card), dark mode inline script, ViewTransitions, AsciiBackground, e container centralizado.

**Props**:
```
title?:               string   (default: "gabriel gabateli")
description?:         string   (default: "computer engineering student @ fiap. sao paulo, brazil.")
ogImage?:             string   (opcional — caminho relativo para imagem OG/Twitter)
hideAsciiBackground?: boolean  (default: false — quando true, esconde o canvas ASCII e remove relative z-10)
```

**Comportamento**:
- Se `title !== "gabriel gabateli"`, o `<title>` vira `"${title} — gabriel gabateli"`
- `og:type` é `"article"` para páginas com título customizado, `"website"` para a home
- Container principal: `<div class="max-w-[620px] mx-auto px-6">` (com `relative z-10` condicional quando AsciiBackground está ativo)
- Corpo: `bg-bg text-fg min-h-screen transition-colors duration-200`
- Canonical URL: `new URL(Astro.url.pathname, Astro.site)` → `https://gabateli.com/...`
- Favicon inicial: `favicon-light.svg` (substituído pelo script inline antes do primeiro paint)
- Renderiza `<slot />` dentro do container

### Nav (`src/components/Nav.astro`)

**Propósito**: Barra de navegação no topo de cada página. Padding `py-10`.

**Links**: Blog (`/blog`), Tags (`/tags`), Projects (`/#projects`), `ThemeToggle`

**Estado ativo**: Detectado via `Astro.url.pathname.startsWith()`. Links "blog" e "tags" recebem `text-fg font-medium` quando na rota correspondente. Demais links são `text-tertiary` com `hover:text-fg`.

**Responsivo**: Brand em `14px/15px`, nav links em `13px/14px` (mobile/desktop).

**Observação**: O link "projects" é um anchor para `/#projects` na home. Se o link de CV for readicionado no futuro, o padrão é `href="/cv.pdf"` com `target="_blank"`.

### PostLayout (`src/layouts/PostLayout.astro`)

**Propósito**: Wrapper para a página individual de post. Encapsula `BaseLayout` (com `hideAsciiBackground`) + `ProgressBar` + `Nav` + conteúdo + `BackToTop`.

**Props**:
```
title:         string
description:   string
date:          string
tag:           string
readTime?:     string   (default: "5 min read", sobrescrito pelo cálculo em [slug].astro)
prevPost?:     { slug: string; title: string } | null
nextPost?:     { slug: string; title: string } | null
```

**Estrutura renderizada**:
1. `ProgressBar` (barra fixa de progresso de leitura)
2. `Nav` (barra de navegação)
3. Link "`← blog`" no topo (mono 12px, `text-tertiary`)
4. Header com linha de meta: date · tag · readTime (mono 11px, `text-quaternary`, separados por `·`)
5. Título (h1, 22px, medium, tracking-tight, leading-[1.3])
6. Descrição (14px, `text-secondary`, leading-relaxed)
7. `<slot />` envolto em `div.prose-custom`
8. Footer: "`← todos os posts`" + prev/next links (mono 12px, truncados a 22 chars)
9. `BackToTop` (botão flutuante)

### PostList (`src/components/PostList.astro`)

**Propósito**: Lista reutilizável de resumos de posts. Usada exclusivamente na home (`index.astro`) para mostrar os 5 posts mais recentes.

**Props**: `posts: Post[]` onde `Post = { slug: string; data: { title: string; date: string; tag: string } }`

**Renderiza**: `<ul class="divide-y divide-border">` com cada item como `<a href="/blog/${slug}">` mostrando:
- Título (mono 13px, `text-secondary`, `truncate`, `group-hover:underline`)
- Tag badge (mono 10px, `text-quaternary`, shrink-0)
- Data (mono 11px, `text-tertiary`, shrink-0)

**Nota**: Não inclui `data-post-tag` — o `TagFilter` só funciona na página `/blog`, não na home.

### TagFilter (`src/components/TagFilter.astro`)

**Propósito**: Filtro client-side na página `/blog`. Renderiza pills clicáveis para cada tag.

**Dados de entrada**: `tags: string[]` (extraídos de `[...new Set(posts.map(p => p.data.tag))]`)

**Funcionamento**: 
- Cada post na listagem inline de `/blog` tem atributo `data-post-tag={post.data.tag}`
- O script client-side mostra/esconde elementos `[data-post-tag]` conforme a tag selecionada
- Tag "all" (ativa por padrão) mostra tudo
- Pills usam estilo próprio (mono 11px, rounded-full, border) — não usam Tailwind (estilo inline via `<style>`)
- Pill ativa: fundo `--color-fg`, texto `--color-bg`, borda `--color-fg`

### ThemeToggle (`src/components/ThemeToggle.astro`)

**Propósito**: Botão client-side que alterna dark/light mode e atualiza o favicon.

**Funcionamento**: 
- Alterna classe `.dark` no `<html>`, persiste em `localStorage`
- Ícone: `☀️` (sol) em dark mode, `🌙` (lua) em light mode
- Também atualiza `href` do elemento `<link id="favicon">`
- Botão com fundo `bg-code-bg`, 30×30px, rounded-[7px]
- Re-inicializa via `astro:page-load` para sobreviver a ViewTransitions
- Guard `btn.dataset.initialized` evita double-init

### AsciiBackground (`src/components/AsciiBackground.astro`)

**Propósito**: Canvas animado fullscreen com grade de caracteres ASCII que reage a ondas senoidais e ao cursor do mouse.

**Detalhes técnicos**:
- Script `is:inline` com `data-astro-rerun` (re-executa em view transitions)
- Grade de caracteres: `['·', ':', '•', '-', '~', '∿', '+', '*']`
- Fonte: Geist Mono 13px, gap de 26px (desktop) / 34px (mobile)
- Três camadas de ondas senoidais (horizontal, vertical, radial) criam padrão fluido
- Cor: `--color-secondary` com alpha variando de 0.05 a 0.22
- Cursor halo: raio de 220px, cor vermelha (#ef4444), boost de alpha e troca de cor nos caracteres próximos
- Performance: ~20fps (50ms entre frames), pausa com `visibilitychange`, respeita `prefers-reduced-motion`
- Double-buffering: canvas com DPR-cap para nitidez em telas de alta densidade
- View transition aware: guard `window.__asciiBgReady` previne múltiplos listeners globais

### ProgressBar (`src/components/ProgressBar.astro`)

**Propósito**: Barra fixa de 2px no topo da viewport que indica o progresso de leitura do post.

**Detalhes**:
- `<div>` fixo com `top-0 left-0 h-[2px] bg-fg z-50`
- Animação via `transform: scaleX(progress)` (GPU-accelerated, sem layout thrashing)
- Progresso = `scrollY / (scrollHeight - innerHeight)`
- Throttle via `requestAnimationFrame` (flag `ticking`)
- Listener de scroll com `{ passive: true }`

### BackToTop (`src/components/BackToTop.astro`)

**Propósito**: Botão flutuante "↑ topo" que aparece ao scrollar a página.

**Detalhes**:
- Fixo em `bottom-6 right-6 z-50`, mono 11px
- Inicialmente invisível (`opacity-0 pointer-events-none`)
- Aparece quando `scrollY > 300`
- Scroll suave: `window.scrollTo({ top: 0, behavior: 'smooth' })`
- Mesmo padrão de throttle via `requestAnimationFrame`

## Páginas e rotas

| URL                 | Arquivo                        | Descrição                                |
|---------------------|--------------------------------|------------------------------------------|
| `/`                 | `pages/index.astro`            | Home: bio + social links + projects (vazio) + writing (PostList, 5 posts) |
| `/blog`             | `pages/blog/index.astro`       | Listagem inline de posts + TagFilter     |
| `/blog/[slug]`      | `pages/blog/[slug].astro`      | Post individual com prev/next + read time |
| `/tags`             | `pages/tags.astro`             | Lista de tags com contagem de posts      |
| `/404.html`         | `pages/404.astro`              | Página 404 customizada (com AsciiBackground) |

**Notas**:
- A seção "writing" da home renderiza `PostList` com os 5 posts não-draft mais recentes
- A seção "projects" da home (`/#projects`) existe como heading mas está vazia (placeholder)
- A página `/tags` lista cada tag com contagem de posts, ordenada por frequência decrescente. Cada tag linka para `/blog`
- Todas as páginas usam `BaseLayout` e `Nav`
- `404.astro` e `50x.html` renderizam o `AsciiBackground` (todas as demais usam `hideAsciiBackground`)

## ViewTransitions

O componente `<ViewTransitions />` do Astro é renderizado no `<head>` do `BaseLayout`, habilitando navegação SPA-like entre páginas. Implicações para componentes client-side:

- Scripts que manipulam o DOM devem se re-inicializar após transições
- `ThemeToggle` e `AsciiBackground` usam `astro:page-load` para re-init
- O script inline de dark mode usa `astro:before-swap` para transferir a classe `.dark`
- `data-astro-rerun` é usado no `AsciiBackground` para re-execução do script inline

## SEO

- **Sitemap**: `@astrojs/sitemap` gera `sitemap-index.xml` automaticamente no build
- **robots.txt**: aponta para `https://gabateli.com/sitemap-index.xml`
- **Canonical URL**: gerada a partir de `Astro.site` + `Astro.url.pathname`
- **Open Graph**: `og:title`, `og:description`, `og:url`, `og:type` (`website` ou `article`), `og:site_name`, `og:locale` (`pt_BR`)
- **Twitter Card**: `summary` com title, description e image (se `ogImage` for fornecida)
- **Meta description**: padrão ou customizada por página

## Comandos

```bash
npm run dev       # servidor local com hot reload (astro dev --host)
npm run build     # build de produção (astro build) → gera dist/
npm run preview   # preview local do build (astro preview --host)
```

O flag `--host` expõe o servidor na rede local (útil para testar em outros dispositivos).

## Convenções para agentes

### Estilo de código

- **Tailwind inline**: Classes utilitárias direto nos elementos. Evitar CSS customizado exceto em `global.css` e estilos locais necessários (ex: `TagFilter.astro`).
- **Props tipadas**: Usar `interface Props` com types explícitos, nunca `any`.
- **Frontmatter dos posts**: Delimitadores `---` padrão, campos em minúsculas, formato de data `YYYY-MM`.
- **Cores e fontes**: Usar as classes Tailwind que referenciam CSS custom properties (`text-secondary`, `bg-border`, etc.). Não usar valores hex diretamente.
- **Idioma**: Conteúdo em pt-BR. Nomes de variáveis, props e classes em inglês. Comentários em inglês.

### Propondo mudanças

1. **Sempre justificar**: Explique a motivação da mudança antes de executar
2. **Listar em bullet points**: Enumere todas as alterações planejadas antes de começar
3. **Testar localmente**: Rode `npm run dev` para validar
4. **Não commitar**: Commits só com autorização explícita
5. **Não modificar `deploy.sh`**: O script de deploy fica em `../script_deploy/` e está fora do escopo

### Padrões comuns

**Adicionar um novo post**:
```bash
criar src/content/blog/titulo-do-post.md
→ frontmatter com title, description, date, tag, draft: false
→ npm run dev para testar localmente
```

**Adicionar uma nova página**:
```bash
criar src/pages/nova-rota.astro
→ usar <BaseLayout> como wrapper (com Nav)
→ decidir se usa AsciiBackground (padrão: sim) ou hideAsciiBackground
→ npm run dev para testar localmente
```

**Adicionar um novo componente**:
```bash
criar src/components/NovoComponente.astro
→ seguir padrão de props tipadas com interface Props
→ usar classes Tailwind com CSS custom properties do design system
→ se tiver script client-side, considerar ViewTransitions (re-init com astro:page-load)
```