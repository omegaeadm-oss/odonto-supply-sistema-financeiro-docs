# Módulo de vendas

Esta página documenta o módulo de vendas do sistema financeiro e comercial interno da Odonto Supply.

O módulo de vendas é uma das partes centrais do sistema, porque conecta operação comercial, financeiro, estoque, parcelas, margem, geração de PDF e upload para Google Drive.

---

## Objetivo do módulo

O objetivo do módulo de vendas é registrar vendas confirmadas de forma organizada, padronizada e integrada com o restante do sistema.

Uma venda registrada corretamente pode impactar:

* faturamento;
* parcelas a receber;
* estoque;
* margem;
* lucro;
* custos;
* relatórios;
* geração de PDF;
* upload para Google Drive;
* histórico comercial.

Por isso, o módulo de vendas precisa ser tratado como uma área sensível do sistema.

---

## Diferença entre venda e orçamento

Uma regra importante do sistema é separar claramente venda de orçamento.

```text
Orçamento = proposta comercial
Venda = operação confirmada
```

O orçamento não deve afetar estoque nem financeiro realizado.

A venda, por outro lado, representa uma operação confirmada e pode gerar:

* parcelas;
* baixa de estoque;
* registros financeiros;
* cálculo de margem;
* documentos comerciais;
* relatórios.

Essa separação evita que uma proposta não aprovada distorça o estoque ou os indicadores financeiros.

---

## Campos principais da venda

Uma venda pode conter informações como:

* nome da venda;
* cliente;
* data da venda;
* valor total;
* custo dos produtos;
* taxas;
* frete;
* entrada;
* forma de pagamento da entrada;
* quantidade de parcelas;
* forma de pagamento das parcelas;
* vencimentos;
* observações;
* produtos vinculados;
* status de custos;
* datas de pagamento de custos diretos.

Esses campos permitem registrar tanto a parte comercial quanto a parte financeira da venda.

---

## Nome da venda

O nome da venda é usado como identificador comercial e também como base para o nome do PDF.

Exemplo:

```text
Venda - 108
```

A padronização do nome evita arquivos inconsistentes e facilita a localização no sistema e no Google Drive.

Após a implementação da numeração comercial única, o sistema passou a preencher o nome da venda automaticamente em novas vendas diretas.

---

## Numeração da venda

A venda participa da sequência comercial única compartilhada com os orçamentos.

Exemplo:

```text
Orçamento - 107
Venda - 108
Orçamento - 109
```

Quando uma venda nasce a partir de um orçamento aprovado, ela pode manter o mesmo número do orçamento.

Exemplo:

```text
Orçamento - 107
→ Venda - 107
```

Quando a venda é criada diretamente, sem orçamento, ela consome o próximo número disponível da sequência.

---

## Venda direta

Venda direta é uma venda criada sem origem em orçamento.

Fluxo simplificado:

```text
usuário cria nova venda
→ sistema reserva próximo número comercial
→ sistema preenche nome da venda
→ usuário informa cliente, produtos e valores
→ sistema salva a venda
→ sistema gera parcelas, se houver
→ sistema baixa estoque
```

A venda direta consome um número novo da sequência comercial.

---

## Venda originada de orçamento

Quando um orçamento é aprovado, ele pode ser convertido em venda.

Nesse caso, a venda não deve consumir novo número.

Fluxo simplificado:

```text
orçamento aprovado
→ usuário inicia conversão
→ sistema carrega dados do orçamento
→ venda mantém referência do orçamento
→ sistema salva como venda confirmada
```

Essa regra preserva o vínculo entre proposta e venda final.

---

## Produtos vinculados à venda

A venda pode ter produtos vinculados.

Esses produtos ajudam a compor:

* descrição comercial;
* valor total;
* custo;
* margem;
* PDF;
* histórico de venda;
* baixa de estoque.

O vínculo entre venda e produtos é importante porque uma venda não é apenas um valor financeiro; ela representa itens reais vendidos.

---

## Estoque na venda

