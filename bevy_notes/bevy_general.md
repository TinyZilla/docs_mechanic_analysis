# Bevy General
- [Plugin Groups](#plugin-groups)
- [Bevy Deferred / Pipelined Rendering](#bevy-deferred)
- [Bevy Feature Flags](https://docs.rs/crate/bevy/latest/features)

## Plugin Groups

Resources: [[Example]](https://bevy.org/examples/application/plugin-group/), [[plugin_group! Macro]](https://docs.rs/bevy/latest/bevy/app/macro.plugin_group.html)

- [Builder API](#builder-api)
- [Configurate Per Plugin](#configure-per-plugin)

> Provides a build ordering to ensure that Plugins which produce/require a Resource are built before/after dependent/depending Plugins. Plugins inside the group can be disabled, enabled or reordered.

### Builder API

Resources: [[Builder API]](https://docs.rs/bevy/latest/bevy/app/struct.PluginGroupBuilder.html)

From Examples, The way to set it up.
```rust
/// A group of plugins that produce the "hello world" behavior
pub struct HelloWorldPlugins;

impl PluginGroup for HelloWorldPlugins {
    fn build(self) -> PluginGroupBuilder {
        PluginGroupBuilder::start::<Self>()
            .add(PrintHelloPlugin)
            .add(PrintWorldPlugin)
    }
}
```

Some interesting action you can do at creation time:
- start
- add
- add_group

#### Modifying
```rust
// You can also modify a PluginGroup (such as disabling plugins) like this:
HelloWorldPlugins
    .build()
    .disable::<PrintWorldPlugin>()
    .add_before::<PrintHelloPlugin>(
        bevy::diagnostic::LogDiagnosticsPlugin::default(),
    ),
```

Action you can do to modify:
- set
- enable
- disable
- add_before
- add_after

Action you can do to check:
- contains
- enabled

### Configure Per Plugin

Resources: [[Trait Page]](https://docs.rs/bevy/latest/bevy/prelude/trait.PluginGroup.html)

After the plugin is built, you can `set` custom values for individual plugins within the Plugin Container. It will keep the ordering.

`set`'s return value is a `PluginGroupBuilder`. You can chain more `set`s or modify it further using the more extensive Builder API. If you just need to access `PluginGroupBuilder` directly, use `build()`

```rust
fn main() {
    App::new()
        .add_plugins(DefaultPlugins.set(PbrPlugin {
            prepass_enabled: false,
            ..default()
        })
        .set(ImagePlugin::default_nearest()))
        .run();
}
```

## Bevy Deferred

Resources: [[Pipeline Performance Page]](https://bevy-cheatbook.github.io/setup/perf.html#pipelined-rendering), [[GPU Pipeline Page]](https://bevy-cheatbook.github.io/gpu/intro.html#pipelined-rendering)

Bevy by default uses Pipelined Rendering.
![Pipeline Rendering Example](https://bevy-cheatbook.github.io/img/pipelined-latency.png)


> This will improve GPU utilization (make it less likely the GPU will sit idle waiting for the CPU to give it work to do), by making better use of CPU multithreading. Typically, it can result in <ins>**10-30% higher framerate**</ins>, sometimes more.

> However, it can also affect perceived input latency (“click-to-photon” latency), often for the worse.

> The actual mouse click happens in-between frames. In both cases, frame #4 is when the input is detected by Bevy. In the pipelined case, rendering of the previous frame is done in parallel, so an additional frame without the input appears on-screen.

> Without pipelining, the user will see their input delayed by 1 frame. With **pipelining**, it will be <ins>**delayed by 2 frames**</ins>.

> However, in the diagram above, the frame rate increase from <ins>**pipelining is big enough that overall the input is processed and displayed sooner**</ins>. Your application might not be so lucky.

You can disable Pipelining by disabling the `PipelinedRenderingPlugin`:

```rust
use bevy::render::pipelined_rendering::PipelinedRenderingPlugin;

App::new()
    .add_plugins(DefaultPlugins.build().disable::<PipelinedRenderingPlugin>())
    // ...
    .run();
```

![Bounded Scenario](https://bevy-cheatbook.github.io/img/pipelined-rendering.png)

What does Deferred mean in bevy?

Since by default bevy is heavily parallelized, all the work done on this frame is going to be displayed 2 frames from now. So for computations it shouldn't matter too much.

Unless pipelining is disabled, then maybe(?) deferred will do something?
From what I understand, the concept of `Deferred` is.

-> Update Positions -> Render -> Update States Etc | Cycle End

so the advice of -> Things doesn't need to be happening immediately in that frame, bevy handles that perfectly. Since it's already built into the render pipeline that there's 1 frame delay on displaying.