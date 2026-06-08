# Metodologia de trabalho com Codex

Esta página documenta a metodologia usada durante o desenvolvimento do sistema financeiro e comercial interno da Odonto Supply com apoio do Codex.

O objetivo dessa metodologia foi evoluir o sistema de forma segura, evitando alterações grandes demais, protegendo regras financeiras sensíveis e mantendo o sistema funcionando em produção.

---

## Contexto

O sistema da Odonto Supply foi desenvolvido de forma incremental.

Durante o processo, várias funções importantes foram criadas:

* vendas;
* orçamentos;
* parcelas;
* estoque;
* PDFs comerciais;
* dashboard;
* relatórios;
* integração com Google Drive;
* numeração comercial única;
* otimização de PDFs;
* ajustes visuais;
* deploy na Vercel.

Como o sistema lida com dados financeiros e comerciais, qualquer alteração mal feita poderia causar problemas reais na operação.

Por isso, foi necessário criar um método de trabalho cuidadoso.

---

## Princípio principal

O princípio mais importante foi:

```text
diagnosticar antes de alterar
```

Antes de qualquer patch, o Codex precisava entender:

* qual arquivo seria alterado;
* qual módulo seria afetado;
* qual regra de negócio estava envolvida;
* se havia risco financeiro;
* se exigia SQL;
* se exigia alteração no Supabase;
* se a mudança era visual ou funcional;
* como testar depois.

Essa etapa evitou muitas alterações arriscadas.

---

## Classificação da mudança

Antes de agir, a mudança precisava ser classificada.

Categorias usadas:

```text
Visual/layout
Cálculo financeiro
Banco/Supabase/SQL
Estoque
Parcelas/recebíveis
Orçamentos/vendas
PDF/documentos
Relatórios/desempenho anual
Dashboard/saldo/extrato
Documentação/contexto
Integração externa
Backend/API
```

Essa classificação ajudava a definir o nível de cuidado necessário.

---

## Risco da mudança

Cada alteração era classificada por risco:

```text
baixo
médio
alto
```

Exemplos:

### Baixo risco

* ajuste visual simples;
* texto de botão;
* espaçamento;
* documentação;
* pequenas melhorias de CSS sem lógica de negócio.

### Médio risco

* alteração em PDF;
* integração com API;
* mudança em fluxo de vendas;
* ajuste em parcelas;
* alteração em dashboard;
* integração com Google Drive.

### Alto risco

* SQL;
* alteração em saldo;
* mudança em estoque;
* mudança em cálculo financeiro;
* alteração em regras de venda;
* alteração estrutural no banco;
* mudança em relatórios financeiros.

---

## Regra para mudanças visuais

Mudanças visuais podiam ser feitas com mais liberdade, desde que não alterassem regras de negócio.

Exemplo:

```text
mudar aparência de botão
ajustar layout mobile
corrigir espaçamento
melhorar tema claro/escuro
```

Mesmo assim, o patch deveria ser pequeno.

Mudança visual não deveria alterar:

* saldo;
* estoque;
* parcelas;
* Supabase;
* SQL;
* custos;
* vendas;
* orçamentos;
* relatórios financeiros.

---

## Regra para mudanças financeiras

Mudanças financeiras exigiam diagnóstico antes de qualquer patch.

Áreas sensíveis:

* saldo;
* extrato;
* parcelas;
* custos fixos;
* despesas;
* receitas;
* margem;
* lucro;
* dashboard;
* relatórios;
* desempenho anual.

Antes de alterar, era necessário identificar:

* variável envolvida;
* cálculo atual;
* origem dos dados;
* impacto esperado;
* o que não deveria ser alterado;
* critério de aceite;
* teste esperado.

---

## Regra para SQL e Supabase

Qualquer mudança envolvendo banco de dados precisava de cuidado especial.

A regra era:

```text
nunca assumir nomes de tabelas, colunas ou policies sem diagnóstico
```

Antes de criar ou alterar SQL, era necessário confirmar:

