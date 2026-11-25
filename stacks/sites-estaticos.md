# Guia de stack — Sites estáticos

Aplique junto com os itens gerais. Este guia não cria itens novos: ele diz como verificar itens existentes em sites entregues como arquivos prontos (HTML, CSS, JS e mídia), escritos à mão ou gerados no build. Use os mesmos IDs no relatório.

Em site estático, boa parte da segurança e do desempenho depende da **configuração da hospedagem**, não do código. Sempre leia o arquivo de configuração do provedor antes de concluir um item.

## Detecção

- **HTML à mão:** arquivos `.html` na raiz ou em `public/`, sem etapa de build obrigatória.
- **Geradores:** Hugo (`hugo.toml`/`config.toml`), Jekyll (`_config.yml`, `Gemfile`), Eleventy (`.eleventy.js`, `eleventy.config.*`), Astro com saída estática (`astro.config.*` sem adaptador de servidor), exportação estática de outros frameworks (ex.: `output: 'export'` no Next).
- **Hospedagem e configuração:** GitHub Pages (`CNAME`, workflow em `.github/workflows`), Netlify (`netlify.toml`, `_headers`, `_redirects`), Cloudflare Pages (`_headers`, `_redirects`), Firebase Hosting (`firebase.json`), Vercel (`vercel.json`), S3/CloudFront, Apache (`.htaccess`) ou nginx.
- **Partes dinâmicas:** formulários (serviço de terceiros, função serverless ou envio por e-mail), CMS headless, busca, comentários, analytics e embeds.

Registre o que é servido de fato: a pasta publicada (`public/`, `dist/`, `_site/`, `out/`) e não só os arquivos-fonte.

## Desempenho

- **05. Estratégia de renderização** — Se o HTML publicado já traz o conteúdo completo, marque **Conforme**. Desconfie de páginas que carregam o conteúdo principal por JavaScript a partir de JSON ou API.
- **06. JavaScript no cliente** — Procure bibliotecas inteiras para pouco uso (jQuery, sliders, animações, ícones em fonte), scripts carregados em todas as páginas e ausência de `defer`/`async`.
- **07–10 e 28–33** — Em geral **Não se aplica**. Avalie só se houver framework no cliente, banco, funções serverless ou integrações.
- **17. Compressão** — Confirme Brotli/Gzip na hospedagem. Em servidor próprio, confira `.htaccess`/nginx.
- **18. Cache HTTP** — HTML deve ter cache curto ou revalidação; arquivos com hash no nome podem usar cache longo com `immutable`. Arquivos sem hash (ex.: `style.css`) com cache longo fazem visitantes verem versões antigas: use hash no nome ou parâmetro de versão.
- **21. CSS** — Procure frameworks CSS inteiros (ex.: Bootstrap completo) para poucos componentes, CSS de páginas antigas e `@import` em cadeia.
- **22. Terceiros** — Inventarie embeds de YouTube, Google Maps, Instagram, widgets de WhatsApp e chat. Prefira carregar sob interação (fachada com imagem e botão).
- **23. Fontes** — Avalie hospedar as fontes no próprio site em vez de carregá-las do Google Fonts: reduz conexões externas e evita enviar o IP do visitante a terceiros (veja o item 79).
- **24–25. Imagens** — Sem otimizador automático, as imagens são publicadas como estão: confira tamanho em disco, dimensões, `srcset`/`sizes`, `width`/`height` e `loading="lazy"` fora da dobra. Geradores costumam ter pipeline de imagens (ex.: Hugo image processing, `@11ty/eleventy-img`, `astro:assets`).
- **34. CDN/estáticos** — Confirme que a hospedagem serve por CDN e que um deploy novo invalida o cache do HTML.
- **38. Runtime/cold start** — Só para funções serverless usadas por formulários ou busca.
- **39. Build de produção** — No gerador, confirme minificação, ausência de rascunhos publicados (`draft`), páginas de teste e arquivos esquecidos na pasta publicada (`.git`, `.env`, backups, `node_modules`).

## Segurança

