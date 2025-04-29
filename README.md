# codecon1 Challenge

There is a video on YouTube proposing a challenge: a battle between three junior developers and one senior developer.

The task is to import and parse a JSON file containing 100,000 records and then provide six endpoints to display this data in different formats and aggregations.

Additionally, there are performance constraints, and one of the endpoints must act as a monitor, providing statistics about the API when called.

All of this must be done without AI and within 1 hour.

## Observations

I plan to tackle this challenge with the following considerations:

- I will allow myself to use Google as much as needed. Since AI-generated content appears at the beginning of the search results, I will skip over it (by scrolling quickly).
- I will try not to check or revise my own code, but I might if necessary.
- I am sure I won't complete it within 1 hour, but I will try to work quickly, focusing only on what is essential (e.g., I won't worry about SQL injection).
- **Started**: 2025-04-28 21:45:00 (Found the video, started watching... then quickly stopped before seeing the solutions. I then began thinking about the challenge. The time reflects my estimated starting point.)
- Not a good start: 2 hours just creating this readme  : )

## Stack

- Node for the API
- SQLite (in-memory) for data storage
- SQL for querying the data
- Insomnia for testing

## Versions

- Node.js 23.4.0

## API Overview

- Starts in `WAITING_DATA` mode.
- 1 endpoint to receive the JSON data.
- Saves the JSON to disk, parses all records, and stores them into an in-memory SQLite database.
- Sets the API to `READY` mode.
- All other endpoints become available.

## Bonus Items 

- NOT: Automated tests
- NOT: Security
- DONE: Project documentation
- NOT: Swagger documentation
- NOT: Great performance
- NOT: Zod as middleware for validation
- NOT: GitHub Actions integration and API publishing

## Links

