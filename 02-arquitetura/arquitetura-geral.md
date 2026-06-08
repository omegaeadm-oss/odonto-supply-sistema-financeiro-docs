# Arquitetura geral do sistema

Esta página documenta a arquitetura geral do sistema financeiro e comercial interno da Odonto Supply.

O objetivo é explicar, de forma organizada, como as principais partes do sistema se conectam: frontend, banco de dados, autenticação, deploy, geração de PDFs, funções serverless e integração com Google Drive.

---

## Visão geral

O sistema foi construído como uma aplicação web interna.

A arquitetura segue o modelo:

```text
Usuário
→ Aplicação web em React
→ Supabase para dados
→ Vercel para deploy
→ Vercel Functions para operações backend específicas
→ Google Drive API para upload de documentos
```

A ideia foi manter uma estrutura relativamente simples, mas capaz de resolver problemas reais da operação:

* registrar vendas;
* criar orçamentos;
* controlar parcelas;
* acompanhar estoque;
* calcular margens;
* gerar PDFs;
* enviar documentos para o Google Drive;
* visualizar indicadores financeiros.

---

## Stack principal

A stack utilizada foi:

```text
React
TypeScript
Vite
Supabase
PostgreSQL
Vercel
Vercel Functions
Google Drive API
jsPDF
html2canvas
CSS customizado
```

Cada tecnologia foi escolhida por uma função específica dentro do sistema.

---

## Frontend

O frontend foi desenvolvido com:

```text
React + TypeScript + Vite
```

O React é responsável pela interface do sistema.

O TypeScript ajuda a reduzir erros durante o desenvolvimento, principalmente em estruturas de dados como vendas, orçamentos, produtos, parcelas e relatórios.

O Vite foi usado como ambiente de desenvolvimento e build.

### Responsabilidades do frontend

O frontend concentra:

* telas do sistema;
* formulários;
* tabelas;
* botões de ação;
* cálculos visuais;
* geração de PDFs comerciais;
* estados de carregamento;
* feedback de sucesso ou erro;
* comunicação com Supabase;
* comunicação com a API de upload para Drive.

---

## Interface

A interface foi feita com CSS customizado.

A escolha por CSS próprio permitiu controlar:

* aparência visual;
* tema escuro;
* cards;
* tabelas;
* botões;
* responsividade;
* layout dos módulos;
* aparência dos PDFs;
* consistência visual da marca.

O sistema foi pensado para uso operacional, então a interface precisava ser clara, rápida e objetiva.

---

## Backend principal

O backend principal de dados usa:

```text
Supabase + PostgreSQL
```

O Supabase foi usado para:

* autenticação;
* banco de dados;
* leitura e escrita de dados;
* políticas de acesso;
* funções RPC em PostgreSQL;
* persistência das informações do sistema.

---

## Banco de dados

O banco de dados guarda as informações principais da operação.

Exemplos de dados controlados:

* vendas;
* orçamentos;
* produtos;
* parcelas;
* receitas;
* despesas;
* custos fixos;
* pagamentos;
* configurações;
* histórico de movimentações;
* itens de venda;
* itens de orçamento.

O sistema depende de consistência no banco, especialmente para áreas sensíveis como:

* saldo;
* parcelas;
* estoque;
* custos;
* margens;
* relatórios.

Por isso, mudanças envolvendo banco de dados precisam ser feitas com diagnóstico cuidadoso.

---

## Supabase Auth

O sistema usa autenticação para controlar acesso à aplicação.

Como o sistema é interno, o uso inicial foi pensado para poucos usuários autorizados.

Mesmo assim, a autenticação é importante para evitar exposição indevida dos dados operacionais.

---

## Row Level Security

O Supabase permite usar Row Level Security, também chamado de RLS.

O objetivo do RLS é controlar quem pode ler, criar, alterar ou excluir dados.

Em um sistema financeiro, isso é importante porque existem dados sensíveis, como:

* valores de venda;
* custos;
* margens;
* dados de clientes;
* parcelas;
* despesas;
* relatórios.

Mesmo em sistemas internos, é recomendado não deixar tabelas abertas sem controle.

---

## RPC e regras críticas

Algumas regras do sistema não devem depender apenas do frontend.

Um exemplo importante é a numeração comercial única entre vendas e orçamentos.

