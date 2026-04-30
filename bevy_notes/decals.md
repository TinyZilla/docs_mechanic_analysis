# Bevy Decals

- [Forward Decals](https://docs.rs/bevy/latest/bevy/pbr/decal/struct.ForwardDecal.html)
    - On Wasm, requires using <ins>**WebGPU**</ins> and <ins>**disabling Msaa**</ins> on your camera.
- [Cluster Decals](https://docs.rs/bevy/latest/bevy/pbr/decal/clustered/index.html)
    - Clustered decals are the <ins>**highest-quality types of decals**</ins> that Bevy supports, but they require bindless textures. This means that they presently <ins>**can’t be used on WebGL 2 or WebGPU**</ins>. 