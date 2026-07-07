# Artigos de isenção/redução do RICMS/SP já mapeados

Referência rápida dos dispositivos já confirmados em pesquisas anteriores. Sempre confirmar vigência (decretos de SP costumam ter prazo de validade e são renovados/alterados com frequência).

## ⚠️ ALERTA DE PRAZO — cBenef obrigatório e "SEM CBENEF" desativado

- Desde **06/04/2026**: cBenef é obrigatório no XML da NF-e/NFC-e sempre que a operação tiver isenção, não incidência, redução de base de cálculo, regime especial de tributação sobre receita bruta, suspensão ou diferimento (Decreto 69.981/2025, Portaria SRE 70/2025).
- Desde **01/07/2026**: o código genérico **"SEM CBENEF" foi desativado**. Não é mais aceito como coringa — precisa ser o código específico correto, senão a nota é rejeitada.
- Regime da loja é **RPA (Lucro Presumido/Real)**, não Simples Nacional — isso importa porque várias isenções do Anexo I (ex: art. 135) são **exclusivas de optantes do Simples Nacional** e não se aplicam à loja, mesmo que o produto apareça citado nesse artigo.

## Regime da loja e por que isso muda tudo

Como a loja é **RPA**, ela não pode usar isenções desenhadas para dar ao Simples Nacional um equivalente à cesta básica (que Simples não pode usar via redução de base, só via isenção). Nesses casos, a loja usa a **redução de base de cálculo do art. 3º, Anexo II** (carga 7%) para o mesmo produto, não a isenção do artigo correspondente do Anexo I.

## Isenções (Anexo I)

### Artigo 36, Anexo I — Hortifrutigranjeiros em estado natural
- **cBenef: SP010360**
- CST: 40 (isenta)
- Cobre: hortifrutigranjeiros e **ovos em estado natural** (inciso IX), desde que não destinados à industrialização.
- Sem estorno de crédito exigido (§2º).
- **Pegadinha**: só vale em "estado natural" — casca inteira/produto não processado. Ovo trincado ainda conta como natural; ovo descascado ou processado (pasteurizado etc.) perde a isenção.
- Ovo pasteurizado/desidratado NÃO entra aqui — vai para redução do art. 3º, XVIII, Anexo II (carga 7%).

## Outras isenções específicas (Anexo I) — confirmadas com produto real

### Artigo 168, Anexo I — Arroz (venda a consumidor final)
- **cBenef: SP011680** | CST: 40 (isenta)
- O art. 3º, XXVI do Anexo II remete a este artigo quando a saída é para consumidor final (caso da loja) — ou seja, na prática vira isenção, não redução de 7%.

### Artigo 169, Anexo I — Feijão (venda a consumidor final)
- **cBenef: SP011690** | CST: 40 (isenta)
- Mesma lógica do arroz: art. 3º, XXVII do Anexo II remete a este artigo na venda a consumidor final.

### Artigo 140, Anexo I — Maçã e pêra em estado natural
- **cBenef: SP011400** | CST: 40 (isenta)
- Isenção específica, separada do art. 36 (que cobre os demais hortifrutigranjeiros) — maçã e pêra têm artigo próprio.

### Artigo 176, Anexo I — Absorventes higiênicos
- **cBenef: SP011760** | CST: 40 (isenta)

### Artigos 43 e 103, Anexo I — Leite cru, pasteurizado tipo A/B/C ou reidratado
- **cBenef: SP011030** | CST: 40 (isenta)
- **Não cobre leite UHT/longa vida em caixinha** — a cesta básica (art. 3º) só cobre leite EM PÓ. Leite fluido comum de caixinha (a maioria do que se vende em conveniência) fica sem benefício algum, tributação normal.

### Artigo 123, Anexo I — Farinha de mandioca
- Isenção só para farinha PURA — farinha de mandioca TEMPERADA fica de fora (Decisão Normativa CAT-03/07).

### Artigo 135, Anexo I — Farinha de trigo, massas, pão francês, biscoitos (água-e-sal/maisena/cream cracker)
- Isenção criada para dar ao Simples Nacional o equivalente à cesta básica.
- **Exclusiva de optante do Simples Nacional.** A loja, sendo RPA, usa a redução do art. 3º do Anexo II pros mesmos itens (carga 7%, cBenef SP020030) — nunca a isenção do 135.

## Reduções de base de cálculo (Anexo II)

### Artigo 3º, Anexo II — Cesta básica
- Carga tributária 7%.
- **cBenef: SP020030** — mesmo código pra qualquer inciso do artigo (confirmado na Tabela cBenef SP oficial e cruzado com planilha de ~1193 produtos reais da loja).
- CST: 20
- Cobre itens como arroz, feijão, farinha de mandioca/trigo, pão francês, carnes em estado natural, ovo pasteurizado/desidratado (inciso XVIII), ovo de codorna preparado (inciso XIV).
- **Não cobre ovo in natura** (esse é isenção do art. 36, não redução daqui).

**Incisos já confirmados com produto real da loja (todos cBenef SP020030, CST 20):**

