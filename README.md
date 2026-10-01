# Desafio 01: Criando sua primeira Landing Page com HTML e CSS

Desafio de Projeto da [Trilha de CSS da DIO](https://www.dio.me/).

> **Versão:** esta branch (`versao-dio`) é a **reprodução fiel do layout do
> desafio** — azul da DIO sobre preto, medindo 32 de 35 propriedades idênticas
> ao gabarito oficial.
>
> A branch [`main`](../../tree/main) traz a versão **Vida no Campo** (Raiz
> Viva), com tema próprio, fonte Fraunces auto-hospedada e acabamento
> refinado. As duas partem do mesmo HTML e da mesma estrutura de CSS —
> compará-las mostra exatamente o que muda quando se troca paleta, tipografia e
> conteúdo mantendo a arquitetura.

O repositório original entrega o HTML e as imagens, **sem nenhum CSS** — o
desafio é escrever toda a estilização do zero, a partir do protótipo do
[Figma](https://www.figma.com/file/3PiokoJj9IhGDnNiWAJbz7/DIO---Desafio-01),
aplicando os fundamentos de CSS e as unidades de medida relativas e absolutas.

---

## Resultado

Landing page da Trilha de CSS da DIO, com banner em gradiente, título em
gradiente recortado no texto, lista de módulos em formato pílula e seção com
imagem fixa (parallax).

### Arquivos

```
trilha-css-desafio-01/
├── index.html
├── assets/
│   ├── css/
│   │   ├── reset.css      Normalização e fonte (Raleway)
│   │   └── styles.css     Toda a estilização (≈330 linhas comentadas)
│   └── images/
│       ├── banner.png
│       ├── dio-logo.png
│       ├── logo.png
│       ├── professional-challenges.png
│       └── woman-code.jpg   (era .png de 22 MB, ver Otimização)
└── README.md
```

---

## O que foi implementado

### Banner

- Fundo com **três camadas**: gradiente escuro → azul translúcido → escuro,
  sobre a imagem `banner.png`
- Logo dentro de um **círculo de 260px** com fundo semitransparente
- **Título com gradiente recortado**: `background-clip: text` + `color: transparent`
- Botão de chamada para ação em formato pílula

### Conteúdo do curso

- Lista de 3 módulos em **pílulas de 530px** com borda azul, fundo `#252525`
  e sombra interna (`inset`)
- Rótulo "Módulo 01" destacado em azul dentro de cada pílula

### Transforme o mundo

- Imagem de fundo com `background-attachment: fixed` (efeito parallax)
- Borda superior e inferior azul translúcida
- Texto em `lowercase`, peso 900, com `text-shadow` azul deslocado

### Desafios profissionais

- Seção centralizada de 800px, com imagem e texto

### Rodapé

- Gradiente de azul transparente para azul 20% (transição suave a partir do fundo)
- Logo da DIO e link para `dio.me`

---

## Decisões técnicas

### 1. Tokens de design em variáveis CSS

Todas as cores, gradientes, larguras e tamanhos de fonte estão em `:root`.
Mudar o tema inteiro é alterar um bloco só, em vez de caçar valores soltos
pelo arquivo.

### 2. O botão ficou realmente arredondado

O botão do protótipo tem **borda em gradiente azul e formato pílula**. A
implementação mais direta usa `border-image`, mas ela tem uma limitação:
**`border-image` ignora `border-radius`**, então o botão sairia com cantos retos.

Comparei as duas abordagens medindo os pixels dos quatro cantos do botão:

| Abordagem | Cantos azuis | Bordas azuis | Resultado |
| --- | --- | --- | --- |
| `border-image` | 4/4 | 4/4 | Retangular — radius ignorado |
| **Máscara + `border-radius`** | **0/4** | **4/4** | **Pílula correta** |

A solução usa um pseudo-elemento `::before` com um gradiente recortado por
`mask-composite: exclude`, deixando apenas o anel da borda visível. Assim a
borda em gradiente **e** o arredondamento coexistem.

### 3. Gradiente no texto com fallback

`background-clip: text` exige prefixo `-webkit-` em alguns navegadores. Em vez
de confiar cegamente, o título recebe **primeiro uma cor sólida azul** e o
gradiente só é aplicado dentro de um `@supports`. Se o navegador não souber
recortar o texto, ele mostra azul sólido em vez de texto invisível.

### 4. Tipografia fluida com `clamp()`

Os tamanhos usam `clamp(minimo, preferencial, maximo)`, por exemplo:

```css
--fonte-titulo-banner: clamp(1.75rem, 4.5vw, 2.5rem);
--espaco-secao: clamp(60px, 9vw, 100px);
```

Isso faz o texto e o espaçamento acompanharem a largura da tela **sem
`@media` query**, e sem os saltos bruscos que as media queries causam em
algumas resoluções.

### 5. Parallax só onde funciona

O `background-attachment: fixed` é aplicado apenas quando faz sentido:

```css
@media (min-width: 1024px) and (hover: hover) and (pointer: fine) { ... }
```

Em celulares e tablets o parallax costuma quebrar (o iOS, por exemplo, ignora
ou renderiza errado). Fora dessas condições, a imagem usa o
comportamento padrão.

### 6. Responsividade

O protótipo usa larguras fixas (`width: 600px`, `width: 800px`), o que
**estoura a tela** em celulares. Aqui as larguras viraram `max-width` com
`width: 100%`, e foi adicionado um respiro lateral nas seções para o texto não
encostar nas bordas.

O respiro é somado à largura máxima para que o **conteúdo** continue com os
800px exatos do protótipo no desktop:

```css
--largura-conteudo: 800px;
--respiro-lateral: 20px;
max-width: calc(var(--largura-conteudo) + var(--respiro-lateral) * 2);
```

### 7. Otimização da imagem

A imagem `woman-code.png` do repositório original tem **4737×3160 pixels e
22,3 MB** — pesada demais para usar como fundo de seção. Foi convertida para
JPEG de 1920px de largura:

| Versão | Dimensões | Peso |
| --- | --- | --- |
| Original | 4737×3160 PNG | 22,3 MB |
| **Otimizada** | **1920×1281 JPEG** | **276 KB** |

**83× menor (98,8% de redução)**, com a pasta de imagens inteira caindo de
~23,8 MB para **786 KB**. Para uma imagem de fundo coberta por gradiente e
texto, a diferença visual é imperceptível.

---

## Acessibilidade

A página recebeu recursos que não estavam no protótipo original:

| Recurso | Motivo |
| --- | --- |
| **Skip link** | Pular o banner e ir direto ao conteúdo pelo teclado |
| **`:focus-visible`** | Contorno de 3px em todos os controles focáveis |
| **`aria-labelledby`** | Cada `<section>` é nomeada pelo próprio título |
| **`alt` descritivo** | Todas as imagens, sem repetir o `title` |
| **`lang="pt-BR"`** | A página original declarava `lang="en"` |
| **`prefers-reduced-motion`** | Respeita quem desativou animações no sistema |
| **`forced-colors`** | Suporte ao alto contraste do Windows |
| **`<ul>` em vez de `<div>`** | A lista de módulos é semanticamente uma lista |
| **`type="button"`** | Deixa explícito que o botão não envia formulário |

O contraste foi calculado com a fórmula de luminância da WCAG: o azul da marca
(`#33a8db`) tem **7,76:1 sobre o fundo preto** e **5,67:1 sobre o cinza dos
módulos** — passa em AA nos dois casos, então a cor do protótipo foi mantida.

---

## Verificações executadas

### Comparação com o resultado de referência (`branch final`)

Rodei os dois lado a lado no Chromium (1440px) e comparei as propriedades
calculadas. **32 de 35 itens idênticos**, incluindo cores, tamanhos, pesos,
`letter-spacing`, `text-transform`, sombras, bordas e larguras.

Posicionamento das seções:

| Seção | Referência | Meu | Diferença |
| --- | --- | --- | --- |
| Banner | y=0, h=600 | y=0, h=600 | 0px |
| Conteúdo do curso | y=700 | y=700 | 0px |
| Transforme o mundo | h=560 | h=560 | 0px |
| Rodapé | h=187 | h=188 | 1px |
| **Altura total** | **2748px** | **2765px** | **17px (0,6%)** |

A diferença de 17px vem do `line-height: 1.5` aplicado ao corpo do texto —
uma escolha de legibilidade, já que o original usa o entrelinhamento padrão
do navegador (~1,2). Os títulos usam 1.15.

As outras diferenças intencionais:

1. **Botão em pílula** em vez de retangular (corrige o bug do `border-image`)
2. **Azul do botão** `#33a8db` em vez de `#31a8dd` — mantém o token da marca
   consistente em todo o arquivo; a diferença é imperceptível

### axe-core (WCAG 2.0/2.1 A + AA + best-practice)

| Violações | Verificações aprovadas |
| --- | --- |
| **0** | 35 |

### Responsividade

| Largura | Overflow horizontal | Botão | Título h1 | Parallax |
| --- | --- | --- | --- | --- |
| 1440px | não | cabe | 40px | fixo |
| 1024px | não | cabe | 40px | fixo |
| 768px | não | cabe | 34,6px | rolável |
| 420px | não | cabe | 28px | rolável |
| 360px | não | cabe | 28px | rolável |

Nenhum overflow horizontal em nenhuma largura, e o tamanho do título escala
junto com a tela via `clamp()`.

---

## Como visualizar

```bash
xdg-open index.html   # Linux
```

Não precisa de servidor local. A fonte Raleway vem do Google Fonts, então sem
internet o navegador usa a fonte de sistema definida no `font-family` como
alternativa.

---

## Referências

- [DIO](https://www.dio.me/)
- [Protótipo no Figma](https://www.figma.com/file/3PiokoJj9IhGDnNiWAJbz7/DIO---Desafio-01)
- [MDN — `background-clip`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/background-clip)
- [MDN — `clamp()`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/clamp)
- [MDN — `mask-composite`](https://developer.mozilla.org/pt-BR/docs/Web/CSS/mask-composite)
- [Repositório original do desafio](https://github.com/digitalinnovationone/trilha-css-desafio-01)

---

Feito durante a Trilha de CSS da DIO — Desafio 01.
