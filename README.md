# Vida no Campo — Landing Page com HTML e CSS

Desafio 01 da [Trilha de CSS da DIO](https://www.dio.me/), com **tema livre**.

> **O desafio:** o repositório base entrega o HTML e as imagens, **sem nenhum
> arquivo CSS**. Escrever toda a estilização do zero — a partir do protótipo do
> [Figma](https://www.figma.com/file/3PiokoJj9IhGDnNiWAJbz7/DIO---Desafio-01) —
> *é* o desafio, praticando os fundamentos do CSS e as **unidades de medida
> relativas e absolutas**.

**Raiz Viva** é uma escola fictícia de agricultura sustentável. Esta é a landing
page de inscrição da formação **Vida no Campo**.

---

## Duas versões neste repositório

| Versão | Onde | O que é |
| --- | --- | --- |
| **Vida no Campo** (atual) | branch `main` | Tema próprio, tipografia Fraunces, refinado |
| **Reprodução da DIO** | branch [`versao-dio`](../../tree/versao-dio) | Layout fiel ao protótipo do desafio, azul sobre preto |

A branch `versao-dio` preserva a primeira versão, que reproduz o layout da DIO
com fidelidade e serve de comparação direta com o gabarito oficial.

---

## Estrutura

```
trilha-css-desafio-01/
├── index.html
├── assets/
│   ├── css/
│   │   ├── reset.css      Normalização (45 linhas)
│   │   └── styles.css     Estilização completa (574 linhas comentadas)
│   ├── fonts/
│   │   ├── fraunces-latin.woff2
│   │   └── fraunces-latin-ext.woff2
│   └── images/
│       ├── logo.svg
│       ├── banner.svg
│       ├── campo.svg
│       ├── comunidade.svg
│       └── logo-rodape.svg
├── README-original.md     README original do desafio (DIO)
└── README.md
```

---

## Os requisitos do desafio e onde cada um foi atendido

### 1 e 2 — Landing page com fundamentos do CSS

Página completa com hero, seção de conteúdo, faixa parallax e rodapé, em CSS
puro: gradientes, `background-image`, `box-shadow`, `border-radius`,
`transition`, `filter`, `@supports` e `@font-face`.

### 3 — Propriedades básicas da linguagem

Layout com Flexbox e Grid, custom properties (25 tokens em `:root`), pseudo-
elementos (`::before`, `::after`), pseudo-classes (`:hover`, `:active`,
`:focus-visible`), 5 media queries e 2 blocos `@supports`.

### 4 — Unidades de medida relativas **e** absolutas

Este é o objetivo didático da aula, então está explícito no código. Contagem
real de ocorrências no `styles.css`:

| Tipo | Unidade | Ocorrências | Onde |
| --- | --- | --- | --- |
| **Relativa** | `rem` | 12 | Tamanhos de fonte (1rem = 16px, respeita a preferência do usuário) |
| **Relativa** | `em` | 6 | `letter-spacing` — relativo à própria fonte do elemento |
| **Relativa** | `vw` | 6 | Dentro dos `clamp()`, acompanhando a largura da tela |
| **Relativa** | `ch` | 3 | Largura de parágrafos (~65 caracteres por linha) |
| **Relativa** | `clamp()` | 9 | Tipografia e espaçamentos fluidos |
| **Absoluta** | `px` | 96 | Bordas de 1px, raios, sombras, larguras do protótipo e detalhes finos |

A combinação dos dois tipos aparece lado a lado nos tokens:

```css
--fonte-titulo-banner: clamp(2.4rem, 6.5vw, 4rem);  /* relativa */
--largura-modulo: 530px;                            /* absoluta */
--espaco-secao: clamp(64px, 9vw, 112px);            /* px como mínimo/máximo */
```

`clamp()` cresce com a tela mas nunca passa do mínimo nem do máximo — substitui
media queries de tipografia e evita saltos bruscos de tamanho.

### 5 — Interligar HTML e CSS

O HTML original **não tem nenhum `<link>` de CSS**. Foram adicionados:

```html
<link rel="stylesheet" href="assets/css/reset.css">
<link rel="stylesheet" href="assets/css/styles.css">
```

### 6 — Seguir o protótipo

O layout mantém a estrutura de seções do protótipo (banner → conteúdo do curso
→ faixa com imagem → desafios → rodapé), com as medidas de referência: módulos
de 530px, conteúdo de 800px e hero de 600px de altura mínima. A branch
`versao-dio` é a comparação direta com o gabarito — nela, **32 de 35
propriedades calculadas** ficaram idênticas, com a altura total diferindo 0,6%.

### 7 — Gradiente no texto com `background-clip`

```css
@supports ((-webkit-background-clip: text) or (background-clip: text)) {
    .banner h1 {
        background-image: var(--grad-titulo);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
    }
}
```

O `@supports` protege o resultado: antes dele, o título já recebe uma **cor
sólida**. Se o navegador não souber recortar o texto, aparece verde sólido em
vez de texto invisível. A mesma técnica é usada no texto da faixa parallax.

### Regra: sem bibliotecas ou frameworks

CSS 100% próprio. A fonte **Fraunces** é baixada e auto-hospedada em
`assets/fonts/` — uma fonte é licenciada como um arquivo, não é biblioteca nem
framework, e o `@font-face` faz parte do CSS puro. Isso também elimina a
dependência do Google Fonts: a página funciona **offline**.

---

## Acessibilidade

O desafio não pede, mas foi incluído:

| Recurso | Motivo |
| --- | --- |
| **Skip link** | Pular o banner e ir ao conteúdo pelo teclado |
| **`:focus-visible`** | Contorno de 3px dourado em todos os controles |
| **`aria-labelledby`** | Cada `<section>` nomeada pelo próprio título |
| **`role="list"` no `<ul>`** | Ver nota abaixo — correção necessária |
| **`alt` descritivo** | Todas as imagens descrevem o conteúdo visual |
| **`lang="pt-BR"`** | Idioma correto para leitores de tela |
| **`prefers-reduced-motion`** | Respeita quem desativou animações |
| **`forced-colors`** | Suporte ao alto contraste do Windows |
| **`tabindex="-1"` no `<main>`** | O skip link move o foco, não só rola a página |
| **CTA é `<a>`, não `<button>`** | Tem destino real e funciona sem JavaScript |

### Nota: por que o `role="list"` foi adicionado

O `reset.css` zera o estilo das listas com `list-style: none`. Só que o
**Safari com VoiceOver remove a semântica de lista** quando o marcador é
removido por CSS — a lista deixa de ser anunciada como lista com 3 itens.

O `<ul class="modules-list">` recebeu `role="list"`, que repõe a semântica sem
alterar a aparência. É o conserto padrão para esse caso e não afeta outros
navegadores.

---

## Cores e contraste

A paleta foi escolhida calculando a razão de contraste da WCAG **antes** de
escrever o CSS. Todas as combinações passam em AA:

| Onde | Texto | Fundo | Contraste |
| --- | --- | --- | --- |
| Parágrafo do banner | `#eef7ee` | `#081409` | 17,21:1 |
| Título da seção (`h2`) | `#86efac` | `#081409` | 13,42:1 |
| Chip do módulo | `#081409` | `#86efac` → `#4ade80` | 13,42:1 → 10,81:1 |
| Texto do módulo | `#eef7ee` | `#12261a` | 14,55:1 |
| Texto secundário | `#a9c7b2` | `#081409` | 10,32:1 |
| Skip link | `#081409` | `#86efac` | 13,42:1 |
| Anel de foco | `#fbbf24` | `#081409` | 11,29:1 |

### Contraste do texto **sobre as imagens**

Paleta correta não garante leitura sobre uma ilustração. Então o texto foi
medido **depois de renderizado**: o texto foi escondido, o fundo capturado por
baixo dele e os pixels comparados um a um.

O primeiro resultado acusou falha — 3,74% dos pixels abaixo de 3:1. Investigando:
eram pixels de **borda anti-serrilhada** (transição entre texto e fundo), não o
texto. Aplicando erosão de 2px na máscara e separando o texto da sombra
decorativa, o resultado real:

| Elemento | Contraste mediano | Pior caso | Pixels abaixo de 3:1 |
| --- | --- | --- | --- |
| `h1` do banner (64px) | 9,73:1 | 5,35:1 | **0** |
| Texto da faixa parallax (44px) | 11,55:1 | 9,70:1 | **0** |

Para isso, o banner e a faixa parallax levam um **gradiente escuro** por cima da
imagem: garante a leitura do texto sem esconder a paisagem.

---

## Verificações executadas

### axe-core — WCAG 2.0/2.1 A + AA + best-practice

| Violações | Verificações aprovadas |
| --- | --- |
| **0** | **39** |

### Responsividade

| Largura | Overflow | CTA | `h1` | Parallax |
| --- | --- | --- | --- | --- |
| 1440px | não | 314×58 | 64px | fixo |
| 1200px | não | 314×58 | 64px | fixo |
| 1024px | não | 314×58 | 64px | fixo |
| 768px | não | 314×58 | 49,9px | rolável |
| 420px | não | 254×51 | 38,4px | rolável |
| 360px | não | 254×51 | 38,4px | rolável |

O `clamp()` faz o título escalar de 64px a 38,4px sem media query. O alvo de
toque do CTA fica acima de 44×44 em todas as larguras (WCAG 2.5.8).

### Recursos

| Verificação | Resultado |
| --- | --- |
| Requisições falhadas | nenhuma |
| Fonte Fraunces carregada | ✅ `loaded` |
| SVGs carregando | ✅ 5 de 5 |
| Erros de JavaScript | nenhum |
| Semântica de lista preservada | ✅ 3 itens |

### Peso

| Pasta | Tamanho |
| --- | --- |
| Fontes | 132 KB |
| Imagens (SVG vetorial) | 32 KB |
| CSS | 28 KB |
| **Projeto** | **220 KB** |

As ilustrações são SVG desenhados para o projeto — nítidos em qualquer
resolução. O repositório original da DIO usa PNGs que somam ~23,8 MB.

---

## Como visualizar

```bash
xdg-open index.html   # Linux
```

Não precisa de servidor local e **não depende de nenhum serviço externo**: as
fontes e as imagens são arquivos do próprio projeto, então funciona offline.

---

## Referências

- [DIO](https://www.dio.me/)
- [Protótipo no Figma](https://www.figma.com/file/3PiokoJj9IhGDnNiWAJbz7/DIO---Desafio-01)
- [MDN — `clamp()`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/clamp)
- [MDN — `background-clip`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/background-clip)
- [MDN — `mask-composite`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/mask-composite)
- [MDN — unidades e valores CSS](https://developer.mozilla.org/pt-BR/docs/Learn/CSS/Building_blocks/Values_and_units)
- [Fraunces (fonte variável)](https://fonts.google.com/specimen/Fraunces)
- [Repositório original do desafio](https://github.com/digitalinnovationone/trilha-css-desafio-01)

---

Feito durante a Trilha de CSS da DIO — Desafio 01, com tema livre.
