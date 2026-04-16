# @verevoir/qrcode — Intent

## Why this project exists

`@verevoir/qr` ships a `node-qrcode`-compatible shim at its `./qrcode` subpath. Technically sufficient — but invisible to anyone searching npm for "qrcode" or typing `npm install qrcode`-adjacent names.

This package exists as a **discoverability layer**. Developers looking to escape `node-qrcode`'s dependency graph, add styling, or get first-class TypeScript types should be able to find a drop-in by name. That means a package literally called `@verevoir/qrcode`, not a subpath of something else.

The package itself is nine re-export stubs. The real work lives in `@verevoir/qr`.

## Key decisions

### Stub package, not a fork

The alternative was re-publishing the shim code here. Rejected: two sources of truth, double the maintenance, version skew risk. The stub approach means every bug fix or feature added to the engine flows through automatically — all we publish is a new `@verevoir/qr` dependency range if anything structural changes.

### `dependencies`, not `peerDependencies`, for `@verevoir/qr`

Users expect `npm install @verevoir/qrcode` to Just Work. Peer dependency would force them to install two packages and know why. Runtime dependency is the right tradeoff for a discovery-oriented stub even though it means a slightly nested `node_modules` layout.

### No build step, no tests, no CI

The package has no logic. TypeScript types are re-exports. Behaviour is tested upstream in `@verevoir/qr`. Adding a build pipeline here would be ceremony for nothing — and the re-export files are simple enough that a reviewer can eyeball the entire source in under a minute.

### README targets `node-qrcode` users specifically

Unlike `@verevoir/qr`'s README which speaks to "developers who want a QR library," this README speaks to a very specific reader: someone currently using `node-qrcode` who's frustrated by one or more of (a) dep graph, (b) single style, (c) TypeScript friction, (d) canvas requirement. The pitch leads with the 1-line migration and is honest about the tradeoffs (PNG peer dep, ignored options, colour limitations on fancy styles, battle-testing).

### Keyword list prioritises `qrcode` and `svg`

`qrcode` is the direct-intent search term. `svg` is the long-tail search with real volume. Tier-2 keywords cover TypeScript, zero-deps, and edge-runtime angles that `node-qrcode` doesn't match. Full list in `package.json`.
