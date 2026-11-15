# nexy-compiler

The build and dev toolchain of Nexy, written in Rust. It replaces Vite and the Python compiler of
`.nexy` files with one native compiler for the client and the server, without changing what a
developer writes or what the build produces.

## The contract

1. The same `nexyconfig.py`, the same files in `src/`, the same commands (`nexy dev`, `nexy build`).
2. The same output layout under `__nexy__/`, so `nexy start` serves a build from either compiler.
3. HMR keeps the state of islands, and compilation errors keep the browser overlay.
4. Vite stays available behind a setting until every framework passes the same test matrix.

## What it compiles

| Input | Today | nexy-compiler |
|---|---|---|
| `.nexy` pages and layouts | Python scanner, parser and generator | Rust parser and generator, same generated module |
| `.tsx`, `.jsx`, `.ts` (React, Preact) | Vite with esbuild | oxc parser and transformer |
| Solid JSX | Vite with the Solid plugin | a Rust port of the Solid JSX transform |
| `.vue`, `.svelte` | Vite plugins | the official compilers, which are written in JavaScript, called through an embedded JS runtime until a native port exists |
| CSS, Tailwind | Vite, PostCSS | Lightning CSS and the Rust engine of Tailwind 4 |
| Bundling, splitting, minifying | Rollup, esbuild | Rolldown and the oxc minifier |
| `.mdx` | Vite with the MDX plugin | a Rust MDX parser |

The dev server lives in the same process as the Nexy server, so there is still one origin and one
port.

## Goals

- Cold start of `nexy dev` under one second on a medium project.
- HMR under 100 ms from save to update in the browser.
- `nexy build` faster than the Vite build of the same project, measured and published per release.
- No Node.js install needed for React, Preact and plain TypeScript projects.

## Roadmap

1. Test matrix: the same projects built by Vite and by nexy-compiler, outputs compared.
2. `.nexy` compiler in Rust, byte-identical to the Python generator.
3. TypeScript and JSX for React and Preact with oxc, bundling with Rolldown.
4. CSS and Tailwind.
5. Dev server and HMR.
6. Solid, then Vue and Svelte.
7. Opt-in setting in Nexy, then the default.

## Status

Design phase. Nothing is published yet.

## License

MIT
