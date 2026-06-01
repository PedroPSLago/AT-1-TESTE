# 📚 Documentação de Endpoints - API de Medicamentos

**Projeto:** SeniorInsight AI  
**Autor:** Pedro Lago  
**Data:** 01/06/2026  
**Versão:** 1.0  

---

## 📑 Índice

1. [Visão Geral](#visão-geral)
2. [Endpoints Documentados](#endpoints-documentados)
3. [Endpoint 1: GET /api/medications](#endpoint-1-get-apimedicamentos)
4. [Endpoint 2: POST /api/medications](#endpoint-2-post-apimedicamentos)
5. [Códigos de Status HTTP](#códigos-de-status-http)
6. [Exemplo de Uso Completo](#exemplo-de-uso-completo)

---

## 🎯 Visão Geral

Esta documentação detalha todos os endpoints da API de **Medicamentos** do projeto SeniorInsight. A API foi desenvolvida seguindo os padrões RESTful com responses consistentes e tratamento de erros robusto.

**Base URL:**
```
http://localhost:8080
```

**Autenticação:**
```
Bearer Token (JWT)
```

**Content-Type:**
```
application/json
```

---

## 🔗 Endpoints Documentados

| Método | Endpoint | Descrição | Status |
|--------|----------|-----------|--------|
| `GET` | `/api/medications` | Listar todos os medicamentos | ✅ Implementado |
| `POST` | `/api/medications` | Criar novo medicamento | ✅ Implementado |
| `GET` | `/api/medications/{id}` | Obter medicamento por ID | 🔄 Planejado |
| `PUT` | `/api/medications/{id}` | Atualizar medicamento | 🔄 Planejado |
| `DELETE` | `/api/medications/{id}` | Deletar medicamento | 🔄 Planejado |

---

## Endpoint 1: GET /api/medications

### 📋 Descrição

Recupera a lista de todos os medicamentos cadastrados para o paciente autenticado. Este endpoint retorna um array com todos os medicamentos ou uma lista vazia se nenhum medicamento foi registrado.

### 🔗 URL

```
GET http://localhost:8080/api/medications
```

### 📤 Headers Obrigatórios

| Header | Valor | Descrição |
|--------|-------|-----------|
| `Content-Type` | `application/json` | Define o formato da requisição |
| `Authorization` | `Bearer {token}` | Token JWT de autenticação |

### 📥 Parâmetros de Consulta (Query Parameters)

```
Nenhum parâmetro obrigatório
```

### 📝 Exemplo de Requisição

#### cURL
```bash
curl -X GET "http://localhost:8080/api/medications" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

#### JavaScript (Fetch API)
```javascript
const response = await fetch('http://localhost:8080/api/medications', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer seu_token_jwt'
  }
});

const data = await response.json();
console.log(data);
```

#### Python (Requests)
```python
import requests

headers = {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer seu_token_jwt'
}

response = requests.get('http://localhost:8080/api/medications', headers=headers)
data = response.json()
print(data)
```

### ✅ Respostas Possíveis

#### 200 - OK - Medicamentos Recuperados com Sucesso

**Status Code:** `200`  
**Content-Type:** `application/json`

**Response Body:**
```json
{
  "status": "sucesso",
  "mensagem": "Medicamentos recuperados com sucesso",
  "dados": [
    {
      "idMedicamento": "med-001",
      "nome": "Paracetamol",
      "dosagem": "500mg",
      "horario": "08:00",
      "dataCriacao": "2026-05-20T10:30:00Z"
    },
    {
      "idMedicamento": "med-002",
      "nome": "Ibuprofeno",
      "dosagem": "600mg",
      "horario": "14:00",
      "dataCriacao": "2026-05-21T15:45:00Z"
    }
  ]
}
```

**Descrição dos campos:**
- `status` (string): Status da operação ("sucesso" ou "erro")
- `mensagem` (string): Mensagem descritiva da operação
- `dados` (array): Array contendo os medicamentos
  - `idMedicamento` (string): ID único do medicamento
  - `nome` (string): Nome do medicamento
  - `dosagem` (string): Dosagem (ex: 500mg, 10ml)
  - `horario` (string): Horário de administração (formato HH:MM)
  - `dataCriacao` (string): Data e hora de criação (ISO 8601)

---

#### 200 - OK - Lista Vazia

**Status Code:** `200`  
**Content-Type:** `application/json`

**Response Body:**
```json
{
  "status": "sucesso",
  "mensagem": "Nenhum medicamento encontrado",
  "dados": []
}
```

---

#### ❌ 401 - Unauthorized (Token Inválido)

**Status Code:** `401`  
**Content-Type:** `application/json`

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Token de autenticação inválido ou expirado",
  "codigoErro": "TOKEN_INVALIDO",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

#### ❌ 500 - Internal Server Error

**Status Code:** `500`  
**Content-Type:** `application/json`

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Erro ao recuperar medicamentos",
  "codigoErro": "ERRO_SERVIDOR",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

## Endpoint 2: POST /api/medications

### 📋 Descrição

Cria um novo medicamento no sistema para o paciente autenticado. O medicamento é armazenado com todas as informações fornecidas e pode ser consultado posteriormente através do GET.

### 🔗 URL

```
POST http://localhost:8080/api/medications
```

### 📤 Headers Obrigatórios

| Header | Valor | Descrição |
|--------|-------|-----------|
| `Content-Type` | `application/json` | Define o formato da requisição |
| `Authorization` | `Bearer {token}` | Token JWT de autenticação |

### 📥 Body (Requisição)

```json
{
  "nome": "string (obrigatório, 3-100 caracteres)",
  "dosagem": "string (obrigatório, ex: 500mg, 10ml, 2 comprimidos)",
  "horario": "string (obrigatório, formato HH:MM em 24h)"
}
```

### ✔️ Validações

| Campo | Tipo | Requerido | Validação | Exemplo |
|-------|------|-----------|-----------|---------|
| `nome` | string | ✅ Sim | 3-100 caracteres, sem duplicação | "Ibuprofeno" |
| `dosagem` | string | ✅ Sim | Formato válido (número + unidade) | "600mg" |
| `horario` | string | ✅ Sim | Formato HH:MM (00:00 a 23:59) | "14:00" |

---

### 📝 Exemplos de Requisição

#### cURL
```bash
curl -X POST "http://localhost:8080/api/medications" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -d '{
    "nome": "Ibuprofeno",
    "dosagem": "600mg",
    "horario": "14:00"
  }'
```

#### JavaScript (Fetch API)
```javascript
const medicamento = {
  nome: "Ibuprofeno",
  dosagem: "600mg",
  horario: "14:00"
};

const response = await fetch('http://localhost:8080/api/medications', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer seu_token_jwt'
  },
  body: JSON.stringify(medicamento)
});

const data = await response.json();
console.log(data);
```

#### Python (Requests)
```python
import requests
import json

headers = {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer seu_token_jwt'
}

payload = {
    "nome": "Ibuprofeno",
    "dosagem": "600mg",
    "horario": "14:00"
}

response = requests.post('http://localhost:8080/api/medications', 
                        headers=headers, 
                        json=payload)
data = response.json()
print(data)
```

---

### ✅ Respostas Possíveis

#### 201 - Created (Sucesso)

**Status Code:** `201`  
**Content-Type:** `application/json`

**Response Body:**
```json
{
  "status": "sucesso",
  "mensagem": "Medicamento criado com sucesso",
  "dados": {
    "idMedicamento": "med-003",
    "nome": "Ibuprofeno",
    "dosagem": "600mg",
    "horario": "14:00",
    "dataCriacao": "2026-06-01T10:15:30Z"
  }
}
```

---

#### ❌ 400 - Bad Request (Nome Obrigatório)

**Status Code:** `400`  
**Content-Type:** `application/json`

**Request Body:**
```json
{
  "nome": "",
  "dosagem": "500mg",
  "horario": "12:00"
}
```

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Nome do medicamento é obrigatório",
  "codigoErro": "NOME_OBRIGATORIO",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

#### ❌ 400 - Bad Request (Dosagem Inválida)

**Status Code:** `400`  
**Content-Type:** `application/json`

**Request Body:**
```json
{
  "nome": "Amoxicilina",
  "dosagem": "invalid",
  "horario": "08:00"
}
```

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Dosagem em formato inválido",
  "codigoErro": "DOSAGEM_INVALIDA",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

#### ❌ 400 - Bad Request (Horário Inválido)

**Status Code:** `400`  
**Content-Type:** `application/json`

**Request Body:**
```json
{
  "nome": "Losartana",
  "dosagem": "50mg",
  "horario": "25:75"
}
```

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Horário deve estar no formato HH:MM",
  "codigoErro": "HORARIO_INVALIDO",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

#### ❌ 401 - Unauthorized

**Status Code:** `401`  
**Content-Type:** `application/json`

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Token de autenticação inválido ou expirado",
  "codigoErro": "TOKEN_INVALIDO",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

#### ❌ 409 - Conflict (Medicamento Duplicado)

**Status Code:** `409`  
**Content-Type:** `application/json`

**Request Body:**
```json
{
  "nome": "Paracetamol",
  "dosagem": "500mg",
  "horario": "08:00"
}
```

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Este medicamento já foi registrado para este paciente",
  "codigoErro": "MEDICAMENTO_DUPLICADO",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

#### ❌ 500 - Internal Server Error

**Status Code:** `500`  
**Content-Type:** `application/json`

**Response Body:**
```json
{
  "status": "erro",
  "mensagem": "Erro ao criar medicamento",
  "codigoErro": "ERRO_SERVIDOR",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

## 📊 Códigos de Status HTTP

| Código | Significado | Descrição |
|--------|-------------|-----------|
| `200` | OK | Requisição bem-sucedida (GET) |
| `201` | Created | Recurso criado com sucesso (POST) |
| `400` | Bad Request | Dados inválidos ou incompletos |
| `401` | Unauthorized | Token inválido ou expirado |
| `409` | Conflict | Recurso duplicado |
| `500` | Internal Server Error | Erro no servidor |

---

## 🔍 Códigos de Erro Customizados

| Código | Significado | Causa Comum |
|--------|-------------|-----------|
| `NOME_OBRIGATORIO` | Nome vazio | Campo nome não preenchido |
| `DOSAGEM_INVALIDA` | Formato de dosagem inválido | Dosagem não segue padrão |
| `HORARIO_INVALIDO` | Formato de horário inválido | Horário não está em HH:MM |
| `MEDICAMENTO_DUPLICADO` | Medicamento já existe | Tentativa de registrar medicamento duplicado |
| `TOKEN_INVALIDO` | Token expirado ou inválido | Falha na autenticação |
| `ERRO_SERVIDOR` | Erro interno | Falha na conexão com banco de dados |

---

## 💡 Exemplo de Uso Completo

### Fluxo Prático: Listar e Criar Medicamentos

**Passo 1: Obter token de autenticação**
```bash
# Primeiro, faça login para obter um token
curl -X POST "http://localhost:8080/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "usuario@example.com",
    "senha": "sua_senha"
  }'

# Resposta:
# {
#   "status": "sucesso",
#   "dados": {
#     "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
#   }
# }
```

**Passo 2: Listar medicamentos existentes**
```bash
curl -X GET "http://localhost:8080/api/medications" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

**Passo 3: Criar novo medicamento**
```bash
curl -X POST "http://localhost:8080/api/medications" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -d '{
    "nome": "Dipirona",
    "dosagem": "500mg",
    "horario": "12:00"
  }'
```

**Passo 4: Verificar novo medicamento na lista**
```bash
curl -X GET "http://localhost:8080/api/medications" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

---

## 🔒 Segurança

- ✅ Todas as requisições requerem autenticação via Bearer Token (JWT)
- ✅ Tokens expiram após 24 horas
- ✅ Senhas são hash utilizando BCrypt
- ✅ HTTPS é obrigatório em produção
- ✅ Rate limiting: 100 requisições por minuto por IP

---

## 📌 Boas Práticas

1. **Sempre incluir o token de autenticação** nos headers
2. **Validar os dados** antes de enviar para a API
3. **Tratar erros** apropriadamente na aplicação cliente
4. **Usar timeouts** para requisições (recomendado: 30s)
5. **Implementar retry logic** para falhas temporárias
6. **Não expor tokens** em logs ou mensagens de erro

---

## 🚀 Próximas Funcionalidades

- [ ] Endpoint GET `/api/medications/{id}` - Obter medicamento específico
- [ ] Endpoint PUT `/api/medications/{id}` - Atualizar medicamento
- [ ] Endpoint DELETE `/api/medications/{id}` - Deletar medicamento
- [ ] Filtros avançados (por data, horário, etc)
- [ ] Paginação para listas grandes
- [ ] Cache para melhorar performance

---

**Versão:** 1.0  
**Última Atualização:** 01/06/2026  
**Autor:** Pedro Lago ([@PedroPSLago](https://github.com/PedroPSLago))  
**Projeto:** [SeniorInsight](https://github.com/luandeferreira/SeniorInsight)
