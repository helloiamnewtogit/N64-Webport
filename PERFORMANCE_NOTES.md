# Performance Notes

## Current likely bottlenecks

### Browser-side

- `dist/script.js` performs synchronous-feeling startup work on the main thread:
  - fetch `assets.zip`
  - write large blobs into MEMFS
  - configure runtime after boot
- audio playback uses `ScriptProcessorNode`, which is deprecated and runs on the main thread
- the audio callback performs repeated JS work every buffer:
  - multiple wasm calls
  - buffer-remaining scans over the ring buffer
- save and state persistence depend on JS filesystem reads/writes and IndexedDB operations on the UI thread

### Build-side

- current Emscripten settings in [`code/Makefile`](/Users/sonu/Downloads/Ultimate-N64onlineemu/code/Makefile) use an older fixed-memory profile
- build does not expose a dedicated profiling configuration
- legacy export/runtime settings increase the chance of carrying more JS support glue than necessary

### Core-side

- emulator frame execution is still coordinated from JS audio timing logic
- software-renderer fallback (`Angrylion`) remains expensive and should stay opt-in only
- mobile input currently crosses JS↔Wasm every frame through `EM_ASM`

## First-pass fixes in this repo

- cache `assets.zip` in the browser to avoid repeated network fetches across sessions
- replace XHR arraybuffer fetches with `fetch`
- reduce audio callback bookkeeping from linear scans to constant-time ring-buffer math
- add safer release/profile Emscripten flag sets with memory growth enabled

## Chrome profiling plan

### CPU

- Use Chrome DevTools Performance panel while booting a ROM.
- Record:
  - startup from page load to first frame
  - 10 to 15 seconds of gameplay
  - audio callback behavior under load

### Memory

- Watch heap growth and wasm memory expansion.
- Compare first boot vs warm boot after asset cache hit.

### Rendering

- Confirm WebGL2 path is active.
- Check whether slowdown is tied to:
  - software renderer use
  - large frame upload cost
  - overlay/UI draw overhead

## Remaining high-value items

- move away from `ScriptProcessorNode` to a browser-safe audio worklet path when practical
- reduce per-frame JS involvement in input/mobile glue
- evaluate whether asset extraction can be made incrementally cached instead of replayed every session
- test newer Emscripten versions once the current target can be rebuilt locally
