# Módulo de parcelas e recebíveis

Esta página documenta o módulo de parcelas e recebíveis do sistema financeiro e comercial interno da Odonto Supply.

O módulo de parcelas é essencial para separar vendas realizadas de valores efetivamente recebidos. Essa separação evita distorções no saldo, nos relatórios financeiros e no acompanhamento do caixa.

---

## Objetivo do módulo

O objetivo do módulo é controlar os valores a receber gerados por vendas.

Uma venda pode ser paga:

* à vista;
* com entrada;
* parcelada;
* por boleto;
* por Pix;
* por cartão;
* por combinação de formas de pagamento.

O sistema precisa registrar não apenas o valor total vendido, mas também quando e quanto será recebido.

---

## Conceito principal

A regra central do módulo é:

```text
venda cria recebíveis
recebimento altera o saldo
```

Ou seja, uma venda parcelada não significa que todo o dinheiro já entrou no caixa.

Exemplo:

```text
Venda de R$ 3.000,00 em 3 parcelas
```

O sistema pode registrar a venda total, mas o saldo só deve aumentar conforme cada parcela for recebida.

---

## Venda lançada vs dinheiro recebido

Uma das principais decisões do sistema foi separar:

```text
valor vendido
```

de:

```text
valor recebido
```

Essa separação é importante porque o faturamento comercial e o caixa real não são a mesma coisa.

Exemplo:

```text
Venda realizada: R$ 3.000,00
Recebido até agora: R$ 1.000,00
A receber: R$ 2.000,00
```

Se o sistema tratasse os R$ 3.000,00 como saldo disponível imediatamente, o financeiro ficaria incorreto.

---

## Parcelas geradas pela venda

Quando uma venda é parcelada, o sistema gera parcelas com informações como:

* número da parcela;
* valor;
* data de vencimento;
* status;
* forma de pagamento;
* venda vinculada;
* cliente;
* data de recebimento, quando paga;
* observações, quando necessário.

Essas parcelas formam a base do controle de recebíveis.

---

## Entrada da venda

Algumas vendas possuem entrada.

A entrada representa um valor inicial pago ou previsto no começo da venda.

Exemplo:

```text
Venda de R$ 5.000,00
Entrada de R$ 1.000,00
Saldo restante em 4 parcelas de R$ 1.000,00
```

A entrada precisa ser controlada de forma clara para não ser confundida com as parcelas futuras.

---

## Status das parcelas

Uma parcela pode ter diferentes situações.

Exemplos conceituais:

```text
pendente
recebida
em atraso
cancelada
```

O status permite acompanhar o que ainda precisa ser cobrado e o que já foi recebido.

---

## Parcela pendente

Uma parcela pendente representa dinheiro previsto, mas ainda não recebido.

Regra:

```text
parcela pendente não aumenta saldo
```

Ela pode aparecer como valor a receber, mas não como caixa realizado.

---

## Parcela recebida

Uma parcela recebida representa entrada real de dinheiro.

Regra:

```text
parcela recebida aumenta saldo
```

Ao marcar uma parcela como recebida, o sistema passa a considerar aquele valor como realizado.

Esse ponto é importante para manter o saldo coerente com o caixa real.

---

## Parcela em atraso

Uma parcela pode ser considerada em atraso quando passa da data de vencimento sem registro de recebimento.

Esse controle ajuda a identificar:

* clientes que precisam ser cobrados;
* valores pendentes;
* impacto no caixa;
* previsibilidade financeira.

---

## Forma de pagamento

Cada parcela pode ter uma forma de pagamento.

Exemplos:

* Pix;
* boleto;
* cartão de crédito;
* cartão de débito;
* dinheiro;
* transferência;
* outros meios definidos pela operação.

A forma de pagamento ajuda na conferência financeira e na conciliação.

---

## Data de vencimento

A data de vencimento define quando a parcela deveria ser paga.

Ela é usada para:

* controle de cobrança;
* identificação de atraso;
* organização do fluxo de caixa;
* previsão de recebimentos futuros.

---

## Data de recebimento

A data de recebimento indica quando o dinheiro realmente entrou.

Essa data é diferente da data de vencimento.

Exemplo:

```text
Vencimento: 05/06/2026
Recebimento: 07/06/2026
```

Esse controle ajuda a entender atrasos, fluxo real de caixa e comportamento dos recebimentos.

---

## Ajuste manual de parcela

Durante o uso real do sistema, surgiu a necessidade de ajustar manualmente uma parcela recebida.

Exemplo:

```text
Valor previsto: R$ 328,32
Valor recebido: R$ 336,84
Diferença por juros/multa: R$ 8,52
```

A regra correta é:

```text
ajuste manual em parcela recebida não deve recalcular parcelas futuras
```

A diferença pertence à parcela ajustada.

O sistema não deve descontar essa diferença de uma parcela futura, porque boletos futuros podem já estar emitidos com valor fixo.

---

## Problema evitado

Antes da correção dessa lógica, o sistema poderia tentar compensar o valor recebido a mais reduzindo uma parcela futura.

Exemplo incorreto:

