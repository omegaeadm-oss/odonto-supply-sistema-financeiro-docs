# Numeração comercial única

Esta página documenta a regra de numeração comercial única implementada no sistema financeiro e comercial interno da Odonto Supply.

Essa foi uma das regras mais importantes do sistema, porque vendas e orçamentos precisam compartilhar uma mesma sequência numérica, sem duplicidade e sem conflito entre usuários.

---

## Contexto

O sistema trabalha com dois tipos principais de documentos comerciais:

* orçamentos;
* vendas.

Na operação comercial, ambos precisam ter identificação clara.

Exemplos:

```text
Orçamento - 107
Venda - 108
```

A numeração precisa ser organizada porque esses documentos podem ser enviados a clientes, salvos em PDF, registrados no Google Drive e usados como referência interna.

---

## Problema inicial

A primeira ideia seria deixar cada formulário gerar seu próprio número.

Por exemplo:

```text
orçamentos com sequência própria
vendas com sequência própria
```

Mas isso poderia gerar confusão.

Exemplo de problema:

```text
Orçamento - 107
Venda - 107
```

Mesmo sendo documentos diferentes, ter o mesmo número poderia causar erro na comunicação interna, no envio de PDFs, no Drive e no histórico comercial.

Por isso, a decisão foi usar uma sequência única para os dois tipos.

---

## Regra principal

A regra definida foi:

```text
vendas e orçamentos compartilham a mesma sequência comercial
```

Ou seja, se o próximo número disponível for 107, o primeiro documento criado, seja venda ou orçamento, recebe esse número.

Exemplo:

```text
Orçamento criado → 107
Venda criada depois → 108
Outro orçamento criado depois → 109
```

A sequência é única e contínua.

---

## Por que essa regra é importante

Essa regra evita:

* números duplicados;
* confusão entre venda e orçamento;
* arquivos PDF com nomes conflitantes;
* documentos salvos com mesmo identificador;
* problemas na comunicação com clientes;
* inconsistência no histórico comercial.

Também facilita a organização no Google Drive e no controle interno.

---

## Orçamento convertido em venda

Uma regra importante é a conversão de orçamento em venda.

Quando um orçamento é aprovado e convertido, a venda deve manter referência ao orçamento original.

Exemplo:

```text
Orçamento - 107
→ convertido em venda
Venda - 107
```

Nesse caso, a conversão não deve consumir um novo número.

Isso preserva o vínculo comercial entre proposta e venda final.

---

## Venda direta

Quando uma venda é criada diretamente, sem origem em orçamento, ela consome o próximo número disponível.

Exemplo:

```text
Último número usado: 107
Nova venda direta: Venda - 108
```

A venda direta entra normalmente na sequência comercial.

---

## Novo orçamento

Quando um novo orçamento é criado, ele também consome o próximo número disponível.

Exemplo:

```text
Último número usado: 108
Novo orçamento: Orçamento - 109
```

O orçamento entra na mesma sequência das vendas.

---

## Edição de documentos

Editar uma venda ou orçamento existente não deve consumir novo número.

A regra é:

```text
criação nova consome número
edição preserva número existente
```

Isso evita que simples correções em dados do cliente, produtos, valores ou observações alterem a identificação comercial do documento.

---

## Exclusão de documentos

Se um documento numerado for criado e depois excluído, o número não deve necessariamente voltar para a sequência.

Exemplo:

```text
Venda - 108 criada
Venda - 108 excluída
Próximo documento: 109
```

Essa decisão evita complexidade e reduz risco de duplicidade.

Em muitos sistemas comerciais, lacunas na numeração são aceitáveis desde que não exista duplicidade.

---

## Lacunas na numeração

A sequência pode ter lacunas.

Exemplo:

```text
107
108
110
```

Isso pode acontecer se:

* um número for reservado;
* o documento falhar depois da reserva;
* uma venda/orçamento for excluído;
* uma operação for cancelada.

A prioridade da regra é:

```text
evitar duplicidade
```

e não necessariamente manter uma sequência perfeita sem buracos.

---

## Por que não gerar número apenas no frontend

Gerar o número apenas no frontend seria arriscado.

Imagine duas pessoas usando o sistema ao mesmo tempo:

```text
Usuário A abre formulário
Usuário B abre formulário
ambos veem próximo número 107
ambos salvam quase ao mesmo tempo
```

Resultado possível:

```text
dois documentos com número 107
```

Por isso, a reserva do número precisa acontecer no backend/banco de dados, de forma atômica.

---

## Solução adotada

A solução adotada foi criar uma função no banco de dados para reservar o próximo número comercial.

A lógica geral é:

```text
frontend solicita número
→ banco incrementa sequência de forma segura
→ banco registra número reservado
→ frontend usa número no documento
```

Essa lógica foi implementada por meio de uma RPC no Supabase/PostgreSQL.

---

## Estrutura conceitual

A estrutura usa duas ideias principais:

### Sequência comercial

Uma tabela mantém o último número usado.

Exemplo conceitual:

```text
commercial_sequence
last_number = 106
```

Quando um novo documento é criado, a sequência avança:

```text
last_number = 107
```

