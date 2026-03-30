# Query Notes

- [Performance Impact](#performance-impact)
- [Whole Entity Access](#whole-entity-access)
- [Sibling Parameters (Populated)](#sibling-parameters-populated)

## Performance Impact

Resource Page: [[Link]](https://docs.rs/bevy/latest/bevy/prelude/struct.Query.html#performance)

Interesting Notes:
> Creating a Query is a low-cost constant operation. 

> Two systems cannot be executed in parallel if both access the same component type where at least one of the accesses is mutable. Because of this, it is recommended for queries to only fetch mutable access to components when necessary, since <u>**immutable access can be parallelized**</u>.

Most of the data fetch is done at access time. You can limit the access to just the ones you are interested instead of looping through all the entities with `iter_many`, `get`, `get_many`

## Whole Entity Access
Resource Page: [[Link]](https://docs.rs/bevy/latest/bevy/prelude/struct.Query.html#whole-entity-access)

> EntityRef can be used in a query to gain <u>**read-only**</u> access to <u>**all components of an entity**</u>. This is useful when dynamically fetching components instead of baking them into the query type.

> The fundamental rule: EntityRef’s ability to read all components means it <u>**can never coexist with mutable access**</u>. With / Without filters can guarantee this by keeping the queries on completely separate entities.

> It is strongly advised to couple EntityRef queries with the use of either With / Without filters or ParamSets. Not only does this improve the performance and parallelization of the system, but it enables systems to gain mutable access to other components.

## Sibling Parameters (Populated)

Resource Page: [[Link]](https://docs.rs/bevy/latest/bevy/prelude/struct.Query.html#similar-parameters)

Notable: [Populated](https://docs.rs/bevy/latest/bevy/prelude/struct.Populated.html).

> This SystemParam fails validation if no matching entities exist. This will cause the system to be <u>**skipped**</u>, according to the rules laid out in SystemParamValidationError.

This can be used in lieu of a run condition for a single system. Run Condition is probably better for a batch of systems, but this can limit just one. 

Integration Tests to confirm the theory:.
```rust
// bevy version 0.18
use bevy::prelude::*;

#[derive(Component, Reflect, Debug)]
#[reflect(Component)]
struct Marker;

// Resource Setup
#[derive(Resource, Reflect, Debug, Default)]
#[reflect(Resource)]
struct RunResult(String);

fn app_setup() -> App {
    // App Setup
    let mut app = App::new();
    app.add_plugins(MinimalPlugins);
    app.init_resource::<RunResult>();
    // app.add_systems(PostStartup, setup_system);
    app
}

// fn setup_system(mut commands: Commands) {
//     commands.spawn(Marker);
// }

#[test]
fn test_system_runs_with_query() {
    fn query_system(_marker: Query<&Marker>, mut run_result: ResMut<RunResult>) {
        // Insert R for Runned.
        run_result.0.push('R');
    }

    let mut app = app_setup();
    app.add_systems(Update, query_system);

    app.update();

    let output = app.world().resource::<RunResult>().0.clone();
    assert_eq!(output, "R")
}

#[test]
fn test_system_doesnt_with_populated() {
    fn populated_system(_marker: Populated<&Marker>, mut run_result: ResMut<RunResult>) {
        // Insert R for Runned.
        run_result.0.push('R');
    }

    let mut app = app_setup();
    app.add_systems(Update, populated_system);

    app.update();

    let output = app.world().resource::<RunResult>().0.clone();
    assert_eq!(output, "")
}

#[test]
fn test_system_doesnt_with_run_condition() {
    fn query_system(_marker: Query<&Marker>, mut run_result: ResMut<RunResult>) {
        // Insert R for Runned.
        run_result.0.push('R');
    }

    let mut app = app_setup();
    app.add_systems(Update, query_system.run_if(any_with_component::<Marker>));

    app.update();

    let output = app.world().resource::<RunResult>().0.clone();
    assert_eq!(output, "")
}
```