---
name: fiscal-produtos-sp
description: Pesquisa a classificação fiscal completa de um produto (NCM, ICMS - CST/CEST/cBenef/redução ou isenção de base de cálculo, PIS/COFINS) para empresas de Lucro Real no estado de São Paulo — voltado a posto de combustível e loja de conveniência (Grupo Dall Agnol / Posto Espaço Botânico / Atempo). Use sempre que o usuário pedir para "pesquisar produto", "buscar NCM", "qual o CST", "qual o cBenef", "tem código de benefício", perguntar sobre tributação de um item específico (por nome, EAN/código de barras, ou NCM já conhecido), pedir para auditar/conferir um cadastro de produto no ERP, ou enviar um print de tela de cadastro de produto / nota fiscal para revisão fiscal. Também use quando o usuário pedir para "adicionar" ou "continuar" a pesquisa de produtos — mantendo e atualizando a tabela de referência em references/tabela-produtos.md.
---

# Pesquisa Fiscal de Produtos — Lucro Real, SP

Workflow para levantar a tributação completa de um produto vendido no posto/conveniência, sempre no mesmo formato de saída (tabela), e manter um histórico consultável de tudo que já foi analisado.

## Antes de tudo: ler a referência acumulada

Sempre que essa skill for usada, primeiro dar uma olhada em:
1. `references/tabela-produtos.md` — produtos já analisados em detalhe (com raciocínio completo).
2. `references/tabela-produtos-loja-cbenef.csv` — levantamento amplo (~1193 itens) do cadastro real da loja, já cruzado com NCM, situação de benefício, dispositivo legal, cBenef sugerido e CST provável. Buscar primeiro aqui por nome do produto ou NCM antes de pesquisar do zero — grande parte do inventário típico de posto/conveniência já está mapeado.
3. `references/artigos-beneficios-sp.md` — os artigos do RICMS/SP já mapeados (incluindo a tabela completa do art. 3º Anexo II com todos os incisos confirmados).

**Contexto fixo da loja:** regime **RPA** (Lucro Presumido/Real), não Simples Nacional. Isso é crítico porque várias isenções do Anexo I (ex: art. 135) são exclusivas de optante do Simples — não usar essas mesmo que o produto apareça citado no artigo.

**⚠️ Lembrete de prazo já vencido:** desde 01/07/2026 o código genérico "SEM CBENEF" foi desativado em SP. Se em algum momento aparecer um cadastro ou pergunta usando esse código genérico, sinalizar isso proativamente — não é mais válido.

## Passo 1 — Identificar o produto e como ele chega até a venda

Isso muda tudo mais na frente, então perguntar/confirmar primeiro:

- **Compra pronto e revende sem alterar?** → CFOP 1.102 (entrada) / 5.102 (saída). É "comercialização".
- **Fabricação própria** (mistura, corta, monta, asa, prepara na loja)? → CFOP 5.101 na saída. O preparo de alimentos em lanchonetes, padarias, bares e semelhantes configura industrialização, modalidade transformação, mesmo que o preparo seja simples (ex: cortar e misturar frutas diferentes já é "preparação" pra fins de NCM — vira capítulo 20, não fica mais no capítulo 8/9/etc. de produto in natura).
- **Só esquenta/reidrata um produto que já vem pronto e recheado do fornecedor?** Isso NÃO conta como industrialização (mesma lógica do "esquentar não transforma" vista no café e no pão de queijo recheado que vem pronto) — trata como revenda simples.

Se a entrada tem NCM/CST diferente do que o usuário está usando na saída, sinalizar a inconsistência — normalmente indica erro de cadastro.

## Passo 2 — Determinar o NCM

Ordem de prioridade pra achar o NCM certo:

1. **Se o usuário tiver EAN/código de barras**: pesquisar `EAN <código> NCM` — sites como Systax e Cosmos (bluesoft) geralmente têm o NCM cadastrado pelo fabricante.
2. **Se o usuário tiver nota fiscal de entrada (a fonte mais confiável)**: usar o NCM que já vem destacado nela. Nota fiscal de compra > qualquer busca genérica.
3. **Se for produto novo/preparado na loja, sem NCM de referência**: pesquisar a classificação certa via NESH (Notas Explicativas do Sistema Harmonizado) e precedentes de consulta da Sefaz-SP (`legislacao.fazenda.sp.gov.br`), prestando atenção em pegadinhas comuns:
   - Mistura de itens diferentes (ex: salada de frutas, mix de saladas) geralmente sai do capítulo do produto in natura e vai pro capítulo 20 ("preparações"), mesmo sem cozimento.
   - Sucos: "integral" (100% fruta, sem água) fica no capítulo 2009; néctar/refresco (diluído, mínimo 30%/10% de suco) vai pro 2202.
   - Receita Federal já mudou classificação de itens comuns (ex: pão de queijo cru congelado saiu de 1905.90.90 e foi para 1901.20.00) — sempre checar se há mudança de entendimento recente antes de confiar num NCM "clássico".