* tabelas existentes;
* colunas;
* constraints;
* policies RLS;
* índices;
* uso no código;
* impacto no app.

Consultas recomendadas:

```text
information_schema.tables
information_schema.columns
pg_constraint
pg_policies
pg_indexes
```

Também era necessário procurar uso no código, por exemplo:

```text
from("nome_da_tabela")
from('nome_da_tabela')
```

---

## Regra para estoque

O estoque tinha regras próprias.

Regra principal:

```text
orçamento não baixa estoque
venda salva baixa estoque
```

Qualquer mudança em estoque precisava preservar essa lógica.

Também era importante manter:

* histórico de movimentação;
* baixa apenas em venda confirmada;
* orçamento como proposta;
* conversão de orçamento em venda sem baixa duplicada.

---

## Regra para PDFs

Os PDFs comerciais eram documentos importantes para a operação.

Mudanças em PDFs deveriam preservar:

* layout;
* conteúdo;
* nome do arquivo;
* dados do cliente;
* produtos;
* valores;
* totais;
* observações;
* paginação;
* qualidade visual.

Quando o PDF ficou pesado demais, a otimização foi feita com cuidado para reduzir tamanho sem destruir legibilidade.

---

## Regra para Google Drive

A integração com Google Drive exigiu um fluxo próprio.

Antes de integrar o botão no frontend, foram testados separadamente:

* API isolada;
* autenticação OAuth;
* variáveis de ambiente;
* upload via curl;
* criação de pastas;
* atualização de arquivo existente;
* destino correto no Drive.

Só depois a integração foi conectada ao botão do sistema.

Essa separação reduziu risco e facilitou o diagnóstico.

---

## Fluxo padrão de desenvolvimento

O fluxo padrão usado foi:

```text
1. Diagnóstico
2. Plano
3. Confirmação
4. Patch mínimo
5. Build
6. Teste local
7. Git diff
8. Commit
9. Deploy
10. Teste em produção
11. Atualização da documentação
```

Esse fluxo evitou mudanças grandes sem controle.

---

## Diagnóstico antes do patch

Antes de editar arquivos, o Codex deveria responder:

```text
1. O que entendi
2. Categoria da mudança
3. Risco
4. Arquivos prováveis
5. Se precisa SQL
6. Diagnóstico necessário
7. O que NÃO será alterado
8. Plano
9. Confirmação antes de editar
```

Esse modelo ajudou a manter clareza e controle.

---

## Confirmação antes de editar

O Codex não deveria aplicar patch sensível sem confirmação explícita.

A confirmação normalmente era algo como:

```text
Pode aplicar.
```

ou:

```text
Vamos seguir.
```

Isso evitava alterações acidentais.

---

## Patch mínimo

A regra era:

```text
alterar o mínimo necessário para resolver o problema
```

Evitar:

* reescrever módulos inteiros;
* refatorar sem necessidade;
* alterar várias áreas ao mesmo tempo;
* misturar visual com financeiro;
* misturar SQL com frontend sem necessidade;
* adicionar melhorias paralelas no mesmo patch.

---

## Build obrigatório

Depois de mudanças relevantes, era necessário rodar:

```bash
npm run build
```

Se o build falhasse, a correção deveria focar apenas no erro mostrado.

Não era recomendado aproveitar o erro para refatorar outras partes.

---

## Limite de erros consecutivos

Se uma mudança gerasse muitos erros seguidos, a recomendação era parar.

Em vez de acumular remendos, o melhor era voltar ao último estado estável e refazer menor.

Exemplo:

```bash
git restore src/App.tsx src/App.css
```

Essa regra evitava transformar um erro pequeno em uma sequência grande de instabilidade.

---

## Backups

Backups nunca deveriam ser criados dentro de `src/`.

Motivo:

```text
Vite/TypeScript pode tentar compilar arquivos de backup dentro da pasta src
```

Local recomendado:

```text
../backups/
```

Exemplo:

```bash
mkdir -p ../backups
cp src/App.tsx ../backups/App-antes-da-mudanca.tsx
cp src/App.css ../backups/App-css-antes-da-mudanca.css
```

