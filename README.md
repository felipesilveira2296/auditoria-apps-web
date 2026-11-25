# Auditoria de apps web

**109 verificações de desempenho, segurança, LGPD, direito do consumidor e SEO técnico para auditar aplicações web com agentes de IA**, antes de lançar e depois de cada mudança importante. Funciona com qualquer stack e traz guias específicos por framework, começando por React e Next.js.

Apps criados com ajuda de IA ficam prontos rápido, mas costumam ir para produção com chaves expostas, banco sem controle de acesso, rotas sem autorização, cookies sem consentimento e páginas invisíveis para o Google. Este repositório é um conjunto de prompts estruturados que faz o agente (Claude Code, Cursor, GitHub Copilot, Codex ou similar) auditar o projeto **com evidências**, em vez de achismo, e entregar um relatório priorizado.

## O que torna esta auditoria diferente

- **Somente leitura por padrão.** O agente não altera código, banco, dependências nem faz deploy sem autorização explícita.
- **Evidência obrigatória.** Cada item recebe um estado: *Problema confirmado*, *Oportunidade provável*, *Conforme*, *Não se aplica* ou *Não verificado*. Ausência de evidência não conta como conformidade.
- **Como comprovar.** Cada área traz critérios objetivos para provar que um problema existe e que a correção funcionou.
- **Relatório padronizado.** IDs rastreáveis (`PERF-`, `SEC-`, `CONF-`, `SEO-`), severidade, confiança, impacto, esforço, risco e plano em fases.
- **Sem vazar segredos.** O relatório cita arquivo, linha e tipo de exposição, nunca o valor de chaves, tokens ou dados pessoais.
- **Legislação brasileira.** LGPD, Código de Defesa do Consumidor, Marco Civil da Internet e Decreto do comércio eletrônico, com as referências conferidas.
- **Modular.** Use só as áreas e o guia de stack que fazem sentido para o projeto.

## Áreas

| Arquivo | Itens | IDs | O que cobre |
|---|---|---|---|
| [`00-metodo.md`](00-metodo.md) | — | — | Modo de trabalho, estados, relatório, correções e ordem recomendada |
| [`01-desempenho.md`](01-desempenho.md) | 01–42 | `PERF-` | Core Web Vitals, renderização, JavaScript no cliente, rede, bundle, imagens, fontes, banco, cache, filas, infraestrutura e build |
| [`02-seguranca.md`](02-seguranca.md) | 43–73 | `SEC-` | Chaves de API, segredos no Git, controle de acesso ao banco, autenticação e autorização no servidor, IDOR, cookies, rate limit, injeção, XSS, uploads, cabeçalhos, dependências, logs, incidentes e governança |
| [`03-privacidade-lgpd.md`](03-privacidade-lgpd.md) | 74–83 | `CONF-` | Política de privacidade, bases legais, minimização, consentimento, cookies, direitos dos titulares, exclusão e retenção |
| [`04-consumidor.md`](04-consumidor.md) | 84–92 | `CONF-` | Termos de uso, preços, cobranças, cancelamento, reembolso, identificação do fornecedor e publicidade |
| [`05-seo.md`](05-seo.md) | 93–109 | `SEO-` | 404, robots.txt, noindex, sitemap, canonical, redirects, www, schema, conteúdo só em JS e páginas órfãs |

A numeração é global: um achado `SEC-003` ou uma referência ao "item 61" continua valendo em qualquer arquivo.

## Guias de stack

Os itens são independentes de framework. Os [guias de stack](stacks/) mostram onde e como verificar cada item em uma tecnologia específica.

| Guia | Cobre |
|---|---|
| [`stacks/react-next.md`](stacks/react-next.md) | React (Vite, CRA, SPA) e Next.js (App Router e Pages Router) |

Sem guia para a sua stack (Vue/Nuxt, Laravel, Django, Rails, WordPress, Astro, site estático...), a auditoria funciona do mesmo jeito: o agente adapta cada item e registra as adaptações no relatório. Guias novos são bem-vindos.

## Como usar

1. Copie os arquivos para o seu projeto (por exemplo, em `docs/auditoria/`) ou deixe este repositório clonado ao lado.
2. Peça ao agente para ler o método, o guia da stack (se houver) e as áreas desejadas:

   > Leia `docs/auditoria/00-metodo.md`, `stacks/react-next.md`, `02-seguranca.md` e `05-seo.md`. Execute apenas a auditoria do login, página inicial e cadastro. Não altere arquivos nem use serviços reais. Gere o relatório priorizado com evidências.

3. Revise o relatório e autorize uma correção por vez:

   > Implemente apenas o achado SEC-003. Comprove antes e depois nas mesmas condições, preserve alterações locais e não publique.

## Qual combinação usar

| Situação | Arquivos |
|---|---|
| Antes de lançar qualquer app | método + segurança + privacidade |
| App com venda ou assinatura | + consumidor |
| Site público | + SEO |
| Painel ou sistema interno | método + segurança + desempenho; do SEO, apenas confirmar `noindex` (item 97) |
| App lento | método + desempenho |
| Auditoria completa | todos, na ordem do método |

Em todos os casos, acrescente o guia da stack quando existir.

## Escopo e limites

- Feito para **aplicações e sites web**: frontend, backend, APIs, CMS e sites estáticos. Apps nativos (Android/iOS) têm questões próprias, como permissões, regras das lojas e armazenamento no aparelho, que não estão cobertas.
- As áreas de privacidade e consumidor seguem a **legislação brasileira** (LGPD, Código de Defesa do Consumidor, Marco Civil da Internet e Decreto nº 7.962/2013).
- **Não é aconselhamento jurídico.** Os itens de conformidade ajudam a encontrar lacunas no produto; a validação final de políticas, contratos e termos cabe a um profissional de direito.
- Não substitui pentest profissional. O método proíbe testes de invasão contra produção ou serviços de terceiros.
- O resultado depende do agente e do acesso disponível; itens sem evidência devem aparecer como **Não verificado**.

## Contribuindo

Sugestões e correções são bem-vindas por issue ou pull request.

- **Novo item geral:** entra no arquivo da área com o próximo número livre (110 em diante), e o critério de comprovação vai para a seção **Como comprovar** do mesmo arquivo.
- **Novo guia de stack:** siga o modelo em [`stacks/README.md`](stacks/README.md).

## Licença

© 2026 Felipe Silveira. Licenciado sob [CC BY 4.0](LICENSE) ([resumo em português](https://creativecommons.org/licenses/by/4.0/deed.pt-br)): use, adapte e compartilhe, inclusive comercialmente, desde que dê o crédito e indique as alterações.

Criado por **Felipe Silveira** · FSJ Desenvolvimento.
