# CST PIS/COFINS — tabela oficial (Tabelas 4.3.3 e 4.3.4, Guia Prático EFD-Contribuições / Ministério da Fazenda)

Fonte: `Tabela_4_3_3.doc` (CST-PIS) e `Tabela_4_3_4.doc` (CST-COFINS), baixados do site do governo em 2026-07. **O código é o mesmo para PIS e COFINS** — as duas tabelas oficiais têm exatamente o mesmo conjunto de códigos/descrições, só nomeando a contribuição diferente no título.

## Saída (venda)

| CST | Descrição |
|---|---|
| 01 | Operação Tributável com Alíquota Básica |
| 02 | Operação Tributável com Alíquota Diferenciada |
| 03 | Operação Tributável com Alíquota por Unidade de Medida de Produto |
| 04 | Operação Tributável Monofásica - Revenda a Alíquota Zero |
| 05 | Operação Tributável por Substituição Tributária |
| 06 | Operação Tributável a Alíquota Zero |
| 07 | Operação Isenta da Contribuição |
| 08 | Operação sem Incidência da Contribuição |
| 09 | Operação com Suspensão da Contribuição |
| 49 | Outras Operações de Saída |

## Entrada (compra) — com direito a crédito

| CST | Descrição |
|---|---|
| 50 | Operação com Direito a Crédito - Vinculada Exclusivamente a Receita Tributada no Mercado Interno |
| 51 | Operação com Direito a Crédito - Vinculada Exclusivamente a Receita Não-Tributada no Mercado Interno |
| 52 | Operação com Direito a Crédito - Vinculada Exclusivamente a Receita de Exportação |
| 53 | Operação com Direito a Crédito - Vinculada a Receitas Tributadas e Não-Tributadas no Mercado Interno |
| 54 | Operação com Direito a Crédito - Vinculada a Receitas Tributadas no Mercado Interno e de Exportação |
| 55 | Operação com Direito a Crédito - Vinculada a Receitas Não-Tributadas no Mercado Interno e de Exportação |
| 56 | Operação com Direito a Crédito - Vinculada a Receitas Tributadas e Não-Tributadas no Mercado Interno, e de Exportação |

## Entrada (compra) — crédito presumido

| CST | Descrição |
|---|---|
| 60 | Crédito Presumido - Operação de Aquisição Vinculada Exclusivamente a Receita Tributada no Mercado Interno |
| 61 | Crédito Presumido - Operação de Aquisição Vinculada Exclusivamente a Receita Não-Tributada no Mercado Interno |
| 62 | Crédito Presumido - Operação de Aquisição Vinculada Exclusivamente a Receita de Exportação |
| 63 | Crédito Presumido - Operação de Aquisição Vinculada a Receitas Tributadas e Não-Tributadas no Mercado Interno |
| 64 | Crédito Presumido - Operação de Aquisição Vinculada a Receitas Tributadas no Mercado Interno e de Exportação |
| 65 | Crédito Presumido - Operação de Aquisição Vinculada a Receitas Não-Tributadas no Mercado Interno e de Exportação |
| 66 | Crédito Presumido - Operação de Aquisição Vinculada a Receitas Tributadas e Não-Tributadas no Mercado Interno e de Exportação |
| 67 | Crédito Presumido - Outras Operações |

## Entrada (compra) — sem crédito / outras

| CST | Descrição |
|---|---|
| 70 | Operação de Aquisição sem Direito a Crédito |
| 71 | Operação de Aquisição com Isenção |
| 72 | Operação de Aquisição com Suspensão |
| 73 | Operação de Aquisição a Alíquota Zero |
| 74 | Operação de Aquisição sem Incidência da Contribuição |
| 75 | Operação de Aquisição por Substituição Tributária |
| 98 | Outras Operações de Entrada |
| 99 | Outras Operações |

## Tabelas oficiais completas (EFD-Contribuições, Guia Prático RFB) — `references/cst-pis-cofins-tabelas-oficiais/`

Baixadas do site do governo em 2026-07, extraídas dos `.doc` originais. Cada uma é a fonte oficial e exaustiva pro CST correspondente — **consultar o arquivo, não confiar de memória**:

