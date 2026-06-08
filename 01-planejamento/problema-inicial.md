# Problema inicial

Esta página documenta o problema inicial que motivou a criação do sistema financeiro e comercial interno da Odonto Supply.

O objetivo é explicar o contexto operacional que existia antes do sistema, quais dificuldades apareciam no dia a dia e por que fez sentido construir uma ferramenta própria em vez de depender apenas de planilhas, pastas manuais e controles separados.

---

## Contexto da operação

A Odonto Supply é uma operação comercial voltada para venda de produtos odontológicos.

Como em muitos negócios pequenos ou em crescimento, parte da organização inicial dependia de ferramentas simples:

* planilhas;
* anotações manuais;
* arquivos PDF salvos no computador;
* pastas no Google Drive;
* conversas de WhatsApp;
* conferências manuais de pagamento;
* controle separado de estoque;
* registros financeiros descentralizados.

Esse tipo de fluxo funciona no começo, mas começa a gerar atrito conforme a quantidade de vendas, orçamentos e documentos aumenta.

---

## O problema central

O problema principal era a falta de centralização.

As informações importantes da operação ficavam espalhadas em vários lugares.

Exemplo:

```text
venda registrada em um lugar
PDF salvo em outro
comprovante em conversa
parcela controlada manualmente
produto conferido em estoque separado
documento final enviado para Drive manualmente
```

Esse modelo aumentava o risco de esquecimento, erro e retrabalho.

---

## Vendas registradas manualmente

Um dos primeiros problemas era o registro manual das vendas.

Cada venda precisava de informações como:

* cliente;
* produto;
* valor;
* custo;
* taxa;
* frete;
* forma de pagamento;
* data;
* parcelas;
* lucro;
* margem;
* observações.

Quando essas informações ficam em planilhas ou mensagens soltas, fica difícil manter consistência.

Também fica mais trabalhoso responder perguntas simples, como:

```text
Quanto vendemos este mês?
Qual venda ainda tem parcela pendente?
Qual foi o lucro real desta venda?
Quais produtos foram vendidos?
Qual cliente ainda precisa pagar?
```

---

## Orçamentos sem fluxo integrado

Outro problema era a criação e acompanhamento de orçamentos.

Um orçamento pode virar venda ou não.

Por isso, ele não deveria afetar estoque e financeiro imediatamente.

Mas, ao mesmo tempo, ele precisa ser organizado, numerado e facilmente convertido em venda se for aprovado.

Sem um sistema, o fluxo ficava mais manual:

```text
montar orçamento
gerar PDF
enviar ao cliente
salvar arquivo
acompanhar retorno
se aprovado, registrar venda manualmente
```

Isso gerava retrabalho e risco de divergência entre o orçamento enviado e a venda registrada depois.

---

## PDFs espalhados

Os PDFs comerciais eram um ponto crítico.

O processo manual normalmente seguia esta lógica:

```text
gerar PDF
baixar no computador
procurar o arquivo na pasta Downloads ou Desktop
renomear se necessário
abrir Google Drive
entrar na pasta correta
enviar manualmente
```

Esse fluxo parece simples, mas no dia a dia gera vários problemas:

* arquivo perdido no computador;
* envio para pasta errada;
* duplicidade;
* demora;
* desorganização;
* dificuldade quando mais de uma pessoa gera documentos;
* risco de usar versão antiga de um arquivo.

A integração com Google Drive nasceu justamente para reduzir esse problema.

---

## Controle de parcelas

Vendas parceladas precisam de controle específico.

Não basta registrar o valor total da venda.

É necessário acompanhar:

* entrada;
* número de parcelas;
* vencimentos;
* parcelas pendentes;
* parcelas recebidas;
* parcelas em atraso;
* saldo a receber;
* forma de pagamento;
* histórico de recebimento.

Sem sistema, esse controle pode ficar facilmente confuso.

Um erro comum é confundir faturamento com dinheiro realmente recebido.

Por isso, o sistema precisava separar:

```text
valor vendido
```

de:

```text
valor recebido
```

---

## Saldo real vs valores previstos

Uma necessidade importante foi controlar o saldo de forma realista.

O sistema não deveria tratar uma parcela pendente como dinheiro já recebido.

Também não deveria tratar um custo fixo cadastrado como dinheiro já pago.

A regra desejada era:

```text
saldo deve representar movimentação real de caixa
```

Isso significa que:

* receita entra quando é recebida;
* despesa sai quando é paga;
* parcelas pendentes são valores a receber;
* custos previstos não são automaticamente saldo realizado.

