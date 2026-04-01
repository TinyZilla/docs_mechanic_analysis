# Schedules Notes

https://docs.rs/bevy/latest/bevy/prelude/struct.Schedule.html

Schedule can be nested? [link](https://bevy-cheatbook.github.io/programming/schedules.html#bevys-app-structure)



[Cheatbook Schedule](https://bevy-cheatbook.github.io/programming/schedules.html)


```rust
/// Defines the schedules to be run for the [`Main`] schedule, including
/// their order.
#[derive(Resource, Debug)]
pub struct MainScheduleOrder {
    /// The labels to run for the main phase of the [`Main`] schedule (in the order they will be run).
    pub labels: Vec<InternedScheduleLabel>,
    /// The labels to run for the startup phase of the [`Main`] schedule (in the order they will be run).
    pub startup_labels: Vec<InternedScheduleLabel>,
}

impl Default for MainScheduleOrder {
    fn default() -> Self {
        Self {
            labels: vec![
                First.intern(),
                PreUpdate.intern(),
                RunFixedMainLoop.intern(),
                Update.intern(),
                SpawnScene.intern(),
                PostUpdate.intern(),
                Last.intern(),
            ],
            startup_labels: vec![PreStartup.intern(), Startup.intern(), PostStartup.intern()],
        }
    }
}
```

```rust
/// Defines the schedules to be run for the [`FixedMain`] schedule, including
/// their order.
#[derive(Resource, Debug)]
pub struct FixedMainScheduleOrder {
    /// The labels to run for the [`FixedMain`] schedule (in the order they will be run).
    pub labels: Vec<InternedScheduleLabel>,
}

impl Default for FixedMainScheduleOrder {
    fn default() -> Self {
        Self {
            labels: vec![
                FixedFirst.intern(),
                FixedPreUpdate.intern(),
                FixedUpdate.intern(),
                FixedPostUpdate.intern(),
                FixedLast.intern(),
            ],
        }
    }
}
```