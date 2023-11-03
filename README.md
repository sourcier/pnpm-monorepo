# PNPM monorepo

## Basic setup

```sh
mkdir pnpm-monorepo
cd pnpm-monorepo
pnpm init
git init
echo "node_modules" > .gitignore
pnpm pkg set engines.node=">=18.18.2" // Use the same node version you installed
pnpm pkg set type="module"
```

## Code formatter

```sh
pnpm add -D prettier
echo '{\n\t"singleQuote": true\n}' > .prettierrc.json
echo "coverage\npublic\ndist\npnpm-lock.yaml\npnpm-workspace.yaml" > .prettierignore
```

[VS Code plugin](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

```sh
mkdir .vscode && touch .vscode/settings.json
echo '{\n\t"editor.formatOnSave": true,\n\t"editor.defaultFormatter": "esbenp.prettier-vscode"\n}' > .vscode/settings.json
```

# TypeScript

```sh
pnpm i -D typescript @types/node
echo '{\n"compilerOptions": {\n\t"target": "ESNext",\n\t"module": "ESNext",\n\t"strict": true,\n\t"esModuleInterop": true,\n\t"skipLibCheck": true,\n\t"forceConsistentCasingInFileNames": true\n\t}\n}' > tsconfig.json
```

## Linting

```sh
pnpm add -D eslint-config-prettier eslint-plugin-prettier
pnpm create @eslint/config --config airbnb,airbnb-typescript
echo "coverage\npublic\ndist\npnpm-lock.yaml\npnpm-workspace.yaml\n.eslintrc.cjs\ncommitlint.config.js" > .eslintignore
pnpm pkg set scripts.lint="eslint ."
pnpm pkg set scripts.format="prettier --write ."
```

### Update eslint config

- Set the root property to true in the ESLint config.
- Add the `plugin:prettier/recommended` as the last element in the extends property in the ESLint config.
- Add `parserOptions` config

```js
module.exports = {
  root: true,
  extends: ['airbnb', 'airbnb-typescript', 'plugin:prettier/recommended'],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: ['./tsconfig.json'],
  },
};
```

## Pre-commit hook validation

```sh
  pnpm add -D @commitlint/cli @commitlint/config-conventional
  echo "export default {\n\textends: ['@commitlint/config-conventional']\n};" > commitlint.config.js
  pnpm add -D husky lint-staged
  pnpm husky install
  pnpm husky add .husky/pre-commit "pnpm lint-staged"
  pnpm husky add .husky/commit-msg  'npx --no -- commitlint --edit ${1}'
  pnpm pkg set scripts.prepare="husky install"
  echo '{\n\t"**/*.{js,ts,tsx}": [\n\t\t"eslint --fix"\n\t],\n\t"**/*": "prettier --write --ignore-unknown"\n}' > .lintstagedrc
```

## Workspace config

```sh
echo "packages:\n  - 'apps/*'\n  - 'packages/*'" > pnpm-workspaces.yaml
mkdir apps packages
touch apps/.gitkeep packages/.gitkeep
```

## Create a library package

```sh
cd packages
pnpm create vite common --template vanilla-ts
cd ../
pnpm install
npm pkg set scripts.common="pnpm --filter common"
```

Update main.ts file

```ts
export const isEmpty = (data: any) => data === null || data === undefined;

export const isObject = (data: any) => data && typeof data === 'object';

export const isBlank = (data: any) =>
  isEmpty(data) ||
  (Array.isArray(data) && data.length === 0) ||
  (isObject(data) && Object.keys(data).length === 0) ||
  (typeof data === 'string' && data.trim().length === 0);
```

Update config to build package in library mode

```sh
cd packages/common
rm -rf src/style.css src/counter.ts src/typescript.svg index.html public
pnpm add -D vite-plugin-dts
touch vite.config.ts
```

Update the vite.config.ts file,

```js
import { defineConfig } from 'vite';
import { resolve } from 'path';
import dts from 'vite-plugin-dts';

// https://vitejs.dev/config/
export default defineConfig({
  build: { lib: { entry: resolve(__dirname, 'src/main.ts'), formats: ['es'] } },
  resolve: { alias: { src: resolve('src/') } },
  plugins: [dts()],
});
```

Update package.json

```js
{
 ...,
 "main": "./dist/common.js",
 "types": "./dist/main.d.ts",
}
```

## Create a web app

```sh
cd apps
pnpm create vite web-app --template react-ts
cd ../
pnpm install
npm pkg set scripts.app="pnpm --filter web-app"
```

Install the common package as a dependency in our web app by updating the web-app package.json.

```js
"dependencies": {
 "common": "workspace:*",
 ...,
 }
```

- Run `pnpm install` to link packages
- Run `pnpm common build` so that the common package can be found by the web-app server

Update App.tsx

```tsx
import { isBlank } from 'common';
import './App.css';

const App = () => {
  return (
    <>
      <p>undefined isBlank - {isBlank(undefined) ? 'true' : 'false'}</p>
      <p>false isBlank - {isBlank(false) ? 'true' : 'false'}</p>
      <p>true isBlank - {isBlank(true) ? 'true' : 'false'}</p>
      <p>Empty object isBlank - {isBlank({}) ? 'true' : 'false'}</p>
    </>
  );
};

export default App;
```

## Dev mode

```
pnpm common build --watch
pnpm web-app dev
```
