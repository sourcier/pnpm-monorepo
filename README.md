# PNPM monorepo

## Basic setup

```sh
mkdir pnpm-monorepo
cd pnpm-monorepo
pnpm init
git init
echo "node_modules" > .gitignore
npm pkg set engines.node=">=18.18.2" // Use the same node version you installed
npm pkg set type="module"
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
