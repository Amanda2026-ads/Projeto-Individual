# Etapa 09 — `app.ts` e `server.ts`

> Objetivo: instanciar o Express, conectar as rotas e o middleware de erro e, por fim, subir o servidor HTTP na porta 3000.

## Pré-requisitos

- Etapas 01 a 08 concluídas.

## Resultado esperado ao final da etapa

- `src/app.ts` — configura a instância do Express, usa JSON parser, rotas e middleware de erro.
- `src/server.ts` — importa o app e chama `listen` na porta 3000.
- `npm run dev` consegue subir o servidor e responde em `http://localhost:3000`.

## Passo a passo

### 1. `app.ts`

Crie **`src/app.ts`** com exatamente este conteúdo:

```typescript
import express from "express";
import routes from "./routes";
import { errorHandler } from "./middlewares/error-handler";

export const app = express();

app.use(express.json());
app.use(routes);
app.use(errorHandler);   // sempre por último
```

> **Ordem dos `app.use` importa**:
> 1. `express.json()` — faz o parse do corpo JSON das requisições.
> 2. `routes` — resolve a URL e chama o controller.
> 3. `errorHandler` — último, porque só captura o que sobrar (erros propagados via `next(err)`).

### 2. `server.ts`

Crie **`src/server.ts`** com exatamente este conteúdo:

```typescript
import { app } from "./app";

const PORT = 3000;

app.listen(PORT, () => {
  console.log(`Servidor rodando em http://localhost:${PORT}`);
});
```

> **Por que separar `app.ts` e `server.ts`?** `app.ts` exporta uma instância do Express **pronta, mas sem estar ouvindo uma porta**. Isso facilita, no futuro, criar testes automatizados (basta importar o `app` e chamar via `supertest`) sem precisar ligar um servidor real.

### 3. Subir o servidor

Execute na raiz do projeto:

```bash
npm run dev
```

Deve imprimir:

```
Servidor rodando em http://localhost:3000
```

### 4. Teste rápido (sem banco populado)

Em outro terminal (deixe o `npm run dev` rodando), rode:

```bash
curl http://localhost:3000/products
```

A resposta esperada é `[]` (array vazio), já que ainda não há produtos — a etapa 10 traz um seed opcional.

## Verificação

1. `npm run dev` sobe sem erros.
2. `curl http://localhost:3000/products` devolve `[]` ou um array de produtos existentes.
3. `curl http://localhost:3000/nao-existe` devolve `Cannot GET /nao-existe` (comportamento padrão do Express) — ou, se você preferir, pode adicionar um handler 404 depois como desafio.
4. Uma requisição com payload inválido (ex.: `POST /products` sem `name`) devolve **status 400** e JSON `{ "error": "Dados inválidos", "details": [...] }`.

## Próxima etapa

Siga para **[etapa-10-seed-e-requests.md](etapa-10-seed-e-requests.md)** para popular o banco com dados de exemplo e criar o arquivo `requests.http`.