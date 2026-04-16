# @verevoir/qrcode

**A drop-in, zero-dependency replacement for [node-qrcode](https://github.com/soldair/node-qrcode).**

`node-qrcode` is the industry standard, but it carries three runtime dependencies, ships one visual style, and leans on `canvas` for much of its browser story. `@verevoir/qrcode` keeps the exact same API surface — no code rewrites — while dropping the dep graph and adding ten built-in styles.

## Why switch?

- **Zero runtime dependencies** — no `pngjs`, `dijkstrajs`, or `yargs` in your lockfile.
- **10 built-in styles** — `square`, `dots`, `diamonds`, `horizontal`, `vertical`, `diagonal`, `network`, `circuit`, `metro`, `scribble`.
- **TypeScript-first** — native `.d.ts`, no `@types/qrcode` needed.
- **Isomorphic** — one import works in Node, browsers, and edge runtimes (Cloudflare Workers, Deno, Bun).
- **No `canvas` package required** — browser usage is native via SVG and the browser's own canvas API.

## 1-minute migration

Keep your logic. Change your import.

```js
// Before
import QRCode from 'qrcode';

// After
import QRCode from '@verevoir/qrcode';

// Same API
const svg = await QRCode.toString('https://verevoir.io', { type: 'svg' });

// New: pick a style
const stylish = await QRCode.toString('https://verevoir.io', {
  type: 'svg',
  style: 'network',
});
```

## Honest comparison

|                             | `qrcode`                           | `@verevoir/qrcode` |
| --------------------------- | ---------------------------------- | ------------------ |
| Runtime dependencies        | 3 (`pngjs`, `dijkstrajs`, `yargs`) | 0                  |
| Installed size              | ~1.5 MB (pngjs alone is 1.1 MB)    | ~135 kB            |
| Bundled (esbuild, min+gzip) | 9.4 kB                             | 8.2 kB             |
| Visual styles               | 1                                  | 10                 |
| TypeScript types            | via `@types/qrcode`                | built-in           |
| Edge/Worker ready           | needs polyfills                    | native (isomorphic entry) |

Numbers are for the isomorphic entry. The bundled-size difference is modest — the real win is the **server-side install footprint and dependency graph**, which matter for cold-start time, supply-chain surface, and Docker image size.

## Tradeoffs & pitfalls

Be aware before you switch:

- **PNG output requires an optional peer dependency.** `toFile('code.png')` and `toBuffer()` use [`@resvg/resvg-js`](https://www.npmjs.com/package/@resvg/resvg-js) under the hood. Install it alongside this package if you need PNG — otherwise you'll see a `Module not found` error only when a PNG call fires:

  ```bash
  npm install @verevoir/qrcode @resvg/resvg-js
  ```

- **`version` and `maskPattern` options are accepted but ignored.** The engine always selects the optimal version and mask automatically. If your call sites pass these, migration is still safe — your code keeps running — but the options don't take effect.

- **`color.dark` / `color.light` are reliable on the default `square` style.** Other styles use SVG path construction where string-replacement colour substitution is less predictable. Stick with `square` if you rely heavily on custom colours, or use the lower-level `@verevoir/qr` API which has proper colour plumbing.

- **Not as battle-tested as `qrcode`.** `node-qrcode` has a decade of production edge cases behind it. This package is newer. The engine is covered by a full test suite, but if you're running at enormous scale, roll it out gradually.

## Entry points

```js
import QRCode from '@verevoir/qrcode';        // isomorphic — works everywhere
import QRCode from '@verevoir/qrcode/node';   // adds toFile, toBuffer (uses node:fs)
import QRCode from '@verevoir/qrcode/web';    // adds toCanvas (browser only)
```

The isomorphic entry covers `create`, `toString`, and `toDataURL`, which is enough for most call sites and is the one that runs on edge runtimes. Use the `/node` or `/web` subpath only when you need platform-specific extensions.

## API

Mirrors `node-qrcode`. If your code calls any of:

- `create(text, options?)`
- `toString(text, options?, cb?)` — returns `Promise<string>` or accepts `(err, result)` callback
- `toFile(path, text, options?, cb?)` — Node only
- `toBuffer(text, options?, cb?)` — Node only
- `toCanvas(canvas, text, options?, cb?)` — browser only
- `toDataURL(text, options?, cb?)`

…it keeps working. Both the Promise and `(err, result)` callback forms are supported, exactly as in `node-qrcode`.

## Going further

If you decide you want more than a drop-in — direct access to the multi-candidate mask ranking, fabrication-ready layer separation, all ten styles with full colour control — use the underlying engine directly:

```js
import { encode, toSvg } from '@verevoir/qr';

const [qr] = encode('https://verevoir.io');
const svg = toSvg(qr, { style: 'scribble', cornerStyle: 'rounded' });
```

See [`@verevoir/qr`](https://www.npmjs.com/package/@verevoir/qr) for the full engine API.

## License

MIT.
