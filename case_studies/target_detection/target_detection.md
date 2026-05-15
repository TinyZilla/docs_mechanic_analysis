# Target Detection

- [Source Points](#source-point)
- [Target Line of Sight Points](#target-los-points)
- [Filtering Criteria](#filtering-criteria)
- [Selection Criteria](#selection-criteria)
- [Methodology](#methodology)
---
Before this study, I've made a previous pass on target selection. It's not very accurate, but it was an interesting idea. So I documented in the [Prior Attempt](./prior_attempt/prior_attempt.md) file.


## Source Point

The source point seems to be determined from two inputs:
- The Location and Direction of the viewing camera.
- Projected onto a Sphere that is centered from the character's Head / Torso.
    - The radius looks rather large since it need to be beyond the shoot point of the weapon. And some weapons are decently long.

Zooming the camera doesn't seem to have an impact on where that point is, unless the camera itself is moved during the zoom process.

In the case where the Source point is inside a wall, there seems to be a raycast from the center of the circle towards the point to prevent collision.

### Media Example.

- [Non-Zoom Aiming](#non-zoom-aiming)
- [Zoom Aiming](#zoom-aiming)
- [Wall Collision](#zoom-wall-collision)

#### Non-Zoom aiming

![Non Zoom Aim Radius Low](./media/los_aim_radius_low.gif)

![Non Zoom Aim Radius High](./media/los_aim_radius_high.gif)

#### Zoom aiming

![Zoom Aiming](./media/los_zoom_radius.gif)

#### Zoom Wall Collision

![Zoom Collision](./media/los_collision.gif)

## Target Los Points

There seems to be 3 different destination points on the selection target.
- One above the head
- One on Left shoulder
- One on Right shoulder

The points are unique for each cast / target direction. it's always horizontally perpendicular from the source.

### Media:

- [Los Points example](#points-on-a-single-character)
- [Multiple Perspective same target](#points-with-multiple-cast-perspective)

#### Points on a Single character

![Los Point Breakdown](./media/deadlock_los_breakdown.png)

#### Points with multiple cast perspective

![Los Point Multi Perspective](./media/casting-multiple-perspective.png)

## Filtering Criteria

There are 2 broad phase check before ray casting begins between the LoS points and Source Point.

- Vision Cone Check
- Vision Wedge Check

Their collider shape is determined by the **Angle** and **Length** of the input parameter.

Collision doesn't seem to matter for these checks. As long as entities are within these collision shapes they get added to the set of entities that get checked.

The visual indicator shows that the cast is from the Camera location. But from visual inspection it seems like entities between the Camera and Cast point gets LoS checked.

### Media:
- [Filtering Wedge](#filtering-wedge)
- [Filtering Cone](#filtering-cone)
- [Filtering Colision Behaviour](#filtering-colision-behaviour)
- [General Filtering Behaviour](#general-filtering-behaviour)

#### Filtering Wedge

![Filtering Wedge](./media/filtering_height.gif)

![Filtering Wedge Height](./media/filtering_height_step_down.gif)

#### Filtering Cone
![Filtering Cone](./media/filtering_cone.gif)

#### Filtering Colision Behaviour
![Filtering Under Over](./media/filtering_under_over.gif)

#### General Filtering Behaviour

![Filtering Pan Left Right](./media/filtering_pan_left_right.gif)

![Filtering Pan Up Down](./media/filtering_pan_up_down.gif)

## Selection Criteria

In order for an entity to be considered as **Selected** it must be within Line of sight of both the character model and source point.
The entity which holds the valid point with the closest distance to the camera projected line would be chosen as currently selected.

### Media:
- [Character Model LoS Requirement](#character-model-los-requirement)
- [Source Point LoS Requirement](#source-point-los-requirement)

#### Character Model LoS Requirement

![Model Los](./media/los_double_check_to_confirm.gif)

#### Source point LoS Requirement

![Cast point Los](./media/cast_point_los.gif)

## Methodology
This is a breakdown analyzing Deadlock's target detection system. How it does Line of Sight checks, and How does ability targeting works.

Using `citadel_debug_ent_los true` in the deadlock console.