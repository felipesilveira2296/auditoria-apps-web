# Auditoria de apps web — Privacidade e LGPD

Escopo: itens 74–83, IDs `CONF-`. Leia antes [`00-metodo.md`](00-metodo.md): modo de trabalho, estados, relatório e regras para implementar valem para todos os arquivos. A numeração dos itens é global e não muda entre arquivos.

## K. Privacidade e LGPD

Estes itens verificam se o produto e a documentação refletem obrigações legais; não substituem análise jurídica. Contratos, registros internos e processos fora do repositório devem ser marcados **Não verificado** quando não houver evidência, com a pergunta objetiva ao responsável.

**74. Política de privacidade** — Verifique política acessível em todas as páginas e antes do cadastro, em linguagem clara, coerente com os dados realmente coletados no código, com controlador, encarregado (DPO) e canal de contato, finalidades, compartilhamentos, transferência internacional, retenção e direitos.

**75. Bases legais** — Para cada tratamento (cadastro, pagamento, marketing, analytics, IA, suporte), verifique base legal definida (execução de contrato, obrigação legal, consentimento, legítimo interesse etc.). Consentimento não deve ser usado como base para o que é necessário ao serviço.

**76. Minimização de dados** — Compare formulários, tabelas, eventos de analytics e payloads com a necessidade real. Procure campos coletados e nunca usados, CPF/data de nascimento sem motivo e dados sensíveis desnecessários.

**77. Finalidades documentadas** — Verifique registro das operações de tratamento (dado, finalidade, base legal, local de armazenamento, compartilhamento, retenção). Uso de dados para finalidade nova exige revisão.

**78. Gestão de consentimento** — Quando o consentimento for a base, verifique que é livre, específico, opcional (sem caixa pré-marcada), registrado com data/versão e revogável tão facilmente quanto foi dado. Nada deve ser coletado antes da aceitação.

**79. Política de cookies** — Verifique banner com aceitar e recusar acessíveis, categorias, scripts de analytics/marketing bloqueados até a aceitação, escolha salva no navegador e link discreto de "Preferências de privacidade" no rodapé para rever a escolha a qualquer momento. Siga o padrão de consentimento já adotado pelo projeto, quando houver.

**80. Direitos dos titulares** — Verifique canal e fluxo para confirmação, acesso, correção, portabilidade, anonimização, informação sobre compartilhamento e revogação de consentimento, com prazo de resposta e verificação de identidade.

**81. Exclusão de dados** — Verifique exclusão de conta pelo próprio usuário ou por solicitação, cobrindo banco, storage, caches, provedores e backups (com prazo de expiração). Preserve apenas o que tiver obrigação legal de guarda, documentando o motivo.

**82. Retenção de dados** — Verifique prazos por tipo de dado e rotina automática de exclusão/anonimização. Considere a guarda sigilosa dos registros de acesso por 6 meses exigida de provedores de aplicação constituídos como pessoa jurídica com atividade organizada e fins econômicos (Marco Civil, art. 15), além de obrigações fiscais.

**83. Contratos com operadores** — Inventarie provedores que tratam dados (hospedagem, banco, e-mail, pagamentos, analytics, IA, suporte) e verifique termos/DPA, localização dos dados, transferência internacional e subcontratados.

## Como comprovar

- **Privacidade:** dados coletados no código e no analytics batem com a política; nada é coletado antes do consentimento.
- **Titulares/exclusão:** solicitação de teste de acesso e exclusão é atendida e remove dados em todos os locais previstos.
