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

## Concepts

- Effect
    - [EffectAsset](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectAsset.html)
        - Builder API
        - mesh - & Mesh Handle.
        - [AlphaMode](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/enum.AlphaMode.html)
        - [SimulationSpace](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/enum.SimulationSpace.html)
            - Local Space vs Global Space
        - [MotionIntegration](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/enum.MotionIntegration.html)
            - No clue yet. but i'd imagine this is why Firework Behaves with Velocity transfer.
        - Adding Modifiers
            - [EffectAsset.init()](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectAsset.html#method.init)
            - [EffectAsset.update()](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectAsset.html#method.update)
            - [EffectAsset.render()](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectAsset.html#method.render)
            - [EffectAsset.mesh()](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectAsset.html#method.mesh)
- Attributes
    - [SetAttributeModifier](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/modifier/attr/struct.SetAttributeModifier.html)
    - An effect attribute is a quantity stored per particle for all particles. Unlike properties, each particle can have a different value for each attribute. Examples of particle attributes include the particle’s own position and its velocity. Attributes are represented by the Attribute type.
- Properties
    - [link](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/properties/index.html)
    - Variable Stored Per efect - CPU Mutable.
    - all particles see the same property value while the effect simulates or renders
    - Use properties to ensure a value from an expression can be mutated while the effect is instantiated, without having to destroy and re-create a new effect. Using properties is a bit more costly in terms of GPU processing than using a hard-coded constant, so avoid using properties if a value doesn’t need to change dynamically at runtime, or changes very infrequently.
- Spawn
    - [SpawnerSettings](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.SpawnerSettings.html)
        - Can be accessed with (EffectSpawner) Component during run time.
        - once
            - [SpawnerSettings.with_emit_on_start()](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.SpawnerSettings.html#method.with_emit_on_start)
            - [EffectSpawner.reset()](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectSpawner.html#method.reset)
        - burst
        - period
        - starts_active
        - with_emit_on_start
- [Module](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/graph/expr/struct.Module.html)
    - A module represents a storage for a set of expressions used in a single EffectAsset. Modules are not reusable accross effect assets; each effect asset owns a single module, containing all the expressions used in all the modifiers attached to that asset. 
- Writer
- Expression
- Modifiers
- Components
    - [ParticleEffect](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.ParticleEffect.html)
    - [Effect Materials](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectMaterial.html)
    - [Effect Properties](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/properties/struct.EffectProperties.html)
        - If using properties, spawn an EffectProperties component on the same entity. Then update properties through that component at any time while the effect is active. This allows some moderate CPU-side control over the simulation and rendering of the effect, without having to destroy the effect and re-create a new one.
    - [Effect Parents](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/struct.EffectParent.html)
        - [EmitSpawnEventModifier](https://docs.rs/bevy_hanabi/latest/bevy_hanabi/modifier/struct.EmitSpawnEventModifier.html)
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