---

## Git diff

Antes de commitar, era importante revisar o que foi alterado.

Comandos usados:

```bash
git diff src/App.tsx src/App.css
```

Para documentação:

```bash
git diff SISTEMA_ODONTO_SUPPLY_CONTEXT.md
```

Para mudanças SQL/documentação técnica:

```bash
git diff SUPABASE_SCHEMA_REFERENCE.md SQL_CHANGELOG.md
```

O objetivo era garantir que o patch não alterou áreas não planejadas.

---

## Commit

Após build e teste local, era feito o commit.

Exemplo:

```bash
git add .
git commit -m "Integra upload manual de PDFs para Drive"
```

Mensagens de commit deveriam ser curtas e claras.

Exemplos:

```text
Adiciona relatório de desempenho anual
Otimiza PDFs comerciais
Integra upload manual de PDFs para Drive
Corrige layout mobile de lançamentos
```

---

## Deploy

Depois do commit, o deploy era feito na Vercel:

```bash
vercel --prod
```

Depois do deploy, a função precisava ser testada no link público.

---

## Teste em produção

Não bastava o sistema funcionar localmente.

Também era necessário testar em produção:

* abrir o link público;
* testar login;
* testar botão alterado;
* conferir dados;
* validar PDF;
* validar upload para Drive;
* confirmar que não quebrou fluxo existente.

---

## Documentação depois da mudança

Quando uma mudança relevante era concluída, ela precisava ser registrada na documentação técnica.

Exemplos de mudanças que exigem documentação:

* novo módulo;
* nova aba;
* nova tabela SQL;
* nova regra financeira;
* nova integração;
* nova API;
* alteração em saldo;
* alteração em estoque;
* alteração em PDFs;
* alteração em relatórios;
* alteração importante de dashboard.

---

## O que não fazer

Durante o desenvolvimento, algumas práticas foram evitadas:

* aplicar patch sem diagnóstico;
* alterar SQL por suposição;
* misturar várias mudanças sensíveis;
* criar backup dentro de `src`;
* mexer em estoque sem confirmar regra;
* alterar saldo junto com layout;
* publicar sem build;
* commitar sem revisar diff;
* expor segredos em documentação;
* usar variáveis frontend como solução final de segurança.

---

## Exemplo de prompt seguro

Um prompt seguro para mudança sensível segue este padrão:

```text
Antes de editar, diagnostique.

Classifique a mudança.
Informe o risco.
Diga quais arquivos pretende alterar.
Confirme se precisa SQL.
Explique o que não será alterado.
Proponha patch mínimo.
Aguarde minha confirmação antes de aplicar.
```

Esse padrão força clareza antes da ação.

---

## Exemplo de resposta esperada do Codex

```text
1. O que entendi
2. Categoria
3. Risco
4. Arquivos prováveis
5. Se precisa SQL
6. Diagnóstico
7. Plano
8. O que NÃO será alterado
9. Confirmação antes de editar
```

Esse formato virou um protocolo de segurança do projeto.

---

## Aprendizados principais

Principais aprendizados do processo:

* sistemas financeiros exigem cuidado maior;
* diagnóstico economiza tempo;
* patch pequeno é mais seguro;
* build é obrigatório;
* local e produção podem se comportar diferente;
* Vite e Vercel local não são a mesma coisa;
* variáveis de ambiente causam muitos erros silenciosos;
* PDFs precisam ser testados pelo peso e pelo visual;
* integrações externas devem ser testadas isoladamente;
* documentação precisa acompanhar evolução do sistema.

---

## Resultado da metodologia

Essa metodologia permitiu desenvolver o sistema de forma progressiva, mantendo controle sobre riscos.

O sistema conseguiu evoluir com novas funções sem perder estabilidade.

A principal vantagem foi transformar o desenvolvimento em um processo repetível:

```text
entender
→ diagnosticar
→ alterar pouco
→ testar
→ publicar
→ documentar
```

Esse processo pode ser reutilizado em outros projetos internos.
