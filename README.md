# @verevoir/qrcode

Zero-dependency QR code library with thirteen visual styles. API-compatible with the existing `qrcode` ecosystem — same method names, same call shapes — so code that already uses a QR library can swap to this one by changing the import.

```bash
npm install @verevoir/qrcode
```

TypeScript-first. Isomorphic (Node, browsers, Cloudflare Workers, Deno, Bun). No `canvas` package required.

## Quick start

```js
import QRCode from '@verevoir/qrcode';

const svg = await QRCode.toString('https://verevoir.io', { type: 'svg' });
```

```js
// Pick a style
const stylish = await QRCode.toString('https://verevoir.io', {
  type: 'svg',
  style: 'network',
});
```

## Why

- **Zero runtime dependencies.** Nothing extra in your lockfile.
- **Ten built-in styles.** `square`, `dots`, `diamonds`, `horizontal`, `vertical`, `diagonal`, `network`, `circuit`, `metro`, `scribble` — all scanning-reliable.
- **Isomorphic.** The default entry runs in Node, browsers, and edge runtimes without polyfills.
- **TypeScript types built in.** No `@types/*` package needed.
- **Small.** ~135 kB installed; ~8 kB minified + gzipped.

## Comparison with `qrcode`

|                             | `qrcode`                           | `@verevoir/qrcode`        |
| --------------------------- | ---------------------------------- | ------------------------- |
| Runtime dependencies        | 3 (`pngjs`, `dijkstrajs`, `yargs`) | 0                         |
| Installed size              | ~1.5 MB (pngjs alone is 1.1 MB)    | ~135 kB                   |
| Bundled (esbuild, min+gzip) | 9.4 kB                             | 8.2 kB                    |
| Visual styles               | 1                                  | 10                        |
| TypeScript types            | via `@types/qrcode`                | built in                  |
| Edge / worker ready         | needs polyfills                    | native (isomorphic entry) |

The biggest practical win is **install footprint and dependency graph** — cold-start time, Docker image size, supply-chain surface area. Bundle-size delta is modest.

## Migrating existing code

Change the import, keep your logic:

```js
// Before
import QRCode from 'qrcode';

// After
import QRCode from '@verevoir/qrcode';

// Same API
const svg = await QRCode.toString(text, { type: 'svg' });
const data = await QRCode.toDataURL(text);
```

The following methods mirror `qrcode`'s surface exactly:

- `create(text, options?)`
- `toString(text, options?, cb?)` — returns `Promise<string>` or accepts `(err, result)` callback
- `toFile(path, text, options?, cb?)` — Node only
- `toBuffer(text, options?, cb?)` — Node only
- `toCanvas(canvas, text, options?, cb?)` — browser only
- `toDataURL(text, options?, cb?)`

Both the Promise and `(err, result)` callback forms are supported.

## Tradeoffs worth knowing

- **PNG output requires an optional peer dep.** `toFile('code.png')` and `toBuffer()` use [`@resvg/resvg-js`](https://www.npmjs.com/package/@resvg/resvg-js) under the hood. Install it alongside if you need PNG; otherwise you'll see a `Module not found` when a PNG call fires:

  ```bash
  npm install @verevoir/qrcode @resvg/resvg-js
  ```

- **`version` and `maskPattern` options are accepted but ignored.** The engine always selects the optimal version and mask automatically. Migration stays safe — your code keeps running — but these options become no-ops.

- **`color.dark` / `color.light` are reliable on the default `square` style.** Other styles use SVG path construction where string-replacement colour substitution is less predictable. Stick with `square` if you rely heavily on custom colours, or drop to [`@verevoir/qr`](https://www.npmjs.com/package/@verevoir/qr) directly for proper colour plumbing.

- **Newer than the alternatives.** Well tested, but doesn't have a decade of production edge cases behind it yet. If you're at enormous scale, roll out gradually.

## Entry points

```js
import QRCode from '@verevoir/qrcode';        // isomorphic — works everywhere
import QRCode from '@verevoir/qrcode/node';   // adds toFile, toBuffer (uses node:fs)
import QRCode from '@verevoir/qrcode/web';    // adds toCanvas (browser only)
```

The isomorphic entry covers `create`, `toString`, and `toDataURL` — enough for most call sites, runs on edge runtimes. Use `/node` or `/web` only when you need platform-specific extensions.

## When to drop to the engine directly

If you want more than what fits through the compat shim — multi-candidate mask ranking, fabrication-ready layer separation, fine-grained colour control — use [`@verevoir/qr`](https://www.npmjs.com/package/@verevoir/qr) directly:

```js
import { encode, toSvg } from '@verevoir/qr';

const [qr] = encode('https://verevoir.io');
const svg = toSvg(qr, { style: 'scribble', cornerStyle: 'rounded' });
```

This package is a thin wrapper around that engine. Both live in the same repo, both go through the same test suite.

## License

MIT
