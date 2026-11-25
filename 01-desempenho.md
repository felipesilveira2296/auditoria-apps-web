# Auditoria React/Next — Desempenho

Escopo: itens 01–42, IDs `PERF-`. Leia antes [`00-metodo.md`](00-metodo.md): modo de trabalho, estados, relatório e regras para implementar valem para todos os arquivos. A numeração dos itens é global e não muda entre arquivos.

## Linha de base reproduzível

Antes de recomendar mudanças, obtenha quando for seguro:

- lint, typecheck, testes e build suportados pelo projeto;
- tamanho total e por rota dos bundles JS/CSS, chunks e dependências grandes;
- Lighthouse/equivalente mobile para rotas principais;
- LCP, INP/TBT, CLS, FCP e TTFB, separando laboratório de dados reais;
- número, tamanho e cascata das requisições de uma jornada crítica;
- tempo de APIs críticas e, com acesso seguro, queries lentas;
- renders em interações suspeitas, apenas quando houver indício;
- condições: build de produção ou dev, máquina, navegador, viewport, CPU, rede, cache e autenticação.

Não compare o servidor de desenvolvimento com metas de produção sem explicitar a limitação. Faça três execuções quando houver variação e use a mediana. Se o ambiente não puder iniciar, marque **não medido** e diga exatamente o que falta. Nunca invente métricas.

## A. Medição e experiência real

**01. Core Web Vitals reais** — Verifique LCP, INP e CLS com consentimento quando necessário, associados à rota e analisados no p75. Diferencie RUM de Lighthouse. Não adicione Analytics sem configuração própria.

**02. Mobile e redes lentas** — Avalie jornadas principais com viewport móvel, CPU 4x e Slow 4G/3G quando viável. Registre o que demora mais de 3 s para ficar utilizável.

**03. Observabilidade** — Verifique duração, correlação, métricas, erros e traces. OpenTelemetry é opção, não obrigação; só recomende se a complexidade justificar.

**04. Orçamentos** — Procure limites automatizados de JS por rota, imagens, Web Vitals e regressões no CI. Baseie novos limites na linha de base.

## B. React e Next.js

**05. Estratégia de renderização** — Em Next, avalie SSR, SSG, ISR, RSC, streaming e cliente por rota conforme atualização, personalização e SEO. Em SPA, avalie shell e buscas após montagem. Não converta tudo para SSR.

**06. Client Components e hidratação** — No App Router, procure `use client` alto demais, props grandes, providers globais e bibliotecas que poderiam ficar em ilhas menores. Identifique custo/erros de hidratação.

**07. Suspense, streaming e carregamento** — Localize telas vazias, spinners e waterfalls. Verifique skeletons com geometria do conteúdo, fallbacks acessíveis e boundaries locais.

**08. Renders desnecessários** — Use React Profiler quando possível. Procure estado alto, contextos amplos, selectors ausentes, referências recriadas e efeitos que derivam estado. Não aplique memoização em massa.

**09. Listas longas** — Verifique paginação, renderização progressiva e virtualização. Preserve busca, teclado, leitores de tela, altura dinâmica e exportação.

**10. Atualização otimista** — Avalie apenas ações simples e reversíveis, com rollback, erro, prevenção de duplicidade e idempotência. Exija desenho específico para ações financeiras/destrutivas.

## C. Rede, dados e APIs

**11. Chamadas duplicadas** — Procure buscas repetidas, efeitos duplicados e revalidações excessivas. Diferencie Strict Mode dev de produção e reutilize a solução de cache já adotada.

**12. Waterfalls** — Identifique awaits sequenciais independentes no cliente e servidor. Paralelize apenas o que for independente, com falhas parciais e limites de concorrência.

**13. Busca/autocompletar** — Verifique debounce coerente, `AbortController`, respostas fora de ordem, mínimo de caracteres, cache e acessibilidade. Não fixe 300 ms sem avaliar a experiência.

**14. Paginação** — Procure listas sem limite, limite máximo ou ordenação estável. Prefira cursor quando dados mudam frequentemente e evite itens perdidos/duplicados.

**15. Payload e campos** — Procure `select *`, includes amplos, JSON/textos grandes, props duplicadas e dados enviados ao cliente sem uso. Verifique campos sensíveis.

**16. Timeout e retry** — Mapeie chamadas externas/internas. Exija timeout, cancelamento e erro claro. Retry com backoff/jitter só em operação idempotente ou protegida; respeite `Retry-After`.

**17. Compressão/protocolo** — Verifique Brotli/Gzip para texto na hospedagem/proxy; não recomprima mídia já comprimida. Registre HTTP/2 ou HTTP/3 quando relevante.

**18. Cache HTTP/framework** — Audite `Cache-Control`, `Vary`, ETag, revalidação, tags e invalidação. Separe conteúdo público, privado, HTML e assets com hash; procure vazamento entre usuários.

## D. JavaScript, CSS, fontes e terceiros

**19. Bundle/dependências** — Analise por rota, tree-shaking, barrel imports, versões duplicadas, polyfills e bibliotecas pesadas. Confirme imports dinâmicos, scripts e tooling antes de remover dependências.

**20. Code splitting** — Verifique divisão por rota e lazy loading de editores, mapas, gráficos e modais. Evite fragmentação excessiva e preserve preload crítico.

**21. CSS** — Procure CSS global excessivo, bloqueio, duplicação, bibliotecas inteiras e geração cara. Só faça purge com proteção para classes dinâmicas.

**22. Terceiros** — Inventarie analytics, pixels, chats, mapas, vídeos e widgets. Meça custo, use estratégia apropriada e respeite consentimento.

