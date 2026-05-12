# Bevy Animation Notes

- [Animation Target Id](#animation-target-id)
- [Animatable Curve](#animatable-curve)
- [Animation Clip](#animation-clip)
- [Animation Graph](#animation-graph)
- [Animation Player](#animation-player)
- [Example](#example)

Note: There was a name change coming [Rename PR 1](https://github.com/bevyengine/bevy/pull/16280), [Rename PR 2](https://github.com/bevyengine/bevy/pull/22782) AnimationGraph -> BlendGraph

## Animation Target Id

- [AnimationTargetId](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationTargetId.html)
- Typically used alongside the [`AnimatedBy`](https://docs.rs/bevy/latest/bevy/animation/struct.AnimatedBy.html) component
    - A component that links an animated entity to an entity containing an **AnimationPlayer**

## Animatable Curve

- [AnimatableCurve](https://docs.rs/bevy/latest/bevy/animation/animation_curves/struct.AnimatableCurve.html)
- Indicate which property is being modified. Eg. `animated_field!(Transform::translation)`
- and the Curve for the animation
    - [Bevy 0.15 Curves Release Note](https://bevy.org/news/bevy-0-15/#curves)
    - [Curve Creation](https://docs.rs/bevy/latest/bevy/math/curve/index.html#defining-curves) --> [**Curve Creation Examples**](#curve-creation-examples)
        - [UnevenSampleCurve](https://docs.rs/bevy/latest/bevy/math/curve/struct.UnevenSampleCurve.html)
        - [EasingCurve](https://docs.rs/bevy/latest/bevy/math/curve/struct.EasingCurve.html)
        - Curve Manipulation
            - [**Curve Combination**](https://docs.rs/bevy/latest/bevy/math/curve/index.html#combining-curves)
            - [Curve Transformation](https://docs.rs/bevy/latest/bevy/math/curve/index.html#transforming-curves)
                - Map - **used for transforming the output and using it to drive something else**
                - Reparameterize - **preserves the curve’s shape but changes the speed and direction in which it is traversed**
    - [StableInterpolate::smooth_nudge](https://docs.rs/bevy/latest/bevy/prelude/trait.StableInterpolate.html#method.smooth_nudge)
        - `Vec3::ZERO.smooth_nudge(&Vec3::ONE, decay_rate, delta_time);`
        - **Frame Rate independent Lerping** for Types which impliment StableInterpolate

## Animation Clip
- [AnimationClip](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationClip.html)
- list of `VariableCurves` and the `AnimationTargetIds` to which they apply
- Because animation clips refer to targets by UUID, they can target any entity with that ID.
    - Target Entity has to also have the `AnimatedBy` Component to be animated.
    - This is useful for Bone animations -- as long as bone structure is the same. E.G. Same `AnimationTargetIds` Genereated from the same path, then the animation clip could be shared between multiple animation players & Skeletons.
- [AnimationClip::add_curve_to_target()](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationClip.html#method.add_curve_to_target)
- Also contains <ins>**Animation Events**</ins> to trigger observers for gameplay stuff.
    - [Bevy Example](https://bevy.org/examples-webgpu/animation/animated-mesh-events/)
    - [AnimationClip::add_event()](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationClip.html#method.add_event)
        - [AnimationClip::add_event_to_target()](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationClip.html#method.add_event_to_target)
            - To a specific Bone w/ AnimationTargetId.
            - Can be referenced in handler. Gets an Entity reference too.
        - [AnimationClip::add_event_fn()](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationClip.html#method.add_event_fn)
            - Does not need to register observer before hand. Just add a handler as a lambda function.
    - Event Types
        - [AnimationEvent](https://docs.rs/bevy/latest/bevy/animation/trait.AnimationEvent.html) -- No Specific target
        - [AnimationEventTrigger](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationEventTrigger.html) -- Handler for `add_event_to_target`. This will provide the entity context.

## Animation Graph

- [AnimationGraph](https://docs.rs/bevy/latest/bevy/animation/graph/struct.AnimationGraph.html)
- Not super indepth about this yet.
- But from the docs it handles **Masking** and **Blending** between animation clips.

Add to this section when diving more indepth.

## Animation Player

- [AnimationPlayer](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationPlayer.html)
- Not much is known about this too.
- I can change the speed scale with [AnimationPlayer.adjust_speeds](https://docs.rs/bevy/latest/bevy/animation/struct.AnimationPlayer.html#method.adjust_speeds), but it sucks. Just get the playing animations and modify the speed there directly. `playing_animation.set_speed`. Check source.

### Example 
```rust
// Example Code: 0.18

use avian3d::prelude::*;
use bevy::{
    animation::{AnimatedBy, AnimationTargetId, animated_field},
    camera_controller::free_camera::{FreeCamera, FreeCameraPlugin},
    image::{ImageAddressMode, ImageLoaderSettings, ImageSampler, ImageSamplerDescriptor},
    math::Affine2,
    prelude::*,
};

pub(crate) struct BouncyBallScenePlugin;

impl Plugin for BouncyBallScenePlugin {
    fn build(&self, app: &mut App) {
        app.add_plugins(FreeCameraPlugin);
        app.init_resource::<SceneSetting>();
        app.add_systems(PostStartup, (setup_level_space, setup_bouncy_balls).chain());
    }
}

#[derive(Resource, Reflect, Debug)]
#[reflect(Resource)]
struct SceneSetting {
    floor_scale: Vec2,
}

impl Default for SceneSetting {
    fn default() -> Self {
        Self {
            floor_scale: Vec2 { x: 20.0, y: 10.0 },
        }
    }
}

// The idea is.. I want to create two bouncy balls. One driven by animation, one driven by physics.
// This will be able to test Slowdown and Pause features.
fn setup_bouncy_balls(
    mut commands: Commands,
    mut meshes: ResMut<Assets<Mesh>>,
    mut materials: ResMut<Assets<StandardMaterial>>,
    mut animations: ResMut<Assets<AnimationClip>>,
    mut graphs: ResMut<Assets<AnimationGraph>>,
) {
    const BALL_INITIAL_HEIGHT: f32 = 3.5;
    const BOUNCE_TIMING: f32 = 0.85;
    // Ball Mesh
    let ball_mesh = meshes.add(Sphere::default());

    // Ball Materials
    let animated_ball_material = materials.add(Color::srgb(0.8, 0.7, 0.6));
    let physics_ball_material = materials.add(Color::srgb(0.0, 0.7, 0.6));

    // Animation Target Id
    let name = Name::new("AnimatedBall");
    let ball_anim_target_id = AnimationTargetId::from_name(&name);

    // Animation Curves
    let timing_curve =
        UnevenSampleAutoCurve::new([0.0, BOUNCE_TIMING, BOUNCE_TIMING * 2.0].into_iter().zip([
            Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
            Vec3::new(-1.0, 0.5, 0.0),
            Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
        ])).unwrap();

    // Try Different Kind of Curves.
    let interval = interval(0.0, BOUNCE_TIMING).unwrap();

    let bounce_down_curve = EasingCurve::new(
        Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
        Vec3::new(-1.0, 0.5, 0.0),
        EaseFunction::SineIn,
    )
    .reparametrize_linear(interval)
    .unwrap();

    let bounce_up_curve = EasingCurve::new(
        Vec3::new(-1.0, 0.5, 0.0),
        Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
        EaseFunction::BounceOut,
    )
    .reparametrize_linear(interval)
    .unwrap();

    let bounce_curve_chainned = bounce_down_curve.chain(bounce_up_curve).unwrap();

    let ball_anim_curve =
        AnimatableCurve::new(animated_field!(Transform::translation), bounce_curve_chainned);

    // Animation Clip
    let mut ball_animation_clip = AnimationClip::default();
    ball_animation_clip.add_curve_to_target(ball_anim_target_id, ball_anim_curve);
    let ball_animation_clip = animations.add(ball_animation_clip);

    // Animation Graph
    let (graph, animation_index) = AnimationGraph::from_clip(ball_animation_clip);
    let graph = graphs.add(graph);

    // Animation Player
    let mut animation_player = AnimationPlayer::default();
    animation_player.play(animation_index).repeat();

    // !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
    //  IMPORTANT: First, add the animation graph & animation player components.
    //  THEN insert the Animation Target ID and Animated By
    //  since AnimatedBy requires an entitty reference.
    // !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

    // Animated Ball
    let animated_ball = commands
        .spawn((
            Mesh3d(ball_mesh.clone()),
            MeshMaterial3d(animated_ball_material),
            // Anim Graph and Players
            AnimationGraphHandle(graph),
            animation_player,
            name,
        ))
        .id();

    // This tells the ball what it's ID is and who is animated by...
    commands
        .entity(animated_ball)
        .insert((ball_anim_target_id, AnimatedBy(animated_ball)));

    // Physics Ball
    commands.spawn((
        Mesh3d(ball_mesh),
        MeshMaterial3d(physics_ball_material),
        Transform::from_xyz(1.0, BALL_INITIAL_HEIGHT, 0.0),
        ColliderConstructor::Sphere { radius: 0.5 },
        RigidBody::Dynamic,
        // Make the ball perfectly bouncy
        Restitution::new(1.045),
        Friction::ZERO,
    ));
}

fn setup_level_space(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    scene_setting: Res<SceneSetting>,
) {
    // Light
    commands.spawn((
        DirectionalLight {
            shadows_enabled: true,
            ..Default::default()
        },
        Transform::from_rotation(Quat::from_rotation_x(-45f32.to_radians())),
    ));

    // Setup a Gizmo for orientatin
    let mut gizmo = GizmoAsset::new();
    gizmo.axes(Transform::from_xyz(0.0, 1.0, 0.0), 2.);
    let gizmo_handle: Handle<GizmoAsset> = asset_server.add(gizmo);
    commands.spawn(Gizmo {
        handle: gizmo_handle,
        ..Default::default()
    });

    // Floor
    let floor_mesh = asset_server.add(
        Plane3d::new(Vec3::Y, scene_setting.floor_scale)
            .mesh()
            .build(),
    );
    let floor_texture =
        asset_server.load_with_settings("textures/floor_texture.ktx2", |s: &mut _| {
            *s = ImageLoaderSettings {
                sampler: ImageSampler::Descriptor(ImageSamplerDescriptor {
                    // rewriting mode to repeat image,
                    address_mode_u: ImageAddressMode::Repeat,
                    address_mode_v: ImageAddressMode::Repeat,
                    ..default()
                }),
                ..default()
            }
        });
    let floor_material = asset_server.add(StandardMaterial {
        base_color_texture: Some(floor_texture),
        uv_transform: Affine2::from_scale(scene_setting.floor_scale * 0.5),
        ..default()
    });

    commands.spawn((
        Mesh3d(floor_mesh),
        MeshMaterial3d(floor_material),
        RigidBody::Static,
        ColliderConstructor::HalfSpace {
            outward_normal: Vec3::Y,
        },
        // Make the surface Perfectly Bouncy for Physics.
        Restitution::PERFECTLY_ELASTIC,
        Friction::ZERO,
    ));

    // Camera
    commands.spawn((
        Camera3d::default(),
        Transform::from_xyz(0.0, 2.0, 4.0),
        FreeCamera {
            sensitivity: 0.2,
            friction: 25.0,
            walk_speed: 3.0,
            run_speed: 9.0,
            ..default()
        },
    ));
}
```

### Curve Creation Examples 
```rust
// Sample Curve Example 0.18

    let timing_curve =
        UnevenSampleAutoCurve::new([0.0, BOUNCE_TIMING, BOUNCE_TIMING * 2.0].into_iter().zip([
            Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
            Vec3::new(-1.0, 0.5, 0.0),
            Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
        ])).unwrap();

// Easing Curve Example 0.18

    let interval = interval(0.0, BOUNCE_TIMING).unwrap();

    let bounce_down_curve = EasingCurve::new(
        Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
        Vec3::new(-1.0, 0.5, 0.0),
        EaseFunction::SineIn,
    )
    .reparametrize_linear(interval)
    .unwrap();

    let bounce_up_curve = EasingCurve::new(
        Vec3::new(-1.0, 0.5, 0.0),
        Vec3::new(-1.0, BALL_INITIAL_HEIGHT, 0.0),
        EaseFunction::BounceOut,
    )
    .reparametrize_linear(interval)
    .unwrap();

    let bounce_curve_chainned = bounce_down_curve.chain(bounce_up_curve).unwrap();
```