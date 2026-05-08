# GLTF Format Notes.

- [gltf module doc](https://docs.rs/bevy/latest/bevy/gltf/index.html)
- [Gltf asset format](https://docs.rs/bevy/latest/bevy/gltf/struct.Gltf.html)
    - named_meshes - [GltfMesh](https://docs.rs/bevy/latest/bevy/gltf/struct.GltfMesh.html)
    - named_materials - StandardMaterial
    - named_skins (armature / skeleton) - [GltfSkin](https://docs.rs/bevy/latest/bevy/gltf/struct.GltfSkin.html)
    - named_animations - AnimationClip
    - named_scenes - Scene
    - named_nodes - [GltfNode](https://docs.rs/bevy/latest/bevy/gltf/struct.GltfNode.html)
    - or a vec variant which contains all the assets.
- [GltfAssetLabel](https://docs.rs/bevy/latest/bevy/gltf/enum.GltfAssetLabel.html)