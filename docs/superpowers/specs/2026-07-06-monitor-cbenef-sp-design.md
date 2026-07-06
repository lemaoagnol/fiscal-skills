# Monitoramento automático da Tabela cBenef-SP — Design

Data: 2026-07-06

## Problema

A skill `fiscal-produtos-sp` depende da Tabela cBenef-SP oficial (SEFAZ-SP) pra confirmar códigos de benefício fiscal. Essa tabela é atualizada periodicamente (nome do arquivo muda, ex: `Tabela-cBenef-SP-v20260626.pdf`), e hoje a única forma de saber que mudou é abrir o portal manualmente e comparar. Ver pesquisa `~/pesquisas/pesquisa-atualizacao-fiscal-sp-2026-07-06.md`, que já identificou o padrão de nome versionado como o sinal mais barato de mudança.

## Objetivo

Checar automaticamente, uma vez por mês, se a tabela cBenef-SP mudou de versão, e notificar o usuário só quando houver mudança real (ou falha na checagem).

## Arquitetura

- **1 rotina em nuvem** (Claude Code cloud routine, criada via `/schedule` → `RemoteTrigger`), independente de qualquer sessão local — roda mesmo com o Claude Code fechado.
- **Agenda:** todo dia 1 de cada mês, ~7h horário de Brasília → cron `7 10 1 * *` (UTC, America/Sao_Paulo = UTC-3 o ano todo).
- **Modelo:** `claude-sonnet-5`.
- **Fonte/estado:** repositório `git@github.com:lemaoagnol/fiscal-skills.git`, clonado automaticamente pela rotina a cada execução (`session_context.sources.git_repository`). Não usa Google Drive nem outro conector — o próprio git é o registro de estado.
- **Ferramentas permitidas:** Bash (pra `curl`/`grep` na página da SEFAZ-SP e operações git), Read/Write/Edit, e `PushNotification`.

### Por que rotina em nuvem, e não script local + Task Scheduler

Rotinas em nuvem sobrevivem indefinidamente (diferente do `CronCreate` local, que expira em 7 dias e morre com a sessão), não exigem manter nada rodando no PC do usuário, nem configurar o Agendador de Tarefas do Windows — mais simples de manter pra quem é iniciante em programação.

## Arquivo de estado

`cbenef-versao-atual.txt` na raiz do repo `fiscal-skills`, contendo só o nome do arquivo oficial mais recente conhecido (ex: `Tabela-cBenef-SP-v20260626.pdf`). Fica separado do conteúdo da skill (`SKILL.md`, `references/*`) — é puramente um marcador de automação, não referenciado pelo `SKILL.md`.

## Fluxo por execução

1. Acessar `https://portal.fazenda.sp.gov.br/servicos/nfe/Paginas/cBenef.aspx` e extrair o nome do arquivo **PDF** vigente (formato canônico pra comparação — a página lista PDF/XLSX/ODS da mesma versão, mas só o PDF é usado como referência), casando o padrão `Tabela-cBenef-SP-v\d{8}\.pdf`.
2. Ler `cbenef-versao-atual.txt` do repo clonado.
3. **Arquivo de estado não existe (primeira execução):** criar o arquivo com a versão atual, commitar (`Inicializa monitoramento cBenef-SP (vAAAAMMDD)`), dar push, e notificar: "Monitoramento cBenef-SP iniciado. Versão atual: vAAAAMMDD."
4. **Versão extraída é igual à do arquivo de estado:** não fazer nada — sem commit, sem notificação (evita ruído mensal quando nada mudou).
5. **Versão extraída é diferente:** sobrescrever o arquivo de estado, commitar (`Atualiza registro cBenef-SP para vAAAAMMDD`), dar push, e notificar: "cBenef-SP atualizado: vANTIGA → vNOVA. Baixe a nova versão em https://portal.fazenda.sp.gov.br/servicos/nfe/Paginas/cBenef.aspx."

## Tratamento de erro

Se a página da SEFAZ-SP estiver inacessível, ou o padrão de nome de arquivo não for encontrado na página: **não alterar o arquivo de estado** (preserva a última versão boa conhecida) e notificar: "Não consegui checar a tabela cBenef-SP este mês — confira manualmente: https://portal.fazenda.sp.gov.br/servicos/nfe/Paginas/cBenef.aspx." Uma falha nunca deve passar em silêncio até o mês seguinte.

## Validação

Depois de criar a rotina, executar uma vez manualmente (`RemoteTrigger` com `action: "run"`) pra confirmar, sem esperar o próximo dia 1, que ela: clona o repo, encontra a versão atual da tabela, cria/atualiza `cbenef-versao-atual.txt`, commita, dá push, e dispara a notificação de baseline corretamente.

## Fora de escopo (por decisão explícita do usuário)

- Não baixa/commita o PDF oficial em si — só o nome/versão como marcador de texto.
- Não resume o que mudou dentro da tabela — só avisa que a versão mudou, o usuário confere o conteúdo manualmente.
- Não roda localmente nem depende do PC do usuário estar ligado.
