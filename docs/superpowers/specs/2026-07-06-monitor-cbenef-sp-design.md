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
- **Ferramentas permitidas:** `WebFetch` (busca a página da SEFAZ-SP), Bash (só pra operações git), Read/Write/Edit.

### ⚠️ Revisão pós-validação (2026-07-07) — duas premissas erradas corrigidas

A primeira versão desta spec usava `curl` (via Bash) pra buscar a página, e `PushNotification` pra avisar o usuário. A validação real (Task 3) mostrou que **nenhuma das duas funciona dentro do sandbox de uma cloud routine**:

1. **Rede do sandbox bloqueia domínios externos arbitrários** — `curl` pra `portal.fazenda.sp.gov.br` voltou HTTP 403 de política de rede (não instabilidade passageira). **Correção:** usar a ferramenta `WebFetch` em vez de `curl` — ela busca a página pela infraestrutura da Anthropic, fora da rede restrita do sandbox.
2. **`PushNotification` não está disponível em cloud routines**, mesmo listada em `allowed_tools` — retorna "tool exists but is not enabled in this context". **Correção:** abandonar `PushNotification` inteiramente. O próprio `git push` passa a ser o mecanismo de notificação — o usuário configura notificação de commits/pushes desse repo no GitHub (Watch → Custom → Pushes) e é avisado nativamente sempre que a rotina commitar algo. Isso exige que **toda ação que vale a pena notificar termine em um commit + push** — inclusive falhas (ver seção de Tratamento de erro, revisada).

### Por que rotina em nuvem, e não script local + Task Scheduler

Rotinas em nuvem sobrevivem indefinidamente (diferente do `CronCreate` local, que expira em 7 dias e morre com a sessão), não exigem manter nada rodando no PC do usuário, nem configurar o Agendador de Tarefas do Windows — mais simples de manter pra quem é iniciante em programação.

## Arquivo de estado

`cbenef-versao-atual.txt` na raiz do repo `fiscal-skills`, contendo só o nome do arquivo oficial mais recente conhecido (ex: `Tabela-cBenef-SP-v20260626.pdf`). Fica separado do conteúdo da skill (`SKILL.md`, `references/*`) — é puramente um marcador de automação, não referenciado pelo `SKILL.md`.

## Fluxo por execução

1. Usar `WebFetch` na URL `https://portal.fazenda.sp.gov.br/servicos/nfe/Paginas/cBenef.aspx` pedindo a lista de nomes de arquivo PDF que casem o padrão `Tabela-cBenef-SP-v\d{8}\.pdf` (formato canônico pra comparação — a página lista PDF/XLSX/ODS da mesma versão, mas só o PDF é usado como referência). Se houver mais de um, usar o de data mais recente.
2. Ler `cbenef-versao-atual.txt` do repo clonado.
3. **Arquivo de estado não existe (primeira execução):** criar o arquivo com a versão atual, commitar (`Inicializa monitoramento cBenef-SP (vAAAAMMDD)`), dar push.
4. **Versão extraída é igual à do arquivo de estado:** não fazer nada — sem commit (evita ruído mensal quando nada mudou; sem commit não há notificação do GitHub, que é o comportamento desejado).
5. **Versão extraída é diferente:** sobrescrever o arquivo de estado, commitar (`Atualiza registro cBenef-SP para vAAAAMMDD`), dar push.

## Tratamento de erro

Se o `WebFetch` falhar (página inacessível) ou o padrão de nome de arquivo não for encontrado: **não alterar `cbenef-versao-atual.txt`** (preserva a última versão boa conhecida), mas **ainda assim commitar e dar push** — sobrescrever um segundo arquivo, `cbenef-ultima-falha.txt`, com a data e o motivo da falha, e commitar com mensagem `Falha ao checar cBenef-SP em AAAA-MM-DD: <motivo>`. Sem `PushNotification` disponível, o commit/push É o mecanismo de aviso — por isso toda falha também precisa gerar um push, não só as mudanças reais. Uma falha nunca deve passar em silêncio até o mês seguinte.

## Notificação (revisado)

Sem `PushNotification` em cloud routines, a notificação passa a ser **o próprio push no GitHub**. O usuário deve configurar, uma vez, notificação de pushes nesse repositório: em `github.com/lemaoagnol/fiscal-skills` → botão **Watch** → **Custom** → marcar **Pushes** → Apply. A partir daí, todo commit da rotina (mudança de versão OU falha) chega como notificação nativa do GitHub (web/e-mail/app mobile, conforme as preferências de notificação da conta).

## Validação

Depois de criar/atualizar a rotina, executar uma vez manualmente (`RemoteTrigger` com `action: "run"`) pra confirmar, sem esperar o próximo dia 1, que ela: clona o repo, busca a página via `WebFetch`, encontra a versão atual da tabela, cria `cbenef-versao-atual.txt`, commita e dá push corretamente. **Nota:** a API do `RemoteTrigger` não expõe logs/status da execução — a única forma de confirmar o resultado é checar o repositório (`git pull`) ou abrir a página da rotina em `claude.ai/code/routines/<id>` no navegador, que mostra o histórico de execuções com log completo.

## Fora de escopo (por decisão explícita do usuário)

- Não baixa/commita o PDF oficial em si — só o nome/versão como marcador de texto.
- Não resume o que mudou dentro da tabela — só avisa que a versão mudou, o usuário confere o conteúdo manualmente.
- Não roda localmente nem depende do PC do usuário estar ligado.

## ❌ Abandonado (2026-07-07) — arquitetura de cloud routine não é viável

Depois de corrigir `curl`→`WebFetch` (ver revisão acima), uma segunda validação real encontrou **dois bloqueios adicionais que inviabilizam essa arquitetura**, não corrigíveis por configuração da rotina em si:

1. **A própria SEFAZ-SP retornou HTTP 403 pro `WebFetch`** — não é mais bloqueio de rede do sandbox, é o site do governo rejeitando a requisição (provável bloqueio a IPs/bots de infraestrutura de nuvem). Sem controle sobre isso.
2. **A integração git usada pela cloud routine é somente-leitura** no repositório `fiscal-skills` — `git push` falhou com 403, e uma tentativa alternativa via API do GitHub confirmou `"403 Resource not accessible by integration"`. O commit local ficou preso no container efêmero da execução e foi perdido ao reciclar. Corrigir isso exigiria conceder permissão de escrita (`contents: write`) ao GitHub App conectado ao Claude Code — fora do alcance da API `RemoteTrigger`, e o usuário optou por não seguir esse caminho.

**Decisão:** a rotina (`trig_017TvJ5LM6SXX31TvgkpcSBw`, nome `monitor-cbenef-sp`) foi **desabilitada** (`enabled: false`), não deletada (a API não permite deleção — só via `claude.ai/code/routines`). A checagem mensal do cBenef-SP passa a ser planejada como feature **dentro do app TributAi** (projeto `consulta-fiscal`), que roda localmente no PC do usuário sem as restrições de rede/permissão do sandbox de nuvem. Ver plano de integração em `docs/superpowers/plans/` do projeto `consulta-fiscal`.

**Lição para desenhos futuros com cloud routines:** não assumir acesso de rede irrestrito nem permissão de escrita em git só porque o repo foi passado como `git_repository` source — validar ambos com uma execução real antes de desenhar o fluxo em torno deles.
