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

## Linting

```sh
pnpm create @eslint/config --config airbnb-typescript
touch .eslintignore
echo "coverage\npublic\ndist\npnpm-lock.yaml\npnpm-workspace.yaml" > .eslintignore
```

### Update eslint config

Set the root property to true in the ESLint config.

```js
module.exports = {
  root: true,
  extends: ['airbnb-typescript'],
};
```

Integrating Prettier with ESLint

```sh
pnpm add -D eslint-config-prettier eslint-plugin-prettier
```

Add the `plugin:prettier/recommended` as the last element in the extends property in the ESLint config.

```js
module.exports = {
  root: true,
  extends: ['airbnb-typescript', 'plugin:prettier/recommended'],
};
```

Create scripts

```sh
pnpm pkg set scripts.lint="eslint ."
pnpm pkg set scripts.format="prettier --write ."
```
