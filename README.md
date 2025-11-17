#📌 Testes de API Automatizados – Cenários, Cobertura e Prioridade

Este documento descreve os principais cenários de teste planejados e automatizados para validação das APIs do sistema, incluindo Login, Usuários, Produtos e Carrinhos.  
Os cenários estão divididos entre **relevantes** (boa cobertura funcional) e **críticos** (prioridade máxima para automação).

---

## 🔐 1. Login (`POST /login`)

### ✔ Cenários relevantes para automação

| Cenário | Validação esperada |
|--------|---------------------|
| Login com credenciais válidas | Status `200`, retorno com token válido. |
| Login com senha incorreta | Status `401`, mensagem de erro adequada, **sem token**. |
| Login com campos obrigatórios ausentes | Status `400`, mensagem de validação específica. |
| Login com payload inválido (formato incorreto / JSON quebrado) | Status `400`. |

### 🚨 Cenários críticos automatizados

- **Login válido retorna token utilizável**
  - Validar `status 200`, presença de `authorization` (Bearer token).
- **Login inválido com senha incorreta**
  - Validar `status 401`, ausência de token e mensagem `"senha inválida"` ou equivalente.

---

## 👤 2. Usuários (`GET/POST/GET{id}/PUT{id}/DELETE{id}`)

### ✔ Cenários relevantes para automação

| Cenário | Validação esperada |
|--------|---------------------|
| Listar usuários (`GET /usuarios`) | Retorna `200` e lista com campos esperados (`nome, email, password, administrador, _id`). |
| Criar usuário válido | `201` + mensagem `"Cadastro realizado com sucesso"` |
| Criar usuário duplicado | `400` + mensagem indicando duplicidade |
| Criar usuário com campos inválidos ou ausentes | `400` com mensagens por regra violada |
| Buscar usuário por ID existente | `200` com dados corretos |
| Buscar usuário inexistente | Resposta vazia ou `404`, conforme regra |
| Editar usuário existente | `200` e alteração refletida ao consultar |
| Editar usuário inexistente | `404` |
| Excluir usuário existente | `200`, e usuário não pode ser encontrado após remoção |
| Excluir usuário inexistente | `404` |

### 🚨 Cenários críticos automatizados

- **Criar usuário válido e validar persistência**
  - `POST /usuarios` → `201`
  - `GET /usuarios/{id}` confirma retorno consistente.
  
- **Editar usuário e validar alteração**
  - `PUT /usuarios/{id}` atualizando um campo (ex. nome)
  - `GET /usuarios/{id}` verificando persistência da mudança.

---

## 📦 3. Produtos (`GET/POST/GET{id}/PUT{id}/DELETE{id}`)

### ✔ Cenários relevantes para automação

| Cenário | Expectativa |
|--------|------------|
| Listar produtos | `200` + estrutura com (`nome, preco, descricao, quantidade, _id`). |
| Criar produto válido | `201` + mensagem adequada. |
| Criar produto inválido (campos faltando ou valores negativos) | `400`. |
| Criar produto duplicado (quando não permitido) | `400` + mensagem de duplicidade. |
| Buscar produto existente | `200` com dados corretos. |
| Buscar produto inexistente | Vazio ou `404`. |
| Editar produto existente | `200`, dados atualizados. |
| Editar inexistente | `404`. |
| Excluir produto existente | `200`, item removido da listagem. |
| Excluir inexistente | `404`. |
| Criar/editar/excluir sem autorização | `401`. |

### 🚨 Cenários críticos automatizados

- **Criar produto e validar uso**
  - `POST /produtos` → `201`
  - `GET /produtos/{id}` confirma persistência.

- **Autorização obrigatória**
  - POST/PUT/DELETE sem token → `401`.

---

## 🛒 4. Carrinhos (`GET /carrinhos`, `POST /carrinhos`, `GET /carrinhos/{id}`, `DELETE /carrinhos/concluir-compra`, `DELETE /carrinhos/cancelar-compra`)

### ✔ Cenários relevantes para automação

| Cenário | Esperado |
|--------|----------|
| Listar carrinhos | `200`. |
| Criar carrinho válido | `201` com ID, total calculado, e itens do usuário logado. |
| Criar carrinho com produto inexistente | `400`. |
| Criar com quantidade maior que estoque | `400` com mensagem adequada. |
| Buscar carrinho existente | `200` com dados completos. |
| Buscar carrinho inexistente | `404`. |
| Concluir compra | `200`, carrinho fechado e estoque atualizado. |
| Cancelar compra | `200`, itens voltam para o estoque. |
| Concluir ou cancelar sem carrinho aberto | `400`. |
| Autorização obrigatória | Operações sem token → `401`. |

### 🚨 Cenários críticos automatizados

- **Fluxo completo de compra**
- **Cancelar compra restaurando estoque**