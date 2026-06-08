# Sistema Financeiro e Comercial Interno — Odonto Supply

Documentação completa do processo de criação de um sistema financeiro e comercial interno para gestão operacional da Odonto Supply.

Este repositório tem como objetivo registrar, de forma organizada e replicável, o passo a passo de desenvolvimento de um sistema web interno voltado para controle de vendas, orçamentos, parcelas, estoque, despesas, relatórios, geração de PDFs comerciais e integração com Google Drive.

A proposta é servir como um guia técnico e operacional para quem deseja criar um sistema parecido para uma pequena empresa, operação comercial, distribuidora, e-commerce, loja especializada ou negócio interno que precise sair de controles manuais em planilhas e arquivos soltos.

---

## Visão geral do projeto

O sistema foi criado para centralizar processos que antes ficavam espalhados entre planilhas, documentos manuais, arquivos PDF baixados no computador, pastas do Google Drive e controles financeiros separados.

A ideia principal foi construir uma aplicação simples, visualmente agradável e funcional para uso interno, sem depender inicialmente de sistemas empresariais complexos ou caros.

O foco do sistema é resolver problemas práticos do dia a dia:

- registrar vendas;
- gerar parcelas automaticamente;
- controlar recebíveis;
- cadastrar orçamentos;
- converter orçamentos em vendas;
- controlar estoque;
- registrar custos, despesas e taxas;
- gerar PDFs comerciais;
- enviar PDFs para o Google Drive;
- acompanhar o desempenho financeiro;
- organizar documentos por mês e ano;
- reduzir retrabalho manual.

---

## Problema inicial

Antes do sistema, parte da rotina comercial e financeira dependia de controles descentralizados.

Alguns problemas comuns:

- vendas registradas manualmente;
- orçamentos sem numeração padronizada;
- PDFs espalhados no computador;
- arquivos enviados para o cliente sem organização automática;
- dificuldade de acompanhar parcelas pendentes;
- dificuldade de saber margem real por venda;
- controle de estoque separado da operação comercial;
- retrabalho para salvar documentos no Google Drive;
- risco de duplicar ou perder arquivos;
- dificuldade para acompanhar indicadores de forma rápida.

O sistema nasceu para reduzir esse atrito operacional.

---

## Objetivo

Criar um sistema interno capaz de organizar a operação comercial e financeira da Odonto Supply em uma única interface.

Objetivos principais:

- substituir controles manuais por fluxo digital;
- manter vendas, orçamentos e parcelas organizados;
- padronizar nomes e numeração de documentos;
- gerar PDFs comerciais com aparência profissional;
- enviar documentos para o Google Drive com um clique;
- facilitar o trabalho de mais de uma pessoa na operação;
- manter uma base escalável para novos módulos futuros.

---

## Stack utilizada

O sistema foi desenvolvido usando uma stack moderna, acessível e adequada para aplicações internas.

### Frontend

- React
- TypeScript
- Vite
- CSS customizado

### Backend e banco de dados

- Supabase
- PostgreSQL
- Row Level Security
- RPC para numeração comercial segura

### Deploy

- Vercel
- Vercel Functions

### PDFs

- jsPDF
- html2canvas

### Integrações

- Google Drive API
- OAuth com refresh token
- Upload via Vercel Function

### Versionamento e documentação

- Git
- GitHub
- Markdown

---

## Principais módulos do sistema

### Dashboard

Área principal para visão geral da operação.

Inclui indicadores financeiros, visão de vendas, custos, saldo, desempenho e atalhos operacionais.

### Lançamentos

Área central do sistema, usada para registrar e acompanhar movimentações comerciais e financeiras.

Inclui:

- vendas cadastradas;
- vendas parceladas;
- parcelas;
- custos pendentes;
- despesas;
- receitas;
- orçamentos;
- conversão de orçamento em venda.

### Vendas

Permite cadastrar vendas e controlar dados como:

- nome da venda;
- cliente;
- data;
- valor total;
- custo;
- taxas;
- frete;
- entrada;
- número de parcelas;
- forma de pagamento;
- observações;
- produtos vinculados;
- parcelas geradas.

### Orçamentos

Permite criar propostas comerciais sem afetar estoque ou financeiro até a conversão em venda.

Inclui:

- número do orçamento;
- nome da venda/orçamento;
- cliente;
- produtos;
- valores;
- descontos;
- condições de pagamento;
- validade;
- observações;
- geração de PDF;
- conversão para venda.

### Parcelas

Controle das parcelas geradas por vendas.

Organização por status:

- parcelas em atraso;
- parcelas pendentes;
- parcelas recebidas;
- parcelas canceladas, quando aplicável.

### Estoque

Controle dos produtos cadastrados e movimentações.

Regra principal:

- orçamento não baixa estoque;
- venda salva baixa estoque;
- movimentações devem ser registradas com histórico.

### Produtos

Base de produtos usados nas vendas e orçamentos.

Permite manter informações comerciais e operacionais dos itens vendidos.

### Relatórios

Área para análise de desempenho.

Pode incluir:

- relatórios mensais;
- desempenho anual;
- vendas por período;
- custos;
- margens;
- indicadores operacionais.

### Configurações

Área para informações técnicas e ajustes internos do sistema.

Inclui:

- tema da interface;
- dados técnicos do sistema;
- informações de arquitetura;
- status operacional.

---

## Numeração comercial única

Uma das regras mais importantes implementadas foi a numeração comercial unificada entre vendas e orçamentos.

A lógica é:

- se o próximo número for 107, tanto uma venda quanto um orçamento podem consumir esse número;
- se um orçamento for criado como 107 e depois convertido em venda, a venda mantém a referência 107;
- se uma venda for criada sem orçamento, ela consome o próximo número disponível;
- vendas e orçamentos não devem gerar números duplicados;
- a sequência precisa funcionar mesmo se duas pessoas estiverem usando o sistema ao mesmo tempo.

Para isso, foi criada uma lógica segura via banco de dados, com uma RPC responsável por reservar números comerciais de forma atômica.

A reserva de número pode gerar lacunas caso algo falhe depois da reserva, mas isso é aceitável porque evita duplicidade.

---

## Geração de PDFs comerciais

O sistema gera PDFs comerciais para vendas e orçamentos.

Os PDFs foram pensados para ter uma aparência profissional e consistente, incluindo:

- cabeçalho visual com identidade da Odonto Supply;
- dados do cliente;
- produtos;
- valores;
- condições de pagamento;
- observações;
- resumo financeiro, quando aplicável;
- nome de arquivo padronizado.

### Nome dos arquivos

A regra adotada foi:

- venda: usa o nome preenchido/salvo no campo da venda;
- orçamento: usa o formato `Orçamento - Número`.

Exemplos:

```text
Venda - 108.pdf
Orçamento - 107.pdf