Para evitar conflito de números e duplicidade, foi criada uma função no banco para reservar números comerciais de forma segura.

Essa lógica precisa estar no banco porque o frontend sozinho não garante segurança em situações como:

```text
duas pessoas criando documentos ao mesmo tempo
```

Com uma RPC no banco, a reserva do número fica mais confiável.

---

## Deploy

O sistema é publicado na Vercel.

A Vercel foi usada para:

* hospedagem do frontend;
* deploy contínuo;
* ambiente de produção;
* variáveis de ambiente;
* Vercel Functions;
* testes em ambiente local com `vercel dev`.

---

## Diferença entre Vite e Vercel local

Durante o desenvolvimento, foi importante entender a diferença entre dois comandos.

### Vite puro

```bash
npm run dev
```

Geralmente abre o sistema em:

```text
localhost:5173
```

Esse ambiente é útil para desenvolver o frontend, mas não é ideal para testar Vercel Functions.

### Vercel local

```bash
vercel dev --listen 3000
```

Geralmente abre em:

```text
localhost:3000
```

Esse ambiente simula melhor a produção da Vercel e permite testar rotas como:

```text
/api/upload-drive
```

Por isso, para testar a integração com Google Drive, foi necessário usar `vercel dev`.

---

## Vercel Functions

Algumas operações não devem acontecer diretamente no frontend.

Exemplo:

```text
upload para Google Drive usando credenciais sensíveis
```

Por isso, foi criada uma Vercel Function:

```text
/api/upload-drive
```

Essa função funciona como uma camada backend intermediária.

Ela recebe o PDF enviado pelo frontend e executa a comunicação com o Google Drive API.

---

## Integração com Google Drive

O sistema possui integração com Google Drive para salvar PDFs comerciais.

Fluxo resumido:

```text
Frontend gera PDF
→ PDF vira Blob
→ frontend envia FormData para /api/upload-drive
→ Vercel Function autentica no Google
→ Google Drive API salva o arquivo
→ arquivo aparece na pasta correta
```

A integração organiza os arquivos em:

```text
ODONTO SUPPLY / Venda-YYYY / Mês-YY
ODONTO SUPPLY / Orçamento-YYYY / Mês-YY
```

Essa organização evita arquivos soltos no computador e melhora a operação entre mais de uma pessoa.

---

## PDFs comerciais

Os PDFs comerciais são gerados no frontend.

Bibliotecas usadas:

```text
jsPDF
html2canvas
```

O fluxo geral é:

```text
HTML visual do documento
→ captura com html2canvas
→ geração do PDF com jsPDF
```

Esse método permite manter visual profissional, mas precisa de cuidado com o tamanho final do arquivo.

Durante o desenvolvimento, foi necessário otimizar os PDFs para evitar arquivos pesados demais.

---

## Otimização dos PDFs

A otimização dos PDFs foi importante para viabilizar o upload para Google Drive.

Problema identificado:

```text
PDF de duas páginas com cerca de 12 MB
```

Solução aplicada:

```text
reduzir escala
usar JPEG com qualidade controlada
ativar compressão
```

Resultado:

```text
PDF muito mais leve, mantendo aparência aceitável
```

---

## Fluxo das vendas

O fluxo básico de venda é:

```text
Cadastrar venda
→ preencher dados comerciais
→ vincular produtos
→ gerar parcelas
→ salvar venda
→ atualizar estoque
→ gerar PDF
→ enviar PDF ao Drive, se desejado
```

A venda afeta áreas importantes:

* faturamento;
* parcelas;
* estoque;
* custos;
* margem;
* relatórios;
* saldo, quando houver recebimentos.

---

## Fluxo dos orçamentos

O fluxo básico de orçamento é:

```text
Cadastrar orçamento
→ preencher dados do cliente
→ adicionar produtos
→ definir condições
→ gerar PDF
→ enviar PDF ao Drive, se desejado
→ converter em venda, se aprovado
```

Regra importante:

```text
orçamento não baixa estoque
```

O orçamento é uma proposta. Ele só deve afetar estoque e financeiro quando for convertido em venda.

---

## Separação entre orçamento e venda

Essa separação é importante para evitar distorções.

Um orçamento pode ser criado, enviado e nunca aprovado.

Se o orçamento já baixasse estoque ou entrasse no financeiro, o sistema ficaria incorreto.

