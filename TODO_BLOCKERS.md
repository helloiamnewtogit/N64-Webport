# TODO / Blockers

## Current blockers

- `emcc` is not installed in the current shell, so local wasm rebuild verification is blocked right now.
- The source folders were extracted without Git history, so branch-based experimentation on the originals was not possible.
- Shipwright is an OOT-native codebase, while this foundation is a generic browser emulator. That limits direct code portability.

## What is still safe to continue without `emcc`

- browser JS performance work in `dist/`
- build-system cleanup in `code/Makefile`
- architecture and portability documentation
- setting up the integrated repo and commit history

## Follow-up once Emscripten is available

1. Build release and profiling variants.
2. Run Chrome DevTools traces against the updated wasm output.
3. Validate memory-growth behavior and startup size.
4. Reassess any Shipwright-derived feature candidates after the faster baseline is confirmed.