| Inciso | Cobre | Pegadinha / exclusão |
|---|---|---|
| II | Leite em pó | Leite fluido (UHT/caixinha) NÃO está aqui — ver art. 43/103 abaixo |
| III | Café torrado em grão ou moído (subposição 0901.2) | NÃO vale para café solúvel (NCM 2101) |
| IV | Óleo vegetal comestível, exceto oliva | Art. 137 Anexo I é isenção só de óleo USADO p/ insumo industrial — não é isso |
| V | Açúcar cristal/refinado | — |
| VII | Farinha de milho/fubá | — |
| IX | Manteiga/margarina/creme vegetal | Queijos NÃO estão mais aqui desde o Decreto 53.631/08 |
| X | "Apresuntado" (produto reconstituído específico) | Termo distinto de "presunto" tradicional — confirmar qual é o produto antes de aplicar |
| XIV | Pão de forma/bisnaga | Só SEM cobertura, fruta ou chocolate — pão com castanha/grãos pode ficar de fora |
| XVII | Farinha de trigo | Existe isenção equivalente no art. 135 Anexo I, mas é EXCLUSIVA do Simples Nacional |
| XIX | Massas alimentícias não cozidas, sem recheio/tempero (pos. 1902.11) | Macarrão instantâneo/temperado/pronto (tipo miojo) tende a ficar FORA — pode ser ST |
| XX | Biscoito água-e-sal/maisena/cream cracker/maria | SEM cacau, recheio ou cobertura — biscoito recheado não entra |
| XXI | Pão francês/de sal | Até 1kg, sem ingrediente que descaracterize |
| XXIII | Salsicha/mortadela | Salame NÃO está incluído |

**Regra geral:** sempre que o produto também aparecer isento no art. 135 do Anexo I, essa isenção é **exclusiva do Simples Nacional** — a loja (RPA) usa a redução do art. 3º, não a isenção do 135.

### Artigo 39, Anexo II — Produtos alimentícios (uso humano)
- Carga tributária 12% (redução de 33,33% na base).
- **cBenef: SP020390**
- CST: 20
- **Só vale operações realizadas por fabricante ou atacadista.**
- **NÃO se aplica a saídas destinadas a consumidor final, nem a saídas de estabelecimento varejista, nem a destinatário do Simples Nacional.**
- Ou seja: vale na sua ENTRADA (comprando de fabricante/atacadista), mas a sua SAÍDA pro consumidor final volta a ser CST 00, sem redução, sem cBenef.
- Exemplo confirmado: massa de pão de queijo (NCM 1901.20.10), comprada de fabricante — nota real conferida trazia exatamente esse enquadramento.

### Artigo 61, Anexo II — Suco de laranja (NCM subposição 2009.1, qualquer subcódigo)
- Carga tributária 12%.
- **cBenef: SP020610**
- CST: 20 na operação própria; 60 quando já vier com ICMS-ST retido (a maioria dos casos de revenda, já que suco de laranja tem CEST 17.010.00 e é ST em SP)
- **Diferente do art. 39**: esse benefício vale a cadeia inteira, incluindo venda ao consumidor final — confirmado por resposta a consulta oficial da Sefaz-SP (RC 18998/2019, RC 27086/2023, RC 31857/2025).
- A redução também entra no cálculo do próprio ICMS-ST (art. 51, parágrafo único do RICMS), então o fornecedor já usa os 12% ao calcular o ICMS-ST retido — não é algo que o varejista recalcula.
- Vigência 12% confirmada até 31/12/2026 pelo Decreto Estadual 69.421/2025 (checar se foi renovado além dessa data quando for usar).
- **Não confundir** com néctar/refresco (NCM 2202.xx) — esses NÃO entram no art. 61, têm tratamento totalmente diferente (podem entrar no art. 39, XVI, se forem "bebidas à base de soja/leite/cacau ou néctar", mas não é o mesmo benefício).

## PIS/COFINS — leis federais específicas

### Lei 10.925/2004, art. 1º, XXI (redação Lei 12.839/2013) — Café
- Alíquota ZERO de PIS/COFINS.
- Cobre café classificado nos códigos **09.01 e 2101.1** da TIPI — "2101.1" é o grupo inteiro, cobre tanto 2101.11 (extratos/concentrados) quanto 2101.12 (preparações à base de café), não importa o subcódigo exato.
- CST PIS/COFINS: 06 (tributável à alíquota zero).
- Judicialmente confirmado que não se restringe a insumo de cesta básica — vale amplamente pra qualquer produto na classificação, inclusive café preparado/vendido pronto (cafezinho, expresso).

## Erros comuns encontrados em cadastros de ERP (padrão a desconfiar)

- **CST "não tributada" (041) junto com CEST preenchido** — contraditório. CEST só existe se o produto tem ST; se tem ST, o CST correto é 60 (ou 10, do lado do substituto), não 41.
- **CST de redução (20) aplicado igual na entrada e na saída sem checar se o artigo permite isso** — muitos artigos (como o 39) só valem um lado da cadeia.
- **NCM desatualizado** — Receita Federal muda entendimento de classificação com alguma frequência (exemplo real: pão de queijo saiu de 1905.90.90 para 1901.20.00).