Sempre lembrar: a responsabilidade pela classificação NCM é do contribuinte — em caso de dúvida genuína, recomendar confirmação com a Receita Federal ou com o fornecedor/fabricante, não apresentar um NCM incerto como definitivo.

## Passo 3 — ICMS: CST, CEST, ST, redução/isenção e cBenef

1. **Checar se tem CEST/ST**: buscar o NCM na Portaria CAT 68/19 (relação de mercadorias com ST em SP) ou CEST do Convênio 142/2018. Se não tiver CEST, não tem ST.
2. **Checar isenção ou redução de base de cálculo específica**, buscando por NCM/produto + "Anexo I" (isenções) ou "Anexo II" (reduções) + "RICMS SP". Ver `references/artigos-beneficios-sp.md` pros artigos já mapeados e suas pegadinhas.
3. **Definir o CST correto**:
   - **00** — tributação integral, sem benefício (padrão quando nada mais se aplica)
   - **20** — com redução de base de cálculo (geralmente exige cBenef)
   - **40** — isenta (geralmente exige cBenef, exceto quando dispensado)
   - **41** — não tributada (não incidência — raro se aplicar de fato; desconfiar se aparecer sem justificativa clara)
   - **60** — ICMS já cobrado por substituição tributária (produto com CEST/ST)
4. **cBenef**: buscar na Tabela cBenef SP oficial (`portal.fazenda.sp.gov.br/servicos/nfe/Downloads/Tabela-cBenef-SP-...pdf`) pelo artigo/dispositivo identificado no passo 2. **CST 00, 02, 10, 15, 60 e 61 são dispensados de cBenef** — os demais (20, 40, 41, 50, 51, 70, 90 etc.) normalmente exigem.

**Atenção à letra miúda de cada artigo de redução/isenção** — nem todo benefício vale a cadeia inteira:
- Alguns benefícios só valem operações entre fabricante/atacadista, **não valem saída de varejista pra consumidor final** (ex: art. 39 Anexo II, produtos alimentícios genéricos).
- Alguns são dedicados a um produto específico e valem a cadeia toda até o consumidor final, inclusive dentro do cálculo do próprio ICMS-ST (ex: art. 61 Anexo II, suco de laranja).
- Sempre ler o artigo inteiro (não só o caput) — parágrafos costumam trazer restrições que mudam tudo.

## Passo 4 — PIS/COFINS

Regime padrão pra Lucro Real: **não-cumulativo**, CST 01 na saída, PIS 1,65% + COFINS 7,6% (9,25% combinado). Ver `references/cst-pis-cofins-oficial.md` pro catálogo completo dos códigos CST-PIS/CST-COFINS (tabela oficial do Ministério da Fazenda — o mesmo código vale pras duas contribuições).

