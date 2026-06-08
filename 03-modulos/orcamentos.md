# Módulo de orçamentos

Esta página documenta o módulo de orçamentos do sistema financeiro e comercial interno da Odonto Supply.

O módulo de orçamentos foi criado para registrar propostas comerciais de forma organizada, gerar PDFs profissionais, acompanhar negociações e permitir conversão posterior em venda confirmada.

---

## Objetivo do módulo

O objetivo do módulo de orçamentos é permitir a criação de propostas comerciais antes da confirmação da venda.

Um orçamento pode ser enviado ao cliente, analisado, ajustado e posteriormente aprovado ou não.

Por isso, ele precisa existir de forma separada da venda.

```text
Orçamento = proposta
Venda = operação confirmada
```

Essa separação é uma das regras mais importantes do sistema.

---

## Por que separar orçamento de venda

Nem todo orçamento vira venda.

Se todo orçamento fosse tratado como venda, o sistema poderia gerar distorções em áreas como:

* estoque;
* faturamento;
* parcelas;
* saldo;
* relatórios;
* margem;
* desempenho mensal.

Por isso, o orçamento deve registrar a intenção comercial, mas não deve produzir efeitos financeiros ou operacionais de venda confirmada.

---

## Regra principal

A regra principal do módulo é:

```text
orçamento não baixa estoque
orçamento não entra como venda realizada
orçamento não deve aumentar saldo
orçamento pode ser convertido em venda
```

Essa regra protege a consistência do sistema.

---

## Campos principais do orçamento

Um orçamento pode conter informações como:

* nome do orçamento;
* cliente;
* data do orçamento;
* validade da proposta;
* produtos;
* quantidades;
* valores;
* condições de pagamento;
* frete;
* observações;
* prazo de entrega;
* responsável pelo atendimento;
* status da negociação.

Esses dados permitem gerar uma proposta comercial clara e profissional.

---

## Nome do orçamento

O nome do orçamento segue o padrão comercial do sistema.

Exemplo:

```text
Orçamento - 107
```

Esse nome também pode ser usado como base para o nome do arquivo PDF.

Exemplo:

```text
Orçamento - 107.pdf
```

A padronização facilita localização, histórico e organização no Google Drive.

---

## Numeração comercial

O orçamento participa da mesma sequência comercial das vendas.

Exemplo:

```text
Orçamento - 107
Venda - 108
Orçamento - 109
```

Isso evita que dois documentos comerciais diferentes tenham o mesmo número.

A numeração única melhora a organização interna e reduz confusão na comunicação com clientes.

---

## Criação de novo orçamento

Fluxo básico:

```text
usuário cria novo orçamento
→ sistema reserva próximo número comercial
→ usuário preenche cliente, produtos e condições
→ orçamento é salvo
→ PDF pode ser gerado
→ PDF pode ser enviado para o Google Drive
```

Ao criar um orçamento novo, o sistema consome um número da sequência comercial.

---

## Edição de orçamento

Editar um orçamento existente não deve consumir novo número.

Regra:

```text
criação consome número
edição preserva número existente
```

Isso permite corrigir informações sem alterar a identidade comercial do documento.

Exemplos de edição:

* alterar observação;
* ajustar prazo;
* corrigir dados do cliente;
* alterar condição de pagamento;
* atualizar produtos;
* revisar valores.

---

## Status do orçamento

O orçamento pode ter diferentes situações no fluxo comercial.

Exemplos conceituais:

* em aberto;
* enviado;
* aprovado;
* recusado;
* vencido;
* convertido em venda.

Esses status ajudam a acompanhar negociações e entender quais propostas ainda exigem retorno.

---

## Orçamento em aberto

Um orçamento em aberto representa uma proposta ainda em negociação.

Ele ainda não deve afetar estoque nem financeiro realizado.

Pode exigir ações como:

* follow-up com cliente;
* revisão de valores;
* ajuste de prazo;
* envio de PDF;
* negociação de pagamento.

---

## Orçamento aprovado

Quando o cliente aprova o orçamento, ele pode ser convertido em venda.

A aprovação é o ponto em que a proposta deixa de ser apenas uma intenção comercial e passa a caminhar para operação confirmada.

Mesmo assim, a baixa de estoque e os efeitos financeiros devem ocorrer no fluxo de venda, não simplesmente na existência do orçamento.

---

## Conversão em venda

A conversão de orçamento em venda é um fluxo sensível.

Regra principal:

```text
orçamento convertido não deve consumir novo número comercial
```

Exemplo:

```text
Orçamento - 107
→ Venda - 107
```

Essa regra mantém o vínculo entre a proposta enviada e a venda final.

---

## Dados reaproveitados na conversão

Ao converter um orçamento em venda, o sistema pode reaproveitar dados como:

* cliente;
* produtos;
* valores;
* condições de pagamento;
* observações;
* número comercial;
* data original da proposta, quando relevante;
* referência do orçamento.

Isso reduz retrabalho e evita divergência entre proposta e venda.

---

## Estoque no orçamento

O orçamento não deve baixar estoque.

Regra:

```text
orçamento não baixa estoque
venda confirmada baixa estoque
```

Essa decisão evita que produtos fiquem indisponíveis no sistema apenas porque foram incluídos em uma proposta ainda não aprovada.

---

## Financeiro no orçamento

O orçamento também não deve ser tratado como receita realizada.

Ele pode representar uma oportunidade comercial, mas não um recebimento.

Regra conceitual:

```text
orçamento não aumenta saldo
orçamento não cria recebimento real
orçamento não deve entrar como venda concluída
```

