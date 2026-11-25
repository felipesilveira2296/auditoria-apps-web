# Auditoria de apps web — SEO técnico e indexação no Google

Escopo: itens 93–109, IDs `SEO-`. Leia antes [`00-metodo.md`](00-metodo.md): modo de trabalho, estados, relatório e regras para implementar valem para todos os arquivos. A numeração dos itens é global e não muda entre arquivos.

## M. SEO técnico e indexação no Google

Site lento e imagem pesada estão nos itens 01, 02 e 24; HTTPS no item 61. Aplique esta seção a páginas públicas que devem aparecer na busca. Áreas logadas, painéis, APIs e ambientes de teste devem ficar **fora** do índice. Verifique no build de produção e no HTML entregue, não só no código-fonte. Search Console exige acesso do responsável; sem ele, marque **Não verificado**.

**93. Páginas quebradas (404/5xx)** — Rastreie as rotas públicas, o sitemap e os links internos procurando 404, 410, 5xx e soft 404 (página de erro respondendo `200`). Rotas inexistentes devem responder status real com página 404 útil. Conteúdo removido ganha redirect 301 para o equivalente mais próximo, nunca tudo para a home.

**94. Links internos errados** — Procure links para rotas inexistentes, URLs antigas, `localhost`/staging, âncoras quebradas, links que dependem de evento de clique sem `<a href>` e navegação feita só com botões. Componentes de link do framework devem gerar `<a>` com `href` real.

**95. H1 e hierarquia de títulos** — Cada página indexável deve ter um único `<h1>` descritivo e títulos `h2`–`h6` em ordem lógica. Não use títulos só para estilo nem esconda o H1.

**96. robots.txt** — Verifique se `robots.txt` existe, responde `200` e não bloqueia páginas públicas, CSS, JS ou imagens necessários à renderização. Confirme que staging/preview não publica um `robots.txt` permissivo e que produção não herdou `Disallow: /` de teste. Inclua a linha `Sitemap:`.

**97. Noindex sem querer** — Procure `noindex` em `<meta name="robots">`, `X-Robots-Tag`, configuração de metadados do framework ou CMS (ex.: "desencorajar mecanismos de busca" no WordPress), middleware, servidor web e variáveis de ambiente. Produção pública não deve ter `noindex`; staging, preview, painéis e páginas de agradecimento/checkout devem ter.

**98. Sitemap** — Verifique `sitemap.xml` (estático ou gerado pelo framework/CMS) com apenas URLs canônicas, indexáveis, com status `200` e HTTPS, `lastmod` real, sem rotas privadas e dividido se passar de 50 mil URLs. Confirme envio no Search Console quando houver acesso.

**99. Quebra no celular** — Verifique `<meta name="viewport">`, ausência de rolagem horizontal, textos legíveis, alvos de toque adequados e o mesmo conteúdo no mobile e no desktop (indexação mobile-first). Teste em 360–390 px de largura.

**100. Canonical** — Cada página indexável deve ter `<link rel="canonical">` absoluto apontando para sua URL preferida. Variações com parâmetros, filtros, paginação, maiúsculas e barra final devem apontar corretamente. Canonical não pode apontar para página com 404, redirect ou `noindex`.

**101. Títulos e descrições repetidos** — Verifique `<title>` e `meta description` únicos por página, descritivos, sem texto padrão de template ou framework (ex.: "Create Next App", "Vite App", "Laravel", "Just another WordPress site") e com tamanho razoável. Defina metadados por rota ou página.

**102. Endereços (URLs) confusos** — Prefira URLs legíveis, curtas, minúsculas, com hífen e palavras relevantes; evite IDs soltos, parâmetros de sessão, `#` para rotas (hash router) e várias URLs para o mesmo conteúdo. Mudança de URL exige redirect 301.

**103. Redirects em cadeia** — Procure cadeias (A→B→C) e loops entre HTTP/HTTPS, www/sem www, barra final e URLs antigas. Cada URL antiga deve ir direto ao destino final com 301/308. Revise redirects do framework, middleware, servidor web (`.htaccess`, nginx), plugins de CMS e regras da hospedagem/CDN.

**104. Dados estruturados (schema)** — Verifique JSON-LD adequado ao conteúdo (`Organization`, `LocalBusiness`, `Product`, `Article`, `BreadcrumbList`, `FAQPage` etc.), coerente com o que aparece na página e sem avaliações ou preços falsos. Valide no Rich Results Test.

**105. Texto em imagem** — Procure títulos, preços, ofertas e textos importantes renderizados só como imagem ou canvas. Use texto HTML real; quando a imagem for necessária, repita a informação no texto.

**106. Conteúdo só em JavaScript** — Confira o HTML inicial de produção (`curl` ou "ver código-fonte") das páginas públicas: título, H1, texto principal, links e metadados devem estar presentes sem executar JS. SPAs puras devem considerar renderização no servidor ou pré-renderização (item 05). Não dependa de conteúdo carregado após interação ou rolagem infinita sem links paginados.

**107. Imagem sem alt** — Imagens com conteúdo precisam de `alt` descritivo; decorativas usam `alt=""`. Verifique também nomes de arquivo legíveis, dimensões e imagens importantes incluídas no HTML, não só em CSS de fundo.

**108. Páginas órfãs** — Cruze sitemap e rotas públicas com os links internos: toda página importante deve ser alcançável por links rastreáveis a poucos cliques da home (menu, rodapé, categorias, breadcrumbs). Página só no sitemap ou acessível só por busca interna é órfã.

**109. Com e sem www (versão única)** — Escolha uma versão (`https://www` ou `https://` sem www) e redirecione as outras três combinações com 301 direto para ela. Canonical, sitemap, links internos, Open Graph e Search Console devem usar a mesma versão.

## Como comprovar

- **Status/links:** rastreio das rotas públicas não encontra 404, 5xx, soft 404 nem links internos quebrados.
- **Indexação:** produção sem `noindex` indevido; staging/painéis com `noindex`; `robots.txt` não bloqueia o necessário.
- **Sitemap/canonical:** todas as URLs do sitemap respondem `200`, são canônicas e coincidem com o `rel="canonical"`.
- **HTML inicial:** `curl` da página mostra título, H1, texto principal, links e metadados sem executar JS.
- **Redirects/www:** cada variação de HTTP/HTTPS e www chega ao destino final em um único 301/308.
- **Metadados/schema:** títulos e descrições únicos; JSON-LD válido no Rich Results Test.
- **Órfãs/mobile:** páginas do sitemap aparecem em links internos; layout sem rolagem horizontal em 360 px.
