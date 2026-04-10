# System Set Notes

From the [Bevy Docs](https://docs.rs/bevy/latest/bevy/ecs/prelude/trait.SystemSet.html):

> System sets are tag-like labels that can be used to **group systems together**.
> 
> This allows you to <u>**share configuration**</u> (like run conditions) across multiple systems, and <u>**order systems or system sets**</u> relative to conceptual groups of systems. 
>
> System sets can belong to any number of other system sets, allowing you to create <u>**nested hierarchies of system sets**</u> to group systems together
>
> Systems sets are also useful for **exposing a consistent public API** for dependencies to hook into across versions of your crate, allowing them to add systems to a specific set, or **order relative to that set**, without leaking implementation details of the exact systems involved.

- [System Set Behaviour](#system-set-behaviour)
- How to use system set
- [Resources](#resources)

## System Set Behaviour
- [System Set Creation](#system-set-creation)
- [System Set Configuration](#system-set-configuration)
- [System Set Ordering](#system-set-ordering)
- [System Run Conditional](#system-run-conditional)
    - [Custom Run Conditions](#custom-run-conditions)
    - [Built In Run Conditions](#built-in-run-conditions)
    - [Run Condition Combinations](#run-condition-combinations)
- nesting


### System Set Creation

```rust
#[derive(SystemSet, Debug, Clone, PartialEq, Eq, Hash)]
struct NestedSetA;

#[derive(SystemSet, Debug, Clone, PartialEq, Eq, Hash)]
struct NestedSetB;

#[derive(SystemSet, Debug, Clone, PartialEq, Eq, Hash)]
enum OrderedSets {
    First,
    Last,
}
```
### System Set Configuration

System Set configuration is set per [Schedule](./schedules.md) basis. So normally it's done with [Schedule.configure_sets()](https://docs.rs/bevy/latest/bevy/prelude/struct.Schedule.html#method.configure_sets). For developer experience, Bevy provides [App.configure_sets()](https://docs.rs/bevy_app/latest/bevy_app/struct.App.html#method.configure_sets) with an additionals `Schedule` as parameter.

```rust
app.configure_sets(Update, (OrderedSets::First, OrderedSets::Last).chain());
app.configure_sets(Update, NestedSetA.in_set(OrderedSets::First));
app.configure_sets(Update, NestedSetB.in_set(OrderedSets::First));
```

Adding system to the sets is done with the usual [App.add_systems()](https://docs.rs/bevy_app/latest/bevy_app/struct.App.html#method.add_systems) API, with the addition of the trait method [IntoScheduleConfigs.in_set()](https://docs.rs/bevy/latest/bevy/prelude/trait.IntoScheduleConfigs.html#method.in_set).

```rust
app.add_systems(Update, system_d.in_set(NestedSetB));
app.add_systems(Update, system_c.in_set(NestedSetA));
app.add_systems(Update, system_b.in_set(OrderedSets::Last));
```

Note: If a system is part of the system set try not to put explicit ordering. Unless the module contains all the systems in the module.

Single system or `()` Tuple system implies the implimenation of `IntoScheduleConfigs` anyways, so it's an [Anonymous Sets](https://bevy-cheatbook.github.io/programming/system-sets.html#anonymous-sets).

In [IntoScheduleConfigs](https://docs.rs/bevy/latest/bevy/prelude/trait.IntoScheduleConfigs.html) Docs:
> Types that can convert into a ScheduleConfigs. <br>
> This trait is implemented for “systems” (functions whose arguments all implement SystemParam), or tuples thereof. It is a common entry point for system configurations.

### System Set Ordering
There are three main method of ordering:
- [before()](https://docs.rs/bevy/latest/bevy/prelude/trait.IntoScheduleConfigs.html#method.before) - run the current `Set` before the param `System` / `Set`
- [after()](https://docs.rs/bevy/latest/bevy/prelude/trait.IntoScheduleConfigs.html#method.after) - run the current `Set` after the param `System` / `Set`
- [chain()](https://docs.rs/bevy/latest/bevy/prelude/trait.IntoScheduleConfigs.html#method.chain) - run the `System` / `Set` in the order of tuple supplied

```rust
// At app level during configure sets.
app.configure_sets(Update, (OrderedSets::First, OrderedSets::Last).chain());
app.configure_sets(Update, OrderedSets::First.before(OrderedSets::Last));
app.configure_sets(Update, OrderedSets::Last.after(OrderedSets::First));

// At add_system level.
app.add_systems(Update, (system_a, system_b, system_c).chain());
app.add_systems(Update, (system_d).before(system_e));
app.add_systems(Update, (system_z).after(system_y));
```

### System Run Conditional

Resource: [[example]](https://github.com/bevyengine/bevy/blob/main/examples/ecs/run_conditions.rs)

You can set a condition to run the `System` or `Set` through existing conditions or custom conditions.

The way to set the conditional is with [IntoScheduleConfigs.run_if()](https://docs.rs/bevy/latest/bevy/prelude/trait.IntoScheduleConfigs.html#method.run_if) API, applied to `System Set` or individual `Systems`. The param is a system with [SystemCondition](https://docs.rs/bevy/latest/bevy/prelude/trait.SystemCondition.html) Trait. in layman's term that means it's <u>**a system that returns a bool**</u>.

<u>Note: check out related from **Query**</u>: [here](./query.md#sibling-parameters-populated)

#### Custom Run Conditions
You can create a `SystemCondition` like this:
```rust
// Simple system
fn false_condition() -> bool {
    false
}

// system with local
fn flip_flop(mut x: Local<bool>) -> bool {
    *x = !*x;
    *x
}

// system with query
fn any_component_is_happy(components: Query<&IsHappy>) -> bool {
    for component in components.iter() {
        if component.is_happy() {
            return true;
        }
    }
    return false;
}
```

Or you can have run condition as a part of closure for one off condition that doesn't need to get reused.

```rust
|counter: Res<InputCounter>| counter.is_changed() && !counter.is_added()
```

#### Built in Run Conditions

- [General Run Conditions](https://docs.rs/bevy/latest/bevy/ecs/schedule/common_conditions/index.html):
    - component
    - filter
    - resources
    - message
- [Utility Run Conditions](https://docs.rs/bevy/latest/bevy/ecs/schedule/common_conditions/index.html):
    - not
    - run_once
- [State Run Conditions](https://docs.rs/bevy/latest/bevy/state/condition/index.html)
    - in_state
    - state_changed
    - state_exists
- [Input Run Conditions](https://docs.rs/bevy/latest/bevy/input/common_conditions/index.html)
    - pressed, just_pressed
    - just_released
    - toggle
- [Time Based Run Conditions](https://docs.rs/bevy/latest/bevy/time/common_conditions/index.html)
    - timer
    - delay
    - pause
    - repeat

#### Run Condition Combinations

Through the [Combine](https://docs.rs/bevy_ecs/latest/bevy_ecs/system/trait.Combine.html) trait, run Condition can be [combined](https://docs.rs/bevy_ecs/latest/bevy_ecs/schedule/index.html#types)(under Type Aliases) with:
- And
- Nand
- Nor
- Or
- Xnor
- Xor

```rust
// Combination example using the RAW Combine Trait. Example from Combine Trait Doc.
app.add_systems(my_system.run_if(Xor::new(
    IntoSystem::into_system(resource_equals(A(1))),
    IntoSystem::into_system(resource_equals(B(1))),
    // The name of the combined system.
    "a ^ b".into(),
)));
```

However, this is pretty cumbersome to use so Bevy alternatively have [SystemCondition](https://docs.rs/bevy/latest/bevy/prelude/trait.SystemCondition.html) trait that every `run_if()` param impliments which strips away the boilerplate. All the classic logical operation listed above can be used.

```rust
// So it can be used as a part of the argument by chaining them together.
app.add_systems(Update, system_a.run_if(false_condition.or(true_condition)));
// or chain the results together
app.add_systems(
    Update,
    system_b
        .run_if(false_condition.or(true_condition).and(true_condition)),
);
```

## Resources

- [Bevy Cheatbook System Set](https://bevy-cheatbook.github.io/programming/system-sets.html)
- [Tainted Coder System Set](https://taintedcoders.com/bevy/systems#custom-system-sets)
