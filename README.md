# Plano de Testes Manuais - API Reconhecimento Facial V1

Este documento descreve os casos de teste manuais para as funcionalidades principais da API de reconhecimento facial.

**Endpoints Principais:**
* `POST /api/cadastrar`
* `POST /api/validar`

---

## Casos de Teste

### TC01 - Cadastrar Novo Usuário com Rosto Válido

* **Objetivo:** Verificar se um novo usuário pode ser cadastrado com sucesso quando uma imagem válida contendo um único rosto é enviada.
* **Pré-condição:** API de backend (`app.py`) está rodando.
* **Dados de Entrada (Controlados):**
    * **Requisição:** `POST /api/cadastrar`
    * **Corpo (JSON):**
        ```json
        {
          "nome_usuario": "UsuarioManual_A",
          "imagem_base64": "[IMAGEM_VALIDA_USUARIO_A_BASE64]"
        }
        ```
        *(Nota: `[IMAGEM_VALIDA_USUARIO_A_BASE64]` representa a string Base64 real de uma imagem de teste válida.)*
* **Procedimento (Passos):**
    1.  **Ação:** Usar uma ferramenta de API (Postman, Insomnia, curl) para enviar a requisição POST com os dados de entrada definidos.
        **Resultado Esperado:** Requisição enviada com sucesso.
    2.  **Ação:** Observar a resposta da API.
        **Resultado Esperado:** A resposta corresponde aos Dados de Saída (Status 201, corpo JSON de sucesso).
* **Dados de Saída (Esperados):**
    * **Status Code:** `201 Created`
    * **Corpo (JSON):**
        ```json
        {
          "status": "sucesso",
          "mensagem": "Usuario UsuarioManual_A cadastrado com sucesso."
        }
        ```

---

### TC02 - Validar Usuário Cadastrado com Rosto Válido

* **Objetivo:** Verificar se um usuário previamente cadastrado é corretamente identificado ao enviar uma nova imagem válida.
* **Pré-condição:** API rodando. Usuário "UsuarioManual_A" cadastrado (via TC01).
* **Dados de Entrada (Controlados):**
    * **Requisição:** `POST /api/validar`
    * **Corpo (JSON):**
        ```json
        {
          "imagem_base64": "[IMAGEM_VALIDA_USUARIO_A_NOVA_BASE64]"
        }
        ```
        *(Nota: Usar uma Base64 de *outra* foto válida do mesmo usuário A.)*
* **Procedimento (Passos):**
    1.  **Ação:** Enviar a requisição POST via ferramenta de API.
        **Resultado Esperado:** Requisição enviada com sucesso.
    2.  **Ação:** Observar a resposta da API.
        **Resultado Esperado:** A resposta corresponde aos Dados de Saída (Status 200, identificado=true, usuario="UsuarioManual_A", distancia < 0.6).
* **Dados de Saída (Esperados):**
    * **Status Code:** `200 OK`
    * **Corpo (JSON):**
        ```json
        {
          "status": "sucesso",
          "identificado": true,
          "usuario": "UsuarioManual_A",
          "distancia": "[VALOR_FLOAT < 0.6]"
        }
        ```

---

### TC03 - Tentar Cadastrar Usuário Duplicado

* **Objetivo:** Verificar se a API impede o cadastro de um usuário com nome já existente.
* **Pré-condição:** API rodando. Usuário "UsuarioManual_A" cadastrado.
* **Dados de Entrada (Controlados):**
    * **Requisição:** `POST /api/cadastrar`
    * **Corpo (JSON):** (Mesmo payload do TC01)
        ```json
        {
          "nome_usuario": "UsuarioManual_A",
          "imagem_base64": "[IMAGEM_VALIDA_USUARIO_A_BASE64]"
        }
        ```
* **Procedimento (Passos):**
    1.  **Ação:** Enviar a requisição POST via ferramenta de API.
        **Resultado Esperado:** Requisição enviada com sucesso.
    2.  **Ação:** Observar a resposta da API.
        **Resultado Esperado:** A resposta corresponde aos Dados de Saída (Status 409, corpo JSON de erro).
* **Dados de Saída (Esperados):**
    * **Status Code:** `409 Conflict`
    * **Corpo (JSON):**
        ```json
        {
          "erro": "Usuario UsuarioManual_A ja existe."
        }
        ```

---

### TC04 - Tentar Cadastrar Imagem Sem Rosto Detectável

* **Objetivo:** Verificar se a API rejeita o cadastro se a imagem enviada não contiver um rosto detectável.
* **Pré-condição:** API rodando.
* **Dados de Entrada (Controlados):**
    * **Requisição:** `POST /api/cadastrar`
    * **Corpo (JSON):**
        ```json
        {
          "nome_usuario": "UsuarioSemRosto",
          "imagem_base64": "[IMAGEM_SEM_ROSTO_BASE64]"
        }
        ```
        *(Nota: Usar Base64 de uma imagem sem rosto.)*
* **Procedimento (Passos):**
    1.  **Ação:** Enviar a requisição POST via ferramenta de API.
        **Resultado Esperado:** Requisição enviada com sucesso.
    2.  **Ação:** Observar a resposta da API.
        **Resultado Esperado:** A resposta corresponde aos Dados de Saída (Status 400, corpo JSON de erro).
* **Dados de Saída (Esperados):**
    * **Status Code:** `400 Bad Request`
    * **Corpo (JSON):**
        ```json
        {
          "erro": "Nao foi possivel detectar um (e apenas um) rosto na imagem."
        }
        ```

---

### TC05 - Validar Usuário Não Cadastrado

* **Objetivo:** Verificar se a API retorna "não encontrado" ao tentar validar um rosto não cadastrado.
* **Pré-condição:** API rodando. O usuário correspondente à imagem de teste NÃO está cadastrado.
* **Dados de Entrada (Controlados):**
    * **Requisição:** `POST /api/validar`
    * **Corpo (JSON):**
        ```json
        {
          "imagem_base64": "[IMAGEM_VALIDA_USUARIO_B_BASE64]"
        }
        ```
        *(Nota: Usar Base64 de um usuário ("B") não cadastrado.)*
* **Procedimento (Passos):**
    1.  **Ação:** Enviar a requisição POST via ferramenta de API.
        **Resultado Esperado:** Requisição enviada com sucesso.
    2.  **Ação:** Observar a resposta da API.
        **Resultado Esperado:** A resposta corresponde aos Dados de Saída (Status 404, identificado=false, usuario=null, distancia >= 0.6).
* **Dados de Saída (Esperados):**
    * **Status Code:** `404 Not Found`
    * **Corpo (JSON):**
        ```json
        {
          "status": "nao_encontrado",
          "identificado": false,
          "usuario": null,
          "distancia": "[VALOR_FLOAT >= 0.6]"
        }
        ```

---

## Testes

Este projeto inclui um plano de testes manuais e uma suíte de testes automatizados para a API.

* **Plano de Testes Manuais:** Detalhes sobre os casos de teste manuais, dados de entrada/saída e procedimentos podem ser encontrados no texto acima.
* **Testes Automatizados (Postman):** Uma coleção do Postman com testes automatizados para os endpoints principais está disponível em `postman/API_Reconhecimento_Facial.postman_collection.json`.
    * **Como Executar:** Importe este arquivo no Postman e use o "Collection Runner" para executar os testes contra a API (certifique-se de que o backend `app.py` esteja rodando localmente em `http://127.0.0.1:5000`).
