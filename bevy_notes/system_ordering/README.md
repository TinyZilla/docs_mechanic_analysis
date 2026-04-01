# Schedule Vs System Set Notes

From my understanding:

[Schedule](./schedules.md) is more of a System Configuration concept. More if an engine internals topic. It defines stages of the app and how the systems within each of the stages are executed.

The main distinction is: [[run_if()]](https://docs.rs/bevy/latest/bevy/prelude/trait.IntoScheduleConfigs.html#method.run_if). This is API is only available at `System` or `SystemSet` level. So it's hard for a `Schedule` to have conditional logic, as it is intenteded to be always be running.

---
On the other hand.

[System Sets](./system_set.md) is designed to be conditioned and parallelized. And should be used as the main way to glue and order game logics pieces.

They need to be [configured](https://docs.rs/bevy/latest/bevy/app/struct.App.html#method.configure_sets) per `Schedule`, so a configuration could be different in `Update` and `FixedUpdate`.

---
### More Resources
- System Set vs Schedule [Discussion](https://github.com/bevyengine/bevy/discussions/9570)
