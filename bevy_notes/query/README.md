# Query Notes

- [Performance Impact](#performance-impact)
- [Whole Entity Access](#whole-entity-access)
- [Sibling Parameters (Populated)](#sibling-parameters-populated)
- [Filters](#filters)
- [QueryLens](#querylens)

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
/// Base Case
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
/// Populated might be better for individual systems.
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
/// Run Conditions might be better applied to a SystemSet, since its a one time cost compare to checking for each systems.
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

## Filters

[Architype Filters](https://docs.rs/bevy/latest/bevy/ecs/query/trait.ArchetypeFilter.html) are filters with minimal performance impact because it's part of the normal Query path, speed up with Architype caching.

> As such, only the <u>**With**</u> and <u>**Without**</u> filters can implement the trait. <u>**Tuples(And)**</u> and <u>**Or**</u> filters are automatically implemented with the trait only if its containing types also implement the same trait.

[Added](https://docs.rs/bevy/latest/bevy/prelude/struct.Added.html#time-complexity), [Changed](https://docs.rs/bevy/latest/bevy/prelude/struct.Changed.html#time-complexity) and [Spawned](https://docs.rs/bevy/latest/bevy/ecs/query/struct.Spawned.html#time-complexity) are not ArchetypeFilter, which practically means that
if the query (with T component filter) matches a million entities,
The filter will **iterate over all of them** even if none of them were just added.

Under the hood **Added** and **Changed** uses [Ref\<T>](https://docs.rs/bevy/latest/bevy/prelude/struct.Ref.html) to get the component change detection data, while **Spawned** uses [SpawnDetails](https://docs.rs/bevy/latest/bevy/ecs/query/struct.SpawnDetails.html) to get the spawned data.

Todo: Uses Cases? Compare to Component Hooks? [Resources](https://docs.rs/bevy_ecs/latest/bevy_ecs/lifecycle/index.html)

## QueryLens

Query Lens is the byproduct of changing query signatures. The new query can be given to a reusable function for further processing.

Key Words: Query Modification

- [Transmutation](#transmutation)
- [Query Joining](#query-joining)

### Transmutation

Resource Page: [transmute_lens](https://docs.rs/bevy/latest/bevy/prelude/struct.Query.html#method.transmute_lens), `_filtered` and `_inner` variants.

> Uses Cases:
> - Remove components, e.g. `Query<(&A, &B)>` to `Query<&A>`.
> - Retrieve an existing component with reduced or equal access, e.g. `Query<&mut A>` to `Query<&A>` or `Query<&T>` to `Query<Ref<T>>`.
> - Add parameters with no new access, for example <u>**adding an Entity parameter**</u>.


### Query Joining

Resource Page: [join](https://docs.rs/bevy/latest/bevy/prelude/struct.Query.html#method.join) and [join_filtered](https://docs.rs/bevy/latest/bevy/prelude/struct.Query.html#method.join_filtered)

Use Cases:
> For example, this can take a `Query<&A>` and a `Query<&B>` and return a `Query<(&A, &B)>`. The returned query will only return items with both A and B. 

Note: There's a current open [PR](https://github.com/bevyengine/bevy/issues/13633) that propose expanding on the Joining API.