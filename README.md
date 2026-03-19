# Ultimate N64 Online Emu

This repo is the integration target for a faster Chrome-focused browser build based on the `N64Wasm-master` foundation. `Shipwright-develop` was reviewed as a source of ideas and selective code candidates, but this project remains a web emulator build, not a replacement with Shipwright’s native engine.

## Status

- Browser/runtime foundation: `N64Wasm-master`
- Integration target: this repo
- Shipwright direct imports in this pass: none
- Current focus: performance, stability, Chrome compatibility, and clean documentation

## Docs

- [`PORT_PLAN.md`](PORT_PLAN.md)
- [`PERFORMANCE_NOTES.md`](PERFORMANCE_NOTES.md)
- [`TODO_BLOCKERS.md`](TODO_BLOCKERS.md)

## What changed in this pass

- Added a dedicated integration repo and Git history in `Ultimate-N64onlineemu`
- Reduced browser-side startup overhead by caching `assets.zip`
- Replaced XHR binary loading with `fetch`
- Reduced audio callback bookkeeping from ring-buffer scans to constant-time math
- Modernized the wasm Makefile with explicit release and profiling flag sets

## Chrome-focused performance changes

- asset caching through the browser Cache API
- constant-time audio buffer remaining calculation
- memory-growth-capable release flags for Emscripten
- explicit profiling build target for DevTools-friendly investigation

## Build

The current shell does not have `emcc`, so rebuild verification is blocked here. Once Emscripten is available:

```bash
cd code
make
```

For a profiling-oriented build:

```bash
cd code
make profile
```

The resulting `n64wasm.js` and `n64wasm.wasm` are placed in `dist/`.

## Run

Serve the contents of `dist/` from a local web server and open it in Chrome. Example:

```bash
cd dist
python3 -m http.server 8000
```

Then open `http://localhost:8000`.

## Shipwright import policy

Only browser-safe logic/QoL code should ever be considered for import, and only after dependency inspection. Native renderer, backend, asset, UI, and platform code from Shipwright should not be copied into this target.
