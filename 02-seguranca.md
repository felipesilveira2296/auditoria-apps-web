# Auditoria React/Next — Segurança

Escopo: itens 43–73, IDs `SEC-`. Leia antes [`00-metodo.md`](00-metodo.md): modo de trabalho, estados, relatório e regras para implementar valem para todos os arquivos. A numeração dos itens é global e não muda entre arquivos.

## I. Segurança antes de lançar

Nunca exiba, copie ou registre valores de segredos, tokens, senhas ou dados pessoais no relatório: cite apenas arquivo, linha, nome da variável e tipo de exposição. Não faça testes de invasão contra produção nem contra serviços de terceiros.

**43. Chaves de API ocultas** — Procure chaves no código do cliente, bundle gerado e variáveis públicas (`NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`). Chaves privadas só no servidor (`server-only`, Route Handlers, Server Actions). Verifique se `.env*` está no `.gitignore` e se existe `.env.example` sem valores reais.

**44. Segredos no Git** — Verifique arquivos rastreados e histórico com a ferramenta já disponível (ex.: `gitleaks`, `git log -p` filtrado). Segredo encontrado no histórico exige rotação da credencial; apenas remover o arquivo não basta. Não reescreva histórico sem autorização.

**45. Chave pública do banco** — Confirme que o cliente usa somente a chave pública/anon (ex.: Supabase `anon`, Firebase config pública) e que chaves administrativas (`service_role`, credenciais de admin, connection string) nunca chegam ao navegador.

**46. RLS ativo** — Em bancos expostos ao cliente (Supabase/Postgres), verifique RLS habilitado em todas as tabelas acessíveis e políticas por operação (`select`, `insert`, `update`, `delete`). Tabela sem política ou com `using (true)` indevido é problema. Em Firebase, audite as Security Rules.

**47. Criptografia de dados sensíveis** — Identifique documentos, dados de saúde, financeiros e tokens de terceiros. Verifique TLS em trânsito, criptografia em repouso do provedor e, quando necessário, criptografia por campo com chave fora do banco. Não registre dados sensíveis em logs.

**48. Autenticação no servidor** — Toda Server Action, Route Handler, API e loader com dados privados deve validar a sessão no servidor. Esconder botão ou proteger só no cliente/middleware não é autorização. Verifique validação de JWT (assinatura, expiração, emissor).

**49. Acesso aos registros travado** — Verifique autorização por recurso com negação por padrão: cada consulta filtra pelo dono, organização ou papel do usuário. Procure endpoints que listam ou retornam registros sem filtro de escopo.

**50. Usuário A não edita dados do B** — Procure IDOR: IDs vindos de URL/corpo usados sem conferir propriedade. O dono deve vir da sessão, nunca do payload. Teste com dois usuários de teste em ambiente local, trocando IDs em leitura, edição e exclusão.

**51. Cookies de sessão seguros** — Verifique `HttpOnly`, `Secure`, `SameSite` (Lax/Strict), expiração, rotação após login e invalidação no logout. Tokens de sessão não devem ficar em `localStorage`. Avalie proteção CSRF em mutações com cookie.

**52. Hash nas senhas** — Senhas próprias devem usar Argon2id, bcrypt ou scrypt com custo adequado; nunca texto puro, MD5, SHA-1/SHA-256 simples ou criptografia reversível. Se o provedor de auth cuida disso, marque **Conforme** com evidência.

**53. Limite de tentativas** — Verifique rate limit em login, cadastro, recuperação de senha, OTP, envio de e-mail/SMS e endpoints caros (IA, upload). Limite por IP e por conta, com resposta `429` e sem revelar se o usuário existe.

**54. Proteção contra bots** — Verifique proteção em cadastro, contato e formulários públicos (ex.: Turnstile, hCaptcha, honeypot, verificação de e-mail). Avalie impacto em acessibilidade e privacidade; não exija captcha onde rate limit basta.

**55. Consultas parametrizadas** — Procure SQL montado por concatenação ou template string, `$queryRawUnsafe`, `sql.raw`, filtros dinâmicos sem lista permitida e injeção em ORDER BY/colunas. Use parâmetros do driver/ORM.

**56. Validar toda entrada** — Toda entrada do cliente (corpo, query, params, headers, formulários, webhooks) deve ser validada no servidor com esquema (ex.: Zod), tipo, tamanho, formato e campos permitidos. Validação no cliente é só experiência. Webhooks exigem verificação de assinatura.

**57. Escape de conteúdo do usuário** — Procure `dangerouslySetInnerHTML`, `innerHTML`, Markdown/HTML renderizado sem sanitização (ex.: DOMPurify), links `javascript:`, `href`/`src` vindos do usuário e dados injetados em `<script>`. React escapa texto, mas não esses casos.

**58. Restringir uploads** — Verifique tipo validado pelo conteúdo (não só extensão), tamanho máximo, quantidade, nome gerado pelo servidor, bucket privado com URL assinada, bloqueio de SVG/HTML executável e upload sem carregar tudo em memória.

**59. Backup automático do banco** — Confirme backup automático, retenção, PITR quando disponível, cópia fora do provedor principal se o risco exigir e teste de restauração documentado. Não execute restauração em produção; teste somente em ambiente separado e autorizado.