- [YouTube Video](https://www.youtube.com/watch?v=AFtRYXJVO-4&t=669s)
- [GitHub Repository](https://github.com/codecon-dev/desafio-1-1s-vs-3j?tab=readme-ov-file)

<br>

## Data Model 

### Test and Production Data

Files containing test and full datasets are located in the `.\Other\json` folder.



### Tables
- `USERS`
- `TEAMS`
- `PROJECTS`
- `LOGS`

### Table: USERS

| Column        | Type     | Nullable | Description                      |
|---------------|----------|----------|----------------------------------|
| id            | TEXT     | NO (PK)  | Unique identifier (UUID)         |
| name          | TEXT     | NO       | Name of the user                 |
| age           | INTEGER  | YES      | Age of the user                  |
| score         | INTEGER  | YES      | User's score                     |
| active        | INTEGER  | NO       | Whether the user is active (0/1) |
| country       | TEXT     | YES      | Country of the user              |

### Table: TEAMS

| Column        | Type     | Nullable | Description                      |
|---------------|----------|----------|----------------------------------|
| id            | INTEGER  | NO (PK)  | Primary key (auto-increment)     |
| user_id       | TEXT     | NO       | Foreign key to users(id)         |
| name          | TEXT     | NO       | Team name                       |
| leader        | INTEGER  | NO       | Whether the user is a leader (0/1) |

### Table: PROJECTS

| Column        | Type     | Nullable | Description                      |
|---------------|----------|----------|----------------------------------|
| id            | INTEGER  | NO (PK)  | Primary key (auto-increment)     |
| team_id       | INTEGER  | NO       | Foreign key to teams(id)         |
| name          | TEXT     | NO       | Project name                    |
| completed     | INTEGER  | NO       | Whether the project is completed (0/1) |

### Table: LOGS

| Column        | Type     | Nullable | Description                      |
|---------------|----------|----------|----------------------------------|
| id            | INTEGER  | NO (PK)  | Primary key (auto-increment)     |
| user_id       | TEXT     | NO       | Foreign key to users(id)         |
| date          | TEXT     | NO       | Log date (format: YYYY-MM-DD)    |
| action        | TEXT     | NO       | Action performed (login/logout)  |



<br>
The JSON register:
<br><br>


```json
{
  "id": "uuid",
  "nome": "string",
  "idade": "int",
  "score": "int",
  "ativo": "bool",
  "pais": "string",
  "equipe": {
    "nome": "string",
    "lider": "bool",
    "projetos": [{ "nome": "string", "concluido": "bool" }]
  },
  "logs": [{ "data": "YYYY-MM-DD", "acao": "login/logout" }]
}
```

<br>

JSON Register Example (kind of random):

```json
{
    "id": "8aba6e19-cfc2-4d6e-a4f9-eb82e6a4f8b2",
    "nome": "João Pedro da Mota",
    "idade": 39,
    "score": 763,
    "ativo": true,
    "pais": "Alemanha",
    "equipe": {
        "nome": "Frontend Avengers",
        "lider": true,
        "projetos": [
            {
                "nome": "Dashboard",
                "concluido": true
            },
            {
                "nome": "Mobile App",
                "concluido": false
            },
            {
                "nome": "Mobile App",
                "concluido": true
            }
        ]
    },
    "logs": [
        {
            "data": "2025-03-26",
            "acao": "login"
        },
        {
            "data": "2025-03-30",
            "acao": "logout"
        },
        {
            "data": "2025-03-30",
            "acao": "login"
        },
        {
            "data": "2025-03-28",
            "acao": "logout"
        }
    ]
}
```

<br><br>
-------------------------------
<br>

Original text from the Challenge:

<br>

# Desafio Técnico: Performance e Análise de Dados via API

## Objetivo

Você tem 1 hora para criar uma API que recebe um arquivo JSON com 100.000 usuários e oferece endpoints performáticos e bem estruturados para análise dos dados.

- [Exemplos de respostas esperadas na API](https://github.com/codecon-dev/desafio-1-1s-vs-3j/blob/main/exemplos-endpoints.json)
- [Arquivo com 100 mil usuários para importar](https://drive.google.com/file/d/1zOweCB2jidgHwirp_8oBnFyDgJKkWdDA/view?usp=sharing)
- [Arquivo com 1 mil usuário para teste](https://drive.google.com/file/d/1BX03cWxkvB_MbZN8_vtTJBDGiCufyO92/view?usp=sharing)

---

## JSON de entrada

O JSON contém uma lista de usuários com a seguinte estrutura:

```json
{
  "id": "uuid",
  "name": "string",
  "age": "int",
  "score": "int",
  "active": "bool",
  "country": "string",
  "team": {
    "name": "string",
    "leader": "bool",
    "projects": [{ "name": "string", "completed": "bool" }]
  },
  "logs": [{ "date": "YYYY-MM-DD", "action": "login/logout" }]
}
```

---

## Endpoints obrigatórios

### `POST /users`

Recebe e armazena os usuários na memória. Pode simular um banco de dados em memória.

### `GET /superusers`

- Filtro: `score >= 900` e `active = true`
- Retorna os dados e o tempo de processamento da requisição.

### `GET /top-countries`

- Agrupa os superusuários por país.
- Retorna os 5 países com maior número de superusuários.

### `GET /team-insights`

- Agrupa por `team.name`.
- Retorna: total de membros, líderes, projetos concluídos e % de membros ativos.

### `GET /active-users-per-day`

- Conta quantos logins aconteceram por data.
- Query param opcional: `?min=3000` para filtrar dias com pelo menos 3.000 logins.

### `GET /evaluation`

Ele deve executar uma autoavaliação dos principais endpoints da API e retornar um relatório de pontuação.

A avaliação deve testar:

- Se o status retornado é 200
- O tempo em milisegundos de resposta
- Se o retorno é um JSON válido

Esse endpoint pode rodar scripts de teste embutidos no próprio projeto e retornar um JSON com os resultados. Ele será utilizado para validar a entrega de forma automática e rápida.

---

## Requisitos Técnicos

- Tempo de resposta < 1s por endpoint.
- Todos os endpoints precisam retornar o tempo de processamento (em milissegundos) e a timestamp da requisição
- Código limpo, modular, com funções bem definidas.
- Pode usar qualquer linguagem/framework.
- Documentação ou explicação final vale pontos bônus.
- Não pode usar IA.