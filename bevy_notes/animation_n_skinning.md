# Animation And Skinning Notes

- Mesh
    - What attrivutes are needed
- Bones
    - Inverse Position Matrix
    - How expendsive is it to copy the skins and have different bones?
- Animation Player
    - It just straight up modify the position of bones.

## Skeletal / Bone Systems
[Video Source: How Skeletal Animations and Skinning work in Games](https://www.youtube.com/watch?v=sgM1FpPd-Eo)

- Skeleton Information
    - Array of Bone Structure
        - Index of parents
        - Inverse Bind Pose Matrix
    - Usually less than 256 bones, so 8 bit memeory.
- Skinning Information
    - Binding 3d Mesh to bone hierarchy.
        - In Vertex data
            - Which Bones affect our verticies
            - Weight - How this particular bone affect the vertex.
- Animation Information
    - Interpolation of bone position between frames
    - model space transform need to calculate the chain of matrix multiplication of the bone chain.
        - This is expendisve with large number of bones & animations running at once.
- Skinning Matrix
    - Inverse Bind Pose Matrix * Transformation matrix from bone B to model space.

## Examples

- [Animated Mesh](https://bevy.org/examples/animation/animated-mesh/)
    - Example of Imported Mesh & Auto scene instancing.
- [Custom Skinned Mesh](https://bevy.org/examples/animation/custom-skinned-mesh/)
    - Example of Vertex Data & Manual Bone chain structure.
    - **Observation**: `Mesh::ATTRIBUTE_JOINT_INDEX` and `Mesh::ATTRIBUTE_JOINT_WEIGHT` are both 4 length array / vector. This means a single vertex can only be influenced by a maximum of 4 bones at once. This is done due to real time graphics limitaitons. (Google it.)
        > If you rig a character with more than 4 influences in modeling software and export it to a game engine, the engine will often "prune" or delete the smallest weights to normalize the vertex back to the 4-bone limit.

## Pose Snapshot Notes
There are two approaches I can think of:
1. Bake the deformation by animation to a mesh.
    - Pros:
        - Pay Computational Cost only once.
        - Good for GPU Time Constraint environment. Since Matrix math is expendsive.
    - Cons:
        - Takes up memory, both RAM and GPU Memory.
        - Since it's a different mesh I don't think Batching will apply.
        - The poses are probably single use and throw away. unless there's some caching mechanism for each instance the mesh is needed.
2. Keep the same mesh but create a different skeleton for the mesh to refer to.
    - Pros:
        - Mesh Batching
        - Skeletal Reuse for different poses.
    - Cons:
        - More Skeleton means more mesh deformation.
        - For meshes with a lot of verticies and bones this might be rough in terms of computation time. Since this might needed to be calculated per frame whenever the mesh is on screen.