### Registro dos números

Outra tabela registra os números reservados.

Exemplo conceitual:

```text
commercial_numbers
number = 107
kind = quote
```

ou:

```text
commercial_numbers
number = 108
kind = revenue
```

---

## Tipos de documento

A função aceita tipos controlados.

Exemplos:

```text
quote
revenue
```

Onde:

```text
quote = orçamento
revenue = venda
```

Isso evita que tipos inválidos sejam gravados na sequência.

---

## Fluxo de criação de orçamento

Fluxo simplificado:

```text
Usuário cria orçamento
→ frontend chama RPC com tipo quote
→ banco reserva próximo número
→ sistema salva o orçamento com esse número
→ PDF usa esse número
→ Drive salva o arquivo com esse identificador
```

Exemplo:

```text
Orçamento - 107
```

---

## Fluxo de criação de venda direta

Fluxo simplificado:

```text
Usuário cria venda direta
→ frontend chama RPC com tipo revenue
→ banco reserva próximo número
→ sistema salva a venda com esse número
→ PDF usa esse número
→ Drive salva o arquivo com esse identificador
```

Exemplo:

```text
Venda - 108
```

---

## Fluxo de conversão de orçamento em venda

Fluxo simplificado:

```text
Usuário converte orçamento aprovado
→ sistema usa número do orçamento
→ não chama RPC novamente
→ venda mantém referência do orçamento
```

Exemplo:

```text
Orçamento - 107
→ Venda - 107
```

Essa regra evita que a mesma negociação tenha dois números diferentes.

---

## Testes realizados

A numeração foi testada com uma transação segura no banco, usando rollback.

A ideia do teste foi:

```text
iniciar transação
reservar número de orçamento
reservar número de venda
verificar sequência
verificar registros
rollback
confirmar que nada foi consumido
```

Durante o teste, a sequência avançava corretamente dentro da transação.

Depois do rollback, a sequência voltava ao estado anterior.

---

## Exemplo de critério de sucesso

Exemplo conceitual:

```text
Antes:
last_number = 106

Durante o teste:
quote retorna 107
revenue retorna 108
last_number aparece como 108

Após rollback:
last_number volta para 106
nenhum número permanece consumido
próximo número real continua sendo 107
```

Esse teste foi importante para confirmar a lógica sem consumir números reais em produção.

---

## Permissões da função

A função de reserva de número não deve ficar aberta para qualquer usuário anônimo.

A regra de permissão adotada foi restringir execução apenas para usuários autenticados.

Conceitualmente:

```text
revogar acesso público
revogar acesso anônimo
permitir acesso para authenticated
```

Isso reduz risco de uso indevido da sequência.

---

## Relação com PDFs

A numeração comercial aparece diretamente nos PDFs.

Exemplos:

```text
Venda - 108.pdf
Orçamento - 107.pdf
```

Por isso, a regra de numeração afeta também:

* nome do arquivo;
* título do documento;
* identificação no Drive;
* comunicação com cliente;
* histórico interno.

---

## Relação com Google Drive

O botão de upload para o Google Drive usa o nome final do PDF.

Se a numeração fosse duplicada, poderiam surgir conflitos como:

```text
Venda - 108.pdf
Orçamento - 108.pdf
```

A sequência única reduz esse risco e mantém a organização dos documentos comerciais.

Além disso, a regra de atualização no Drive considera arquivos com mesmo nome dentro da mesma pasta mensal.

---

## Cuidados importantes

A numeração comercial é uma regra sensível.

Mudanças nessa área devem ser feitas com cuidado, porque podem afetar:

* vendas;
* orçamentos;
* PDFs;
* Google Drive;
* histórico comercial;
* identificação de documentos;
* confiança da operação.

Antes de qualquer alteração, é recomendado diagnosticar:

* onde o número é gerado;
* onde ele é salvo;
* se a operação é criação ou edição;
* se existe conversão de orçamento;
* se a RPC está sendo chamada no momento correto;
* se há risco de duplicidade.

---

## Decisões tomadas

Resumo das principais decisões:

```text
vendas e orçamentos usam a mesma sequência
criação nova consome número
edição não consome número
conversão de orçamento não consome novo número
lacunas são aceitáveis
duplicidade não é aceitável
a reserva acontece no banco, não só no frontend
```

---

## Possíveis melhorias futuras

Melhorias possíveis:

* registrar entidade final vinculada ao número reservado;
* preencher `entity_id` após salvar venda ou orçamento;
* registrar `source_quote_id` em vendas convertidas;
* criar tela de auditoria da numeração;
* exibir histórico de números consumidos;
* permitir rastreio entre orçamento e venda;
* criar alertas para falhas após reserva;
* documentar a sequência em relatório administrativo.

---

## Aprendizados

Principais aprendizados dessa regra:

* regras críticas não devem depender apenas do frontend;
* sequência sem duplicidade é mais importante que sequência sem lacunas;
* edição e criação precisam ser tratadas de forma diferente;
* conversão de orçamento precisa preservar vínculo comercial;
* testes com rollback são úteis para validar lógica sem consumir dados reais;
* permissões da função são parte essencial da segurança.
