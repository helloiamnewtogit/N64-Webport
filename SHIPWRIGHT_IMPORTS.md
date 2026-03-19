# Shipwright Imports

## Direct imports in this pass

- None.

## Why

- The current foundation is a generic browser emulator.
- `Shipwright-develop` is an OOT-native engine with heavy dependencies on `libultraship`, native UI, native backend code, and its own asset pipeline.
- Copying those systems directly would violate the browser-first constraints for this repo.

## What was imported conceptually

- autosave policy is being treated as a useful pattern for future browser-safe work
- pause/menu QoL patches were reviewed as examples of the kind of isolated logic patches that could be portable in a title-specific future pass

## What was explicitly rejected for direct copy

- renderer/backend code
- native windowing and ImGui UI
- OTR/MPQ asset tooling
- platform services such as speech, notifications, and desktop filesystem access
