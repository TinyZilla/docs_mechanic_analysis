# System Set Notes


[Cheatbook system set](https://bevy-cheatbook.github.io/programming/system-sets.html)



---
Single system or () Tuple system implies scheduule `IntoScheduleConfigs `

https://docs.rs/bevy_ecs/0.18.1/bevy_ecs/schedule/trait.IntoScheduleConfigs.html
> Types that can convert into a ScheduleConfigs.
> This trait is implemented for “systems” (functions whose arguments all implement SystemParam), or tuples thereof. It is a common entry point for system configurations.

---
https://docs.rs/bevy_app/latest/bevy_app/struct.App.html#method.add_systems

---

Conditions

check out related from Query: [here](./query.md#sibling-parameters-populated)

```rust
app.add_systems(
    Update,
    (print_system, remove_component)
        .chain()
        .run_if(any_with_component::<SelfDeletingComponent>),
);
```

Schedule:
https://docs.rs/bevy/latest/bevy/ecs/schedule/common_conditions/index.html
(not, component, filter, resources, message, run once)

State:
https://docs.rs/bevy/latest/bevy/state/condition/index.html
(state, state_changed, state exists)

Input:
https://docs.rs/bevy/latest/bevy/input/common_conditions/index.html
(pressed, released)

Time:
https://docs.rs/bevy/latest/bevy/time/common_conditions/index.html
(Timer, Delay, Pause)

---
Combinations

https://docs.rs/bevy_ecs/latest/bevy_ecs/schedule/index.html#types

---

Ordering

before
after 
chain
