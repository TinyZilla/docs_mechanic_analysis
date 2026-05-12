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
- Hierarchy
    - Gltf Node
        - Gltf Node (children)
        - Gltf Mesh
            - Gltf Primitive
                - Mesh
                - StandardMaterial - Option
        - Gltf Skin
        - Gltf Extras
- [GltfLoaderSettings](https://docs.rs/bevy/latest/bevy/gltf/struct.GltfLoaderSettings.html)
    - asset_server.load_with_settings

---

random notes here for now:

- GLTF Loader constructs a world (scene) for the GLTF file there.
    - Can i access that data in any way?
    - seems like the handles and stuff is shared between the main world.
    - if i can just get a subset of the world it should be ok.
- There's a bunch of aux stuff during processing time that makes the skeletal construction & animation binding a lot easier:
    - Animation
        - Animation target is generated from Name Paths.
            - So potentially skeletons with the same names will get poo-poo'd?
                - There's the `Animated_by` relationship maybe not.


---
todo list:
1. Does skeletons need to place in an hierarchy for the bones to work correctly?
    - I am imaging yes since there's a bunch of parent bones stuff.
    - but we can test this out.
        - Tested... It doesn't work. all squished together.
2. Port Bevy animation notes from One note to here.
    - Reorganize some stuff while i am at it.
    - Notes about Animation Target ID & Animated By.
3. i probably need a visual representation to make it clearer eh?
4. Understanding world asset
    - can i keep a skeleton there and instantiate it sort of like prefab / blueprint.
5. if I need to parent, Then I need to prefab with the same path, having the same target id.
    - My animation clips should target the same id's

---
ok it's somewhat coming together.

- BSN has a lot of good solution to what i am seeking.
    - [Scene Components](https://github.com/bevyengine/bevy/pull/24008)
    - Template
- There's talks about porting Gltf Loader to use BSN, and as a part of Scene Component.
- Currently what I have to do is to basically re-construct it.
    - but with BSN maybe I can sneak into the specs and do stuff.


---

ok ok ok ok ok ok ok ok 
it's time. 
