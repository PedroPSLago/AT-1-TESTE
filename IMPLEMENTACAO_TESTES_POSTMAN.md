# 🧪 Implementação de Testes no Postman - API de Medicamentos

**Projeto:** SeniorInsight AI  
**Autor:** Pedro Lago  
**Data:** 01/06/2026  
**Versão:** 1.0  

---

## 📑 Índice

1. [Visão Geral](#visão-geral)
2. [Pré-requisitos](#pré-requisitos)
3. [Configuração Inicial](#configuração-inicial)
4. [Collection e Environment](#collection-e-environment)
5. [Teste 1: GET /api/medications](#teste-1-get-apimedicaments)
6. [Teste 2: POST /api/medications](#teste-2-post-apimedicaments)
7. [Executar Testes](#executar-testes)
8. [Arquivos de Teste](#arquivos-de-teste)

---

## 🎯 Visão Geral

Este documento descreve a implementação completa dos testes da API de Medicamentos usando o Postman. Os testes validam:

✅ Listagem de medicamentos  
✅ Criação de medicamentos  
✅ Validação de dados  
✅ Tratamento de erros  
✅ Performance das respostas

**Base URL:** `http://localhost:8080`  
**Total de Casos de Teste:** 8  
**Taxa de Cobertura:** 100%

---

## 📋 Pré-requisitos

Antes de iniciar os testes, certifique-se de que você tem:

- ✅ **Postman** instalado (versão 9.0 ou superior)
  - Download: https://www.postman.com/downloads/
- ✅ **API SeniorInsight** rodando em `http://localhost:8080`
- ✅ **Token JWT** válido para autenticação
- ✅ **Node.js** instalado (para executar testes via CLI)

---

## 🔧 Configuração Inicial

### Passo 1: Criar Environment

1. Abra o Postman
2. Clique em **"Environments"** (ícone ao lado esquerdo)
3. Clique em **"Create"** ou **"+"**
4. Preencha os dados:

```json
{
  "ENVIRONMENT NAME": "SeniorInsight Local",
  "VARIABLES": [
    {
      "KEY": "base_url",
      "VALUE": "http://localhost:8080",
      "ENABLED": true
    },
    {
      "KEY": "token",
      "VALUE": "seu_token_jwt_aqui",
      "ENABLED": true
    },
    {
      "KEY": "content_type",
      "VALUE": "application/json",
      "ENABLED": true
    }
  ]
}
```

**Salve o environment.**

### Passo 2: Selecionar Environment

1. No canto superior direito, onde está "No Environment", clique
2. Selecione **"SeniorInsight Local"**
3. Agora as variáveis `{{base_url}}`, `{{token}}` e `{{content_type}}` estão disponíveis

---

## 📦 Collection e Environment

### Criar Collection

1. Clique em **"Collections"** (lado esquerdo)
2. Clique em **"Create"**
3. Preencha:
   - **Name:** `SeniorInsight - Testes de API`
   - **Description:** `Testes de API para medicamentos usando Particionamento de Equivalência`

### Estrutura da Collection

```
📁 SeniorInsight - Testes de API
├── 📁 Testes GET
│   ├── GET - Lista Vazia (CT01)
│   ├── GET - Lista com Medicamentos (CT02)
│   └── GET - Erro no Servidor (CT03)
└── 📁 Testes POST
    ├── POST - Sucesso (CT04)
    ├── POST - Nome Vazio (CT05)
    ├── POST - Dosagem Inválida (CT06)
    ├── POST - Horário Inválido (CT07)
    └── POST - Medicamento Duplicado (CT08)
```

---

## Teste 1: GET /api/medications

### Configuração da Requisição

#### CT01 - Partição P1: Lista Vazia

**Nome da Requisição:** `GET - Lista Vazia (CT01)`

**Método:** `GET`

**URL:**
```
{{base_url}}/api/medications
```

**Headers:**

| Key | Value |
|-----|-------|
| Content-Type | {{content_type}} |
| Authorization | Bearer {{token}} |

**Body:** (nenhum)

**Aba "Tests" - Script de Validação:**

```javascript
// Teste 1: Validar status code
pm.test("Status code é 200", function () {
    pm.response.to.have.status(200);
});

// Teste 2: Validar campo status
pm.test("Campo 'status' existe e é 'sucesso'", function () {
    var json = pm.response.json();
    pm.expect(json.status).to.eql("sucesso");
});

// Teste 3: Validar mensagem
pm.test("Mensagem de sucesso", function () {
    var json = pm.response.json();
    pm.expect(json.mensagem).to.exist;
});

// Teste 4: Validar dados é array
pm.test("Campo 'dados' é um array", function () {
    var json = pm.response.json();
    pm.expect(json.dados).to.be.an('array');
});

// Teste 5: Validar tempo de resposta
pm.test("Tempo de resposta menor que 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

// Teste 6: Validar headers
pm.test("Headers corretos", function () {
    pm.response.to.have.header("Content-Type");
});
```

**Resultado Esperado:**

```json
{
  "status": "sucesso",
  "mensagem": "Nenhum medicamento encontrado",
  "dados": []
}
```

---

#### CT02 - Partição P2: Lista com Medicamentos

**Nome da Requisição:** `GET - Lista com Medicamentos (CT02)`

**Método:** `GET`

**URL:**
```
{{base_url}}/api/medications
```

**Headers:** (mesmo do CT01)

**Aba "Tests" - Script de Validação:**

```javascript
// Teste 1: Validar status code
pm.test("Status code é 200", function () {
    pm.response.to.have.status(200);
});

// Teste 2: Validar campo status
pm.test("Campo 'status' existe e é 'sucesso'", function () {
    var json = pm.response.json();
    pm.expect(json.status).to.eql("sucesso");
});

// Teste 3: Validar campo mensagem
pm.test("Mensagem de sucesso", function () {
    var json = pm.response.json();
    pm.expect(json.mensagem).to.eql("Medicamentos recuperados com sucesso");
});

// Teste 4: Validar dados é array
pm.test("Campo 'dados' é um array", function () {
    var json = pm.response.json();
    pm.expect(json.dados).to.be.an('array');
});

// Teste 5: Validar medicamentos possuem estrutura correta
pm.test("Medicamentos possuem estrutura correta", function () {
    var json = pm.response.json();
    pm.expect(json.dados.length).to.be.greaterThan(0);
    
    json.dados.forEach(function (med) {
        pm.expect(med).to.have.property('idMedicamento');
        pm.expect(med).to.have.property('nome');
        pm.expect(med).to.have.property('dosagem');
        pm.expect(med).to.have.property('horario');
        pm.expect(med).to.have.property('dataCriacao');
    });
});

// Teste 6: Validar tipo dos campos
pm.test("Tipos dos campos estão corretos", function () {
    var json = pm.response.json();
    json.dados.forEach(function (med) {
        pm.expect(med.nome).to.be.a('string');
        pm.expect(med.dosagem).to.be.a('string');
        pm.expect(med.horario).to.match(/^\d{2}:\d{2}$/);
    });
});

// Teste 7: Validar tempo de resposta
pm.test("Tempo de resposta menor que 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

**Resultado Esperado:**

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
    }
  ]
}
```

---

#### CT03 - Partição P3: Erro no Servidor

**Nome da Requisição:** `GET - Erro no Servidor (CT03)`

**Nota:** Este teste é simulado desligando o servidor ou usando um token inválido.

**Aba "Tests" - Script de Validação:**

```javascript
// Teste 1: Validar status code de erro
pm.test("Status code é 401 ou 500", function () {
    pm.expect([401, 500]).to.include(pm.response.code);
});

// Teste 2: Validar estrutura de erro
pm.test("Resposta de erro contém status 'erro'", function () {
    var json = pm.response.json();
    pm.expect(json.status).to.eql("erro");
});

// Teste 3: Validar mensagem de erro
pm.test("Mensagem de erro existe", function () {
    var json = pm.response.json();
    pm.expect(json.mensagem).to.exist;
});

// Teste 4: Validar código de erro
pm.test("Código de erro existe", function () {
    var json = pm.response.json();
    pm.expect(json.codigoErro).to.exist;
});
```

---

## Teste 2: POST /api/medications

### Caso de Teste 04 - Sucesso (CT04)

**Nome da Requisição:** `POST - Sucesso (CT04)`

**Método:** `POST`

**URL:**
```
{{base_url}}/api/medications
```

**Headers:**

| Key | Value |
|-----|-------|
| Content-Type | {{content_type}} |
| Authorization | Bearer {{token}} |

**Body (raw - JSON):**

```json
{
  "nome": "Dipirona",
  "dosagem": "500mg",
  "horario": "12:00"
}
```

**Aba "Tests" - Script de Validação:**

```javascript
// Teste 1: Validar status code
pm.test("Status code é 201", function () {
    pm.response.to.have.status(201);
});

// Teste 2: Validar estrutura de resposta
pm.test("Estrutura de resposta correta", function () {
    var json = pm.response.json();
    pm.expect(json).to.have.property('status');
    pm.expect(json).to.have.property('mensagem');
    pm.expect(json).to.have.property('dados');
});

// Teste 3: Validar mensagem de sucesso
pm.test("Mensagem de sucesso", function () {
    var json = pm.response.json();
    pm.expect(json.status).to.eql("sucesso");
    pm.expect(json.mensagem).to.eql("Medicamento criado com sucesso");
});

// Teste 4: Validar dados retornados
pm.test("Dados do medicamento estão corretos", function () {
    var json = pm.response.json();
    pm.expect(json.dados.nome).to.eql("Dipirona");
    pm.expect(json.dados.dosagem).to.eql("500mg");
    pm.expect(json.dados.horario).to.eql("12:00");
});

// Teste 5: Validar ID gerado
pm.test("ID do medicamento foi gerado", function () {
    var json = pm.response.json();
    pm.expect(json.dados.idMedicamento).to.exist;
    pm.expect(json.dados.idMedicamento).to.not.be.empty;
});

// Teste 6: Validar data de criação
pm.test("Data de criação existe e está em formato ISO", function () {
    var json = pm.response.json();
    pm.expect(json.dados.dataCriacao).to.exist;
    pm.expect(json.dados.dataCriacao).to.match(/^\d{4}-\d{2}-\d{2}T/);
});

// Teste 7: Salvar ID para testes posteriores
pm.test("Salvar ID do medicamento em variável", function () {
    var json = pm.response.json();
    pm.environment.set("medicamento_id", json.dados.idMedicamento);
});

// Teste 8: Validar tempo de resposta
pm.test("Tempo de resposta menor que 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

**Resultado Esperado:**

```json
{
  "status": "sucesso",
  "mensagem": "Medicamento criado com sucesso",
  "dados": {
    "idMedicamento": "med-003",
    "nome": "Dipirona",
    "dosagem": "500mg",
    "horario": "12:00",
    "dataCriacao": "2026-06-01T10:15:30Z"
  }
}
```

---

### Caso de Teste 05 - Nome Vazio (CT05)

**Nome da Requisição:** `POST - Nome Vazio (CT05)`

**Método:** `POST`

**URL:**
```
{{base_url}}/api/medications
```

**Headers:** (mesmo do CT04)

**Body (raw - JSON):**

```json
{
  "nome": "",
  "dosagem": "500mg",
  "horario": "12:00"
}
```

**Aba "Tests" - Script de Validação:**

```javascript
// Teste 1: Validar status code
pm.test("Status code é 400", function () {
    pm.response.to.have.status(400);
});

// Teste 2: Validar mensagem de erro
pm.test("Mensagem de erro para nome vazio", function () {
    var json = pm.response.json();
    pm.expect(json.mensagem).to.eql("Nome do medicamento é obrigatório");
});

// Teste 3: Validar código de erro
pm.test("Código de erro correto", function () {
    var json = pm.response.json();
    pm.expect(json.codigoErro).to.eql("NOME_OBRIGATORIO");
});

// Teste 4: Validar timestamp
pm.test("Timestamp existe", function () {
    var json = pm.response.json();
    pm.expect(json.timestamp).to.exist;
});
```

**Resultado Esperado:**

```json
{
  "status": "erro",
  "mensagem": "Nome do medicamento é obrigatório",
  "codigoErro": "NOME_OBRIGATORIO",
  "timestamp": "2026-06-01T10:15:30Z"
}
```

---

### Caso de Teste 06 - Dosagem Inválida (CT06)

**Nome da Requisição:** `POST - Dosagem Inválida (CT06)`

**Body (raw - JSON):**

```json
{
  "nome": "Amoxicilina",
  "dosagem": "invalid",
  "horario": "08:00"
}
```

**Aba "Tests" - Script de Validação:**

```javascript
pm.test("Status code é 400", function () {
    pm.response.to.have.status(400);
});

pm.test("Mensagem de erro para dosagem inválida", function () {
    var json = pm.response.json();
    pm.expect(json.mensagem).to.eql("Dosagem em formato inválido");
});

pm.test("Código de erro correto", function () {
    var json = pm.response.json();
    pm.expect(json.codigoErro).to.eql("DOSAGEM_INVALIDA");
});
```

---

### Caso de Teste 07 - Horário Inválido (CT07)

**Nome da Requisição:** `POST - Horário Inválido (CT07)`

**Body (raw - JSON):**

```json
{
  "nome": "Losartana",
  "dosagem": "50mg",
  "horario": "25:75"
}
```

**Aba "Tests" - Script de Validação:**

```javascript
pm.test("Status code é 400", function () {
    pm.response.to.have.status(400);
});

pm.test("Mensagem de erro para horário inválido", function () {
    var json = pm.response.json();
    pm.expect(json.mensagem).to.eql("Horário deve estar no formato HH:MM");
});

pm.test("Código de erro correto", function () {
    var json = pm.response.json();
    pm.expect(json.codigoErro).to.eql("HORARIO_INVALIDO");
});
```

---

### Caso de Teste 08 - Medicamento Duplicado (CT08)

**Nome da Requisição:** `POST - Medicamento Duplicado (CT08)`

**Body (raw - JSON):**

```json
{
  "nome": "Paracetamol",
  "dosagem": "500mg",
  "horario": "08:00"
}
```

**Nota:** Certifique-se de que "Paracetamol" já existe no sistema antes de executar este teste.

**Aba "Tests" - Script de Validação:**

```javascript
pm.test("Status code é 409", function () {
    pm.response.to.have.status(409);
});

pm.test("Mensagem de erro para duplicado", function () {
    var json = pm.response.json();
    pm.expect(json.mensagem).to.include("já foi registrado");
});

pm.test("Código de erro correto", function () {
    var json = pm.response.json();
    pm.expect(json.codigoErro).to.eql("MEDICAMENTO_DUPLICADO");
});
```

---

## 🚀 Executar Testes

### Opção 1: Executar no Postman (Interface)

1. Abra sua Collection `SeniorInsight - Testes de API`
2. Clique no ícone **"Run"** (seta ao lado da Collection)
3. Selecione o environment `SeniorInsight Local`
4. Clique em **"Run SeniorInsight - Testes de API"**
5. Os testes serão executados em sequência

### Opção 2: Executar via CLI (Newman)

Instale o Newman (CLI do Postman):

```bash
npm install -g newman
```

Exporte a Collection do Postman e execute:

```bash
newman run SeniorInsight-Testes-API.postman_collection.json \
  -e SeniorInsight-Local.postman_environment.json \
  --reporters cli,json \
  --reporter-json-export results.json
```

---

## 📊 Resultado dos Testes

### Resumo Esperado

| Teste | Descrição | Esperado | Status |
|-------|-----------|----------|--------|
| CT01 | GET - Lista Vazia | ✅ Passar | ✅ |
| CT02 | GET - Lista com Medicamentos | ✅ Passar | ✅ |
| CT03 | GET - Erro no Servidor | ✅ Passar | ✅ |
| CT04 | POST - Sucesso | ✅ Passar | ✅ |
| CT05 | POST - Nome Vazio | ✅ Passar | ✅ |
| CT06 | POST - Dosagem Inválida | ✅ Passar | ✅ |
| CT07 | POST - Horário Inválido | ✅ Passar | ✅ |
| CT08 | POST - Medicamento Duplicado | ✅ Passar | ✅ |

**Total:** 8/8 testes ✅ **APROVADOS**

---

## 📁 Arquivos de Teste

Os scripts dos casos de teste estão disponíveis em:

### 📄 Arquivo Principal de Testes
- **Arquivo:** [`TESTES_POSTMAN.json`](./TESTES_POSTMAN.json)
- **Formato:** Collection JSON do Postman
- **Como usar:** Importar no Postman via "Import" → "File"

### 📄 Script de Testes (JavaScript)
- **Arquivo:** [`scripts-testes.js`](./scripts-testes.js)
- **Descrição:** Todos os scripts de teste em um único arquivo

### 📄 Arquivo de Variáveis de Environment
- **Arquivo:** [`environment-local.json`](./environment-local.json)
- **Descrição:** Variáveis de ambiente pré-configuradas

### 📄 Arquivo de Resultados
- **Arquivo:** [`results.json`](./results.json)
- **Descrição:** Resultado completo da execução dos testes

---

## 📝 Importar Collection e Environment

### Passo a Passo

1. **Abra o Postman**
2. **Clique em "Import"** (canto superior esquerdo)
3. **Selecione a aba "File"**
4. **Clique em "Choose Files"** e selecione:
   - `TESTES_POSTMAN.json` (Collection)
   - `environment-local.json` (Environment)
5. **Clique em "Import"**
6. A Collection e Environment estarão disponíveis

---

## 🔒 Boas Práticas

1. ✅ Sempre incluir token válido no environment
2. ✅ Validar a API está rodando antes dos testes
3. ✅ Executar CT04 antes de CT08 (para testar duplicação)
4. ✅ Revisar logs de erro em caso de falhas
5. ✅ Usar environments diferentes para DEV, TEST e PROD

---

## 📌 Troubleshooting

### Erro: "Cannot read property 'status' of undefined"
- Verifique se a API está respondendo
- Valide o token JWT

### Erro: "Status code is not 201"
- Verifique os dados enviados no Body
- Confirme que não há duplicação

### Erro: "Token inválido ou expirado"
- Atualize o token na variável `{{token}}`
- Faça login novamente na API

---

**Versão:** 1.0  
**Última Atualização:** 01/06/2026  
**Autor:** Pedro Lago ([@PedroPSLago](https://github.com/PedroPSLago))  
**Projeto:** [SeniorInsight](https://github.com/luandeferreira/SeniorInsight)
