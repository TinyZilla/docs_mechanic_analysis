# Bevy Hanabi Notes

Overview:
- Mesh
- Texture
- Animated Texture
- Alpha
- Velocity / Direction
- Inheret Velocity

## Questions

- For what sort of effect is GPU batching is needed.
    - High Particle count only?
    - Any Effect?
- How about just a single, or just two effects?
    - like gun Smoke(?) stuff.

## Resources

Mesh: https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectAsset.html#method.mesh


Expression: https://docs.rs/bevy_hanabi/latest/bevy_hanabi/graph/expr/

Batched Hanabi: https://github.com/pcwalton/bevy_hanabi



## Cool Examples

- [puffs.rs](https://github.com/djeedai/bevy_hanabi/blob/main/examples/puffs.rs)
    - Custom Modifier: `LambertianLightingModifier `
    - Custom Mesh: `.mesh()` functin.
- [billboard.rs](https://github.com/djeedai/bevy_hanabi/blob/main/examples/billboard.rs)
    - billboarding & textures & animated Texture. Alpha Masking.
    - [EffectMaterial](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectMaterial.html)
    - Texture Slots.
        - [Module.add_texture_slot()](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/graph/expr/struct.Module.html#method.add_texture_slot)
- [spawn_on_command.rs](https://github.com/djeedai/bevy_hanabi/blob/main/examples/spawn_on_command.rs)
    - Cone shape ejection
    - Triggers on Command
- [ribbon.rs](https://github.com/djeedai/bevy_hanabi/blob/main/examples/ribbon.rs)
    - Trails (Like Dead Lock Slide or Bullet trails.)
- [circle.rs](https://github.com/djeedai/bevy_hanabi/blob/main/examples/circle.rs)
    - FlipbookModifier -- Sprite Sheet and UV Animations
    - procedurally generated sprite sheet
- [activate.rs](https://github.com/djeedai/bevy_hanabi/blob/main/examples/activate.rs)
    - Toggle between active and inactive for effects with frequent on / off states.
    - Takes up GPU resources when not in use.
- [expr.rs](https://github.com/djeedai/bevy_hanabi/blob/main/examples/expr.rs)
    - Probably teaches you how to write a custom expression. iunno.


## Texture adding process

1. Add Texture to texture slot.
2. Use texture in process / modifiers.


## General Vibes

> Hanabi is actually quite a capable VFX system on paper, it's just in an extremely rough state

[Discord Link](https://discord.com/channels/691052431525675048/743663924229963868/1496731967574773851)