**Não é só padrão-vs-exceção — checar antes qual desses 3 ramos se aplica** (tabelas oficiais completas em `references/cst-pis-cofins-tabelas-oficiais/`, ver índice em `references/cst-pis-cofins-oficial.md`):
1. **Alíquota zero por lei federal específica sobre o produto** (CST 06 saída) — usar `references/tabela-pis-cofins-cesta-basica-cst06.json`, uma versão já curada e filtrada pra relevância de loja de conveniência (23 itens, com NCM normalizado em `ncmPrefixos`). A maior parte vem do grupo 100 da tabela oficial `4.3.13-aliquota-zero-cst06.txt` (Lei 10.925/2004 — cesta básica): arroz/farinhas, farinha de milho, **leite (inclui UHT — ver observação do item 110, diferente da regra de ICMS)**, queijos, farinha de trigo, pão/pré-mistura, hortícolas/frutas in natura, ovos, massas, carnes, peixes, **café**, **açúcar**, óleo vegetal, manteiga, margarina, sabão de toucador, produtos de higiene bucal, papel higiênico. Mas também inclui os itens **B1/B2 — bebidas frias não alcoólicas monofásicas** (águas NCM 22.01, refrigerantes/isotônicos/energéticos NCM 22.02), fundamento legal diferente (monofásico, não Lei 10.925) mas mesmo CST 06 na prática — **confirmado em 2026-07 contra cadastro real auditado (Imendes) pro NCM 2202.99.00, energético**. Muitos itens da lista 4.3.13 já são cesta básica pro ICMS (art. 3º Anexo II RICMS-SP) — **são benefícios independentes, confirmar os dois separadamente**, nunca assumir que um implica o outro.
2. **Monofásico de combustível (CST 04 saída na definição oficial)** — regime de tributação concentrada numa etapa da cadeia (refinaria/distribuidora recolhe antes), pros **combustíveis** propriamente ditos (gasolina, etanol, diesel, GLP, querosene — ver `4.3.10`/`4.3.11`, grupos 100-300). Relevante pro posto de combustível. `4.3.11` tem histórico de alíquotas; usar só a linha sem "Término de Escrituração" preenchido. **Nota:** bebidas frias (item 1 acima) também são monofásicas na origem, mas na prática do cadastro real usam CST 06, não CST 04 — o código exato pode variar por convenção do ERP/contador; o que importa é a alíquota zero e a referência webPosto certa.
3. **Substituição tributária (CST 05 saída)** — **residual hoje**: veículos/autopeças/pneus migraram pra monofásico em 14/05/2014, e o "catch-all" genérico de ST expirou em 2013 (ver `4.3.12`). Na prática só sobra **cigarrilhas (NCM 2402.10.00)** como caso ativo relevante — se a loja não vende cigarrilhas, ignorar CST 05.

Pra loja de conveniência pura (sem combustível), na prática só entram em jogo CST 01 (padrão) e CST 06 (cesta básica + bebidas frias monofásicas). Se o produto não se encaixar claramente em nenhum dos 3 ramos, aplicar o padrão (CST 01) mas avisar que a checagem não é 100% exaustiva — recomendar confirmação com o contador em caso de dúvida genuína.

**Depois de decidir o CST/alíquota (padrão ou exceção), traduzir pro código que a funcionária seleciona no webPosto** usando `references/tabela-tributos-pis-cofins-webposto.csv` — é a tabela de "Referência" (26 combinações válidas de CST/alíquota entrada e saída) do dropdown de PIS/COFINS do ERP, no mesmo espírito da tabela cBenef pro ICMS. Ex.: padrão não-cumulativo saída = CST 01, 1,65%/7,6% → referência `0000000002` ou `0000000008` (checar entrada correspondente); alíquota zero (café/açúcar) = CST 06 saída, 0% → referência `0000000006`, `0000000017`, `0000000007` ou `0000000025` (diferem pelo CST de entrada).

## Passo 5 — Apresentar no formato padrão (tabela)

Sempre entregar o resultado nesse formato — isso é o padrão agora, não perguntar de novo:

**Resumo rápido (tabela principal):**

| NCM | CEST | CST ICMS | cBenef | Redução/Isenção | PIS | COFINS |
|---|---|---|---|---|---|---|

**Detalhamento abaixo da tabela**, só quando relevante: CFOP recomendado, base legal específica, alertas de risco/inconsistência encontrados, e o que precisa ser confirmado com nota fiscal real antes de considerar definitivo.

## Passo 6 — Atualizar a referência

Depois de fechar a análise de um produto, adicionar (ou atualizar) a linha correspondente em `references/tabela-produtos.md`, incluindo a data e a fonte/base legal usada. Isso constrói o histórico de auditoria do usuário ao longo do tempo.

## Limitações — ser direto sobre isso

- NCM e cBenef corretos, na prática, **dependem de confirmação com nota fiscal real** sempre que possível — buscas genéricas na web são um bom ponto de partida, mas não substituem a nota do fornecedor nem a palavra final de um contador.
- Legislação de redução/benefício muda com frequência em SP (decretos revisam vigência e percentuais quase todo ano) — sempre mencionar quando uma fonte encontrada tiver mais de ~1 ano, para o usuário confirmar se ainda está vigente.
- Quando a classificação for genuinamente ambígua (ex: produto que pode se enquadrar em duas posições), apresentar as duas possibilidades com a diferença que as separa, em vez de escolher uma arbitrariamente.
