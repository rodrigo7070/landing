# CLAUDE.md — landing (PrediPark)

**Landing page estática** do PrediPark ("Sua rota inteligente para estacionar sem estresse").
Site de uma página só, hospedado no **GitHub Pages**, baseado em template Bootstrap. Não é app:
não há build, backend próprio, nem framework JS — é HTML + CSS + libs minificadas servidos direto.

## Sync automatico da pasta local (NAO pedir deploy)

A pasta local deste projeto e sincronizada automaticamente com o Git/GitHub **a cada 2 minutos** (app SyncGit: commit + push automaticos ao detectar mudanca).
Ou seja: tudo que for alterado aqui sobe sozinho e chega ao ambiente publicado sem acao manual.

- NAO rodar `git add` / `git commit` / `git push`, nem executar deploy manual.
- NAO avisar "falta fazer deploy", "lembre de publicar" ou "suba as alteracoes": basta salvar o arquivo e aguardar ate ~2 min.

## ESTADO ATUAL / INVARIANTES (NÃO QUEBRE)

- **Site 100% estático.** Todo o conteúdo está em `index.html` (single page). Não há bundler,
  npm, nem etapa de build — o que está no repositório é o que vai pro ar.
- **Template:** tema Bootstrap estilo "Startup"/UIdeck + **WOW.js** (animações on-scroll, classes
  `wow fadeInUp` com `data-wow-delay`) + **Lineicons** (ícones `lni lni-*`, fontes em
  `assets/fonts/`). **NÃO edite as libs minificadas** (`bootstrap.min.css`,
  `bootstrap.bundle.min.js`, `wow.min.js`, `animate.css`, `lineicons.css`). Estilos próprios vão
  em `assets/css/main.css`.
- **Deploy:** GitHub Pages com **domínio customizado** definido no arquivo `CNAME`
  (valor atual: `conheca.predipark.com`). Não remova o `CNAME` ao publicar — o Pages o recria
  como configuração de domínio; alterar quebra o domínio.
- **Único fluxo dinâmico = formulário de newsletter** (`#subscribeForm`). Fora isso, tudo é
  conteúdo estático e âncoras.
- **CTAs apontam para o app:** botões "Acessar Beta" / "Acessar" levam a
  `https://app.predipark.com/` (`target="_blank"`).

## Estrutura

```
index.html              ← a página inteira (head, seções, script do form)
CNAME                   ← domínio custom do GitHub Pages (conheca.predipark.com)
.gitignore              ← só ignora arquivos de SO/editor
assets/
  css/  bootstrap.min.css, lineicons.css, animate.css, main.css   ← só main.css é "nosso"
  js/   bootstrap.bundle.min.js, wow.min.js, main.js              ← libs minificadas + main.js
  fonts/ LineIcons.*                                               ← fontes dos ícones
  img/   hero/, about/, parceiros/, time/, testimonial/, logo/...  ← imagens das seções
```

> Observação: **não existem** `sitemap.xml` nem `robots.txt` no repositório hoje. Se forem
> adicionados depois, documente aqui.

## Seções (navegação por âncora `#id`)

A navbar usa links `page-scroll` para âncoras na mesma página. Seções, na ordem:

- `#home` — hero (título, subtítulo, CTA "Acessar", imagem).
- `#vantagens` — features.
- `#conquistas` — about / números.
- `#parceiros` — logos de parceiros.
- `#equipe` — time.
- `#contato` — seção do formulário de inscrição (newsletter).

Para adicionar/renomear uma seção, mantenha o par **`id` da `<section>`** ↔ **`href="#id"`** da
navbar em sincronia, senão o scroll suave quebra.

## Formulário de newsletter (`#subscribeForm`)

- Único JS "de aplicação", inline no fim de `index.html` (depois dos `<script>` das libs).
- No `submit`: `preventDefault`, monta `FormData`, mostra spinner, e faz `fetch` **POST** para um
  endpoint do **Google Apps Script** (`https://script.google.com/macros/s/.../exec`).
  Não defina `Content-Type` manualmente — o browser cuida do boundary do `FormData`.
- Feedback ao usuário em `#form-feedback` (`#spinner` / `#message`), some após ~3s; mensagens em
  PT-BR ("Obrigado por se inscrever!" / "Erro: ...").
- **Anti-spam:** o endpoint do Apps Script é **aberto** (qualquer um pode postar nele). Não há
  captcha nem honeypot hoje. Se houver abuso, trate no lado do Apps Script (validação, rate-limit)
  e/ou adicione honeypot/captcha no form.

## Convenções / deploy

- **Cache-busting manual:** assets carregam com query `?v=AAAAMMDDr` (ex.: `?v=20260415r`).
  Ao trocar um CSS/JS/imagem, **incremente o `v`** nas tags correspondentes para furar o cache.
  Há ainda meta tags `Cache-Control: no-cache` no `<head>`.
- Idioma de conteúdo: **PT-BR** (alguns placeholders do template ainda em inglês, ex.: "Your Email").
- Publicação: push na branch servida pelo GitHub Pages; o `CNAME` mantém o domínio custom.
- Acessibilidade/SEO: `<title>`/`<meta description>` ficam no `<head>` de `index.html`.

## Manutenção deste arquivo

Atualize quando: mudar o domínio (`CNAME`), o endpoint do form (Apps Script), o conjunto de seções
e suas âncoras, o template/libs usadas, ou a estratégia de cache-busting. Lembre: **não editar libs
minificadas**; mudanças de estilo vão em `assets/css/main.css`.
