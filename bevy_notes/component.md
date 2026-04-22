# Component Notes
- [Storage Type](#storage-type)
- [Required Component](#required-component)
- [Component Hooks](#component-hooks)

## Storage Type
Resource Page: [[Link]](https://docs.rs/bevy/latest/bevy/ecs/component/trait.Component.html#choosing-a-storage-type)

- `Table` (Default) -- optimized for <ins>**query iteration**</ins>
- `SparseSet` -- optimized for component <ins>**insertion and removal**</ins>
    - indicated by macro `#[component(storage = "SparseSet")]`

## Required Component

- [Functions in Required Macro](#passing-function-in-require-component-macro)
- [Multiple Inheritance](#multiple-inheritance)

Resource Page: [[Link]](https://docs.rs/bevy/latest/bevy/ecs/component/trait.Component.html#required-components)

### Passing Function in Require Component Macro

```rust
// You can also define arbitrary expressions by using `=`

#[derive(Component)]
#[require(C = init_c())]
struct A;

#[derive(Component, PartialEq, Eq, Debug)]
struct C(usize);

fn init_c() -> C {
    C(10)
}

// This will implicitly also insert C with the init_c() constructor
let id = world.spawn(A).id();
assert_eq!(&C(10), world.entity(id).get::<C>().unwrap());
```

### Multiple Inheritance

> In general, this shouldn’t happen often, but when it does the algorithm for choosing the constructor from the tree is simple and predictable:
>    1. A constructor from a direct `#[require()]`, if one exists, is selected with priority.
>    2. Otherwise, perform a Depth First Search on the tree of requirements and select the first one found.

> From a user perspective, just think about this as the following:
>    1. Specifying a required component constructor for Foo directly on a spawned component Bar will result in that constructor being used (and overriding existing constructors lower in the inheritance tree). This is the classic “inheritance override” behavior people expect.
>    2. For cases where “multiple inheritance” results in constructor clashes, Components should be listed in “importance order”. List a component earlier in the requirement list to initialize its inheritance tree earlier.

## Component Hooks

Resource Page: [[Link]](https://docs.rs/bevy/latest/bevy/ecs/component/trait.Component.html#adding-components-hooks)

