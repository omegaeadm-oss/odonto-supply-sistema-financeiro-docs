# Integração com Google Drive API

Esta página documenta a integração criada para enviar PDFs comerciais de vendas e orçamentos diretamente para o Google Drive da empresa.

A função foi implementada para reduzir trabalho manual, evitar perda de arquivos no computador e manter os documentos comerciais organizados automaticamente por tipo, ano e mês.

---

## Objetivo da integração

Antes da integração, o fluxo era manual:

```text
Gerar PDF no sistema
→ baixar no computador
→ localizar o arquivo
→ abrir Google Drive
→ encontrar pasta correta
→ enviar manualmente
```

Esse processo criava risco de:

* arquivos perdidos no Desktop ou Downloads;
* documentos salvos na pasta errada;
* duplicidade;
* falta de padronização;
* retrabalho quando mais de uma pessoa usava o sistema.

A integração foi criada para permitir que o usuário envie o PDF para o Google Drive com um clique.

---

## Decisão de produto

A primeira decisão importante foi escolher entre upload automático ou upload manual.

A opção escolhida foi:

```text
Upload manual por botão
```

Motivos:

* o usuário pode revisar a venda ou orçamento antes de enviar;
* evita salvar documentos incompletos;
* reduz risco de subir arquivo errado;
* facilita controle operacional;
* simplifica o MVP;
* evita efeitos colaterais ao apenas criar ou editar registros.

---

## Fluxo final

Para vendas:

```text
Venda cadastrada
→ usuário clica no ícone do Drive
→ sistema gera o PDF internamente
→ sistema envia o arquivo para /api/upload-drive
→ API salva no Google Drive
→ arquivo aparece em Venda-YYYY / Mês-YY
```

Para orçamentos:

```text
Orçamento cadastrado
→ usuário clica no ícone do Drive
→ sistema gera o PDF internamente
→ sistema envia o arquivo para /api/upload-drive
→ API salva no Google Drive
→ arquivo aparece em Orçamento-YYYY / Mês-YY
```

---

## Botão manual no sistema

O botão foi adicionado ao lado do botão de PDF.

Características:

* usa apenas o ícone do Google Drive;
* não possui texto visível;
* mantém o padrão visual dos botões de ação;
* possui feedback de envio;
* não altera o download tradicional do PDF;
* não faz upload automático.

O botão de PDF continua existindo normalmente.

---

## Estrutura de pastas no Drive

A organização escolhida foi baseada em tipo de documento, ano e mês.

### Vendas

```text
ODONTO SUPPLY / Venda-YYYY / Mês-YY
```

Exemplo:

```text
ODONTO SUPPLY / Venda-2026 / Junho-26
```

### Orçamentos

```text
ODONTO SUPPLY / Orçamento-YYYY / Mês-YY
```

Exemplo:

```text
ODONTO SUPPLY / Orçamento-2026 / Junho-26
```

---

## Endpoint criado

A integração usa uma Vercel Function:

```text
/api/upload-drive
```

Esse endpoint recebe um `multipart/form-data` com os seguintes campos:

```text
file
documentType
documentDate
filename
```

Tipos aceitos:

```text
revenue
quote
```

---

## Campos enviados pelo frontend

Para vendas:

```text
documentType = revenue
documentDate = sale_date
filename = nome final do PDF da venda
file = PDF gerado internamente
```

Para orçamentos:

```text
documentType = quote
documentDate = quote_date
filename = nome final do PDF do orçamento
file = PDF gerado internamente
```

---

## Autenticação com Google

A integração foi feita usando OAuth com refresh token.

Variáveis de ambiente utilizadas:

```text
GOOGLE_CLIENT_ID
GOOGLE_CLIENT_SECRET
GOOGLE_REFRESH_TOKEN
GOOGLE_DRIVE_ROOT_FOLDER_ID
```

Essas variáveis ficam no backend e não devem ser expostas publicamente.

---

## Pasta raiz

A API usa a variável:

