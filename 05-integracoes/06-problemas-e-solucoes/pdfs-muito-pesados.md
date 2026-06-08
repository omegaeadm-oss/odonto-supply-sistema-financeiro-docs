# PDFs comerciais muito pesados

Esta página documenta um problema encontrado durante o desenvolvimento do sistema: os PDFs comerciais de vendas e orçamentos estavam ficando grandes demais, mesmo quando tinham poucas páginas.

Esse problema impactava diretamente o uso prático do sistema, principalmente na integração com Google Drive, envio de arquivos e performance geral.

---

## Contexto

O sistema gera PDFs comerciais para vendas e orçamentos.

Esses PDFs são usados para:

* enviar propostas para clientes;
* registrar vendas fechadas;
* manter documentos comerciais organizados;
* salvar arquivos no Google Drive;
* padronizar a apresentação da empresa.

A aparência visual dos PDFs era importante, porque eles representam a empresa comercialmente. Por isso, desde o início, a geração buscava preservar um layout escuro, moderno, organizado e com identidade visual.

---

## Problema encontrado

Durante os testes da integração com Google Drive, um PDF de venda com apenas duas páginas ficou com aproximadamente 12 MB.

Esse tamanho era alto demais para um documento comercial simples.

Exemplo do problema:

```text
Arquivo: Venda - 108.pdf
Páginas: 2
Tamanho aproximado: 12 MB
```

Esse peso causava problemas como:

* upload lento;
* falha no envio para API;
* risco de ultrapassar limite da Vercel Function;
* dificuldade para enviar por WhatsApp ou e-mail;
* consumo desnecessário de armazenamento;
* pior experiência para uso diário.

---

## Impacto na integração com Google Drive

A integração com Google Drive usa uma Vercel Function para receber o PDF e enviá-lo ao Drive.

Durante os testes, a API rejeitou o arquivo por limite de tamanho.

O erro indicava que o arquivo estava ultrapassando o limite interno configurado para upload.

Inicialmente, foi considerado aumentar o limite da API, mas isso não resolveria a causa real do problema.

A conclusão foi:

```text
O problema principal não era a API.
O problema principal era o tamanho excessivo do PDF gerado.
```

---

## Causa provável

O sistema gerava o PDF a partir de uma captura visual do HTML.

O fluxo técnico era aproximadamente:

```text
HTML do documento
→ html2canvas
→ imagem PNG
→ jsPDF
→ arquivo PDF
```

Esse processo preservava bem o visual, mas tinha um problema: o PDF final ficava parecido com uma grande imagem dentro de um arquivo PDF.

A combinação mais pesada era:

```text
html2canvas com escala alta
+
imagem PNG
+
PDF com páginas renderizadas como imagem
```

PNG preserva qualidade, mas costuma gerar arquivos grandes quando o conteúdo possui:

* fundos escuros;
* sombras;
* gradientes;
* logos;
* tabelas;
* textos;
* áreas grandes de imagem.

---

## Diagnóstico técnico

O diagnóstico mostrou que os PDFs comerciais usavam uma lógica parecida com:

```text
html2canvas(sheet, { scale: 2 })
canvas.toDataURL("image/png")
pdf.addImage(..., "PNG", ...)
```

Essa abordagem gerava ótima fidelidade visual, mas o tamanho final era muito alto.

O problema ficava ainda mais evidente porque o PDF tinha apenas duas páginas, ou seja, o peso não era causado por quantidade excessiva de conteúdo.

---

## Decisão tomada

Em vez de aumentar indefinidamente o limite de upload, a decisão foi otimizar a geração dos PDFs.

A estratégia foi:

```text
reduzir o peso do arquivo
sem destruir o visual profissional
```

O objetivo era manter:

* legibilidade;
* cabeçalho visual;
* logo;
* tabelas;
* dados do cliente;
* produtos;
* totais;
* paginação;
* nome do arquivo;
* estrutura geral do documento.

