# Bevy Decals

- [Bevy Feature Overview 0.16 Decals](https://bevy.org/news/bevy-0-16/#decals)
- [Forward Decals](https://docs.rs/bevy/latest/bevy/pbr/decal/struct.ForwardDecal.html)
    - Any camera rendering a forward decal <ins>**must**</ins> have the `DepthPrepass` component
    - On Wasm, requires using <ins>**WebGPU**</ins> and <ins>**disabling Msaa**</ins> on your camera.
    - [Example](https://bevy.org/examples-webgpu/3d-rendering/decal/)
- [Cluster Decals](https://docs.rs/bevy/latest/bevy/pbr/decal/clustered/index.html)
    - Clustered decals are the <ins>**highest-quality types of decals**</ins> that Bevy supports, but they require bindless textures. This means that they presently <ins>**can’t be used on WebGL 2 or WebGPU**</ins>. 