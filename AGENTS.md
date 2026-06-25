# AGENTS.md — gabrielgabateli site

Site pessoal estático de Gabriel Gabateli, estudante de engenharia da computação na FIAP. Minimalista, em português brasileiro, com blog em markdown e tipografia limpa.

## Stack

| Camada      | Tecnologia                 |
|-------------|----------------------------|
| Framework   | Astro v5, output `static`  |
| Estilo      | Tailwind CSS v3, class-based dark mode |
| Conteúdo    | Markdown/MDX (coleção `blog`) |
| Tipografia  | Geist (sans) + Geist Mono, Google Fonts |
| Validação   | Zod (frontmatter dos posts) |

## Estrutura de diretórios

```
site/
├── src/
│   ├── content/
│   │   ├── blog/           ← posts .md (coleção Zod-validada)
│   │   └── config.ts       ← schema da coleção blog
│   ├── pages/
│   │   ├── index.astro     ← home (posts recentes comentados)
│   │   ├── 404.astro       ← página 404 customizada
│   │   └── blog/
│   │       ├── index.astro ← listagem de posts + TagFilter
│   │       └── [slug].astro← rota dinâmica de post individual
│   ├── layouts/
│   │   ├── BaseLayout.astro ← shell HTML: <head>, fontes, dark mode, max-w-[620px]
│   │   └── PostLayout.astro ← wrapper de post: header + conteúdo + prev/next
│   ├── components/
│   │   ├── Nav.astro        ← barra superior: links + ThemeToggle
│   │   ├── PostList.astro   ← lista reutilizável de resumos de posts
│   │   ├── TagFilter.astro  ← filtro client-side por tag (pills)
│   │   └── ThemeToggle.astro← botão dark/light mode
│   └── styles/
│       └── global.css       ← estilos base, prose-custom, selection colors
├── public/
│   ├── cv.pdf               ← asset cru copiado pra raiz do build
│   ├── 50x.html             ← página de erro 500 standalone
│   └── images/              ← imagens dos posts: ![alt](/images/nome.ext)
├── astro.config.ts
├── tailwind.config.ts
└── package.json
```

## Design system

### Fontes
- **Sans-serif**: Geist (pesos 400, 500) — corpo, títulos, links
- **Monospace**: Geist Mono (peso 400) — datas, tags, código, labels

### Cores

| Token               | Light              | Dark                |
|---------------------|--------------------|---------------------|
| Fundo               | `white`            | `#0a0a0a`           |
| Texto principal     | `#111`             | `#ededed`            |
| Texto secundário    | `#555`             | `#aaa`              |
| Texto terciário     | `#888`             | `#555` / `#666`      |
| Texto quaternário   | `#ccc`             | `#333`              |
| Bordas/dividers     | `#ebebeb`          | `#1e1e1e`           |
| Selection bg        | `#111`             | `#ededed`           |
| Selection fg        | `white`            | `#0a0a0a`           |

### Escala tipográfica

| Uso                  | Tamanho  | Peso    | Família |
|----------------------|----------|---------|---------|
| Código de erro (404) | `72px`   | 500     | Geist   |
| Título do site       | `20px`   | 500     | Geist   |
| Título de post       | `17px`   | 500     | Geist   |
| Corpo, links nav     | `15px`   | 400     | Geist   |
| Sub-headings         | `13px`   | 500     | Geist   |
| Mono labels, links   | `12px`   | 400     | Geist Mono |
| Rodapé               | `11px`   | 400     | Geist Mono |
| Label de erro        | `10px`   | 400     | Geist Mono |

### Dark mode

- **Método**: class-based — toggle `.dark` no `<html>`
- **Persistência**: `localStorage.getItem('theme')`
- **Flicker-free**: script inline no `<head>` do `BaseLayout` lê o tema salvo antes do primeiro paint
- **Toggle**: `ThemeToggle.astro` — botão client-side que alterna a classe e salva

### Prose (conteúdo de posts)

Classes `.prose-custom` definidas manualmente em `global.css` (não usa plugin typography):

- Parágrafos: 15px, line-height 1.8, cor secundária
- Links: underline, offset, animação de cor na decoração
- Código inline: mono 12px, fundo cinza, borda arredondada
- Blocos de código: mono 12px, fundo cinza, borda, scroll-x
- Blockquotes: borda esquerda 2px, itálico, cor terciária

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

O campo `draft: true` exclui o post em **3 locais de query**:
1. `blog/[slug].astro` → `getStaticPaths()` — não gera página HTML
2. `blog/index.astro` → listagem — não aparece na lista
3. `index.astro` → home — não aparece nos posts recentes

Para publicar um draft existente: mude `draft: true` para `draft: false` (ou remova o campo).

### Comportamento de listagem

- Posts ordenados por `date` decrescente em todas as listagens
- Tags extraídas do conjunto único de todos os posts não-draft
- Sem paginação — todos os posts em uma página
- Sem RSS configurado

### Imagens em posts

Coloque os arquivos em `public/images/` e referencie no markdown como:

```md
![texto alternativo](/images/foto.png)
```

A pasta `public/` é copiada crua para a raiz do `dist/` no build.

## Componentes

### BaseLayout (`src/layouts/BaseLayout.astro`)

