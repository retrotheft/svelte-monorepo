# Svelte Monorepo Setup

This guide is based on Level Up Tuts' course on monorepos with pnpm workspaces.

## Setup

- `pnpm init`
- create `apps`, `packages`, `sites`
- `pnpm i` in each
- add `pnpm-workspace.yaml`
- add subfolders:

```yaml
packages:
   - "packages/**"
   - "apps/**"
   - "sites/**"
```

- add `@{organisation}` to sub package.json names
- add `@{organisation}` to root package.json

## Scripts

- add script shortcuts for sub projects to root (I swapped the order from Scott's course to keep the command the same when typing)

```json
"scripts": { // -F means filter
  "dev:library": "pnpm -F @svelte-monorepo/svelte-library-example run dev",
  "dev:spa": "pnpm -F @svelte-monorepo/svelte-spa-example run dev",
  "dev:site": "pnpm -F @svelte-monorepo/svelte-site-example run dev"
}
```

- to run scripts from root just use:

```sh
pnpm dev:spa
```

- add recursive dev script (optional)

```json
"scripts": { // -r means recursive
   "dev": "pnpm -r run dev"
}
```

## Imports

- make each subproject an `esm` project (though they probably are already if svelte)

```json
"type": "module" // turns project into esm, allowing import syntax
```

- add one subproject dependency to another

```json
"dependencies": {
   "@svelte-monorepo/svelte-library-example": "0.0.1"
}
```
- at this point it's important to mention semantic versioning and especially how breaking changes work with 0s

- setup exported library component to import and remember to add to index.ts!

svelte-library-example/src/lib/index.ts
```ts
export {default as Counter} from "$lib/components/Counter.svelte"
```

- run `pnpm i` in root folder. Now you should see the dependency inside the depender's node_modules as a symlink
- `pnpm build` the dependency.
- implement import from dependency in depender
- run depender to confirm is working

## Versioning

- to tell `pnpm` to always look inside the monorepo for versions, change the dependency to:

```json
"dependencies": {
   "@svelte-monorepo/svelte-library-example": "workspace:0.0.1"
}
```

- to always get the latest version:

```json
"dependencies": {
   "@svelte-monorepo/svelte-library-example": "workspace:*"
}
```

## Engines

- to specifiy `node` and `pnpm` versions for the monorepo: **(highly recommended!)**

~/package.json
```json
"engines": {
   "node": ">=20.10.0",
   "pnpm": ">=8.11.0"
}
```

## Installing External Dependencies

- `pnpm` will warn you if you try to add a package to root
- to add a dependency to a particular subproject, use:

```sh
pnpm -F @svelte-monorepo/svelte-spa-example add {package-name}
```

- alternatively just change directories to install to a particular subproject

## Updating Versions and Keeping in Sync

- the root `node_modules/.pnpm` folder will keep multiple versions of subproject dependencies
- to keep external dependencies in sync you can set up an update all script:

```json
"scripts": {
   "update:all": "pnpm -r udpate -i -L" // -i means interactive, -L means latest
}
```

- run `pnpm i` after updating!

## Installing Dependencies to Root

- to install dependencies to root without getting the standard warning, use:

```sh
pnpm add -w {package-name}
```

- Now you can import dependencies in each subproject just as you would their own dependencies.
- This can be a big problem if you publish the subproject, as the dependency isn't in its package.json!
- default to installing to subprojects

## Cleaning node_modules

```json
"scripts": {
   "clean": "find ./ -name node_modules -type d -exec rm -rf {} +"
}
```

- this will find all directories named `node_modules` and run `rm -rf` in each to delete everything inside each one

## List and Why

- to get information on particular dependencies you can use the following, either in root for all subprojects, or by filtering for a particular subproject:

```sh
pnpm list -r svelte-mainloop
pnpm why -r svelte-mainloop
pnpm -F @svelte-monorepo/svelte-library-example list svelte-mainloop
pnpm -F @svelte-monorepo/svelte-library-example why svelte-mainloop
```
