# Rendering Volumetric Data

For a general explanation on Volume Rendering see [Volume Ray Casting](https://en.wikipedia.org/wiki/Volume\_ray\_casting)

In Scenery volumes are represented by `Volume` nodes. But, unlike regular meshes, those volumes are not rendered directly, but together at the same time. This is coordinated by the `VolumeManager,` which is both `Hubable` and a `Node` at the same time.

The sampling of each volume is done in the same shader. The shader is auto generated and specific to the amount and type of used volumes. Therefore, adding a volume may trigger a shader rebuild, which is automatically handled by the framework. For more info, see [Volume Shaders and Uniforms](../advanced-topics/volumen-shader-uniforms.md).

> _**NOTE:**_ The scene graph subtree below a `Volume` node is scaled by the dimensions of the volume. Example: To move a sub node to the position of the voxel at (20,120,49), set its local position to (20,120,49).

## See Also

* Chapter 6 in Ulrik Günters PHD Thesis
* Advanced Topic [Volume Shaders and Uniforms](../advanced-topics/volumen-shader-uniforms.md)
