# Guia de stack — React e Next.js

Aplique junto com os itens gerais. Este guia não cria itens novos: ele diz como verificar itens existentes em projetos com React (Vite, CRA, SPA) e Next.js (App Router, Pages Router ou híbrido). Use os mesmos IDs no relatório.

## Detecção

- Next.js: `next` no `package.json`, `next.config.*`, pastas `app/` (App Router) e/ou `pages/` (Pages Router).
- React sem Next: `react` com Vite (`vite.config.*`), CRA (`react-scripts`) ou outro bundler.
- Registre versões de Node, React, Next e gerenciador de pacotes, e quais modos de renderização aparecem por rota: SSR, SSG, ISR, React Server Components (RSC), streaming e componentes de cliente.

## Desempenho

- **05. Estratégia de renderização** — Em Next, avalie por rota SSR, SSG, ISR, RSC, streaming e componentes de cliente. No App Router, confira `dynamic`, `revalidate`, uso de `cookies()`/`headers()` que tornam rotas dinâmicas sem necessidade e `generateStaticParams`. Em SPA com Vite/CRA, avalie shell e buscas após montagem.
- **06. JavaScript no cliente e hidratação** — No App Router, procure `"use client"` alto demais na árvore, props grandes serializadas para o cliente, providers globais no layout raiz e bibliotecas pesadas que poderiam ficar em componentes de cliente menores. Verifique avisos de hydration mismatch no console.
- **07. Estados de carregamento e streaming** — Verifique `loading.tsx`, boundaries de `<Suspense>` locais (não só no layout) e `error.tsx`.
- **08. Renderizações desnecessárias** — Use o React Profiler (React DevTools). Procure contextos amplos, stores sem seletor (Zustand, Redux, Jotai), objetos e funções recriados em props e `useEffect` que só derivam estado. Não espalhe `useMemo`/`useCallback`/`React.memo`; se o React Compiler estiver ativo, considere-o antes de memoização manual.
- **11. Chamadas duplicadas** — Em desenvolvimento, o Strict Mode executa efeitos duas vezes; conte chamadas no build de produção. Reutilize a solução já adotada (`fetch` com cache do Next, React Query/TanStack Query, SWR).
- **13. Busca/autocompletar** — `AbortController` no `useEffect` com cleanup, ou cancelamento nativo da biblioteca de dados.
- **18. Cache HTTP/framework** — Em Next, audite cache de `fetch`, `revalidate`, `revalidateTag`/`revalidatePath`, cache de rotas e páginas personalizadas servidas de cache compartilhado.
- **19–20. Bundle e code splitting** — Use `@next/bundle-analyzer` ou `vite-bundle-visualizer` quando já disponíveis; confira `next/dynamic` e `React.lazy` para editores, mapas, gráficos e modais.
- **23. Fontes** — Avalie `next/font` (auto-hospedagem, subconjuntos e fallback ajustado).
- **24–25. Imagens** — Avalie `next/image` com `sizes` corretos e `priority`/`fetchPriority` só na imagem LCP; confira `images.remotePatterns` e o custo do otimizador na hospedagem.
- **38. Runtime/cold start** — Confira `export const runtime = 'edge'` em rotas que acessam banco distante ou usam bibliotecas só de Node.
- **39. Build de produção** — Leia a saída do `next build`: rotas estáticas (○), dinâmicas (ƒ) e tamanho de JS por rota.

## Segurança

- **43. Chaves de API ocultas** — Em Next, só variáveis com `NEXT_PUBLIC_` vão ao cliente; em Vite, `VITE_`; em CRA, `REACT_APP_`. Use o pacote `server-only` em módulos com segredos para o build falhar se forem importados no cliente.
- **48. Autenticação no servidor** — Toda Server Action, Route Handler (`app/**/route.ts`), API Route (`pages/api`), `getServerSideProps` e Server Component com dados privados deve validar a sessão. Server Actions são endpoints públicos chamáveis diretamente: valide sessão e permissão dentro de cada uma. `middleware.ts` ajuda no redirecionamento, mas não substitui a checagem perto do dado.
- **56. Validar toda entrada** — Valide `FormData` e argumentos de Server Actions e corpos de Route Handlers com esquema (ex.: Zod).
- **57. Escape de conteúdo do usuário** — Procure `dangerouslySetInnerHTML` e `href` montados com dados do usuário; JSX escapa texto, mas não esses casos.
- **60. Cabeçalhos de segurança** — Configure em `headers()` do `next.config.*` ou no middleware; CSP com nonce exige renderização dinâmica.
- **62. Atualizar dependências** — Acompanhe os avisos de segurança do Next.js e do React (incluindo RSC e Server Actions) e atualize versões de correção com prioridade.

## SEO

- **94. Links internos** — Use `next/link` com `href` real; evite `router.push` em elementos que deveriam ser links.
- **97. Noindex** — Confira `metadata.robots`/`generateMetadata`, `X-Robots-Tag` em `headers()` e variáveis de ambiente que ligam `noindex` por ambiente (preview da Vercel, staging).
- **98. Sitemap** — `app/sitemap.ts` e `app/robots.ts` (App Router) ou pacote `next-sitemap`.
- **100–101. Canonical e metadados** — Defina `metadata`/`generateMetadata` por rota com `alternates.canonical` e `metadataBase` corretos; remova títulos padrão como "Create Next App".
- **103. Redirects** — Revise `redirects()` no `next.config.*`, `middleware.ts` e regras da hospedagem (ex.: `vercel.json`) para evitar cadeias.
- **106. Conteúdo só em JavaScript** — SPAs em Vite/CRA entregam HTML vazio; avalie migração de rotas públicas para Next, pré-renderização ou outra solução de SSG.

## Como comprovar (específico)

- **Build:** saída do `next build` mostra rotas públicas estáticas quando esperado e JS por rota dentro do orçamento.
- **Server Actions:** chamar a action sem sessão ou com outro usuário de teste retorna erro de autorização.
- **Segredos:** busca no `.next/static` ou `dist/` do build não encontra chaves privadas.
- **Hidratação:** console do build de produção sem avisos de hydration mismatch nas rotas principais.
