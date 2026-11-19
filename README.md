# nexy-compiler

The build and dev toolchain of Nexy, written in Rust. It replaces Vite and the Python compiler of
`.nexy` files with one native compiler for the client and the server. Everything Nexy builds today
keeps working the same way, faster: a project sees no difference except the timings.

## The contract

1. **Full parity first.** Every feature in the table below works exactly as it does on Vite, or
   nexy-compiler is not the default. The test matrix proves it, file by file.
2. The same `nexyconfig.py`, the same files in `src/`, the same commands (`nexy dev`, `nexy build`,
   `nexy start`), the same output layout under `__nexy__/`.
3. The `vite.config.ts` of a project keeps working: its plugins run through a plugin layer that
   speaks the Vite and Rollup plugin API.
4. Vite stays available behind `useVite` until the whole matrix passes.

## Parity with Nexy today

| Feature today | Done by today | In nexy-compiler |
|---|---|---|
| `.nexy` pages, layouts, components | Python scanner, parser, generator | Rust, byte-identical generated module |
| `.tsx`, `.jsx`, `.ts` | Vite with esbuild | oxc parser and transformer, native |
| React, Preact islands and pages, Fast Refresh | `@vitejs/plugin-react`, `@preact/preset-vite` | oxc JSX transform and refresh, native |
| Solid islands and pages, `ssr: true` | `vite-plugin-solid` | Rust port of the Solid JSX transform |
| Vue and Svelte islands and pages, HMR | `@vitejs/plugin-vue`, `@sveltejs/vite-plugin-svelte` | the official compilers, run by the plugin layer |
| Root layout of Vue and Svelte stripped of `<html>` in the browser | `nexy` Vite plugin | native |
| `.mdx` pages with GFM | `@mdx-js/rollup`, `remark-gfm` | Rust MDX parser, remark plugins through the plugin layer |
| Tailwind CSS 4 (`@import "tailwindcss"`, `@theme`, `@plugin`) | `@tailwindcss/vite` | the Tailwind compiler: its Rust scanner natively, its CSS compiler and any `@plugin` through the plugin layer |
| UnoCSS | `unocss/vite` | the plugin layer |
| Plain CSS, CSS imports, CSS modules | Vite, PostCSS | Lightning CSS, native |
| Aliases (`useAliases`, `@/`, `@nexy/`) | `resolve.alias` | native resolver |
| npm packages, ESM and CommonJS | Vite dependency pre-bundling | Rolldown, native |
| Assets (`?url`, `?raw`, images, fonts), `import.meta.env` | Vite | native |
| Client router and islands runtime | `__nexy__/main.ts` entry | same entry, native |
| Server components and SSG HTML of islands | `entries.ts`, `ssg.*.ts` on esbuild and Node | bundled natively, rendered by the framework's own server renderer in an embedded JS engine |
| Dev server on the Nexy origin, warmup, watch list | Vite dev server | in the Nexy process, same origin and port |
| Full reload on `.mdx`, Nexy socket for `.py`, `.nexy`, `.md` | `nexy` Vite plugin | native watcher, same rules |
| Browser error overlay | Vite and Nexy | same overlay |
| Dev render endpoint `/__nexy__/render` (loopback only) | `nexy` Vite plugin | native, same restrictions |
| Production manifest, base `/__nexy__/client/` | Vite build | same manifest format |
| Clean terminal output | `nexy.log()` | native, same format |

## Where the speed comes from, and where it does not

- **Native** (Rust): parsing, TypeScript and JSX, React, Preact and Solid transforms, CSS, bundling,
  minifying, resolving, watching, the `.nexy` compiler. This is most of the work of a build.
- **Plugin layer** (JavaScript run from Rust): code whose only implementation is JavaScript, such as
  the Tailwind CSS compiler and its plugins, UnoCSS, the Vue and Svelte compilers, remark plugins,
  and the plugins of a project's `vite.config.ts`. These run at the speed of JavaScript, as they do
  today, and move to native code one by one where a port exists.
- **Server rendering** of islands runs `react-dom/server`, `vue/server-renderer` and the others,
  which are JavaScript. They run in an embedded JS engine, so no separate Node process is started.

Packages are still installed with the project's package manager (bun, pnpm or npm), as today.

## Goals

- Cold start of `nexy dev` under one second on a medium project.
- HMR under 100 ms from save to update in the browser.
- `nexy build` faster than the Vite build of the same project, measured and published per release.
- No Node.js process during dev and build.

## Roadmap

1. Parity test matrix: the same projects (none, React, Preact, Solid, Vue, Svelte, MDX, Tailwind,
   UnoCSS, aliases, a custom Vite plugin) built by Vite and by nexy-compiler, outputs compared.
2. `.nexy` compiler in Rust, byte-identical to the Python generator.
3. TypeScript and JSX for React and Preact with oxc, bundling with Rolldown.
4. The plugin layer, with Tailwind CSS and UnoCSS as its first users.
5. CSS with Lightning CSS.
6. Dev server and HMR.
7. Server rendering and SSG in the embedded JS engine.
8. Solid, then Vue and Svelte, then MDX.
9. Opt-in setting in Nexy, then the default.

## Branches and releases

`develop` is the integration branch. Work branches start from `develop` and target `develop`.
`main` receives reviewed merges and release commits. The release workflow is prepared but dormant
until this module has a package to publish. See `docs/release-setup.md` for the registry setup.

## Status

Design phase. Nothing is published yet.

## License

MIT
