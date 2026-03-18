# 🏥 Voll.med API

API REST desenvolvida em Java com Spring Boot durante o curso **Java e Spring Boot** da [Alura](https://www.alura.com.br/). O projeto simula o backend de uma clínica médica, com funcionalidades de cadastro de médicos e pacientes, agendamento e cancelamento de consultas, autenticação com JWT e documentação via Swagger.

---

## 🛠️ Tecnologias Utilizadas

- **Java 21**
- **Spring Boot 3**
- **Spring Security** — autenticação e autorização
- **Spring Data JPA** — acesso ao banco de dados
- **PostgreSQL** — banco de dados relacional
- **Flyway** — migrations e versionamento do banco
- **Lombok** — redução de código boilerplate
- **JWT (Auth0)** — autenticação stateless via token
- **SpringDoc / Swagger UI** — documentação da API
- **Maven** — gerenciamento de dependências

---

## ✅ Funcionalidades

- Cadastro, edição, listagem e exclusão lógica de **Médicos**
- Cadastro, edição, listagem e exclusão lógica de **Pacientes**
- **Agendamento de consultas** com validações de negócio:
  - Antecedência mínima de 30 minutos
  - Horário de funcionamento da clínica (segunda a sábado, 07h–18h)
  - Médico deve estar ativo e disponível no horário
  - Paciente deve estar ativo e sem outra consulta no mesmo dia
  - Seleção automática de médico disponível por especialidade
- **Cancelamento de consultas** com antecedência mínima de 24h
- **Autenticação via JWT** — rotas protegidas exigem token
- **Documentação interativa** via Swagger UI
- **Testes automatizados** de controller e repository

---

## 🗂️ Estrutura do Projeto

```
src/
├── main/
│   ├── java/med/voll/api/
│   │   ├── controller/
│   │   ├── domain/
│   │   │   ├── consulta/
│   │   │   ├── medico/
│   │   │   ├── paciente/
│   │   │   ├── endereco/
│   │   │   └── usuario/
│   │   └── infra/
│   │       ├── exception/
│   │       ├── security/
│   │       └── springdoc/
│   └── resources/
│       ├── application.properties
│       ├── application-prod.properties
│       ├── application-test.properties
│       └── db/migration/
└── test/                    # Testes automatizados
```

---

## 🗃️ Estrutura do Banco de Dados

```
medicos
├── id (PK)
├── nome, email, crm, telefone
├── especialidade
├── endereco (logradouro, bairro, cep, cidade, uf, numero, complemento)
└── ativo

pacientes
├── id (PK)
├── nome, email, cpf, telefone
├── endereco (logradouro, bairro, cep, cidade, uf, numero, complemento)
└── ativo

usuarios
├── id (PK)
├── login
└── senha

consultas
├── id (PK)
├── medico_id (FK → medicos)
├── paciente_id (FK → pacientes)
├── data
└── motivo_cancelamento
```

---

## ▶️ Como Executar

### Pré-requisitos

- [Java 21+](https://www.oracle.com/java/technologies/downloads/)
- [PostgreSQL](https://www.postgresql.org/download/) instalado e rodando
- [Maven](https://maven.apache.org/) (ou usar o `./mvnw` incluído no projeto)

### Passo a passo

1. **Clone o repositório**
   ```bash
   git clone https://github.com/tiagoeich/Voll.med.git
   ```

2. **Crie o banco de dados no PostgreSQL**
   ```sql
   CREATE DATABASE vollmed;
   ```

3. **Configure as variáveis de ambiente**

   Antes de executar, defina as variáveis de ambiente na sua máquina:
   - `DATASOURCE_URL` → ex: `jdbc:postgresql://localhost:5432/vollmed`
   - `DATASOURCE_USERNAME` → seu usuário do PostgreSQL
   - `DATASOURCE_PASSWORD` → sua senha do PostgreSQL
   ```

4. **Execute o projeto**
   ```bash
   ./mvnw spring-boot:run
   ```
   > O Flyway aplicará as migrations automaticamente na primeira execução.

5. **Acesse a documentação da API**

   Abra no navegador: `http://localhost:8080/swagger-ui.html`

---

## 📌 Endpoints Principais

| Método | Rota | Descrição | Auth |
|--------|------|-----------|------|
| `POST` | `/login` | Autenticação, retorna token JWT | ❌ |
| `POST` | `/medicos` | Cadastrar médico | ✅ |
| `GET` | `/medicos` | Listar médicos ativos (paginado) | ✅ |
| `PUT` | `/medicos` | Atualizar médico | ✅ |
| `DELETE` | `/medicos/{id}` | Excluir médico (exclusão lógica) | ✅ |
| `GET` | `/medicos/{id}` | Detalhar médico | ✅ |
| `POST` | `/pacientes` | Cadastrar paciente | ✅ |
| `GET` | `/pacientes` | Listar pacientes ativos (paginado) | ✅ |
| `PUT` | `/pacientes` | Atualizar paciente | ✅ |
| `DELETE` | `/pacientes/{id}` | Excluir paciente (exclusão lógica) | ✅ |
| `GET` | `/pacientes/{id}` | Detalhar paciente | ✅ |
| `POST` | `/consultas` | Agendar consulta | ✅ |
| `DELETE` | `/consultas` | Cancelar consulta | ✅ |

> ✅ Requer token JWT no header: `Authorization: Bearer {token}`

---

## 📋 Exemplos de Requisição

**Login**
```json
POST /login
{
  "login": "user@vollmed.com",
  "senha": "123456"
}
```

**Cadastrar Médico**
```json
POST /medicos
{
  "nome": "Maria Souza",
  "email": "maria@vollmed.com",
  "telefone": "51999999999",
  "crm": "123456",
  "especialidade": "CARDIOLOGIA",
  "endereco": {
    "logradouro": "Rua das Flores",
    "bairro": "Centro",
    "cep": "01310100",
    "cidade": "São Leopoldo",
    "uf": "RS",
    "numero": "100"
  }
}
```

**Agendar Consulta**
```json
POST /consultas
{
  "idMedico": 1,
  "idPaciente": 1,
  "data": "2026-04-10T10:00:00",
  "especialidade": "CARDIOLOGIA"
}
```

**Cancelar Consulta**
```json
DELETE /consultas
{
  "idConsulta": 1,
  "motivo": "PACIENTE_DESISTIU"
}
```

---

## 🔐 Autenticação

A API utiliza **JWT (JSON Web Token)** com validade de 2 horas. Para acessar os endpoints protegidos, faça login em `POST /login` e inclua o token retornado no header das requisições:

```
Authorization: Bearer {seu_token_aqui}
```

---

## 📖 Desenvolvido durante o curso

Projeto desenvolvido acompanhando o curso **Java e Spring Boot** da [Alura](https://www.alura.com.br/).
