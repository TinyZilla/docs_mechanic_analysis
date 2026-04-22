# Schedules Notes

- [System Default Schedules](#system-default-schedules)
- [Headless Schedule](#headless-schedule)
- [Schedule Configuration](#schedule-configuration)
- [System Set Executing Schedule](#system-set-execute-schedule-pattern)
- [Resources](#resources)

Think of `Schedule` as the top level container for `SystemSets` and `Systems`.

From my understanding, `Schedule` is not designed to be parallelized. It simply try to schedule and run all the Systems belong to the Schedule according to a configured order.

It doesn't have:
- Run conditions
- Implicit Nested Schedule Label
    - It requires explicit setup for nested execution. See [Main::run_main](https://github.com/bevyengine/bevy/blob/main/crates/bevy_app/src/main_schedule.rs#L290)

So it <ins>**should NOT be used for System Ordering**</ins>. It is useful for custom system setup logic, aka adding another fix loop running at a different frequency, etc. Adding Special processing step that needs to happen after all the system defaults during setup or game loop. Or just defining your own loop if you don't like the name or whatever.

## System Default Schedules

- [Main](#main-setup)
- [Fixed Main](#fixed-main-setup)
    - [Fixed Main Runner](#fixed-main-runner)

Bevy Cheat Book Overview on [System Schedules](https://bevy-cheatbook.github.io/programming/schedules.html#bevys-app-structure)

### Main Setup

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

### Fixed Main Setup

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

#### Fixed Main Runner

Fixed main is a bit special. Since the desired behaviour is for Fixed main to be runned at [64 Hz](https://github.com/bevyengine/bevy/blob/main/crates/bevy_time/src/fixed.rs#L76), there's a special system called `pub fn run_fixed_main_schedule(world: &mut World)` that is in `RunFixedMainLoop`schedule that is part of the `Main` schedule which is responsible for triggering the `FixedMain` Schedule.

Check out the source code [here](https://github.com/bevyengine/bevy/blob/main/crates/bevy_time/src/fixed.rs#L239-L258)

> In case the source code location got changed and not sure what I am looking for <br>
> &nbsp; `pub fn run_fixed_main_schedule(world: &mut World) `

## Headless Schedule

In the case where the window isn't created, the `bevy_winit app runner` is not started, therefore you need to manually set a loop to trigger update for the schedules. This is how you would start it.

Reference: [Example Link](https://github.com/bevyengine/bevy/blob/main/examples/ecs/ecs_guide.rs#L299-L302)

```rust
/// The plugin below runs our app's "system schedule" once every 5 seconds.
        .add_plugins(ScheduleRunnerPlugin::run_loop(Duration::from_secs(5)))
```

## Schedule Configuration

Resource Page: [[Link]](https://bevy-cheatbook.github.io/programming/schedules.html#configuring-schedules)

There are some settings that you can set on a per Schedule basis.

- [schedule.set_executor_kind](https://docs.rs/bevy/latest/bevy/prelude/struct.Schedule.html#method.set_executor_kind) - Single-Threaded or Multi-Threaded
- [ScheduleBuildSettings](https://docs.rs/bevy/latest/bevy/ecs/schedule/struct.ScheduleBuildSettings.html) 
    - [ambiguity_detection](https://docs.rs/bevy/latest/bevy/ecs/schedule/struct.ScheduleBuildSettings.html#structfield.ambiguity_detection)
    - [auto_insert_apply_deferred](https://docs.rs/bevy/latest/bevy/ecs/schedule/struct.ScheduleBuildSettings.html#structfield.auto_insert_apply_deferred)

## System Set Execute Schedule Pattern

Triggering conditional execution of a Specific schedule by using [World.try_schedule_scope()](https://docs.rs/bevy_ecs/latest/bevy_ecs/world/struct.World.html#method.try_schedule_scope) in an exclusive system.

### Example 1: Fixed Update
Source Code: [[Invoke]](https://github.com/bevyengine/bevy/blob/main/crates/bevy_time/src/lib.rs#L89-L92), and [[Function]](https://github.com/bevyengine/bevy/blob/main/crates/bevy_time/src/fixed.rs#L243-L258)

In Bevy Core, the way Fixed Update Schedules is triggered by an exclusive system on the Main Schedule using this exact pattern.

### Example 2: Physics Schedule

Source Code: [[Invoke]](https://github.com/avianphysics/avian/blob/main/src/schedule/mod.rs#L111-L114), and [[Function]](https://github.com/avianphysics/avian/blob/main/src/schedule/mod.rs#L240-L284)

In Avian, Physics processing is part of a different schedule called `PhysicsSchedule`, and is triggered as part of the `FixedPostUpdate` schedule using `fn run_physics_schedule` function.





## Resources

- [Bevy Docs](https://docs.rs/bevy/latest/bevy/prelude/struct.Schedule.html)
- [Cheatbook - Schedule](https://bevy-cheatbook.github.io/programming/schedules.html)
