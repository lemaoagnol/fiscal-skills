# Monitoramento automático cBenef-SP Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Criar e validar uma rotina em nuvem (Claude Code cloud routine) que roda todo dia 1 do mês, checa se a Tabela cBenef-SP oficial mudou de versão, e avisa o usuário só quando há mudança real ou falha na checagem.

**Architecture:** Rotina criada via `RemoteTrigger` (ferramenta por trás do skill `/schedule`), sem repositório de código a escrever — a "implementação" é a configuração exata do `job_config` (prompt do agente, cron, repo fonte, ferramentas permitidas) e a validação operacional de que ela funciona de ponta a ponta. Não há testes automatizados no sentido tradicional (pytest/etc.); a validação é feita rodando a rotina manualmente uma vez e conferindo o resultado no repositório git e na notificação recebida.

**Tech Stack:** Claude Code cloud routines (RemoteTrigger API), git/GitHub (`lemaoagnol/fiscal-skills`), Bash (`curl`, `grep`, `git`) dentro do ambiente da rotina.

## Global Constraints

- Cron: `7 10 1 * *` (UTC) = todo dia 1 do mês, ~7h07 horário de Brasília (America/Sao_Paulo, UTC-3 o ano todo).
- Modelo: `claude-sonnet-5`.
- Repositório fonte: `https://github.com/lemaoagnol/fiscal-skills` (sem sufixo `.git`), sem outras integrações MCP (não usa Google Drive nem GitHub App separado — a rotina só precisa do clone + push do próprio `git_repository` configurado).
- Environment: `env_01UhFADZw3jQzRSQ8FNYAsgr` (Default, anthropic_cloud) — único ambiente disponível.
- Arquivo de estado: `cbenef-versao-atual.txt` na raiz do repo — só o nome do arquivo PDF vigente, nada mais.
- Nunca commitar o PDF oficial em si, nem resumir o conteúdo da tabela — só o nome/versão.
- Se a checagem falhar (site fora do ar / padrão de nome não encontrado): não tocar no arquivo de estado, e notificar o usuário mesmo assim (falha nunca deve ficar silenciosa).

---

### Task 1: Carregar a ferramenta RemoteTrigger e confirmar autenticação

**Files:** nenhum (chamada de API apenas).

**Interfaces:**
- Produz: acesso à ferramenta `RemoteTrigger` para as tasks seguintes (`action: "list" | "get" | "create" | "update" | "run"`).

- [ ] **Step 1: Carregar o schema da ferramenta**

Chamar `ToolSearch` com `query: "select:RemoteTrigger"`, `max_results: 1`.

Esperado: retorna o schema completo de `RemoteTrigger` (parâmetros `action`, `trigger_id`, `body`).

- [ ] **Step 2: Confirmar que a autenticação funciona**

Chamar `RemoteTrigger` com `{"action": "list"}`.

Esperado: resposta HTTP 200 com uma lista (pode vir vazia, `[]`, se nunca houve rotina criada) — sem erro de autenticação/permissão.

---

### Task 2: Criar a rotina de monitoramento

**Files:** nenhum (chamada de API apenas — o corpo da rotina é o "código").

**Interfaces:**
- Consome: ferramenta `RemoteTrigger` carregada na Task 1.
- Produz: `trigger_id` da rotina criada, usado nas Tasks 3 e 4.

- [ ] **Step 1: Montar o prompt completo do agente da rotina**

O agente da rotina roda em ambiente isolado, sem contexto desta conversa — o prompt abaixo precisa ser autocontido:

```text
Você está rodando como uma rotina mensal automatizada de monitoramento da Tabela cBenef-SP oficial (SEFAZ-SP). Siga exatamente estes passos, nesta ordem:

1. Baixe o HTML de https://portal.fazenda.sp.gov.br/servicos/nfe/Paginas/cBenef.aspx com `curl -sL`.

2. Nesse HTML, encontre o nome do arquivo PDF vigente que corresponda ao padrão `Tabela-cBenef-SP-v\d{8}\.pdf` (ex: Tabela-cBenef-SP-v20260626.pdf). Se houver mais de uma ocorrência, use a de data mais recente (AAAAMMDD maior).

3. Se o `curl` falhar (erro de rede, página fora do ar) OU nenhum nome nesse padrão for encontrado no HTML: NÃO altere nenhum arquivo do repositório. Envie uma notificação com a ferramenta PushNotification, mensagem exata: "Não consegui checar a tabela cBenef-SP este mês — confira manualmente: https://portal.fazenda.sp.gov.br/servicos/nfe/Paginas/cBenef.aspx". Pare a execução aqui, não faça mais nada.

4. Leia o conteúdo do arquivo `cbenef-versao-atual.txt` na raiz deste repositório (já clonado no seu ambiente).

5. Se esse arquivo NÃO existir:
   - Crie `cbenef-versao-atual.txt` na raiz do repo contendo exatamente o nome do arquivo PDF encontrado no passo 2 (sem quebra de linha extra, sem mais nada no arquivo).
   - Rode: `git add cbenef-versao-atual.txt`
   - Rode: `git commit -m "Inicializa monitoramento cBenef-SP (vAAAAMMDD)"`, substituindo AAAAMMDD pela data extraída do nome do arquivo (ex: v20260626).
   - Rode: `git push`
   - Envie PushNotification com a mensagem exata: "Monitoramento cBenef-SP iniciado. Versão atual: <nome do arquivo completo>."
   - Pare a execução aqui.

6. Se o conteúdo do arquivo for EXATAMENTE IGUAL ao nome de arquivo encontrado no passo 2: não faça mais nada — sem commit, sem push, sem notificação. Encerre a execução.

7. Se o conteúdo do arquivo for DIFERENTE do nome de arquivo encontrado no passo 2:
   - Sobrescreva `cbenef-versao-atual.txt` com o novo nome de arquivo (mesmo formato do passo 5).
   - Rode: `git add cbenef-versao-atual.txt`
   - Rode: `git commit -m "Atualiza registro cBenef-SP para vAAAAMMDD"`, com AAAAMMDD da NOVA versão.
   - Rode: `git push`
   - Envie PushNotification com a mensagem exata: "cBenef-SP atualizado: <nome antigo> → <nome novo>. Baixe a nova versão em https://portal.fazenda.sp.gov.br/servicos/nfe/Paginas/cBenef.aspx."

Não faça nada além do descrito acima. Não resuma o conteúdo da tabela cBenef, não baixe/commite o PDF em si, não modifique nenhum outro arquivo do repositório.
```