---

## Otimizações aplicadas

As principais otimizações foram:

### Redução da escala de renderização

A escala do `html2canvas` foi reduzida.

Isso diminui a quantidade de pixels gerados na captura do HTML.

Menos pixels significam:

* menos peso;
* renderização mais rápida;
* upload mais leve.

### Troca de PNG para JPEG

O PDF deixou de usar imagem PNG e passou a usar JPEG com qualidade controlada.

Essa mudança costuma reduzir muito o tamanho final do arquivo.

PNG é melhor para fidelidade exata, mas JPEG pode ser muito mais eficiente para documentos visuais com fundos e gradientes.

### Compressão no jsPDF

Também foi utilizada compressão na criação do PDF quando possível.

Essa etapa ajuda a reduzir ainda mais o tamanho final.

---

## Resultado

Após a otimização, um PDF comercial de teste ficou com aproximadamente 152 KB.

Comparação:

```text
Antes:
aproximadamente 12 MB

Depois:
aproximadamente 152 KB
```

A redução foi significativa e tornou o upload para Google Drive muito mais viável.

---

## Benefícios obtidos

A otimização trouxe benefícios práticos:

* PDFs muito mais leves;
* upload mais rápido;
* menor risco de erro na Vercel Function;
* melhor experiência no sistema;
* arquivos mais fáceis de enviar;
* melhor organização no Drive;
* menor consumo de armazenamento;
* abertura mais rápida dos documentos.

---

## Cuidados durante a otimização

A otimização de PDF precisa ser feita com cuidado.

Reduzir demais a qualidade pode causar:

* texto borrado;
* logo com baixa nitidez;
* tabelas difíceis de ler;
* aparência menos profissional;
* problemas na impressão.

Por isso, a otimização precisa equilibrar:

```text
tamanho do arquivo
+
qualidade visual
+
legibilidade
```

---

## Testes realizados

Após a alteração, os PDFs precisaram ser testados visualmente.

Checklist usado:

* abrir o PDF no computador;
* conferir tamanho do arquivo;
* conferir número de páginas;
* verificar logo;
* verificar textos pequenos;
* verificar dados do cliente;
* verificar tabelas;
* verificar totais;
* verificar quebra de página;
* testar upload para Google Drive;
* testar atualização de arquivo existente no Drive.

---

## Relação com o upload para Drive

A otimização dos PDFs foi essencial para a integração com o Google Drive.

Sem essa etapa, a função de upload ficaria instável, porque PDFs maiores poderiam ultrapassar limites da Vercel Function.

A solução correta foi atacar a origem do problema:

```text
PDF pesado demais
→ otimizar geração do PDF
→ upload fica viável
```

E não apenas:

```text
PDF pesado demais
→ aumentar limite da API
```

---

## Lição aprendida

Ao gerar PDFs a partir de HTML visual, é importante lembrar que o resultado pode não ser um PDF leve baseado em texto vetorial.

Muitas vezes, a biblioteca transforma a página em imagem e coloca essa imagem dentro do PDF.

Isso pode ser adequado para manter fidelidade visual, mas exige atenção ao tamanho final do arquivo.

---

## Estratégia recomendada para projetos semelhantes

Para sistemas que geram PDFs comerciais com visual personalizado:

1. testar o tamanho do PDF desde o início;
2. evitar PNG pesado quando não for necessário;
3. usar JPEG com qualidade controlada quando fizer sentido;
4. reduzir escala sem perder legibilidade;
5. testar o PDF em computador e celular;
6. validar upload e envio em condições reais;
7. não depender de limites máximos de API como solução principal.

---

## Antes e depois

Resumo da melhoria:

```text
Antes:
PDF bonito, porém pesado demais.

Depois:
PDF visualmente aceitável, muito mais leve e adequado para upload.
```

Essa otimização foi uma etapa essencial para transformar a geração de PDFs em uma função realmente prática para o uso diário.