**Propósito**: Shell HTML de todas as páginas. Fornece `<html>`, `<head>`, fontes, meta tags, dark mode inline script, e container centralizado.

**Props**:
```
title?:        string   (default: "gabriel gabateli")
description?:  string   (default: descrição padrão)
```

**Comportamento**:
- Se `title !== "gabriel gabateli"`, o `<title>` vira `"${title} — gabriel gabateli"`
- Container principal: `<div class="max-w-[620px] mx-auto px-6">`
- Corpo: `bg-white dark:bg-[#0a0a0a] text-[#111] dark:text-[#ededed] min-h-screen`
- Renderiza `<slot />` dentro do container

### Nav (`src/components/Nav.astro`)

**Propósito**: Barra de navegação fixa no topo de cada página.

**Links**: Home (`/`), Blog (`/blog`), Projects (`/#projects`), CV (`/cv.pdf`), `ThemeToggle`

**Estado ativo**: Link "blog" fica com texto `#111` (dark: `#ededed`) e `font-medium` quando `path.startsWith('/blog')`. Demais links são `#666` com hover.

### PostLayout (`src/layouts/PostLayout.astro`)

**Propósito**: Wrapper para a página individual de post. Encapsula `BaseLayout` + `Nav`.

**Props**:
```
title:         string
description:   string
date:          string
tag:           string
readTime:      string   (ex: "3 min read")
prevPost?:     { slug, data: { title } }
nextPost?:     { slug, data: { title } }
```

**Estrutura renderizada**:
1. Link "`← blog`" no topo
2. Header: data · tag · readTime (mono 12px)
3. Título (h1, 20px, tracking-tight)
4. Descrição (15px, cor secundária)
5. `<slot />` envolto em `div.prose-custom`
6. Footer: "`← todos os posts`" + prev/next (truncados a 22 chars)

### PostList (`src/components/PostList.astro`)

**Propósito**: Lista reutilizável de resumos de posts. Atualmente usada na home (seção comentada). A página `/blog` renderiza sua própria lista inline, não usa este componente.

**Props**: `posts: Array<{ slug: string, data: { title, date, tag } }>`

**Renderiza**: `<ul>` com cada item como `<a href="/blog/${slug}">` mostrando título, tag badge, e data.

### TagFilter (`src/components/TagFilter.astro`)

**Propósito**: Filtro client-side na página `/blog`. Renderiza pills clicáveis para cada tag.

**Dados de entrada**: `tags: string[]` (extraídos de `[...new Set(posts.map(p => p.data.tag))]`)

**Funcionamento**: Cada post na listagem tem atributo `data-post-tag`. O script client-side mostra/esconde elementos `[data-post-tag]` conforme a tag selecionada. Tag "todos" mostra tudo.

### ThemeToggle (`src/components/ThemeToggle.astro`)

**Propósito**: Botão client-side que alterna dark/light mode.

**Funcionamento**: Alterna classe `.dark` no `<html>`, persiste em `localStorage`. Ícone: sol (`☀`) em dark mode, lua (`☽`) em light mode.

## Páginas e rotas

| URL                 | Arquivo                        | Descrição                          |
|---------------------|--------------------------------|------------------------------------|
| `/`                 | `pages/index.astro`            | Home com bio, links sociais        |
| `/blog`             | `pages/blog/index.astro`       | Listagem + filtro por tag          |
| `/blog/[slug]`      | `pages/blog/[slug].astro`      | Post individual com prev/next      |
| `/404.html`         | `pages/404.astro`              | Página 404 customizada             |

**Nota**: A seção "writing" da home com `PostList` está comentada no código. Posts recentes não aparecem na home atualmente, apenas em `/blog`.

## Build output

`npm run build` → `astro build` gera `dist/`:

```
dist/
├── index.html
├── 404.html
├── 50x.html            ← copiado de public/
├── cv.pdf              ← copiado de public/
├── imagens/            ← copiados de public/images/
├── blog/
│   └── index.html
│   └── [slug]/
│       └── index.html
└── _astro/
    └── _slug_.XXXXX.css ← CSS hasheado
```

Arquivos em `public/` são copiados crus para a raiz do `dist/`. As páginas `.astro` viram HTML no build.

## Comandos

```bash
npm run dev       # servidor local com hot reload (astro dev --host)
npm run build     # build de produção (astro build) → gera dist/
npm run preview   # preview local do build (astro preview --host)
```

## Convenções para agentes

### Estilo de código

- **Tailwind inline**: Classes utilitárias direto nos elementos. Evitar CSS customizado exceto em `global.css`.
- **Props tipadas**: Usar `interface Props` com types explícitos, nunca `any`.
- **Frontmatter dos posts**: Delimitadores `---` padrão, campos em minúsculas, formato de data `YYYY-MM`.
- **Cores e fontes**: Seguir os tokens do design system. Não introduzir novas cores ou fontes sem justificativa.
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
→ usar <BaseLayout> como wrapper (com Nav se apropriado)
→ npm run dev para testar localmente
```

**Adicionar um novo componente**:
```bash
criar src/components/NovoComponente.astro
→ seguir padrão de props tipadas com interface Props
→ seguir sistema de cores e fontes do design system
```
