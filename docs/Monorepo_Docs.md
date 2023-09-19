# Scouting System Monorepo Setup Documentation

<!--v0.3.0-SNAPSHOT-->
<!--By Josiah Fu and ChatGPT-->

Instructions for how to set up a monorepo that includes several react apps and a shared components package.

### Benefits of a Monorepo

There are several benefits to using a monorepo for managing your projects:

- **Code Reusability**: Sharing code and components becomes seamless, reducing duplication and maintaining consistency.
- **Simplified Dependency Management**: All projects within the monorepo can share dependencies, reducing version conflicts and making updates easier.
- **Centralized Tooling**: You can configure common development tools and scripts at the monorepo level, ensuring consistency across projects.
- **Easier Cross-Project Changes**: Changes that affect multiple projects can be made in a single commit, making cross-project updates more manageable.
- **Streamlined Collaboration**: Collaborators can easily switch between projects and contribute changes to different parts of the codebase.

## NPM Workspaces

[NPM workspaces](https://docs.npmjs.com/cli/v7/using-npm/workspaces) allow you to manage multiple packages within a single, top-level, root package. By defining a `"workspaces"` field in your `package.json`, you specify directories that contain packages you want to treat as workspaces. NPM then links these workspaces together, enabling seamless dependency resolution and making development and testing more efficient.

For instance, in your monorepo's `package.json`, the following configuration enables workspaces:

```json
"workspaces": [
  "shared-components",
  "apps/*"
]
```

This configuration specifies that the `apps/*` pattern (all subdirectories under `apps`) and the `shared-components` directory are workspaces.

## Prettier

[Prettier](https://prettier.io/) is a code formatting tool that helps maintain consistent code style across the entire codebase. It automatically formats your code according to predefined rules.

## Repository Structure

The monorepo is structured as follows:

```
(root)
├── apps/
│   ├── scouting-app (React app)
│   ├── super-scouting (React app)
│   └── ... (other React apps)
└── shared-components (Reusable React components)

- The `apps` directory contains individual React projects or applications.
- The `shared-components` directory contains shared React code, libraries, or components used across multiple React projects.

## Shared Components

Shared components are important to avoid duplicating code across projects. Any component that is not strictly specific to a certain app should be placed in here, even if they are for now used in only one app.

React apps use [Webpack](https://webpack.js.org/) under the hood to translate and bundle all the code and assets together. This component library will instead use [Rollup](https://rollupjs.org/), which is a bundler similar to Webpack but is geared towards libraries rather than apps.

Changes to the shared components do not immediately propogate to the apps using it, so inside the components package you need to either run the command

```bash
npm run build
```

to update the package once, or

```bash
npm run start
```

to continuously update the package.

## Steps

Follow these steps to set up your monorepo:

### 1. Initialize Package

Start by creating a new directory for your monorepo and creating a `package.json`:

```json
{
  "name": "vitruvian-scouting",
  "version": "1.0.0"
}
```

### 2. Configure Git

Initialize the git repository

```bash
git init
```

Add a `.gitignore`:

```
# dependencies
node_modules

```

### 3. Configure Workspaces

Enable NPM workspaces by adding the following to your `package.json`:

```json
"workspaces": [
  "shared-components",
  "apps/*"
]
```

This configuration allows NPM to manage packages within the specified directories as workspaces.

### 4. Create React Apps

Create your React apps within the `apps` directory using either Create React App or Vite.

#### Using Create React App

```bash
npx create-react-app apps/scouting-app --template typescript
npx create-react-app apps/super-scouting --template typescript
# Create more apps if needed
```

Remove the `.git` directory in each app since the whole monorepo is already a git repository.

```bash
rm -rf apps/scouting-app/.git
rm -rf apps/super-scouting/.git
...
```

#### Using Vite

```bash
npm init vite@latest apps/scouting-app
npm init vite@latest apps/super-scouting
# Create more apps if needed
```

Choose "React" for the framework and then "TypeScript" for the variant when prompted.

### 4. Create and Configure Shared Components Library

Create a shared components library within the `shared-components` directory using TypeScript and Rollup:

```bash
mkdir shared-components
cd shared-components
```

Create a `package.json` file with the following content:

```json
{
  "name": "shared-components",
  "version": "1.0.0",
  "main": "dist/bundle.js",
  "type": "module",
  "scripts": {
    "build": "rollup -c",
    "lint": "eslint --fix",
    "lint:check": "eslint"
  },
  "eslintConfig": {
    "extends": [
      "react-app"
    ],
    "ignorePatterns": [
      "*.config.js",
      "dist"
    ]
  }
}
```

#### Install Dependencies

```bash
npm install react
npm install --save-dev @types/react rollup-plugin-dts @rollup/plugin-commonjs @rollup/plugin-node-resolve @rollup/plugin-typescript @rollup/plugin-eslint eslint-config-react-app
```

#### TypeScript Configuration

Create a `tsconfig.json` file in the `shared-components` directory and configure it for TypeScript:

```json
{
  "compilerOptions": {
    "declaration": true,
    "outDir": "dist",
    "rootDir": "src",
    "module": "ESNext",
    "target": "ESNext",
    "moduleResolution": "node",
    "jsx": "react",
    "strict": true,
    "esModuleInterop": true,
    "noEmitOnError": true
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.tsx"
  ]
}
```

#### Rollup Configuration

Create a `rollup.config.js` file in the `shared-components` directory for Rollup bundling:

```js
import eslint from "@rollup/plugin-eslint";
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import typescript from "@rollup/plugin-typescript";

export default {
  input: "src/index.ts", // Entry point of your library
  output: {
    file: "dist/bundle.js", // Output file
    format: "cjs", // CommonJS module format
    sourcemap: true,
  },
  plugins: [
    eslint({
      throwOnWarning: process.env.CI, // Warnings fail if building for production
      throwOnError: true
    }),
    peerDepsExternal(),
    resolve(),
    commonjs(),
    typescript(),
  ],
  external: ['react'],
};
```

#### Create source files

Create these files:

```
shared-components
└── src/
    ├── components/
    │   └── ...
    └── index.ts
```

Each component will be its own file in `components/`.

`index.ts` will have content like the following as components are added, but for now can be left blank:

```
export { Component1 } from './components/Component1.tsx';
export { Component2 } from './components/Component2.tsx';
...
```

Once you are done here, move back to the monorepo root using

```bash
cd ..
```


### 5. Initialize and Configure Prettier

Install Prettier in the root directory and create a configuration file:

```bash
npm install prettier --save-dev
```

Create a `.prettierrc.json` file in the root directory.

```json
{
    "tabWidth": 4,
    "singleQuote": true,
    "jsxSingleQuote": true,
    "trailingComma": "es5",
    "bracketSpacing": true,
    "bracketSameLine": true,
    "arrowParens": "avoid",
    "endOfLine": "lf",
    "singleAttributePerLine": false
}
```

Create a `.prettierignore` file in the root directory.

```
# dependencies
node_modules

# production
build
dist

# configuration
package.json
package-lock.json
README.md
tsconfig.json
```

### 6. Add Prettier Scripts

Open your `package.json` in the root directory and add the following scripts to enable Prettier formatting:

```json
"scripts": {
  "format": "prettier --write \"**/*.{js,jsx,ts,tsx}\"",
  "format:check": "prettier --check \"**/*.{js,jsx,ts,tsx}\""
}
```

Now you can use `npm run format` to format your code and `npm run format:check` to check if your code conforms to the Prettier style.

With these steps completed, you now have a functional monorepo with React apps, a shared components library, and Prettier code formatting integrated.