```text
Parcela 1 recebida com R$ 8,52 a mais
→ sistema reduz Parcela 2 em R$ 8,52
```

Esse comportamento seria ruim porque alteraria cobranças futuras já acordadas.

A correção preserva as parcelas futuras.

---

## Regra de preservação das parcelas

Regra adotada:

```text
parcelas já criadas devem ser preservadas, salvo ação explícita do usuário
```

Isso evita mudanças automáticas inesperadas.

Principalmente em casos de:

* boleto já emitido;
* acordo comercial já feito;
* parcela já enviada ao cliente;
* vencimentos definidos;
* ajustes pontuais em parcela recebida.

---

## Recalcular parcelas

Recalcular parcelas pode ser necessário em alguns casos, mas deve ser uma ação controlada.

Exemplos em que pode fazer sentido recalcular:

* alteração no valor total da venda antes de cobranças serem emitidas;
* mudança explícita na condição de pagamento;
* alteração na quantidade de parcelas;
* correção completa do plano financeiro da venda.

Mesmo nesses casos, o sistema precisa evitar recalcular sem intenção clara.

---

## Relação com vendas

As parcelas nascem a partir da venda.

Fluxo simplificado:

```text
venda confirmada
→ sistema identifica condição de pagamento
→ gera parcelas
→ parcelas ficam disponíveis para acompanhamento
```

A venda define a origem do recebível.

A parcela define o acompanhamento do recebimento.

---

## Relação com orçamento

Orçamentos podem apresentar condições de pagamento, mas não devem gerar recebíveis reais.

Regra:

```text
orçamento apresenta condição
venda gera parcela real
```

Isso evita que propostas ainda não aprovadas apareçam como dinheiro a receber.

---

## Relação com saldo

O módulo de parcelas tem relação direta com o saldo.

Regra principal:

```text
saldo muda com recebimento real
```

Não basta existir uma parcela prevista.

O saldo deve ser alterado quando a parcela for marcada como recebida ou quando houver lançamento financeiro equivalente.

---

## Relação com relatórios

As parcelas alimentam análises importantes.

Exemplos:

* total a receber;
* valores vencidos;
* valores recebidos;
* previsão de recebimento;
* fluxo de caixa;
* inadimplência;
* desempenho mensal;
* diferença entre vendido e recebido.

Essas informações ajudam a entender o financeiro com mais precisão.

---

## Relação com dashboard

O dashboard pode usar dados de parcelas para mostrar indicadores como:

* recebíveis pendentes;
* valores em atraso;
* valores recebidos no mês;
* previsão de entradas;
* saldo realizado;
* quantidade de parcelas pendentes.

É importante que o dashboard não confunda parcela pendente com dinheiro recebido.

---

## Cuidados ao alterar o módulo

O módulo de parcelas é sensível porque afeta o financeiro.

Antes de qualquer alteração, é necessário verificar:

* se a mudança altera saldo;
* se recalcula parcelas;
* se afeta vendas antigas;
* se altera vencimentos;
* se altera valores recebidos;
* se afeta relatórios;
* se afeta dashboard;
* se exige alteração em banco de dados;
* se há parcelas já recebidas;
* se há boletos já emitidos.

---

## Testes importantes

Ao alterar o módulo de parcelas, é recomendado testar:

* venda à vista;
* venda com entrada;
* venda parcelada;
* marcação de parcela como recebida;
* parcela recebida com valor diferente;
* parcela pendente;
* parcela em atraso;
* edição de venda sem recalcular parcelas indevidamente;
* relatório de recebíveis;
* dashboard;
* saldo após recebimento;
* preservação de parcelas futuras.

---

## O que evitar

Evitar comportamentos como:

```text
parcela pendente aumentando saldo
orçamento gerando recebível real
ajuste manual alterando parcelas futuras
edição simples recriando todas as parcelas
recalcular boletos já acordados
misturar faturamento com caixa
```

Esses comportamentos poderiam causar distorção financeira.

---

## Critério de sucesso

O módulo funciona corretamente quando:

* parcelas pendentes aparecem como valores a receber;
* parcelas recebidas entram no saldo;
* parcelas futuras são preservadas;
* venda lançada não é confundida com dinheiro recebido;
* orçamento não gera recebíveis reais;
* dashboard mostra valores coerentes;
* relatórios diferenciam previsto e realizado.

---

## Aprendizados

Principais aprendizados do módulo:

* venda não é igual a recebimento;
* parcela pendente não é saldo;
* ajuste manual precisa preservar parcelas futuras;
* boletos e cobranças já emitidas não devem ser alterados automaticamente;
* o financeiro precisa separar previsão de realização;
* pequenas mudanças em parcelas podem afetar saldo e relatórios.

---

## Resumo

O módulo de parcelas e recebíveis controla a entrada real de dinheiro no sistema.

Ele conecta:

```text
vendas
condições de pagamento
vencimentos
recebimentos
saldo
relatórios
dashboard
```

Sua principal função é garantir que o sistema mostre o que foi vendido, o que foi recebido e o que ainda falta receber.
