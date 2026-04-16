# @verevoir/qrcode — node-qrcode drop-in

Thin stub package. Re-exports `@verevoir/qr/qrcode` under the name `@verevoir/qrcode` so developers searching npm for "qrcode" or typing `npm install @verevoir/qrcode` find it directly.

## What this package is

Nine tiny files that re-export the real shim from `@verevoir/qr/qrcode`:

- `index.{js,cjs,d.ts}` — isomorphic entry
- `node.{js,cjs,d.ts}` — Node extensions (`toFile`, `toBuffer`)
- `web.{js,cjs,d.ts}` — browser extensions (`toCanvas`)

No build step. No source folder. No tests (the engine is tested in `@verevoir/qr`).

## What this package is **not**

The engine. All QR encoding and SVG rendering logic lives in `@verevoir/qr`. This package has no logic of its own — it exists solely for **discoverability**:

- Users searching npm for "qrcode" find it by name.
- `npm install @verevoir/qrcode` works without a scope the user has to know.
- Migration from `node-qrcode` reads as a straight name substitution.

## When to edit this package

Almost never. If you're changing behaviour you're in the wrong repo — go to `@verevoir/qr`.

Edit here when:
- The README needs a copy tweak.
- Keywords need adjusting for npm search.
- The `exports` map needs a new subpath (only if `@verevoir/qr` adds one first).
- The peer-dep version range changes.

## Publishing

1. Bump `version` in `package.json`.
2. If `@verevoir/qr`'s major version has changed, update the `dependencies` range.
3. `npm publish --access public`.

No build, no tests, no pre-publish script. The re-export files ship as-is.

## Dependency on `@verevoir/qr`

This package declares `@verevoir/qr` as a regular `dependencies` entry (not `peerDependencies`) so `npm install @verevoir/qrcode` is self-sufficient. The optional PNG peer (`@resvg/resvg-js`) is declared here too so the install story matches the underlying engine.