**23. Fontes** — Verifique WOFF2, subconjuntos, pesos realmente usados, `font-display`, preload seletivo e fallback compatível. Em Next, avalie `next/font`.

## E. Imagens, mídia e estabilidade

**24. Imagens** — Inventarie PNG/JPEG grandes, SVG inadequado, dimensões excessivas e falta de `srcset`/`sizes`. Em Next, avalie `next/image`. WebP/AVIF só vence se tamanho e qualidade forem melhores.

**25. Prioridade/lazy loading** — Carregue abaixo da dobra sob demanda, nunca a imagem LCP. Verifique prioridade, preload, largura/altura e placeholders.

**26. Vídeos/iframes** — Verifique poster, preload, codecs, streaming e embeds por interação. Preserve controles e acessibilidade.

**27. CLS** — Procure mídia sem dimensões, embeds sem reserva, fontes que mudam métricas, barras inseridas acima e animações que alteram layout.

## F. Servidor, banco e tarefas

**28. N+1/queries lentas** — Use logs/profiler em ambiente seguro. Verifique joins, includes, batch e data loaders. Compare quantidade e duração sem expor dados.

**29. Índices/planos** — Use `EXPLAIN` sem efeitos e consulta real. Considere seletividade, ordenação, compostos, escrita e volume. Entregue como migration separada e não aplique sem autorização.

**30. Pool** — Verifique singleton, limites, timeouts, vazamentos e hot reload/serverless/edge. Considere o total de conexões de todas as instâncias.

**31. Cache de dados** — Avalie primeiro cache do framework/CDN. Redis só com leitura cara/repetida comprovada, TTL, invalidação, isolamento, prevenção de stampede e fallback.

**32. Jobs** — Identifique e-mail, PDF, IA, mídia e integrações lentas dentro da requisição. Fila deve ter idempotência, retry seguro, backoff, dead-letter, observabilidade e status ao usuário.

**33. IA** — Quando houver, avalie streaming, primeiro token, parar/cancelar, timeout, limites, custo, moderação e persistência parcial. Streaming pode não servir a respostas estruturadas.

## G. Infraestrutura e resiliência

**34. CDN/estáticos** — Verifique assets com hash, arquivos não versionados, origem, headers, invalidação e cache HIT. Diferencie CDN de cache do navegador.

**35. Regiões** — Comprove regiões do app, banco e serviços e meça latência a partir do servidor. Considere residência de dados e custo; não deduza por domínio.

**36. Escala horizontal** — Procure sessão, arquivos, locks, cache e filas em memória. Verifique health/readiness, shutdown gracioso, afinidade e idempotência.

**37. Concorrência/backpressure** — Procure `Promise.all` sem limite, uploads em memória, endpoints caros sem rate limit e filas sem controle.

**38. Runtime/cold start** — Em serverless/edge, avalie bundle da função, inicialização, compatibilidade, duração e cold start. Não mova acesso ao banco distante ou bibliotecas incompatíveis para edge.

## H. Build e regressão

**39. Build de produção** — Verifique warnings, páginas dinâmicas inesperadas, prerender, source maps, minificação e variáveis públicas. Não conclua desempenho só no modo dev.

**40. Regressões automáticas** — Procure testes de jornada, Lighthouse CI, budgets, bundle diff e alertas. Proponha o menor conjunto estável e sem depender de produção.

**41. Memória/trabalho contínuo** — Procure listeners, timers, observers, subscriptions, sockets e requests sem cleanup; polling excessivo; loops; retenção e crescimento após navegações.

**42. Acessibilidade/preferências** — Garanta teclado, foco, leitores de tela, contraste, `prefers-reduced-motion`, zoom e mensagens de carregamento. Otimização não pode remover acessibilidade.

## Como comprovar

- **Skeleton/CLS:** rede lenta, geometria semelhante e CLS sem regressão.
- **Imagens:** tamanho transferido próximo ao exibido, formato realmente menor e LCP priorizado.
- **Duplicidade:** conte chamadas no build de produção e explique Strict Mode.
- **Debounce:** digitação não gera chamada útil por tecla; pedido antigo é cancelado/ignorado.
- **Paginação:** limite fixo, ordem estável e navegação sem perder/duplicar itens.
- **Cache:** compare frio/quente e valide header, chave, escopo e invalidação.
- **Fila:** resposta rápida, job rastreável, retry seguro e sem duplicação.
- **N+1:** queries não crescem linearmente com os itens.
- **Compressão:** `content-encoding` e `Vary: Accept-Encoding` corretos.
- **Timeout:** serviço lento simulado falha no limite e libera recursos.
- **Streaming:** primeiro trecho melhora e cancelamento interrompe cliente/servidor.
- **Lazy loading:** fora da dobra não baixa cedo e LCP não atrasa.
- **Paralelismo:** waterfall simultâneo sem carga insegura.
- **Índice:** plano e duração melhoram com volume representativo.
- **SSR/SSG/RSC:** HTML traz conteúdo esperado sem regressão de TTFB/LCP/hidratação.
- **Bundle:** compare builds equivalentes, total e por rota.
- **Virtualização:** DOM limitado, rolagem e acessibilidade preservadas.
- **Pool:** conexões estáveis sob teste controlado.
- **Renders:** Profiler mostra menos commits/tempo na interação-alvo.
- **Fontes:** pesos limitados, sem origem não planejada e texto legível durante carga.
- **Escala:** retirar uma instância em ambiente controlado não perde sessão/trabalho.
