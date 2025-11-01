# Plano de Testes Manuais - API Reconhecimento Facial V1

## 📌 Integrantes

- Eduardo Gomes Pinho Junior - 97919
- Gustavo Ferreira Lopes - 98887
- Pedro Henrique Salvitti - 88166
- Enzo de Oliveira Cunha - 550985

Este documento descreve os casos de teste manuais para as funcionalidades principais da API de reconhecimento facial.

**Endpoints Principais:**
* `POST /api/cadastrar`
* `POST /api/validar`

---

# Relatório de Testes Automatizados - API Reconhecimento Facial

Este documento resume os resultados da execução da suíte de testes automatizados para a API de reconhecimento facial, utilizando a coleção Postman.

**Coleção Postman:** [`postman/API_Reconhecimento_Facial.postman_collection.json`](./postman/API_Reconhecimento_Facial.postman_collection.json)

**Ambiente:** Local (`http://127.0.0.1:5000`)

---

## Resumo da Execução

A suíte de testes foi executada utilizando o Postman Collection Runner.

* **Total de Requisições:** 4
* **Total de Testes (Asserções):** 8
* **Testes Passaram:** 8
* **Testes Falharam:** 0
* **Testes Ignorados:** 0

**Resultado Geral:** ✅ SUCESSO (Todos os testes passaram)

---

## Detalhes por Requisição

### AT01 - POST Cadastrar Usuario Valido

* **Endpoint:** `POST /api/cadastrar`
* **Objetivo:** Verificar o cadastro bem-sucedido de um novo usuário com imagem válida.
* **Corpo da Requisição (JSON):**
    ```json
    {
      "nome_usuario": "PostmanUser_A",
      "imagem_base64": "[BASE64_REAL_IMAGEM_VALIDA_A]" 
    }
    ```
* **Resultados dos Testes:**
    * ✅ **PASS:** Status code is 201 Created
    * ✅ **PASS:** Response body includes success status and message
* **Status HTTP Retornado:** `201 Created`

---

### AT02 - POST Tentar Cadastrar Usuário Duplicado

* **Endpoint:** `POST /api/cadastrar`
* **Objetivo:** Verificar se a API impede o cadastro de um usuário com nome já existente.
* **Pré-condição:** Usuário `PostmanUser_A` já cadastrado pela requisição AT01.
* **Corpo da Requisição (JSON):** (Mesmo payload da AT01)
    ```json
    {
      "nome_usuario": "PostmanUser_A",
      "imagem_base64": "[BASE64_REAL_IMAGEM_VALIDA_A]" 
    }
    ```
* **Resultados dos Testes:**
    * ✅ **PASS:** Status code is 409 Conflict
    * ✅ **PASS:** Response body includes error message for duplicate user
* **Status HTTP Retornado:** `409 Conflict`

---

### AT03 - POST Validar Usuario Cadastrado

* **Endpoint:** `POST /api/validar`
* **Objetivo:** Verificar a identificação correta de um usuário previamente cadastrado.
* **Pré-condição:** Usuário `PostmanUser_A` já cadastrado pela requisição AT01.
* **Corpo da Requisição (JSON):**
    ```json
    {
      "imagem_base64": "[BASE64_REAL_IMAGEM_VALIDA_A_NOVA]" 
    }
    ``` 
    *(Nota: Utiliza uma segunda imagem válida do mesmo usuário)*
* **Resultados dos Testes:**
    * ✅ **PASS:** Status code is 200 OK
    * ✅ **PASS:** Response body shows identified user A (inclui `identificado: true`, `usuario: "PostmanUser_A"`, `distancia < 0.6`)
* **Status HTTP Retornado:** `200 OK`

---

### AT04 - POST Validar Usuario Nao Cadastrado

* **Endpoint:** `POST /api/validar`
* **Objetivo:** Verificar se a API retorna "não encontrado" para um rosto não cadastrado.
* **Pré-condição:** O usuário correspondente à imagem de teste (`PostmanUser_B`) não está cadastrado.
* **Corpo da Requisição (JSON):**
    ```json
    {
      "imagem_base64": "[BASE64_REAL_IMAGEM_VALIDA_B]" 
    }
    ```
    *(Nota: Utiliza imagem de um usuário diferente e não cadastrado)*
* **Resultados dos Testes:**
    * ✅ **PASS:** Status code is 404 Not Found
    * ✅ **PASS:** Response body shows user not found (inclui `identificado: false`, `usuario: null`, `distancia >= 0.6`)
* **Status HTTP Retornado:** `404 Not Found`

---

**Observações:**
* Os placeholders `[BASE64_REAL_...]` nos corpos das requisições referem-se às strings Base64 reais utilizadas durante a execução dos testes no Postman.
* O threshold de distância para reconhecimento (`THRESH`) configurado na API é `0.6`.

## Testes

Este projeto inclui um plano de testes manuais e uma suíte de testes automatizados para a API.

- [Link Postman](https://app.getpostman.com/join-team?invite_code=7c6cd230f29383186cb656b0fd23818099748924af3c29c2645eb7cd8bba78af&target_code=329519929f3645977769c149b324b0fd)
- [Link para repo da API](https://github.com/NExT-fiap2025/api-reconhecimento-facial.git)

* **Plano de Testes Manuais:** Detalhes sobre os casos de teste manuais, dados de entrada/saída e procedimentos podem ser encontrados no texto acima.
* **Testes Automatizados (Postman):** Uma coleção do Postman com testes automatizados para os endpoints principais está disponível em `postman/API_Reconhecimento_Facial.postman_collection.json`. 
    * **Como Executar:** Importe este arquivo no Postman e use o "Collection Runner" para executar os testes contra a API (certifique-se de que o backend `app.py` esteja rodando localmente em `http://127.0.0.1:5000`).
