# Camera Rig

- [Camera Zoom](#camera-zoom)
- [Camera Collision](#camera-collision)
    - [Wall Types](#wall-types)
    - [Collision Behaviour](#collision-behaviour)
        - [Media](#media-for-reference)


## Camera Zoom

There are three cases for Camera zooming:
1. Camera is not obstructed
    - Camera zooming behaves normally. it simply changes the FOV of the camera to zoom in
    - Question: There's basically no distortion. Can this be achieved with a FOV change? or is this something related to frustum? or some sort of projection mapping stuff?
2. Camera is obstructed in the Z axis (Backwards)
    - Camera retains the zooming behavior. There's no position change at all, simply just a FOV / Setting change.
3. Camera is obstructed in the X axis (Sideways, usually right side.)
    - Camera moves a little, changing it's center point.
    - I believe this is due to a new camera location due to some sort of difference between colliders for zoom / non zoom behaviour.
    - Note: Not investigating too deeply on this for now. If it's a big issue will look into it.

### Examples:

#### Case 1 and 3:
![Camera Zooming](./media/camera_aiming.gif)

#### Case 1, 2, 3
![Camera Zooming 2](./media/hard_wall_zoom.gif)

Note:
- Zooming doesn't seem to take account of the geometry when it's in Case 1 and 2. It simply clips the part of the geometry that the camera is inside of.

## Camera Collision

- [Wall Types](#wall-types)
- [Collision Behaviour](#collision-behaviour)

### Wall Types

There seems to be 3 types of walls in Deadlock.
1. "Soft" Walls
    - the walls have no camera collision at all.
2. "Special" Walls
    - the wall can accommodate <ins>**SOME**</ins> camera obstruction as long as the original camera location is not occupied.
    - but the full camera wall behaviour kicks in after certain distance.
3. "Hard" Walls
    - the wall has full on camera collision

### Collision Behaviour

Only really applicable to "Special" and "Hard" walls.
- the camera position is adjusted to the closest point between the head / pivot and camera location.

To go into more details:
- Get the intended camera location
- if it's in collision
- Ray cast towards the camera location from pivoit point.
- use that point as the new camera location.

### Media for reference.

- [Soft Wall](#soft-wall)
- [Special Wall](#special-wall)
- [Hard Wall](#hard-wall)
- [Soft Hard Blend](#soft-hard-blend)

#### Soft Wall

![Soft Wall Intro](./media/soft_wall_intro.gif)

![Soft Wall Pan 1](./media/soft_wall_pan.gif)

![Soft Wall Pan 2](./media/soft_wall_pan_1.gif)

Note: Thickness of the wall doesn't matter.
It seems to be hand marked around the map for better gameplay stuff. Having cover and doesn't make camera movement jerky.

#### Special Wall
![Special Wall](./media/special_wall.gif)

#### Hard Wall

![Hard Wall Intro](./media/hard_wall_intro.gif)

![Hard Wall Pan](./media/hard_wall_pan.gif)

#### Soft Hard Blend

![Soft Hard Blend](./media/soft_hard_blend.gif)