- [ ] **Step 2: Montar o corpo da requisição de criação**

```json
{
  "name": "monitor-cbenef-sp",
  "cron_expression": "7 10 1 * *",
  "enabled": true,
  "job_config": {
    "ccr": {
      "environment_id": "env_01UhFADZw3jQzRSQ8FNYAsgr",
      "session_context": {
        "model": "claude-sonnet-5",
        "sources": [
          {"git_repository": {"url": "https://github.com/lemaoagnol/fiscal-skills"}}
        ],
        "allowed_tools": ["Bash", "Read", "Write", "Edit", "PushNotification"]
      },
      "events": [
        {"data": {
          "uuid": "<gerar um UUID v4 minúsculo aqui>",
          "session_id": "",
          "type": "user",
          "parent_tool_use_id": null,
          "message": {"content": "<colar o prompt completo do Step 1 aqui>", "role": "user"}
        }}
      ]
    }
  }
}
```

- [ ] **Step 3: Criar a rotina**

Chamar `RemoteTrigger` com `{"action": "create", "body": <JSON do Step 2>}`.

Esperado: resposta contém um `trigger_id` (ou `id`) válido. Anotar esse valor — é usado nas próximas tasks.

- [ ] **Step 4: Confirmar a criação**

Chamar `RemoteTrigger` com `{"action": "get", "trigger_id": "<id da Step 3>"}`.

Esperado: `cron_expression` = `"7 10 1 * *"`, `enabled` = `true`, `name` = `"monitor-cbenef-sp"`.

---

### Task 3: Validar de ponta a ponta com uma execução manual

**Files:**
- Verificar (não criar): `cbenef-versao-atual.txt` na raiz de `C:\Users\guilh\fiscal-skills` (local, após `git pull`).

**Interfaces:**
- Consome: `trigger_id` da Task 2.

- [ ] **Step 1: Disparar uma execução manual**

Chamar `RemoteTrigger` com `{"action": "run", "trigger_id": "<id>"}`.

Esperado: a execução é enfileirada/inicia sem erro.

- [ ] **Step 2: Esperar a execução terminar e conferir o repositório local**

```bash
cd /c/Users/guilh/fiscal-skills
git pull
cat cbenef-versao-atual.txt
```

Esperado: o arquivo existe e contém um nome de arquivo no formato `Tabela-cBenef-SP-vAAAAMMDD.pdf`.

- [ ] **Step 3: Conferir o commit gerado pela rotina**

```bash
git log -1 --oneline
```

Esperado (primeira execução): mensagem começando com `Inicializa monitoramento cBenef-SP (v...)`.

- [ ] **Step 4: Confirmar que a notificação de baseline chegou**

Verificação manual (não automatizável): confirmar com o usuário que uma notificação com o texto "Monitoramento cBenef-SP iniciado. Versão atual: ..." foi recebida (desktop e/ou celular, se Remote Control conectado).

---

### Task 4: Confirmar que o agendamento futuro está ativo

**Files:** nenhum.

**Interfaces:**
- Consome: `trigger_id` da Task 2.

- [ ] **Step 1: Listar a rotina e conferir o próximo disparo**

Chamar `RemoteTrigger` com `{"action": "get", "trigger_id": "<id>"}`.

Esperado: `enabled: true`, `cron_expression: "7 10 1 * *"`, e (se o campo existir na resposta) próxima execução prevista para o dia 1 do próximo mês.

- [ ] **Step 2: Registrar o link da rotina para o usuário**

Montar e reportar ao usuário: `https://claude.ai/code/routines/<trigger_id>` — link onde ele pode ver/editar/desabilitar a rotina depois, já que rotinas não podem ser deletadas por API (só por essa página).
