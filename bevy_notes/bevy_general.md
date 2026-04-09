# Bevy General
- [Plugin Groups](#plugin-groups)

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

