# Svelte Monorepo

This is an example monorepo using Svelte, initially based on LevelUpTuts' [Monorepos With Pnpm](https://levelup.video/tutorials/monorepos-with-pnpm) course.

Setup instructions are in `setup.md`. This is just shorthand notes I made based on the course.

Some extra info is in `questions.md`. I usually keep note of any questions I have and then ask Claude about them afterward. The answers in that document should be treated accordingly.

Since I use Svelte pretty much exclusively and I was learning about Monorepos to help with a complex Svelte app that I develop, I've also documented (and will continue to update) some Svelte specific info.

## Svelte

I've divided the monorepo's projects into three folders: **apps**, **sites**, and **packages**. I don't think separating apps and sites is particularly conventional but I've decided to since they're created and built differently.

### Apps

`apps` contains projects that were created with `pnpm create vite` and the **Svelte** option.

Projects created this way compile to a single javascript file and are great when:

- you're first learning Svelte
- you want a single script file to embed in an html page
- you're making a SPA and want to keep things as simple as possible
- you're making a SPA and don't need any SvelteKit functionality (this could use elaboration)

### Sites

`sites` contains projects that were created with `npx sv create` and the **SvelteKit Minimal** option.

These projects are best for actual websites. SvelteKit has a lot of functionality on top of regular Svelte which is a bit beyond this README. I've spent the last few years getting pretty good at Svelte but I've only used SvelteKit a little bit since I haven't needed to deploy many sites until recently.

### Packages

`packages` contains projects that were created with `npx sv create` and the **SvelteKit Library** option.

These projects are great for dependent functionality that your apps and sites use. You have to export any components or other files that you want to use, by declaring them in `lib/index.ts`.

Svelte Library projects also have a great testing solution in that you can set up a test page in the `routes` folder. Nothing in the `routes` folder becomes part of your built library so it's perfect for building a testing environment for your library. I've found this is a great alternative to writing unit tests.

## Deploying to Vercel

Deploying specific projects to Vercel is super easy! Just visit their [Monorepos](https://vercel.com/docs/monorepos) page and follow the instructions - basically you just add a new project, import the whole repository, and then another screen appears where you select the directory you want to use for that project.

You may also wish to set up related projects, the instructions for which are also on that Monorepos page. This will help ensure that pre-production deployments can speak to each other, i.e. that your backend and frontend use the same urls for their API.
