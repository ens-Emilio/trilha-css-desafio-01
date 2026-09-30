# Vida no Campo — Landing Page com HTML e CSS

Desafio 01 da [Trilha de CSS da DIO](https://www.dio.me/), com **tema livre**.

> **Sobre o desafio:** o repositório base da DIO entrega o HTML e as imagens,
> **sem nenhum CSS**. O desafio é escrever toda a estilização do zero, a partir
> do protótipo do [Figma](https://www.figma.com/file/3PiokoJj9IhGDnNiWAJbz7/DIO---Desafio-01),
> praticando os fundamentos do CSS e as **unidades de medida relativas e
> absolutas**.
>
> Mantive a **arquitetura e as técnicas** do desafio, mas troquei o conteúdo e a
> identidade visual por um tema próprio: **Raiz Viva**, uma escola de vida no
> campo e agricultura sustentável.

---

## Sobre o projeto

**Raiz Viva** é uma escola fictícia de agricultura sustentável. A página é a
landing page de inscrição da formação **Vida no Campo**.

| Seção | Conteúdo |
| --- | --- |
| **Banner** | Logo, título "Vida no Campo", chamada e botão de inscrição |
| **O que vou aprender?** | 3 módulos em formato pílula |
| **Cultive o futuro** | Faixa com imagem fixa (parallax) |
| **Desafios no campo** | Imagem e texto sobre a comunidade |
| **Rodapé** | Logo e link para o código |

### Estrutura

```
trilha-css-desafio-01/
├── index.html
├── assets/
│   ├── css/
│   │   ├── reset.css      Normalização, box-sizing e fonte Raleway
│   │   └── styles.css     Estilização completa (433 linhas comentadas)
│   └── images/
│       ├── logo.svg        Broto de duas folhas
│       ├── banner.svg      Amanhecer sobre campos cultivados
│       ├── campo.svg       Pessoa trabalhando na plantação (parallax)
│       ├── comunidade.svg  Três pessoas plantando juntas
│       └── logo-rodape.svg
└── README.md
```

---

## Decisões técnicas

### 1. Tokens de design em variáveis CSS

Cores, gradientes e medidas ficam todos em `:root`. Mudar o tema inteiro é
alterar um bloco só:

```css
--cor-fundo: #0b1a10;
--cor-acento: #4ade80;
--cor-sol: #fbbf24;
```

### 2. Unidades relativas e absolutas

Este era o objetivo didático da aula, então está explícito no código:

| Tipo | Onde | Por quê |
| --- | --- | --- |
| `rem` | Tamanhos de fonte | Escala junto com a preferência do usuário |
| `vw` | Dentro dos `clamp()` | Acompanha a largura da tela |
| `px` | Larguras, bordas, raios | Medidas que não devem escalar |
| `%` | Larguras fluidas | Ocupa o espaço disponível |

O `clamp()` combina as duas: `clamp(1.75rem, 4.5vw, 2.5rem)` cresce com a tela
mas nunca passa do mínimo nem do máximo. Isso substitui media queries de
tipografia e evita os saltos bruscos de tamanho.

### 3. O botão ficou realmente arredondado

O botão tem **borda em gradiente** e formato **pílula** ao mesmo tempo. A
implementação mais direta usa `border-image` — mas essa propriedade
**ignora o `border-radius`**, então o botão sairia com cantos retos.

A solução usa um `::before` com gradiente recortado por
`mask-composite: exclude`, que deixa só o anel da borda visível e preserva o
arredondamento. Verifiquei medindo os pixels das bordas e dos cantos:

| Região | Borda visível |
| --- | --- |
| Meio das 4 bordas | **4/4** |
| 4 cantos | **0/4** ← é isso que prova a curva |

### 4. Gradiente no texto com fallback

`background-clip: text` exige prefixo `-webkit-` em alguns navegadores. Em vez
de confiar cegamente, o título recebe **primeiro uma cor sólida** e o gradiente
só é aplicado dentro de um `@supports`. Sem suporte, aparece verde sólido em
vez de texto invisível.

### 5. Parallax só onde funciona

`background-attachment: fixed` quebra em celulares e tablets. Foi restrito a:

```css
@media (min-width: 1024px) and (hover: hover) and (pointer: fine) { ... }
```

### 6. Contraste medido, não estimado

Escolhi a paleta calculando a razão de contraste da WCAG antes de escrever o
CSS:

| Uso | Cor | Sobre o fundo |
| --- | --- | --- |
| Texto | `#f0fdf4` | 17,2:1 |
| Acento verde | `#4ade80` | 10,3:1 |
| Acento verde claro | `#6ee7a8` | 11,7:1 |
| Dourado (sol) | `#fbbf24` | 10,8:1 |
| Texto sobre o painel | `#f0fdf4` sobre `#1b2f22` | 13,6:1 |

Mas o texto **sobre as imagens** não dá para garantir só pela paleta. Então
medi depois de renderizar: escondi o texto, capturei o fundo exato por baixo
dele, e comparei pixel a pixel.

| Elemento | Contraste mediano | Pior caso | Abaixo de 3:1 |
| --- | --- | --- | --- |
| `h1` do banner | 8,51:1 | 5,85:1 | **0 pixels** |
| Destaque parallax | 16,55:1 | 8,49:1 | **0 pixels** |

Para isso, o banner e a faixa parallax levam um **gradiente escuro** por cima
da imagem: garante a leitura do texto sem esconder a paisagem.

### 7. Imagens em SVG

Todas as ilustrações são SVG desenhados para este tema — nada copiado de
terceiros. Como são vetoriais, ficam nítidas em qualquer resolução e pesam
pouquíssimo:

| Versão | Peso das imagens |
| --- | --- |
| PNGs do repositório original da DIO | ~23,8 MB |
| **SVGs deste projeto** | **13,9 KB** |

**Cerca de 1.670× menor.** O projeto inteiro cabe em 84 KB.

---

## Acessibilidade

| Recurso | Motivo |
| --- | --- |
| **Skip link** | Pular o banner e ir ao conteúdo pelo teclado |
| **`:focus-visible`** | Contorno de 3px dourado em todos os controles |
| **`aria-labelledby`** | Cada `<section>` nomeada pelo próprio título |
| **`alt` descritivo** | Todas as imagens descrevem o conteúdo visual |
| **`lang="pt-BR"`** | Idioma correto para leitores de tela |
| **`prefers-reduced-motion`** | Respeita quem desativou animações |
| **`forced-colors`** | Suporte ao alto contraste do Windows |
| **`<ul>` na lista de módulos** | São itens de lista de verdade |
| **CTA é um `<a>`, não `<button>`** | Tem destino real, funciona sem JavaScript |
| **`tabindex="-1"` no `<main>`** | O skip link move o foco, não só rola a página |

---

## Verificações executadas

### axe-core — WCAG 2.0/2.1 A + AA + best-practice

| Violações | Verificações aprovadas |
| --- | --- |
| **0** | 35 |

### Layout medido no navegador (1440px)

| Medida | Valor |
| --- | --- |
| Largura do conteúdo | 800px exatos |
| Largura da pílula de módulo | 530px |
| Título `h1` | 48px, peso 900, gradiente recortado |
| Título `h2` | 32px, verde `#4ade80` |
| Overflow horizontal | nenhum |
| Erros de JavaScript | nenhum |

### Teclado

| Teste | Resultado |
| --- | --- |
| Skip link é o primeiro elemento focável | ✅ aparece em `x=0` |
| Enter move o foco para o `<main>` | ✅ |
| Ordem de foco | skip link → CTA |

### Responsividade

| Largura | Overflow | CTA | `h1` | Parallax |
| --- | --- | --- | --- | --- |
| 1440px | não | cabe | 48px | fixo |
| 1024px | não | cabe | 48px | fixo |
| 768px | não | cabe | 38,4px | rolável |
| 420px | não | cabe | 30,4px | rolável |
| 360px | não | cabe | 30,4px | rolável |

### Imagens

Todas carregam e têm `alt`:

| Arquivo | Dimensões |
| --- | --- |
| `logo.svg` | 360×214 |
| `comunidade.svg` | 659×429 |
| `logo-rodape.svg` | 420×60 |

---

## Como visualizar

```bash
xdg-open index.html   # Linux
```

Não precisa de servidor local e não depende de nenhum serviço externo — as
imagens são arquivos do próprio projeto. A fonte Raleway vem do Google Fonts;
sem internet, o navegador usa a fonte de sistema definida no `font-family`.

---

## O que mudou em relação à versão anterior

Este repositório nasceu como a reprodução fiel do layout da DIO. Depois
recebeu tema próprio, a pedido. A arquitetura CSS é a mesma; mudaram o
conteúdo, a paleta e os assets.

| | Versão anterior | Versão atual |
| --- | --- | --- |
| Tema | Trilha de CSS da DIO | Vida no Campo (Raiz Viva) |
| Paleta | Azul `#33a8db` sobre preto | Verde `#4ade80` sobre verde-escuro |
| Imagens | PNGs da DIO (23,8 MB) | SVGs próprios (13,9 KB) |
| CTA | `<button>` sem ação | `<a>` com destino real |
| Peso do projeto | 836 KB | **84 KB** |

A versão anterior continua no histórico do Git.

---

## Referências

- [DIO](https://www.dio.me/)
- [Protótipo no Figma](https://www.figma.com/file/3PiokoJj9IhGDnNiWAJbz7/DIO---Desafio-01)
- [MDN — `background-clip`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/background-clip)
- [MDN — `clamp()`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/clamp)
- [MDN — `mask-composite`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/mask-composite)
- [MDN — unidades CSS](https://developer.mozilla.org/pt-BR/docs/Learn/CSS/Building_blocks/Values_and_units)

---

Feito durante a Trilha de CSS da DIO — Desafio 01, com tema livre.