**60. Cabeçalhos de segurança** — Verifique `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `frame-ancestors`/`X-Frame-Options`, `Referrer-Policy` e `Permissions-Policy`. Em Next, avalie `headers()` ou middleware; comece CSP em modo report-only se houver risco de quebra.

**61. Forçar HTTPS** — Verifique redirecionamento HTTP→HTTPS, HSTS, ausência de conteúdo misto, cookies `Secure` e URLs de callback/OAuth apenas em HTTPS.

**62. Atualizar dependências** — Rode `npm audit`/`pnpm audit`/`yarn npm audit` e `outdated` sem alterar o lockfile. Priorize CVEs exploráveis em Next, React, auth, ORM e parsers. Verifique Dependabot/Renovate. Não atualize automaticamente; proponha lotes pequenos com testes.

## J. Segurança operacional e governança

Autenticação segura, controle de acesso, criptografia, HTTPS/TLS, hash de senhas, SQL injection, XSS/CSRF, rate limiting, auditoria de dependências e backups já estão nos itens 43–62. Na autenticação (48), verifique também MFA para contas administrativas, expiração de sessão e recuperação de senha segura.

**63. Proteção de APIs** — Inventarie todas as rotas expostas, inclusive esquecidas, de debug ou de versões antigas. Verifique CORS restrito às origens próprias, métodos permitidos, autenticação por padrão, limites de tamanho de corpo, erros sem stack trace e documentação/Swagger público sem necessidade.

**64. Logs seguros** — Logs não podem conter senhas, tokens, cookies, documentos, cartões ou dados pessoais além do necessário; mascare campos. Verifique acesso restrito aos logs, retenção definida e registro de eventos de segurança (login, falhas, mudanças de permissão, exclusões).

**65. Monitoramento de ameaças** — Verifique alertas para picos de erro 401/403/429, tentativas de login, uso anômalo de API, custos inesperados e mudanças em contas administrativas. Use o que o provedor já oferece antes de propor ferramenta nova.

**66. Resposta a incidentes** — Procure plano documentado: quem decide, como revogar chaves, derrubar sessões, isolar o sistema, preservar evidências e restaurar backup. Ausência de documento é **Problema confirmado** antes do lançamento.

**67. Notificação de incidentes** — Verifique procedimento para avaliar risco e comunicar ANPD e titulares quando o incidente puder causar risco ou dano relevante, em até 3 dias úteis a partir do conhecimento do incidente (Resolução CD/ANPD nº 15/2024, art. 6º; agentes de pequeno porte e legislação setorial podem ter prazos próprios), com modelo de comunicação e registro interno do incidente.

**68. Testes de segurança** — Procure testes automatizados de autorização (usuário sem acesso recebe 401/403), validação de entrada e regressões de segurança; SAST/secret scanning no CI. Pentest externo é recomendação conforme risco, nunca executado pelo agente contra produção.

**69. Revisão de código** — Verifique se mudanças passam por revisão antes de produção (PR, branch protegida, checks obrigatórios), com atenção extra a código gerado por IA: autorização, validação, segredos e dependências inventadas ou desnecessárias.

**70. Privilégios administrativos** — Inventarie contas admin no app, banco, hospedagem, domínio, repositório e provedores. Verifique menor privilégio, MFA, remoção de acessos antigos, separação entre ambientes e registro de ações administrativas.

**71. Gestão de vulnerabilidades** — Verifique processo para receber, priorizar e corrigir vulnerabilidades: alertas de dependências, prazos por severidade, responsável e canal de contato para pesquisadores (ex.: `security.txt`).

**72. Licenças de uso** — Verifique licenças das dependências (copyleft como GPL/AGPL em produto fechado), fontes, imagens, ícones, vídeos e modelos de IA usados. Registre ativos sem licença comprovada.

**73. Transparência sobre IA** — Se o app usa IA, verifique se o usuário é informado quando interage com IA ou recebe conteúdo gerado, quais dados são enviados ao provedor, se são usados para treinamento e quais limites/erros a IA pode ter. Decisões tomadas unicamente com base em tratamento automatizado que afetem os interesses do titular devem permitir pedido de revisão e informação sobre os critérios usados (LGPD, art. 20).

## Como comprovar

- **Segredos:** busca no bundle de produção e no histórico Git não encontra chaves privadas.
- **RLS/autorização:** usuário de teste B recebe 401/403 ou lista vazia ao ler, editar ou excluir dados do usuário A.
- **Cookies/cabeçalhos/HTTPS:** headers da resposta mostram flags e políticas esperadas; HTTP redireciona para HTTPS.
- **Rate limit:** tentativas acima do limite retornam `429` sem revelar existência de conta.
- **Entrada/escape:** payloads inválidos são rejeitados no servidor e conteúdo com HTML/script aparece como texto.
- **Uploads:** arquivo com extensão trocada, grande demais ou executável é recusado.
- **Backup:** existe registro de backup recente e restauração testada em ambiente separado.
- **Dependências:** audit sem vulnerabilidades altas/críticas exploráveis pendentes.
- **APIs:** inventário de rotas bate com o código; rota sem sessão e origem não permitida são recusadas.
- **Logs:** amostra de logs de login, erro e pagamento não contém segredos nem dados pessoais desnecessários.
- **Incidentes:** plano de resposta e notificação existe, tem responsáveis e foi simulado ao menos uma vez.
