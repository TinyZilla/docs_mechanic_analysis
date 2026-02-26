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


## Cone Check Straight Below
![test cone below 2](/media/target_detection/cone-under-los-check.gif)

## Cone Check Step Down
![test cone step down](/media/target_detection/cone-step-down-horizontal.gif)

## Cone Check Below
![test cone below](/media/target_detection/cone-under-check.gif)
![test cone below 3](/media/target_detection/cone-under-over.gif)

## Casting Origin Cast
![test origin cast](/media/target_detection/origin-wall-los-different.gif)

## Casting Origin Collision
![test origin collision](/media/target_detection/origin-wall-collision.gif)

# Conclusion:
