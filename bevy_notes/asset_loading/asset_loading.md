# Asset Loading Notes

- [asset module docs](https://docs.rs/bevy/latest/bevy/asset/index.html)
- Asset Server
    - [load](https://docs.rs/bevy/latest/bevy/asset/struct.AssetServer.html#method.load)
    - [is_loaded_with_dependencies](https://docs.rs/bevy/latest/bevy/prelude/struct.AssetServer.html#method.is_loaded_with_dependencies)
- [Gltf Formats](./gltf_formats.md)

## PR Info

- [Asset V2 PR](https://github.com/bevyengine/bevy/pull/8624)
    - There IS asset pre-processing. cool.
- [Asset V2 Tracking Issue](https://github.com/bevyengine/bevy/issues/9714)
- Observer w/ Asset Handles
    - [Issue #1](https://github.com/bevyengine/bevy/issues/16041)
    - [Issue #2](https://github.com/bevyengine/bevy/issues/17431)
    - so it's on the radar.
---
Work on a new asset system is ongoing. So it might be better to just learn what I need and move on. 0.20 would have asset as entities.
- [Asset as entities v0](https://github.com/bevyengine/bevy/pull/22939)