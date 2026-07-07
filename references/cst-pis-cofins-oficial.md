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

## Leitura pro caso da loja/posto (RPA, Lucro Real, não-cumulativo)

- **Padrão (maioria dos produtos de conveniência):** saída CST 01 (1,65%/7,6%), entrada CST 50 ou 60 conforme direito a crédito.
- **Alíquota zero por lei federal específica** (café, açúcar — ver `tabela-produtos.md`): saída CST 06.
- **Monofásico (CST 04 saída)** — relevante pro **posto de combustível**: PIS/COFINS de combustíveis já é recolhido antes na cadeia (refinaria/distribuidora), então a revenda é a alíquota zero por não-cumulatividade monofásica, não por isenção. **Não confundir com CST 06** — são fundamentos legais diferentes mesmo ambos dando alíquota zero na saída.
- **Substituição tributária (CST 05 saída)** — existe uma lista de produtos com PIS/COFINS-ST (bem mais restrita que ICMS-ST); ainda não mapeamos quais itens do catálogo da loja se enquadram aqui — **pendente de pesquisa**.
- Pra loja de conveniência pura (sem combustível), CST 04/05 provavelmente não aparecem — mas como o Grupo Dall'Agnol também é posto, o TributAi/skill não pode assumir que só existe padrão-vs-alíquota-zero.

## Pendências

- Mapear quais NCMs de combustível/derivados usam CST 04 monofásico (gasolina, etanol, diesel — cada um tem regramento federal próprio de PIS/COFINS monofásico).
- Confirmar se algum item do catálogo da conveniência cai em PIS/COFINS-ST (CST 05) — verificar lista oficial de produtos sujeitos a essa modalidade.
- Cruzar essa tabela com `tabela-tributos-pis-cofins-webposto.csv` pra confirmar que as 26 referências cobrem os casos monofásico/ST relevantes, ou se faltam referências a cadastrar no webPosto.