- **43. Chaves de API ocultas** — Tudo que está no site é público. Chaves de Maps, Firebase, reCAPTCHA ou serviços de formulário só são aceitáveis se forem chaves públicas restritas por domínio/referer e com cota. Nunca chaves privadas, tokens de API de e-mail ou de CMS com permissão de escrita.
- **45–46. Banco acessado pelo navegador** — Se o site usa Firebase ou Supabase direto do navegador, audite regras/RLS como em qualquer app; caso contrário, **Não se aplica**.
- **48–52** — Em geral **Não se aplica**. Se houver área restrita, ela não pode ser protegida só por JavaScript ou por URL "secreta": exija autenticação real na hospedagem ou em uma função.
- **53–54. Limite de tentativas e bots** — Formulários de contato são o principal alvo. Verifique honeypot, captcha ou proteção do serviço de formulário e limite de envios.
- **56. Validar toda entrada** — A validação vale no serviço ou função que recebe o formulário, não só no HTML (`required`, `pattern`).
- **57. Escape de conteúdo** — Procure `innerHTML` com dados externos (feeds, JSON de CMS, parâmetros da URL) e Markdown de CMS renderizado sem sanitização.
- **58. Uploads** — Só se o formulário aceitar anexos: confira tipo, tamanho e onde os arquivos ficam guardados.
- **59. Backup** — A fonte da verdade costuma ser o repositório. Confirme que tudo necessário para reconstruir o site está versionado e que conteúdo de CMS headless ou de serviço de formulário tem exportação.
- **60. Cabeçalhos de segurança** — Configure em `_headers`, `netlify.toml`, `firebase.json`, `vercel.json`, `.htaccess`/nginx ou na CDN. O GitHub Pages não permite cabeçalhos personalizados: nesse caso, use uma CDN na frente ou outra hospedagem. CSP via `<meta>` é limitada e não suporta `frame-ancestors`.
- **61. Forçar HTTPS** — Ative "forçar HTTPS" na hospedagem e confira recursos com `http://` fixos no HTML.
- **62. Atualizar dependências** — Inclua o gerador e seus plugins. Scripts carregados de CDN pública devem ter versão fixa e, quando possível, `integrity` (SRI) com `crossorigin`.
- **63. Proteção de APIs** — Só para funções serverless: CORS restrito ao domínio e limite de tamanho do corpo.
- **64. Logs seguros** — Envios de formulário guardados no serviço ou na caixa de e-mail contêm dados pessoais: restrinja acesso.
- **70. Privilégios administrativos** — Inventarie acessos ao registro do domínio, DNS, hospedagem, repositório, serviço de formulário, CMS e analytics, com MFA. Confira a renovação automática e a data de expiração do domínio.
- **72. Licenças de uso** — Verifique licença de templates, temas, fontes, ícones, fotos de banco de imagens e scripts copiados.

## Privacidade

- **74. Política de privacidade** — Mesmo um site só com formulário de contato e analytics trata dados pessoais e precisa de política acessível.
- **76. Minimização** — O formulário deve pedir só o necessário para responder (nome, contato e mensagem costumam bastar).
- **78–79. Consentimento e cookies** — Analytics, pixels e embeds que gravam cookies (YouTube, Maps, Instagram) só devem carregar após aceitação. Para YouTube, use o domínio `youtube-nocookie.com` e carregamento sob clique. Google Fonts carregado do Google também envia dados a terceiros: prefira hospedar no site.
- **81–82. Exclusão e retenção** — Defina por quanto tempo os envios de formulário ficam no serviço e no e-mail, e como apagá-los a pedido.
- **83. Operadores** — Hospedagem, serviço de formulário, analytics, CDN e e-mail transacional são operadores: liste-os na política.

## Consumidor

- **84–88** — Em geral **Não se aplica** a sites institucionais. Passa a valer se o site oferecer contratação, venda ou assinatura.
- **89. Identificação do fornecedor** — Obrigatória quando há oferta ou contratação online. Em site só institucional, nome, CNPJ e contato visíveis continuam sendo boa prática.
- **90–91. Publicidade e depoimentos** — Depoimentos, logos de clientes e números escritos direto no HTML precisam ser reais e autorizados.

## SEO

- **93. Páginas quebradas** — Confirme que existe `404.html` personalizada e que ela responde com status `404`. Regras de reescrita do tipo "tudo vai para `index.html`" criam soft 404.
- **96 e 98. robots.txt e sitemap** — Em HTML à mão, o sitemap costuma ser manual e desatualizado: confira se cada página publicada está nele. Geradores têm plugins ou templates próprios.
- **97. Noindex** — Confira `<meta name="robots">` copiado de páginas de teste e previews da hospedagem (deploy previews) indexáveis.
- **100. Canonical** — Variações como `/sobre`, `/sobre.html` e `/sobre/` servem o mesmo conteúdo: escolha uma, configure URLs limpas e barra final na hospedagem (ex.: `cleanUrls` e `trailingSlash` no Firebase/Vercel) e aponte o canonical para ela.
- **101. Títulos e descrições** — Em HTML à mão é comum copiar o `<head>` entre páginas: confira `<title>` e `meta description` únicos em cada arquivo.
- **103. Redirects** — Use redirect 301 configurado na hospedagem (`_redirects`, `netlify.toml`, `firebase.json`, `vercel.json`, `.htaccess`). Redirect por `<meta http-equiv="refresh">` ou JavaScript não equivale a 301; no GitHub Pages essa é a única opção sem CDN na frente.
- **104. Dados estruturados** — Sites de empresa local devem considerar `LocalBusiness` ou `Organization` com nome, endereço e contato iguais aos do rodapé.
- **109. Com e sem www** — Configure o domínio principal na hospedagem e o DNS da outra versão para redirecionar para ele.

## Como comprovar (específico)

- **Pasta publicada:** listagem da saída do build (ou do que a hospedagem serve) não contém `.git`, `.env`, backups, rascunhos nem páginas de teste.
- **Cabeçalhos e cache:** `curl -I` no HTML e em um asset mostra os cabeçalhos de segurança, cache curto no HTML e cache longo só em arquivos com hash.
- **URLs duplicadas:** `/pagina`, `/pagina.html` e `/pagina/` levam à mesma URL final por um único redirect, ou só uma delas responde `200`.
- **404:** uma URL inexistente responde status `404` com a página personalizada.
- **Formulário:** envio sem preencher o campo honeypot ou acima do limite é recusado pelo serviço; envio válido chega ao destino sem expor a chave do serviço a ponto de permitir abuso.
- **Domínio:** registro mostra renovação automática ativa e expiração distante.
