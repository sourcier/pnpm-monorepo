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
