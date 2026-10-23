# vite-plugin-iconify-embed

A Vite plugin that embeds only the [Iconify](https://iconify.design/) icons your sources name, so the app never calls the Iconify API.

- Scans source files for `prefix:name` strings and bundles only those icons
- Registers them with your Iconify runtime, so `<Icon icon="lucide:house" />` keeps working unchanged
- Blocks the Iconify API by default: an icon missing from the bundle renders empty instead of fetching
- Scans several folders, so a monorepo app can include its shared packages
- Reloads the dev server when a source adds or removes an icon

## Install

```sh
bun add -d vite-plugin-iconify-embed @iconify-json/lucide
```

Install one `@iconify-json/*` package per icon set you use.

## Usage

```ts
import { icons as lucide } from "@iconify-json/lucide"
import { defineConfig } from "vite"
import { iconifyEmbed } from "vite-plugin-iconify-embed"

export default defineConfig({
  plugins: [
    iconifyEmbed({
      collections: [lucide],
      scan: ["src"],
    }),
  ],
})
```

Import the virtual module once, before any icon renders. In SvelteKit, the root `+layout.svelte` is the place:

```ts
import "virtual:iconify-embed"
```

Add the types for the virtual module:

```ts
/// <reference types="vite-plugin-iconify-embed/client" />
```

## Options

| Option        | Description                                                                 | Default                          |
| ------------- | --------------------------------------------------------------------------- | -------------------------------- |
| `collections` | Icon sets to embed from                                                     | required                         |
| `scan`        | Folders to scan, relative to the Vite root                                  | required                         |
| `extensions`  | File extensions to scan                                                     | `.svelte .ts .js .tsx .jsx .vue` |
| `runtime`     | Package that receives the icons, or `false` to only export them             | detected from `package.json`     |
| `api`         | Let the runtime fetch icons missing from the bundle from the Iconify API    | `false`                          |

The detected runtimes are `@iconify/svelte`, `@iconify/vue`, `@iconify/react`, `@iconify-icon/solid` and `iconify-icon`.

## Icon names

Write every icon name as one whole string, such as `"lucide:house"`. A name built at run time, such as `` `lucide:${name}` ``, is invisible to the scan. Write the full names somewhere in the scanned sources instead, for example in a lookup table.

## Development

```sh
bun install
bun run check
bun run lint
bun run build
```

## License

MIT