Essa separação evita uma visão financeira artificial.

---

## Controle de estoque

Outro problema operacional era o estoque.

A operação precisava saber:

* quais produtos existem;
* quantas unidades estão disponíveis;
* quais produtos foram vendidos;
* quando o estoque deve baixar;
* quando não deve baixar.

A regra principal definida foi:

```text
orçamento não baixa estoque
venda confirmada baixa estoque
```

Essa regra é importante porque um orçamento é apenas uma proposta.

Se o orçamento baixasse estoque antes de ser aprovado, o sistema poderia indicar falta de produto mesmo sem venda real.

---

## Dificuldade em analisar desempenho

Sem um sistema centralizado, também ficava difícil analisar desempenho.

Perguntas importantes exigiam muito esforço:

```text
Qual foi o faturamento do mês?
Qual foi o lucro?
Qual foi a margem?
Quais vendas tiveram custo pendente?
Quais parcelas ainda faltam receber?
Quanto saiu em despesas?
Qual foi o resultado anual?
```

A ideia do sistema foi transformar essas respostas em algo visível dentro de uma interface.

---

## Risco de erro humano

O fluxo manual dependia de muita atenção.

Problemas possíveis:

* esquecer de lançar venda;
* esquecer parcela;
* salvar PDF em pasta errada;
* duplicar documento;
* errar nome de arquivo;
* confundir orçamento com venda;
* esquecer custo;
* não baixar estoque;
* baixar estoque antes da hora;
* não registrar pagamento;
* perder histórico.

O sistema foi criado para reduzir esses riscos.

---

## Necessidade de uma ferramenta própria

Poderia ser usado um sistema pronto, mas existiam motivos para criar uma solução própria:

* necessidade de adaptar ao fluxo real da empresa;
* controle específico de vendas e orçamentos;
* integração com Google Drive;
* geração de PDFs no padrão visual desejado;
* controle de parcelas de forma simples;
* possibilidade de evoluir por etapas;
* aprendizado técnico;
* flexibilidade para criar regras próprias.

A ferramenta própria permitiu construir exatamente o que a operação precisava, sem excesso de funções desnecessárias.

---

## Objetivo inicial do sistema

O objetivo inicial não era criar um ERP completo.

O objetivo era criar uma ferramenta interna prática para:

* registrar vendas;
* gerar orçamentos;
* controlar parcelas;
* acompanhar estoque;
* gerar PDFs;
* organizar documentos;
* visualizar indicadores;
* reduzir retrabalho.

Com o tempo, o sistema passou a evoluir para uma base mais robusta.

---

## Evolução gradual

O sistema não nasceu completo.

Ele foi evoluindo por etapas:

```text
primeiros controles
→ vendas
→ parcelas
→ estoque
→ orçamentos
→ PDFs
→ relatórios
→ desempenho anual
→ Google Drive
→ documentação
```

Essa evolução incremental ajudou a manter o sistema utilizável durante o desenvolvimento.

---

## Critério de sucesso

O sistema começou a fazer sentido quando passou a reduzir tarefas repetitivas.

Exemplos de sucesso:

* venda registrada em poucos passos;
* PDF gerado com padrão visual;
* orçamento convertido em venda;
* parcelas geradas automaticamente;
* estoque baixado apenas na venda;
* documento enviado ao Drive com um clique;
* indicadores aparecendo no dashboard;
* menos arquivos perdidos no computador.

O objetivo não era só tecnologia, mas eficiência operacional.

---

## Benefícios esperados

Com o sistema, os principais benefícios esperados eram:

* mais organização;
* menos retrabalho;
* menos erro manual;
* melhor controle financeiro;
* melhor controle comercial;
* melhor gestão de documentos;
* visão mais clara de desempenho;
* operação mais profissional;
* base para crescimento futuro.

---

## Aprendizado principal

O principal aprendizado desta etapa foi que muitos problemas operacionais não surgem por falta de esforço, mas por falta de sistema.

Quando a operação depende de memória, planilha e arquivos soltos, o risco de erro aumenta.

Um sistema interno bem desenhado pode transformar processos repetitivos em fluxos padronizados.

---

## Resumo

O problema inicial pode ser resumido assim:

```text
A operação tinha informações importantes espalhadas em planilhas, arquivos, conversas e pastas manuais.
```

A solução foi começar a construir uma aplicação interna capaz de centralizar vendas, orçamentos, financeiro, estoque, PDFs e documentos.

Essa centralização foi a base para todas as funções criadas depois.
