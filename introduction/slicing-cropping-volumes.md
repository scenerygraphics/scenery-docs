# Slice and Crop Volume Renderings

Slicing and Cropping allow to view otherwise hidden, inner parts of a volume without tinkering with the transfer function or the data.

Slicing and cropping of a volume rendering is done by a `SlicingPlane` which is assigned to the `Volume`. Up to 16 `SlicingPlane` can be assigned to a volume via the `addTargetVolume` method. One SlicingPlane can be assigned to unlimited volumes. How those planes interact with the volume is governed by the `slicingPlaneEquations` property of the volume. The possible exclusive options are:

* **None**, no interaction between planes and volume. The volume will be rendered in full
* **Cropping**, the rendering will be split in half by a intersecting plane. One half will be rendered the other will not. Further intersecting planes may reduce the rendered parts further.
* **Slicing**, only a small slice around the plane will be rendered with full opacity. Multiple intersecting planes will each reveal the area around themselves.  
* **Both**, Both Cropping and Slicing are active. The area around the slicing plane will be rendered with zero transparency and bellow it the volume will be rendered regularly.

The `SlicingPlane` node itself has no geometry. To make it perceivable or allow user interaction it needs to be attached to other nodes. The calculation for the cropping/slicing happen in world space therefore volume and slicing plane transforms can and should be manipulated via the scene graph.

## See Also

* Scenery examples/volume/OrthoViewExample
* Scenery examples/volume/CroppingExample 

