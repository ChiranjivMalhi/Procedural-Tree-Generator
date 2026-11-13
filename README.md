# Procedural Tree Generator

**Unity · C# · Procedural Content Generation · Runtime Mesh Generation**

This tool grows 3D trees from a random seed and builds the trunk and branches as meshes at runtime. In the Unity Inspector, you can tune the silhouette, switch between upward and outward branch growth, and add foliage using your own leaf and flower prefabs.

## Source code availability

I’m keeping the source code private in accordance with my Georgia Tech course policy. I’ll upload it after the semester ends.

## Screenshots

> Add a screenshot of a generated tree here.

<img width="1200" height="700" alt="Screenshot 2026-10-25 002011" src="https://github.com/user-attachments/assets/1197f85f-29c3-42e5-85dc-d2e69880b9e2" />

https://github.com/user-attachments/assets/7500c26c-9ae9-411e-85c9-7a49d96ceec9

> Add a side-by-side comparison of the two growth modes and a close-up of the bark or foliage here.

<img width="457" height="492" alt="Screenshot 2026-10-16 211351" src="https://github.com/user-attachments/assets/52655b26-e83e-4601-988f-7654eacb40ea" />

<img width="477" height="481" alt="Screenshot 2026-10-16 211305" src="https://github.com/user-attachments/assets/ac085782-bfbd-4071-8caa-b5e7a6370507" />

Save the images in `screenshots/` and uncomment the corresponding Markdown lines to display them on GitHub.

## Getting started

Once the source code is available, follow these steps to run the generator in Unity.

1. Add the script to your Unity project as `TreeGenerator.cs`.
2. Create an empty GameObject and attach the **Tree Generator** component. Start at the scene origin for the first preview.
3. Assign a **Bark Material**. To generate foliage, assign both **Leaf Prefab** and **Flower Prefab**. Clear **Enable Leaves** for a bare tree.
4. Enter **Play Mode**. The component generates the tree in `Start()` and parents its meshes and prefab instances under the GameObject.
5. Exit Play Mode, adjust the Inspector settings, and enter Play Mode again to preview another tree. Change **Seed** to get a new variation with the same settings.

The source includes Unity Editor imports. For a standalone player build, remove the unused `using` directives that reference `UnityEditor`, NUnit, and Visual Scripting.

## Inspector controls

| Control | What it changes |
| --- | --- |
| Seed | Reproduces a tree with the same settings or generates a different variation. |
| Trunk Length, Section Height, and Trunk Base Radius | Set the number of trunk growth steps, the distance per step, and the starting radius. |
| Timestep | Sets the number of branch growth passes. Later branch orders have fewer steps available and use shorter steps. |
| Branching Start Height | Skips this many internodes at the base of the trunk before branching begins. |
| Max Branches and Branch Probability | Control the bud count and the chance an eligible internode produces buds. The bud count uses an exclusive upper bound when Max Branches is greater than one. |
| Growth | **Orthotropic** biases branches upward. **Plagiotropic** biases branches toward a more horizontal direction. |
| Tropism | Adds a downward bend for positive values and an upward bend for negative values. The effect increases with branch order. |
| Randomness and Surface Noise | Add variation to branch direction and mesh ring positions, and roughen the mesh surface, respectively. |
| Sections and Segments | Set the number of sampled rings per internode and vertices around each ring. Use at least 2 sections and 3 segments. |
| Enable Leaves | Toggles leaf and flower prefab placement along thin internodes. |

## How it works

1. **Grow the trunk.** The generator starts at the GameObject’s position and advances along a slightly perturbed upward direction. Each `Node` stores a position, radius, and local frame. An `InterNode` connects two adjacent nodes.
2. **Grow branches by order.** Each pass can sprout buds from the previous branch order. Bud directions use 137.5° golden-angle offsets around the parent’s local frame. Child branches start thinner than their parent and taper as they grow. The selected growth mode, tropism, and randomness steer each step.
3. **Build the mesh.** Each branch is sampled into circular vertex rings using its local frame and interpolated radius. The script connects the rings with triangles, assigns bark UVs around and along the branch, and recalculates normals. The trunk and branches are separate meshes.
4. **Place foliage.** Thin internodes receive leaf prefab instances in pairs, with a 90° rotation applied to the second instance. A small fraction of placements use a flower prefab instead.
