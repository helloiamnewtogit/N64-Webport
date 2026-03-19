# Port Plan

## Current repo state

### `N64Wasm-master` foundation

- Browser-first N64 emulator port built with Emscripten from [`code/Makefile`](/Users/sonu/Downloads/Ultimate-N64onlineemu/code/Makefile).
- Runtime is split between:
  - C/C++ emulator core in [`code/`]( /Users/sonu/Downloads/Ultimate-N64onlineemu/code )
  - browser UI and persistence glue in [`dist/script.js`](/Users/sonu/Downloads/Ultimate-N64onlineemu/dist/script.js)
  - static web assets in [`dist/`](/Users/sonu/Downloads/Ultimate-N64onlineemu/dist)
- Current web boot flow:
  1. Browser loads `dist/index.html`
  2. JS fetches `assets.zip`
  3. JS writes ROM and assets into the Emscripten FS
  4. Wasm boots via `Module.callMain(['custom.v64'])`
  5. Audio callback drives or coordinates the main loop
- Current build assumes older Emscripten flags and a large fixed heap.

### `Shipwright-develop`

- Native CMake project built around `libultraship`, platform backends, an OTR asset pipeline, and extensive OOT-specific hooks.
- Major code areas:
  - engine/runtime: `libultraship/`
  - OOT game code: `soh/src/`
  - enhancements and hooks: `soh/soh/Enhancements/`
  - native UI/debug tooling: `soh/soh/SohGui/`, `Notification/`, platform folders
- It is not a drop-in source donor for a generic browser emulator.

### `Ultimate-N64onlineemu`

- Was empty at the start of this pass.
- It now contains a clean copy of the `N64Wasm-master` browser foundation and is the integration repo going forward.

## Shipwright portability assessment

### Browser-safe in principle

- Small isolated gameplay/QoL patches that only mutate game state and do not depend on:
  - `libultraship`
  - ImGui/native menu systems
  - desktop filesystem access
  - native graphics backend switching
  - platform services
- Examples worth studying as patterns, not blind copy candidates:
  - autosave policy: `soh/soh/Enhancements/QoL/Autosave.cpp`
  - pause-menu behavior tweaks: `soh/soh/Enhancements/QoL/PauseWarp.cpp`
  - faster pause transitions: `soh/soh/Enhancements/TimeSavers/FasterPauseMenu.cpp`

### Native-only or wrong abstraction for this web build

- `libultraship` windowing, renderer, backend, and asset systems
- ImGui windows and native debug tooling
- DirectX/Metal/OpenGL backend selection logic
- OTR/MPQ extraction pipeline and desktop mod packaging
- platform-specific speech, notifications, OS integration, and filesystem code
- Shipwright networking/crowd-control/mod systems without a browser-specific redesign

## Important constraint

`N64Wasm-master` is a generic emulator. Most Shipwright enhancements are OOT-specific source patches, not emulator features. Direct imports should be rare and only happen if:

1. the feature is game-logic-only,
2. it can live inside the emulated title boundary without desktop dependencies, and
3. it does not compromise Chrome performance or wasm stability.

## Integration priorities

### Phase 1: performance and release hygiene

- modernize Emscripten release flags
- reduce JS main-thread overhead in audio and asset loading
- keep hot execution inside wasm
- add a profiling-oriented build target and notes for Chrome DevTools

### Phase 2: browser-safe framework work

- improve persistence/caching for assets and save data
- tighten the JS↔Wasm boundary
- document renderer/audio/input areas that must remain web-specific

### Phase 3: selective Shipwright-derived work

- only evaluate isolated OOT logic/QoL patches after the browser build is stable
- port one feature at a time behind explicit toggles
- reject anything that pulls in native UI, asset, or backend code

## First candidates if OOT-specific patching is later introduced

- autosave behavior policy
- pause-menu speed improvements
- low-risk message/UI timing adjustments

## Explicit non-goals

- replacing the web port with Shipwright’s native engine
- importing native renderer or window-system code
- importing desktop-only debugging or toolchain dependencies