```text
GOOGLE_DRIVE_ROOT_FOLDER_ID
```

Essa variável aponta para a pasta raiz da empresa no Google Drive.

A partir dessa pasta, a API cria ou encontra:

```text
Venda-YYYY
Orçamento-YYYY
Mês-YY
```

Importante: o ID da pasta raiz pode mudar caso a empresa reorganize o Drive, duplique pastas ou altere a estrutura de sincronização.

---

## Regra de duplicidade

A API foi criada para evitar arquivos duplicados.

Antes de criar um arquivo novo, ela verifica se já existe um arquivo com o mesmo nome dentro da pasta mensal.

Se existir:

```text
o arquivo é atualizado/substituído
```

Se não existir:

```text
um novo arquivo é criado
```

A resposta da API retorna:

```text
created
```

ou:

```text
updated
```

---

## Resposta da API

Exemplo de resposta:

```json
{
  "ok": true,
  "fileId": "ID_DO_ARQUIVO",
  "webViewLink": "LINK_DO_ARQUIVO_NO_DRIVE",
  "filename": "Venda - 108.pdf",
  "folderId": "ID_DA_PASTA_MENSAL",
  "action": "created"
}
```

O campo `folderId` representa a pasta mensal final, não a pasta raiz.

---

## Problema encontrado: pasta antiga do Drive

Durante os testes, foi identificado que os uploads estavam indo para uma pasta antiga da empresa.

Causa:

```text
GOOGLE_DRIVE_ROOT_FOLDER_ID apontava para uma pasta ODONTO SUPPLY antiga
```

Solução:

```text
identificar o ID correto da pasta atual
→ atualizar .env.local
→ atualizar a variável na Vercel
→ reiniciar ambiente local
→ fazer novo deploy
```

---

## Diferença entre Drive na nuvem e Drive local

A função não salva arquivos diretamente na pasta local do computador.

Ela salva no Google Drive via API.

Depois disso, o Google Drive Desktop sincroniza os arquivos para o computador, se a sincronização estiver configurada corretamente.

Por isso, o destino real do upload é definido pelo ID da pasta no Google Drive, não pelo caminho local do computador.

---

## Teste local

Para testar a função localmente, é necessário usar:

```bash
vercel dev --listen 3000
```

E acessar:

```text
http://localhost:3000
```

Usar apenas:

```bash
npm run dev
```

abre o Vite em `localhost:5173`, mas não testa corretamente a Vercel Function.

---

## Problemas comuns

### Erro 401 Unauthorized

Causa provável:

```text
segredo de upload ausente ou diferente entre frontend e backend
```

### Arquivo não aparece na pasta esperada

Causa provável:

```text
GOOGLE_DRIVE_ROOT_FOLDER_ID aponta para pasta errada
```

### Upload funciona no curl, mas não no botão

Causa provável:

```text
variável VITE_ não carregada no frontend
```

### Upload falha por tamanho

Causa provável:

```text
PDF gerado pesado demais
```

Solução:

```text
otimizar geração do PDF antes de enviar
```

---

## Segurança

A versão inicial usa um segredo temporário para proteger o endpoint.

Variáveis:

```text
DRIVE_UPLOAD_TEST_SECRET
VITE_DRIVE_UPLOAD_TEST_SECRET
```

Essa solução foi útil para o MVP, mas não é ideal no longo prazo, porque variáveis com prefixo `VITE_` ficam disponíveis no frontend.

Melhoria futura recomendada:

```text
substituir o segredo temporário por validação backend com sessão Supabase
```

---

## Aprendizados

Principais aprendizados dessa integração:

* Google Drive API trabalha com IDs de pasta, não caminhos locais;
* Drive Desktop e Drive API são coisas diferentes;
* Vercel Functions precisam ser testadas com `vercel dev`;
* variáveis `VITE_` são públicas no frontend;
* PDFs grandes quebram upload facilmente;
* regra de atualização evita arquivos duplicados;
* separar upload manual de upload automático reduz risco operacional.