| Arquivo | CST | Conteúdo |
|---|---|---|
| `4.3.10-monofasico-diferenciadas-cst02-04.txt` | 02, 04 | Alíquotas diferenciadas monofásico/pauta — combustíveis (gasolina, diesel, GLP, querosene), veículos, autopeças, pneus, perfumaria — **alíquotas atuais** |
| `4.3.11-monofasico-pauta-unidade-medida-cst03-04.txt` | 03, 04 | Mesma família de produtos monofásicos, mas por unidade de medida — **histórico completo de mudanças de alíquota ao longo dos anos** (arquivo grande, ~1900 linhas; usar só a linha sem "Término de Escrituração" preenchido = alíquota vigente) |
| `4.3.12-substituicao-tributaria-cst05.txt` | 05 | Lista completa de produtos com PIS/COFINS-ST — **curta** (cigarrilhas, motocicletas, máquinas agrícolas, vendas à Zona Franca de Manaus) |
| `4.3.13-aliquota-zero-cst06.txt` | 06 | **A mais relevante pra loja de conveniência** — grupo 100 é uma cesta básica ampla (adubos, sementes, farinha de milho, pintos, leite, queijos, farinha de trigo, trigo, pré-mistura de pão, hortícolas/frutas in natura, ovos, massas alimentícias, carnes, peixes, **café, açúcar**, óleo vegetal, manteiga, margarina, sabão de toucador, produtos de higiene bucal, papel higiênico — códigos 101 a 130); grupo 200 é infraestrutura/combustíveis de aeronaves e embarcações (não relevante aqui) |
| `4.3.14-isencao-cst07.txt` | 07 | Isenção — bordo de embarcações/aeronaves internacionais, estaleiros navais, entidades especiais — não relevante pra conveniência |
| `4.3.15-sem-incidencia-cst08.txt` | 08 | Sem incidência — querosene de aviação, biodiesel (não-produtor), Itaipu, exportação |
| `4.3.16-suspensao-cst09.txt` | 09 | Suspensão — matérias-primas p/ exportação, drawback, insumos industriais |
| `4.3.17-outros-aliquotas-diferenciadas-cst02.txt` | 02 | Papel imune, Zona Franca de Manaus, REIQ (química) — não relevante pra conveniência |

## Leitura pro caso da loja/posto (RPA, Lucro Real, não-cumulativo)

- **Padrão (maioria dos produtos de conveniência):** saída CST 01 (1,65%/7,6%), entrada CST 50 ou 60 conforme direito a crédito.
- **Alíquota zero por lei federal específica (CST 06):** não é só café e açúcar — a tabela oficial `4.3.13` tem ~30 itens de cesta básica ampla (ver tabela acima). **Sempre checar o NCM contra esse arquivo antes de assumir o padrão.**
- **Monofásico (CST 04 saída)** — relevante pro **posto de combustível**: PIS/COFINS de combustíveis já é recolhido antes na cadeia (refinaria/distribuidora), então a revenda é a alíquota zero por não-cumulatividade monofásica, não por isenção. **Não confundir com CST 06** — são fundamentos legais diferentes mesmo ambos dando alíquota zero na saída. Ver `4.3.10`/`4.3.11` pras alíquotas específicas por produto (gasolina, diesel, GLP, etc. — cuidado com `4.3.11`, que tem histórico; usar só a linha vigente).
- **Substituição tributária (CST 05 saída)** — **RESOLVIDO:** a tabela `4.3.12` mostra que ST de PIS/COFINS é praticamente residual hoje — veículos/autopeças/pneus migraram pra monofásico em 14/05/2014 (Lei 12.973/2014), e o "catch-all" de revenda ST genérica expirou em 31/12/2013. O único grupo ainda ativo relevante pra uma loja de conveniência é **cigarrilhas (NCM 2402.10.00)** — se a loja não vende cigarrilhas, CST 05 não aparece na prática.
- Pra loja de conveniência pura (sem combustível), na prática só entram em jogo CST 01 (padrão) e CST 06 (lista de exceções do grupo 100 da `4.3.13`) — CST 04/05 ficam restritos a combustível (posto) e cigarrilhas, respectivamente.

## Pendências

- Cruzar a lista completa de NCMs do grupo 100 da `4.3.13` com o catálogo real da loja (`tabela-produtos-loja-cbenef.csv`) pra identificar TODOS os produtos com PIS/COFINS zero, não só os já pesquisados manualmente (café, açúcar) — provavelmente inclui leite, queijos, farinha de trigo, ovos, carnes, óleo, manteiga, margarina, papel higiênico.
- Extrair a alíquota vigente (linha sem término) de `4.3.10`/`4.3.11` pros combustíveis do posto, se/quando o escopo da skill/TributAi abranger o lado do posto.
- Cruzar essa tabela com `tabela-tributos-pis-cofins-webposto.csv` pra confirmar que as 26 referências cobrem os casos monofásico/CST06/ST relevantes, ou se faltam referências a cadastrar no webPosto.
