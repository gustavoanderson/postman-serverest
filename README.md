<div align="center">

# 📮 ServeRest · Testes de API com Postman

**Montei uma collection no Postman para testar produtos, usuários e login da API ServeRest, com asserções automáticas e token capturado por script**

![Postman](https://img.shields.io/badge/Postman-Collection-FF6C37?style=for-the-badge&logo=postman&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-pm.test-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![REST](https://img.shields.io/badge/API-REST-0969da?style=for-the-badge)
![Requisições](https://img.shields.io/badge/requisi%C3%A7%C3%B5es-8-2ea44f?style=for-the-badge)
![Asserções](https://img.shields.io/badge/asser%C3%A7%C3%B5es-6-2ea44f?style=for-the-badge)

🇧🇷 [Português](#-português) · 🇺🇸 [English](#-english)

</div>

---

## 🇧🇷 Português

### 🎯 Objetivo

Antes de automatizar testes de API em código, quis dominar a ferramenta que todo time de QA usa no dia a dia para explorar e validar endpoints: o **Postman**.

Usei a **ServeRest**, uma API REST que simula uma loja virtual, e organizei uma collection para validar:

- o **CRUD de produtos** (listar, consultar, cadastrar, editar e excluir),
- o **cadastro e a consulta de usuários**,
- e o **login**, com o token de autenticação reaproveitado nas rotas protegidas.

### 🧭 Estratégia

Organizei a collection por recurso da API e automatizei a parte que mais atrasa um teste manual de API: copiar e colar o token de autenticação.

```mermaid
flowchart LR
    L["🔐 POST /login"] -->|script de teste| T[("🎟️ {{token}}<br/>variável global")]
    T --> P1["POST /produtos"]
    T --> P2["PUT /produtos/:id"]
    P3["DELETE /produtos/:id"]
    G1["GET /produtos"] --> V{{"pm.test"}}
    P1 --> V
    P3 --> V
    V --> OK(["✅ Status + mensagem<br/>validados"])

    style OK fill:#1a7f37,color:#fff,stroke:#1a7f37
```

**Token automático.** No retorno do login, um script lê o campo `authorization` e salva em uma variável global. Todas as requisições protegidas usam essa variável, e o fluxo inteiro roda sem nenhuma intervenção manual.

```javascript
const resposta = pm.response.json();
pm.globals.set("token", resposta.authorization);
```

**Asserções em cada operação crítica.** Nas requisições de listar, cadastrar e excluir produto, escrevi testes que conferem o **status HTTP** e a **mensagem de negócio** devolvida pela API.

### 🧪 Cobertura

```mermaid
%%{init: {"themeVariables": {"pieOpacity": "1", "pieStrokeColor": "#ffffff", "pieStrokeWidth": "2px", "pieOuterStrokeColor": "#8c959f", "pieSectionTextColor": "#ffffff", "pieSectionTextSize": "15px", "pieTitleTextColor": "#57606a", "pieLegendTextColor": "#57606a", "pie1": "#1a7f37", "pie2": "#0969da", "pie3": "#8250df", "pie4": "#bf3989", "pie5": "#9a6700", "pie6": "#cf222e", "pie7": "#1b7c83", "pie8": "#57606a"}}}%%
pie showData
    title Requisições por recurso (8 no total)
    "Produtos" : 5
    "Usuários" : 2
    "Login" : 1
```

| Recurso | Requisição | Método | Validação automática |
|---|---|:-:|---|
| 📦 Produtos | Listar produtos | `GET` | ✅ Status 200 + produto esperado na lista |
| 📦 Produtos | Cadastrar produto | `POST` | ✅ Status 201 + `Cadastro realizado com sucesso` |
| 📦 Produtos | Consultar por ID | `GET` | Exploratória |
| 📦 Produtos | Editar produto | `PUT` | Exploratória |
| 📦 Produtos | Excluir produto | `DELETE` | ✅ Status 200 + `Registro excluído com sucesso` |
| 👤 Usuários | Criar usuário | `POST` | Exploratória |
| 👤 Usuários | Buscar por ID | `GET` | Exploratória |
| 🔐 Login | Autenticar | `POST` | ⚙️ Salva o token em variável global |

```mermaid
%%{init: {"themeVariables": {"pieOpacity": "1", "pieStrokeColor": "#ffffff", "pieStrokeWidth": "2px", "pieOuterStrokeColor": "#8c959f", "pieSectionTextColor": "#ffffff", "pieSectionTextSize": "15px", "pieTitleTextColor": "#57606a", "pieLegendTextColor": "#57606a", "pie1": "#1a7f37", "pie2": "#0969da", "pie3": "#8250df", "pie4": "#bf3989", "pie5": "#9a6700", "pie6": "#cf222e", "pie7": "#1b7c83", "pie8": "#57606a"}}}%%
pie showData
    title Métodos HTTP exercitados
    "GET" : 3
    "POST" : 3
    "PUT" : 1
    "DELETE" : 1
```

### 📈 Resultados

- **Cobri o CRUD completo de produtos**, com os quatro métodos HTTP.
- **Automatizei 6 asserções** de status e de mensagem de negócio nas operações de listar, cadastrar e excluir.
- **Eliminei o passo manual do token:** o login alimenta sozinho as requisições protegidas.
- **Entreguei uma collection versionada:** o arquivo `.json` está no repositório e qualquer pessoa do time pode importar e rodar.

### 🚀 Onde esse trabalho se aplica

- **Exploração de API nova:** a collection é o primeiro passo para entender uma API antes de automatizar em código.
- **Execução em lote:** pelo Collection Runner ou pelo Newman, a mesma collection roda inteira de uma vez, inclusive em pipeline de CI.
- **Documentação viva:** as requisições organizadas por recurso servem de referência de uso da API para o time.
- **Evolução para código:** levei esses mesmos endpoints para testes automatizados com Cypress e Jenkins no projeto [teste-api-serverest-cypress-jenkins](https://github.com/gustavoanderson/teste-api-serverest-cypress-jenkins).

### ▶️ Como executar

1. Suba a ServeRest localmente: `npx serverest@latest`
2. No Postman, clique em **Import** e selecione o arquivo `Teste Serverest.postman_collection.json`
3. Rode a requisição **login** primeiro, para gerar o token
4. Execute as demais requisições, ou a collection inteira pelo **Collection Runner**

Pela linha de comando, com Newman:

```bash
npx newman run "Teste Serverest.postman_collection.json"
```

---

## 🇺🇸 English

### 🎯 Goal

Before automating API tests in code, I wanted to master the tool every QA team uses daily to explore and validate endpoints: **Postman**.

I used **ServeRest**, a REST API that simulates an online store, and built a collection to validate the **products CRUD**, **user sign-up and lookup**, and **login**, with the auth token reused on protected routes.

### 🧭 Strategy

```mermaid
flowchart LR
    L["🔐 POST /login"] -->|test script| T[("🎟️ {{token}}<br/>global variable")]
    T --> P1["POST /produtos"]
    T --> P2["PUT /produtos/:id"]
    P3["DELETE /produtos/:id"]
    G1["GET /produtos"] --> V{{"pm.test"}}
    P1 --> V
    P3 --> V
    V --> OK(["✅ Status + message<br/>validated"])

    style OK fill:#1a7f37,color:#fff,stroke:#1a7f37
```

**Automatic token.** On the login response, a script reads the `authorization` field and saves it as a global variable, so every protected request runs without manual copy-paste.

**Assertions on critical operations.** On list, create and delete product, I wrote tests that check both the **HTTP status** and the **business message** returned by the API.

### 🧪 Coverage

```mermaid
%%{init: {"themeVariables": {"pieOpacity": "1", "pieStrokeColor": "#ffffff", "pieStrokeWidth": "2px", "pieOuterStrokeColor": "#8c959f", "pieSectionTextColor": "#ffffff", "pieSectionTextSize": "15px", "pieTitleTextColor": "#57606a", "pieLegendTextColor": "#57606a", "pie1": "#1a7f37", "pie2": "#0969da", "pie3": "#8250df", "pie4": "#bf3989", "pie5": "#9a6700", "pie6": "#cf222e", "pie7": "#1b7c83", "pie8": "#57606a"}}}%%
pie showData
    title Requests per resource (8 total)
    "Products" : 5
    "Users" : 2
    "Login" : 1
```

### 📈 Results

- **I covered the full products CRUD**, with all four HTTP methods.
- **I automated 6 assertions** on status and business messages.
- **I removed the manual token step:** login feeds protected requests automatically.
- **I delivered a versioned collection** that anyone on the team can import and run.

### 🚀 Where this applies

- **Exploring a new API** before automating it in code.
- **Batch runs** through Collection Runner or Newman, including in CI pipelines.
- **Living documentation** of how the API is used.
- **Moving to code:** I took these endpoints into Cypress + Jenkins in [teste-api-serverest-cypress-jenkins](https://github.com/gustavoanderson/teste-api-serverest-cypress-jenkins).

### ▶️ How to run

1. Start ServeRest locally: `npx serverest@latest`
2. In Postman, **Import** `Teste Serverest.postman_collection.json`
3. Run **login** first to generate the token
4. Run the other requests, or the whole collection with **Collection Runner**

---

<div align="center">

Feito por **Gustavo Anderson** · QA Engineer
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/gustavo-anderson)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/gustavoanderson)

</div>
