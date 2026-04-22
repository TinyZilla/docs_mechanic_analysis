# Time Dilation

- [Chain Attack Timings](#chain-attack-timings)
- [Projectiles](#projectiles)
- [Dodge Counter Timings](#dodge-counter-timings)
- [Parry](#parry)

There seems to be a <u>**Friendly / Enemy Particle distinction**</u>. It is possible for enemy particle to freeze while player character partiles moves at full speed. In chain trigger everything freezes. I seee some gameplay potential where the enemy triggers the time slow instead.

From my understanding <u>**physics is entirely Kinematic based**</u>. Friendly and Enemy projectiles and movement can run on different time ratios. That either means the application scales with different time deltas or delta multipliers. It is possible that there's two different physics worlds running at two different clocks, but it's complicated to maintain.

## Chain Attack Timings

- [Nicole](#nicole-chain-timing)
- [Caesar](#caesar-chain-timing)
- [Koleda](#koleda-chain-timing)
- [Billy](#billy-chain-timing)
- [Miyabi](#miyabi-chain-timing)

Averages at 0.16s - 0.2s ish at 24 FPS. (keep in mind of low framerate & human error) That's rougly 10 ticks of FixedUpdate tick at 60 Hz (0.01666666666s). So there IS a bit of a time slow transition but it's very fast.

With this short of a transition time the Easing function is probably not needed...

The transition feel probably comes from the gradiant around the sides from the UI & The Camera Zoom positioning Tween. The time slow is near instant.

All of the chain attacks -- <u>**Time slow kicks in some time after the initial hit**</u>.
In Caesar's case, the 2nd hit follow up happens after the timing of the first hit, so freeze frame is cool.

#### Nicole Chain Timing

![nicole chain](./media/t_slow_chain_nicole.gif)

#### Caesar Chain Timing

![caesar chain timing](./media/t_slow_chain_caeser.gif)

#### Koleda Chain Timing

![koleda chain timing](./media/t_slow_chain_kolaeda.gif)

#### Billy Chain Timing

![billy chain](./media/t_slow_chain_billy.gif)

#### Miyabi Chain Timing

![miyabi chain](./media/t_slow_chain_miyabi.gif)

## Projectiles

- [Enemy Projectiles](#enemy-projectiles)
- [Friendly Projectiles](#friendly-projectiles)

Similar to dodge counter, the controlled character moves at full speed. While the enemy movement is slowed down. This includes projectiles and VFX from friendly source and enemy source.

### Enemy Projectiles

![molotove thrower projectiles](./media/t_slow_enemy_projectile.gif)

### Friendly Projectiles

![astra projectiles](./media/t_slow_dodge_astra_atk.gif)

## Dodge Counter Timings

- [Billy Dodge](#billy-dodge-timing)
- [Astra Dodge](#astra-dodge-timing)

On a dodge counter, the controlled character moves at full speed. While the enemy movement is slowed down.

#### Billy Dodge Timing

![Billy Dodge Timing](./media/t_slow_dodge_billy.gif)

#### Astra Dodge Timing

![Astra dodge timing](./media/t_slow_dodge_astra.gif)

## Parry

- [Parry Timing](#parry-timing)
- [Parry Camera Movement](#parry-camera-transition)

There is a bit of a time freeze during parry as shown by the freezing on the enemy particle system. The purpose is probably to simulate hit lag / impact of the parry. Roughly 0.5 second duration.

### Parry Timing

![Parry Time Slow Timing](./media/t_slow_parry.gif)

### Parry Camera Transition

- [Camera Front](#parry-camera-front)
- [Camera Right](#parry-camera-right)

On parry, the camera seems to move to a fixed position. There's two of them. One on the Back Left and Back Right side of the character.

The camera will move to the closest point. When the camera is roughly centered on the front or back side of the character, the default is Back Right.

#### Parry Camera Front
![Parry Camera Front](./media/t_slow_parry_camera_front.gif)

#### Parry Camera Right

![Parry Camera Right](./media/t_slow_parry_camera_right.gif)