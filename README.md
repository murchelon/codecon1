# codecon1
https://github.com/codecon-dev/desafio-1-1s-vs-3j?tab=readme-ov-file

There is this video on Youtube, where they propose a battle between 3 junior and 1 senior developer.
They have to import and parse a 100k registers json file and later, provide 6 endpoints to exibit
this data in different formats and aggregations.
Also, there are performance restrictions and 1 of those endpoints must act as an examiner of the other,
providing statistics about the API, when called.

All of this must be done without AI and within 1 hour.

I plan to tackle this challange with the following considerations:

- I will allow myself to use google as much as needed. There is AI in the begining of the results, where I will skip (by scrolling fast)
- I will try not to check my own code, but I might
- I'm sure I wont do it in 1 hour.

The plan:

- Use Node for the API
- SQL Lite for the data in memmory


The API:

- 1 endpoint to recieve the JSON with the data
- parse it all, saves in sql lite in memory
- define api as ready
- endpoints became available


The JSON register:

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

JSON Register Example:

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

Fluxo criacao boilerplate:
https://medium.com/@gabrieldrouin/node-js-2025-guide-how-to-setup-express-js-with-typescript-eslint-and-prettier-b342cd21c30d#2245


1 - Cria nvmrc
node -v > .nvmrc

2 - cria package.json:
npm init -y

3 - add "type": "module" no package para usar ES Modules

4 - express
npm i express

5 - cria index.js

6 - instala TS:
npm i -D typescript @types/node @types/express @tsconfig/node23
npx tsc --init

7 - instala tsx (para rodar com watch: npx tsx --watch src/index.ts)
npm i -D tsx

8 - install Prettier e cria o arquivo de config e ignore
npm i -D prettier
echo '{}' > .prettierrc
echo 'dist' > .prettierignore

9 - cria VS Code configuration files
mkdir .vscode
cd .vscode
echo '{}' > extensions.json
echo '{}' > settings.json

10 - instala ESLint
npm i -D eslint typescript-eslint @eslint/js eslint-plugin-perfectionist
echo '' > eslint.config.js

11 - criar .env e adicionar no gitignore
echo PORT=3000 > .env
echo PORT= > .env.example

12 - Instalar Vitest
npm i -D vitest @vitest/coverage-v8 @vitest/eslint-plugin
mkdir -p src/__tests__
mkdir -p src/utils
echo '' > src/__tests__/sum.spec.ts
echo '' > src/utils/sum.ts
echo '' > tsconfig.build.json
echo '' > vitest.config.js



Original text from the Challenge:

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