A venda confirmada pode afetar o estoque.

Regra principal:

```text
venda salva baixa estoque
orçamento não baixa estoque
```

Essa regra evita baixa indevida antes da venda ser confirmada.

O sistema precisa ter cuidado para não baixar estoque duas vezes em casos de edição ou conversão.

---

## Parcelas da venda

Uma venda pode ser parcelada.

O sistema pode gerar parcelas a partir de informações como:

* valor total;
* entrada;
* quantidade de parcelas;
* vencimentos;
* forma de pagamento;
* valor de cada parcela.

As parcelas são usadas para controlar o que ainda precisa ser recebido.

---

## Entrada

Algumas vendas possuem entrada.

A entrada representa um valor inicial da venda.

Ela pode ter:

* valor específico;
* forma de pagamento;
* data de vencimento;
* data de recebimento, quando aplicável.

A entrada deve ser tratada separadamente do saldo parcelado.

---

## Parcelas a receber

As parcelas geradas por uma venda ficam disponíveis na área de parcelas a receber.

Elas podem ter status como:

* pendente;
* recebida;
* em atraso;
* cancelada, se aplicável.

O sistema precisa separar valores previstos de valores realmente recebidos.

Uma parcela pendente não deve ser tratada como dinheiro em caixa.

---

## Ajuste manual de parcela

Durante o uso real, surgiu um caso importante envolvendo juros.

Uma parcela foi recebida com valor maior por causa de juros/multa, e o sistema tentou compensar essa diferença reduzindo uma parcela futura.

Esse comportamento estava incorreto.

A regra corrigida foi:

```text
ajuste manual em uma parcela recebida não deve alterar parcelas futuras já criadas
```

Exemplo:

```text
Parcela original: R$ 328,32
Parcela recebida com juros: R$ 336,84
Diferença: R$ 8,52
```

Essa diferença pertence à parcela ajustada, não deve ser descontada da próxima parcela.

Essa correção preserva boletos futuros e evita divergência entre sistema e cobrança real.

---

## Edição de venda

Editar uma venda existente precisa ser feito com cuidado.

Nem toda edição deve recalcular parcelas.

Exemplos de edições que não deveriam recriar parcelas automaticamente:

* ajuste em dados do cliente;
* observações;
* pequenos dados cadastrais;
* edição de produtos sem mudança explícita no plano financeiro;
* alteração em uma parcela já recebida.

A lógica de edição precisa preservar parcelas existentes por padrão e recalcular apenas quando houver mudança explícita no plano financeiro.

---

## Custos da venda

A venda pode ter custos associados, como:

* custo dos produtos;
* taxa de pagamento;
* frete;
* custos diretos;
* outras despesas vinculadas.

Esses custos são importantes para calcular lucro e margem.

É importante diferenciar:

```text
custo da venda
```

de:

```text
despesa fixa da empresa
```

Custos da venda entram na análise da margem daquela operação. Despesas fixas fazem parte da operação mensal.

---

## Margem e lucro

A venda deve permitir calcular resultado comercial.

Exemplo conceitual:

```text
valor de venda
- custo dos produtos
- taxas
- frete
= resultado bruto
```

A margem mostra a relação entre resultado e faturamento.

Esse cálculo ajuda a entender se a venda foi realmente lucrativa.

---

## Status dos custos diretos

Alguns custos podem ser pagos depois da venda.

Por isso, o sistema pode controlar status de custos diretos.

Exemplos:

* custo pendente;
* custo pago;
* data de pagamento;
* forma de controle.

Essa separação ajuda a entender o resultado da venda e o impacto no caixa.

---

## Venda e saldo

A venda em si não deve necessariamente aumentar o saldo imediatamente.

O saldo deve refletir movimentação real.

Regra conceitual:

```text
venda cria recebíveis
recebimento aumenta saldo
```

Isso evita que uma venda parcelada seja tratada como dinheiro já recebido.

---

## Venda e relatórios

As vendas alimentam relatórios e indicadores.

Podem impactar:

