# Guias de stack

Os itens 01–109 são independentes de framework. Os guias desta pasta dizem **como verificar esses itens em uma stack específica**: onde procurar, quais APIs e arquivos olhar, quais armadilhas são comuns e quais comandos usar.

| Guia | Cobre |
|---|---|
| [`react-next.md`](react-next.md) | React (Vite, CRA, SPA) e Next.js (App Router e Pages Router) |

Sem guia para a sua stack, a auditoria funciona do mesmo jeito: o agente traduz cada item para os equivalentes do projeto e registra as adaptações no relatório (veja o passo 4 do [método](../00-metodo.md)).

## Como criar um guia

1. Crie `stacks/<nome>.md` (ex.: `vue-nuxt.md`, `laravel.md`, `django.md`, `wordpress.md`, `astro.md`).
2. Comece com **Detecção**: arquivos e dependências que identificam a stack.
3. Organize por área (Desempenho, Segurança, Privacidade, Consumidor, SEO) e cite sempre o número do item geral, por exemplo `**48. Autenticação no servidor** — ...`.
4. Não crie itens novos nem mude números. Se algo importante não couber em nenhum item, proponha um item geral novo no arquivo da área.
5. Termine com **Como comprovar (específico)**, quando houver provas próprias da stack.
6. Acrescente o guia na tabela acima.
