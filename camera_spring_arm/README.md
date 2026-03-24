# Camera Spring Arm 

A break down of how camera spring arm works in deadlock.

## There are two types of walls, Soft wall and Hard wall

### Soft wall Introduction
- has no interaction with camera arm.
    - There are character collisions, but not camera collisions.

![soft wall intro](/media/camera_spring_arm/soft_wall_intro.gif)

---

### Hard Wall Introduction
- interacts with camera arm
    - if the camera is too close to the hardwall, it snaps forward.

![hard wall intro](/media/camera_spring_arm/hard_wall_intro.gif)

---

The soft and hard walls are hand marked. I don't think there's a way to automate this. It's part of game play smoothness tuning.

## Camera Pan

### Soft Wall
Just to get this out of the way -- softwall has no interaction what so ever. Camera simply clips through geometry, and the usual camera clipping / transparency takes place.

![soft wall pan](/media/camera_spring_arm/soft_wall_pan.gif)

It doesn't seem like thickness of the geometry matters on what makes it a soft or hard wall. it's just reduce camera movement for certain geometries for a more plesent viewing experience.

![soft wall pan 1](/media/camera_spring_arm/soft_wall_pan_1.gif)

### Hard Wall

On the other hand, no matter the thickness, hard wall always interact with the camera arm. 

![hard wall pan](/media/camera_spring_arm/hard_wall_pan.gif)


### Soft-Hard wall interaction

the collision properties of each type of wall is applied as expected on blended scenario.

![blend soft hard](/media/camera_spring_arm/soft_hard_blend.gif)

## Hard wall specific camera interactions

The condition for camera snapping forward is interesting. It's not just a simple collision ray cast. There seems to be attempts to maintain camera's current position even if wall is between the shoulder point and camera.

When the camera collides with the geometry it then does the ray cast backwards.

Unsure: Maybe this is a new type of wall. Something in between hard and soft.

![hard wall camera snap](/media/camera_spring_arm/hard_wall_camera_snap.gif)

## Camera Zoom interaction

Zooming doesn't seem to care about geometry(?) except for the yellow case where it jumped backwards...

![hard wall zoom](/media/camera_spring_arm/hard_wall_zoom.gif)
