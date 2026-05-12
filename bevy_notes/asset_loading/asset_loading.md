# Asset Loading Notes

Asset Loading is currently undergoing a big change due to BSN.
- [Asset as entities v0 (0.20)](https://github.com/bevyengine/bevy/pull/22939)
- [BSN PR (0.19)](https://github.com/bevyengine/bevy/pull/23413)
    - Templates
    - [Scene Components](https://github.com/bevyengine/bevy/pull/24008)
        - Talks about Gltf Importer to utilize the new Templating / BSN format.
            - This will probably done in conjunction with **Asset as Entitity**. (0.20)

---


At the end of the day, I want to do Gltf stuff due to animations.
- Currently everything lives in a `Scene`. 
    - This is a problem since there are effectively 3 parts to the skeletal mesh that I want to get out of the file effectively.
        - Mesh & Material
        - Skeleton & Inverse Bind Matrix
        - Animation Info & Animation Id.
    - it would be nice if theses parts i can get separately from the GLTF file, and instance them as I needed. And Scene is just simply those things applied to each other.


---

I have two options here.
1. I can join the effort for the Gltf to BSN port, the official support won't be until 0.20, and I have to live with that for a while.
2. I can just wait for the port to happen and join the discussion.
    - I got other stuff to worry about -- Namely Character controller
    - I can just use the current system as a placeholder for now.
        - Learning work isn't wasted since I know what I want and what i am aiming for now. I can join the discussion.

## Old ish info.

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