Por isso:

```text
orçamento = proposta
venda = operação confirmada
```

---

## Saldo e financeiro

O saldo do sistema precisa representar movimentação real.

Uma regra importante do projeto foi não confundir:

```text
valor previsto
```

com:

```text
valor realmente recebido ou pago
```

Por exemplo:

* parcela pendente não deve ser tratada como dinheiro recebido;
* custo fixo cadastrado não deve afetar saldo até ser pago;
* venda parcelada deve gerar recebíveis;
* pagamento recebido deve entrar no saldo.

Essa lógica evita que o dashboard mostre uma visão artificial do financeiro.

---

## Estoque

O estoque é afetado por vendas confirmadas.

Regra principal:

```text
orçamento não baixa estoque
venda salva baixa estoque
```

Essa regra protege a operação contra baixa indevida de produtos que ainda não foram vendidos.

---

## Relatórios

Os relatórios usam os dados do sistema para gerar visão gerencial.

Eles podem considerar:

* vendas;
* faturamento;
* custos;
* despesas;
* margem;
* resultado;
* desempenho por mês;
* histórico financeiro.

A qualidade dos relatórios depende diretamente da consistência dos lançamentos.

---

## Variáveis de ambiente

O sistema depende de variáveis de ambiente para funcionar com segurança.

Exemplos:

```text
VITE_SUPABASE_URL
VITE_SUPABASE_ANON_KEY
GOOGLE_CLIENT_ID
GOOGLE_CLIENT_SECRET
GOOGLE_REFRESH_TOKEN
GOOGLE_DRIVE_ROOT_FOLDER_ID
DRIVE_UPLOAD_TEST_SECRET
VITE_DRIVE_UPLOAD_TEST_SECRET
```

Variáveis sensíveis nunca devem ser publicadas no GitHub.

---

## Segurança

Alguns pontos de segurança importantes:

* não publicar arquivos `.env`;
* não expor refresh tokens;
* não publicar segredos do Google;
* não publicar dados reais de clientes;
* não publicar PDFs reais;
* não usar variáveis frontend como proteção definitiva;
* validar operações sensíveis no backend quando possível.

A integração atual com Drive usa um segredo temporário para proteger o endpoint.

Esse modelo é aceitável para MVP interno, mas a evolução recomendada é usar validação backend com autenticação real.

---

## Organização geral da arquitetura

Resumo da arquitetura:

```text
React/Vite
→ interface e lógica do usuário

Supabase/PostgreSQL
→ dados, autenticação e regras críticas

Vercel
→ deploy e hospedagem

Vercel Functions
→ operações backend específicas

Google Drive API
→ armazenamento organizado dos PDFs

jsPDF/html2canvas
→ geração de documentos comerciais
```

---

## Metodologia de alteração

Como o sistema lida com dados financeiros e comerciais, as mudanças precisam ser feitas com cuidado.

Fluxo recomendado:

```text
diagnóstico
→ patch mínimo
→ build
→ teste local
→ teste em produção
→ commit
→ documentação
```

Mudanças sensíveis devem ser diagnosticadas antes de qualquer alteração.

Áreas sensíveis:

* saldo;
* parcelas;
* estoque;
* Supabase;
* SQL;
* dashboard;
* relatórios;
* PDFs;
* Google Drive;
* vendas;
* orçamentos.

---

## Aprendizados de arquitetura

Principais aprendizados:

* separar frontend de operações sensíveis;
* usar backend/serverless para integrações externas;
* testar Vercel Functions com `vercel dev`;
* não confiar em caminho local do Drive para upload via API;
* controlar bem variáveis de ambiente;
* otimizar arquivos antes de enviar;
* manter orçamento e venda como entidades diferentes;
* proteger regras críticas no banco;
* documentar decisões para facilitar manutenção futura.

---

## Próximas melhorias possíveis

Melhorias futuras na arquitetura:

* autenticação mais robusta para upload de Drive;
* registro de histórico de uploads;
* link direto para abrir arquivo no Drive;
* permissões por usuário;
* logs de auditoria;
* dashboard com métricas avançadas;
* melhoria da estrutura modular do frontend;
* extração de funções grandes para arquivos separados;
* documentação técnica do schema Supabase;
* testes automatizados para regras críticas.
