# codecon1

Fluxo:
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