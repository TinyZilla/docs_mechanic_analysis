# Target Detection
This is a breakdown analyzing Deadlock's target detection system. How it does Line of Sight checks, and How does ability targeting works.

Using `citadel_debug_ent_los true` in the deadlock console.

1. How are the target LOS position determined?

2. Where is the characters's Origin point? or where the indicator marker is located?

3. Where is the LOS cast source point located?

    3.1 How does it intereact with Walls / Collisions? 

4. Abilities targeting minions specifically. How are they checked? (Rem)

5. Character LOS check from other player's perspective, and their rotational points.

6. Extreme view angles, and how the aim priorities is determined? Ground Cone?

## Origin Point Location

![test](/media/target_detection/deadlock_los_breakdown.png)

## Different Perspective

![test2](/media/target_detection/casting-multiple-perspective.png)

## Cone Check Same level
![test cone same level](/media/target_detection/cone-same-level-horizontal.gif)
![test cone same level vert](/media/target_detection/cone-same-level-vertical.gif)

Notes:
- left right seems to only start ray casting when it's within the floor cone.
- up down seems to always be casting since the target is within the floor cone.

## Cone Check Straight Below
![test cone below 2](/media/target_detection/cone-under-los-check.gif)

Note:
- Looks like floor cone has a height to it.
- Ray casting starts when either condition is achieved.
    - the target is within the floor cone.
    - or the target is within the aim cone. regardless of wall collision. 

## Cone Check Step Down
![test cone step down](/media/target_detection/cone-step-down-horizontal.gif)

Note:
- The height of the floor cone doesn't seem that high.

## Cone Check Below
![test cone below](/media/target_detection/cone-under-check.gif)
![test cone below 3](/media/target_detection/cone-under-over.gif)

## Casting Origin Cast
![test origin cast](/media/target_detection/origin-wall-los-different.gif)

Note:
- Depends on ray cast origin position, certain angles can cause LOS loss. 
- Ray cast origin position is projected forward from the gun.

## Casting Origin Collision
![test origin collision](/media/target_detection/origin-wall-collision.gif)

Note:
- seems like it's not a simple straight cast forward. it seems to collide in a circle shape.

# Conclusion:

1. There's a broad phase entity check first.
    - Floor cone (Frustum of a Square Pyramid)
    - Aim cone (Frustrum of a Cone)
2. Line of sight ray cast to the target entity's ray cast points.
    - Shoulder points are equal offset from the head
    - the shoulder points rotates according to the caster's position.
    - the head doesn't need to be directly up from entity origin
    