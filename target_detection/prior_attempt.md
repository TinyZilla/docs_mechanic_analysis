# Target Detection prior attempt

This entry is create for the sake of progress tracking.

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

## Drawbacks

![bad example](/media/target_detection/prior_attempt/bad_example.jpg)

## Benchmarking

![tracy stress test](/media/target_detection/prior_attempt/tracy_stressed.png)