O financeiro deve ser afetado apenas quando houver venda, recebimento ou pagamento real.

---

## Parcelas no orçamento

Um orçamento pode apresentar condições de pagamento parceladas, mas isso não significa que as parcelas já existam como recebíveis reais.

Exemplo:

```text
10x no cartão
entrada + parcelas
boleto em datas específicas
```

Essas condições fazem parte da proposta.

As parcelas efetivas devem ser criadas no fluxo de venda, após aprovação/conversão.

---

## Produtos no orçamento

Os produtos do orçamento são usados para montar a proposta comercial.

Eles podem aparecer no PDF com:

* descrição;
* quantidade;
* valor unitário;
* valor total;
* observações;
* condições específicas.

Mesmo que o produto apareça no orçamento, ele ainda não deve ser baixado do estoque.

---

## PDF do orçamento

O sistema permite gerar PDF do orçamento.

O PDF serve para enviar uma proposta profissional ao cliente.

Ele pode conter:

* identificação da empresa;
* dados do cliente;
* produtos;
* valores;
* condições comerciais;
* prazo;
* observações;
* validade da proposta.

Exemplo de nome:

```text
Orçamento - 107.pdf
```

---

## Upload do orçamento para Google Drive

O orçamento possui botão manual de upload para Google Drive.

Fluxo resumido:

```text
sistema gera o PDF do orçamento
→ PDF é enviado para /api/upload-drive
→ API salva o arquivo no Google Drive
→ documento fica organizado por ano e mês
```

Destino padrão:

```text
ODONTO SUPPLY / Orçamento-YYYY / Mês-YY
```

Exemplo:

```text
ODONTO SUPPLY / Orçamento-2026 / Junho-26
```

---

## Decisão por upload manual

Assim como nas vendas, o upload do orçamento para Drive é manual.

Motivos:

* permite revisar a proposta antes de salvar;
* evita upload de orçamento incompleto;
* dá controle ao usuário;
* reduz duplicidade;
* evita salvar versões erradas;
* facilita diagnóstico em caso de erro.

O usuário decide quando o orçamento está pronto para ser armazenado.

---

## Atualização de arquivo existente no Drive

Se um PDF com o mesmo nome já existir na pasta mensal do Google Drive, a integração pode atualizar o arquivo em vez de criar uma cópia duplicada.

Isso é útil quando o orçamento é revisado e reenviado.

Exemplo:

```text
Orçamento - 107.pdf já existe
→ sistema atualiza o arquivo
```

Essa regra ajuda a reduzir duplicidade.

---

## Relação com vendas

O módulo de orçamentos está diretamente ligado ao módulo de vendas.

Relação principal:

```text
orçamento pode virar venda
venda pode nascer de orçamento
venda confirma operação
orçamento registra proposta
```

Essa relação precisa ser bem controlada para evitar:

* baixa duplicada de estoque;
* consumo indevido de número;
* criação duplicada de venda;
* divergência entre orçamento e venda;
* conflito no histórico comercial.

---

## Relação com relatórios

Orçamentos podem ser úteis para análise comercial, mas devem ser tratados separadamente das vendas.

Possíveis análises futuras:

* quantidade de orçamentos emitidos;
* taxa de conversão;
* valor total orçado;
* valor convertido em vendas;
* propostas vencidas;
* propostas aprovadas;
* desempenho por período.

Essas métricas ajudam na gestão comercial sem confundir proposta com faturamento.

---

## Cuidados ao alterar o módulo

O módulo de orçamentos precisa de cuidado principalmente por sua ligação com vendas.

Antes de alterar, é recomendado verificar:

* se a mudança afeta numeração;
* se afeta conversão em venda;
* se afeta estoque;
* se afeta parcelas;
* se afeta PDF;
* se afeta Google Drive;
* se afeta relatórios;
* se exige SQL;
* se altera criação ou edição de orçamento.

---

## O que evitar

Evitar que orçamento gere efeitos de venda antes da aprovação.

Exemplos de problemas:

```text
orçamento baixando estoque
orçamento entrando como receita
orçamento criando saldo recebido
orçamento gerando parcelas reais
orçamento consumindo novo número na conversão
```

Esses comportamentos poderiam distorcer a operação.

---

## Testes importantes

Ao alterar o módulo de orçamentos, alguns testes são recomendados:

* criar novo orçamento;
* editar orçamento existente;
* gerar PDF;
* enviar PDF para Drive;
* revisar arquivo no Drive;
* converter orçamento em venda;
* confirmar que não consumiu novo número na conversão;
* confirmar que orçamento não baixou estoque;
* confirmar que venda baixou estoque;
* confirmar que parcelas só aparecem na venda;
* confirmar que relatório de vendas não conta orçamento como venda realizada.

---

## Aprendizados

Principais aprendizados do módulo de orçamentos:

* orçamento e venda precisam ser entidades diferentes;
* proposta não deve afetar estoque;
* proposta não deve afetar saldo real;
* conversão deve preservar número comercial;
* PDF de orçamento precisa ser fácil de gerar e armazenar;
* Drive ajuda a manter histórico organizado;
* orçamento pode ser usado no futuro para medir taxa de conversão comercial.

---

## Resumo

O módulo de orçamentos permite registrar propostas comerciais de forma organizada, sem confundir proposta com venda confirmada.

Ele conecta:

```text
cliente
produtos
condições comerciais
PDF
Google Drive
conversão em venda
histórico comercial
```

Sua principal função é apoiar o processo comercial antes da confirmação da venda.