* faturamento mensal;
* quantidade de vendas;
* margem;
* resultado;
* desempenho anual;
* produtos vendidos;
* recebíveis;
* histórico comercial.

Por isso, erros no lançamento de vendas podem afetar várias áreas do sistema.

---

## PDF da venda

O sistema permite gerar PDF comercial da venda.

O PDF pode incluir:

* cabeçalho visual;
* dados do cliente;
* produtos;
* valores;
* condições de pagamento;
* parcelas;
* observações;
* resumo financeiro.

O nome do arquivo segue o nome da venda.

Exemplo:

```text
Venda - 108.pdf
```

---

## Upload da venda para Google Drive

A venda possui um botão manual com ícone do Google Drive.

Ao clicar:

```text
sistema gera o PDF internamente
→ envia para /api/upload-drive
→ API salva no Google Drive
→ arquivo fica organizado por ano e mês
```

Destino padrão:

```text
ODONTO SUPPLY / Venda-YYYY / Mês-YY
```

Exemplo:

```text
ODONTO SUPPLY / Venda-2026 / Junho-26
```

Se o arquivo já existir na pasta mensal, ele é atualizado em vez de duplicado.

---

## Decisão por upload manual

O upload para Drive é manual, não automático.

Motivos:

* permite revisar a venda antes de enviar;
* evita salvar documento incompleto;
* reduz risco de upload acidental;
* dá mais controle ao usuário;
* facilita o diagnóstico em caso de erro.

O usuário decide quando o PDF está pronto para ser enviado ao Drive.

---

## Relação com Google Drive

A venda se conecta ao Drive por meio do PDF.

A API organiza os arquivos por:

```text
tipo de documento
ano
mês
```

Para vendas, o tipo é:

```text
revenue
```

A pasta anual usa o padrão:

```text
Venda-YYYY
```

A pasta mensal usa o padrão:

```text
Mês-YY
```

---

## Testes importantes

Ao alterar o módulo de vendas, alguns testes são importantes:

* criar venda direta;
* criar venda parcelada;
* criar venda com entrada;
* converter orçamento em venda;
* conferir numeração comercial;
* conferir parcelas geradas;
* conferir estoque;
* marcar parcela como recebida;
* editar venda sem alterar parcelas indevidamente;
* gerar PDF;
* enviar PDF para Drive;
* conferir relatórios;
* conferir saldo, quando houver recebimento.

---

## Cuidados ao alterar o módulo

O módulo de vendas é sensível.

Antes de qualquer alteração, é importante diagnosticar:

* se a mudança afeta parcelas;
* se afeta estoque;
* se afeta saldo;
* se afeta PDFs;
* se afeta Google Drive;
* se afeta relatórios;
* se afeta numeração;
* se exige SQL;
* se altera criação ou edição de venda.

Mudanças nesse módulo devem ser pequenas e bem testadas.

---

## O que evitar

Evitar alterações que misturem muitas áreas ao mesmo tempo.

Exemplo ruim:

```text
alterar layout da venda
+ mexer em parcelas
+ alterar estoque
+ mudar PDF
+ ajustar dashboard
```

Isso aumenta muito o risco de quebrar algo.

O ideal é separar por etapas.

---

## Aprendizados

Principais aprendizados do módulo de vendas:

* venda é uma entidade central do sistema;
* venda afeta várias áreas ao mesmo tempo;
* orçamento e venda precisam ter regras separadas;
* parcelas futuras não devem ser recalculadas sem necessidade;
* estoque só deve baixar com venda confirmada;
* saldo deve refletir recebimento real, não apenas venda lançada;
* PDFs e Drive dependem da padronização da venda;
* pequenas mudanças em vendas podem ter impacto grande.

---

## Resumo

O módulo de vendas centraliza a operação comercial confirmada da Odonto Supply.

Ele conecta:

```text
cliente
produtos
valores
custos
parcelas
estoque
PDF
Google Drive
relatórios
```

Por isso, é um dos módulos mais importantes e sensíveis do sistema.
