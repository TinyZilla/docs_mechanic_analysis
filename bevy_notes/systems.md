# System Notes

- [Exclusive System](#exclusive-systems)
- [Pipe System](#pipe-system)
- [Flush Point](#flush-point)
- [System Params](#system-params)
- [Resources](#resources)

From the Docs:
> Systems are functions with all arguments implementing SystemParam.
>
> Systems are executed in parallel, in opportunistic order; data access is managed automatically. It’s possible to specify explicit execution order between specific systems, see `IntoScheduleConfigs`.

## Exclusive Systems

Resource Page: [[Link]](https://taintedcoders.com/bevy/systems#exclusive-systems)

Special system with direct mutable world access -- which means it can reach all the data and mutate the data directly.

> For example we can run commands exactly when the system runs, instead of scheduling them to run later:
>
> The downside of exclusive systems is that they <ins>**cannot be run in parallel**</ins> if other systems also need to mutate the world. Otherwise they work exactly the same as your normal systems.

## Pipe System

Resource Pages: [[Overview]](https://taintedcoders.com/bevy/systems#combining-systems), [[Docs & Example]](https://docs.rs/bevy_ecs/latest/bevy_ecs/system/struct.PipeSystem.html)

> Higher order systems can be composed of many other systems using the pipe method. This function will take the output of the system and pass it as input to the next system.

The downside is that there's no branching / early exit on Pipe systems. This is simply to deconstruct a big, complex system into chunks.

> For FunctionSystems the output is the return value of the function and the input is the **first** `SystemParam` if it is tagged with `In` or `()` if the function has no designated input parameter.

## Flush Point

There is a special system that will apply all the changes in the command queue. This is useful if the future system is dependent on the data changes of previous systems when put in a schedule order.

Resource: [apply_deferred()](https://docs.rs/bevy/latest/bevy/prelude/trait.System.html#tymethod.apply_deferred)

## System Params

Resource Page: [[Link (0.11)]](https://bevy-cheatbook.github.io/builtins.html#systemparams), [[Doc Link]](https://docs.rs/bevy/latest/bevy/ecs/system/trait.SystemParam.html#derive)


## Resources
- [Bevy Cheat Book](https://bevy-cheatbook.github.io/programming/systems.html)
- [Tainted Coder](https://taintedcoders.com/bevy/systems)
- [Bevy Docs](https://docs.rs/bevy/latest/bevy/prelude/trait.System.html)
