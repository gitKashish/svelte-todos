# Todos with Svelte 5

A minimalist web app to track you tasks ✨

![Todo (Phone)](https://github.com/user-attachments/assets/22c91ba5-0053-4dfe-909a-1f7e319319a6)

## Features
1. **Create** Todo tasks.
2. **Edit** existing tasks.
3. **Mark** todo as "Done".
4. **Filter** todos ('All', 'Active', 'Done').
5. Persistent storage with `window.localStorage`.

## Cloning the project

To customize or work on this yourself, clone the repo. `Node.js` is a prerequisite (Node v20.16.0 used in development):

```bash
# create a new project in the current directory
git clone https://github.com/gitKashish/svelte-todos

# follow up steps to install dependencies
cd svelte-todos
npm install
```

## Developing

Once you've cloned the repo and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open

# or start the server and expose the app to the devices on the network
npm run dev -- --host
```

## Building

To create a production version of the app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://kit.svelte.dev/docs/adapters) for your target environment.
