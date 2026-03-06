# Target Detection prior attempt

This entry is create for the sake of progress tracking. [Commit](https://github.com/TinyZilla/bevy_prototype/commit/c0f95beceb1d7fcbe759db3143fd1a2c15dbbdbc)

Before settling down on the deadlock case studies, I've done a pass base on my own interpretation of the requirements for target selection.

The soultion is as follows:
1. shape cast a REVERSE cone shape infront of the Camera location, pointing at Camera forward.
2. base on the collision points up to the collision limit, pick out the entity with the following criteria:
    - Take the [Rejection](https://docs.rs/bevy/latest/bevy/prelude/struct.Vec3.html#method.reject_from) of the direction vector of \<Camera to Collision point> onto the Camera direction vector. 
    - use the entity with the smallest value of that vector rejection.
    - if there are multiple points that have a sufficiently close rejection value, take the point with the closest Distance relative to the Shape cast origin.
    - Translation -- the contact point that is closest to the aim vector is taken.

### Graphical Representation.

![prior attempt](/media/target_detection/prior_attempt/target-selection-prior-attempt-explain.png)

### Video Demonstration.

Left Right Swing pass.

![prior attempt sample 1](/media/target_detection/prior_attempt/prior_attempt_sample_1.gif)

Precision targeting.

![prior attempt sample 2](/media/target_detection/prior_attempt/prior_attempt_sample_2.gif)


## Benefits
This approach looks to relatively accurate to the collision shape of the target object.

If it's based on Object origin instead, an object with a large profile would still not be selected if an object with a small profile is placed infront of it.

Since this is a projected shape cast, it should be relatively accurate in terms of the collided point when it comes to different orientation / sizes / gaps in the hitbox.

An object like an Arch, which has large empty space in between, would be relatively accurately traced out by the contact point test.

## Drawbacks
- Potentially be computationally expendsive as collision shapes becomes more complicated / numerous
- Hard to control range / distance of the cast check precisely.
    - Example: I want to limit the cone range to 30 meters. If the shape cast distance is 30 meters, then the protrusion of the cone would extend that distance and ended up considering points beyond that distance.
        - altho it's fixable, it's not the most elegant solution.
- Since it's an inverted cone, objects close to the camera that should not be in the cone of vision get detected..
    - Or Object that is behind the cast point get detected / considered as well. 
    - (Example in Screen Shot below.)

![bad example](/media/target_detection/prior_attempt/bad_example.jpg)

## Benchmarking
This Bench Mark is taken on the stack of cubes example above, when the cone is evaluating all 7 cubes at once. (Some cubes is obstructed by others.)

For the record the averages are:
- Mean: 204.93 μs (Microsecond)
- Median: 212.86 μs 
- Mode: 202.35 μs 

![tracy stress test](/media/target_detection/prior_attempt/tracy_stressed.png)