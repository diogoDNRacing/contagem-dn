# Leitor de código de barras → SKU

Aplicativo web para **contagem de estoque** com leitura de código de barras pela câmera do celular. Várias pessoas podem contar ao mesmo tempo: tudo é sincronizado em tempo real e a contagem final é exportada em Excel, no modelo de atualização de estoque do sistema.

## Como funciona

1. A pessoa aponta a câmera para o código de barras (ou digita o código, ou usa um leitor USB/Bluetooth que "digita" no campo).
2. Se o código já foi associado a um SKU, o SKU é preenchido sozinho. Se for novo, informa-se o SKU uma única vez e o app lembra para sempre, para todos os usuários.
3. Informa a quantidade e clica em **Adicionar**.
4. No final, **Exportar para o sistema (Excel)** gera a planilha pronta para importar.

## Funcionalidades

- Leitura por **câmera ao vivo** (EAN-13, EAN-8, UPC-A, UPC-E, Code 128, Code 39, ITF e QR Code).
- Botão **Tirar foto do código** como alternativa quando a câmera ao vivo não está disponível.
- **Multiusuário em tempo real**: contagem, associações código → SKU e cadastro de SKUs são compartilhados via Supabase.
- **Soma sem conflito**: se duas pessoas lerem o mesmo SKU ao mesmo tempo, as quantidades são somadas no servidor, sem uma sobrescrever a outra.
- Edição e remoção de itens da contagem (refletem para todos).
- **Importação do cadastro** (`.xlsx`, `.xls` ou `.csv`) com colunas `SKU`, `Título` e, opcionalmente, `Código de barras` / `EAN` / `GTIN`.
- Exportação da tabela código → SKU em CSV.
- Tema claro/escuro automático e layout pensado para celular.

## Arquivos

| Arquivo | Descrição |
|---|---|
| `index.html` | O aplicativo (página única, sem build). É o `leitor-supabase.html` renomeado. |
| `setup.sql` | Script que cria as tabelas, a função de soma e o tempo real no Supabase. |

## Instalação

### 1. Criar o banco no Supabase

1. Crie um projeto gratuito em [supabase.com](https://supabase.com).
2. Abra **SQL Editor**, cole o conteúdo de `setup.sql` e clique em **Run**.

### 2. Configurar o app

Em **Project Settings → API / Data API**, copie:

- a **Project URL** (`https://SEU-ID.supabase.co`);
- a **Publishable key** (`sb_publishable_...`, antiga chave `anon`).

Cole os dois no início do `<script>` do `index.html`:

```js
var SUPABASE_URL="https://SEU-ID.supabase.co";
var SUPABASE_KEY="sb_publishable_...";
```

> ⚠️ **Nunca** use a *Secret key* (`sb_secret_...`) no app. Ela dá acesso total ao banco e ficaria exposta publicamente.

### 3. Publicar no GitHub Pages

1. Suba o `index.html` no repositório.
2. Em **Settings → Pages**, escolha a branch e a pasta raiz.
3. Abra o link gerado. No topo da página deve aparecer **"Sincronizado em tempo real"**.

A câmera exige **HTTPS**, que o GitHub Pages já fornece.

## Formato da exportação

A planilha segue o modelo do sistema, com uma linha por SKU:

| SKU* | Estoque Baixo | Qtd. Total Atualizado | Custo Médio Atualizado |
|---|---|---|---|
| (SKU) | *(vazio)* | (quantidade contada) | *(vazio)* |

As colunas deixadas em branco não são atualizadas pelo sistema. 

## Observações de segurança

As políticas do `setup.sql` permitem leitura e escrita para qualquer pessoa que tenha o link do app, pois não há login. Isso serve para uso interno com um link restrito. Se o repositório for público ou os dados forem sensíveis, considere adicionar autenticação (Supabase Auth) e restringir as políticas de RLS a usuários logados.

## Limpar contagem

O botão **Limpar contagem** apaga a contagem **para todos os usuários**. As associações código → SKU e o cadastro são mantidos.

## Tecnologias

- HTML, CSS e JavaScript puro
- [html5-qrcode](https://github.com/mebjas/html5-qrcode) para leitura de códigos
- [SheetJS](https://sheetjs.com/) para importar e exportar Excel
- [Supabase](https://supabase.com) (Postgres + Realtime) para armazenamento e sincronização
