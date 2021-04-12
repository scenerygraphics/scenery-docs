# Rendering Volumetric Data

For a general explanation on Volumen Rendering see [Volumen Ray Casting](https://en.wikipedia.org/wiki/Volume_ray_casting)

In Scenery volumen are represented by `Volumen` nodes. But unlike regular meshes those volumes are not rendered directly but together at the same time. This is coordinated by the `VolumeManager` which is `hubable` and a `Node` at the same time. 

The sampling of each volume is done in the same shader. The shader is auto generated and specific to the amount and type of used volumes. Therefore, adding a volume may trigger a shader rebuild which automatically handled by the framework.
For more see [Volumen Shader and Uniforms](../advanced-topics/volumen-shader-uniforms.md).

> **_NOTE:_** The scene graph subtree below a `Volume` node is scaled by the dimensions of the volume. Example: To move a sub node to the position of the voxel at (20,120,49) set its local position to (20,120,49).


##See Also 
- Chapter 6 in Ulrik Günters PHD Thesis
- Advanced Topic [Volumen Shader and Uniforms](../advanced-topics/volumen-shader-uniforms.md)