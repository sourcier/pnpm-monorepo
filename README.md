# PNPM monorepo

## Basic setup

```sh
mkdir pnpm-monorepo
cd pnpm-monorepo
pnpm init
git init
echo -e "node_modules" > .gitignore
npm pkg set engines.node=">=18.18.2" // Use the same node version you installed
npm pkg set type="module"
echo "#PNPM monorepo" > README.md
```
