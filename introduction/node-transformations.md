---
description: This page details how nodes are placed in the world coordinate system.
---

# Node transformations

## Overview

Nodes with attribute type **Spatial** can be positioned in the world coordinate system. By default, the spatial properties `position`, `rotation`, and `scale` are used:

```kotlin
with(myNode) {
  spatial {
    position = Vector3f(0.0f, 0.0f, 5.0f)
    rotation = Quaternionf(0.0f, 0.0f, 0.0f, 1.0f)
    scale = Vector3f(2.0f, 1.0f, 1.0f)    
  }    
}
```

These properties are used by `(Default)Spatial::composeModel` (or any class overriding this function) to construct the Node's model matrix. Model matrices are 4x4 matrices that determine the Node's transformation within the world coordinate system, and they use [homogenous coordinates](https://en.wikipedia.org/wiki/Transformation\_matrix) (if you want to know more about homogenous coordinates and their general application in computer graphics, [check out the Realtime Rendering book and website by Tomas Akenine-Möller and colleagues, especially chapter 4 on Transforms](http://www.realtimerendering.com/#xforms)).

If the Node has any parent objects, the world matrix of it is updated by `(Default)Spatial::updateWorld()`. If a Node does not have a parent, the world matrix will simply be its model matrix. If it does have a parent, the world matrix will be the model matrix, multiplied with the parent's world matrix, such that hierarchical transforms become possible.

## Update cycle

An important aspect to know about model and world matrices is the update cycle. Updates to the world and model matrix are handled asynchronously by scenery. This means any changes e.g. to the position, rotation, and scale properties _will not be reflected immediately in the model and world matrix_. Both matrices are updated in the background by scenery such that they are available to the renderer with the next frame rendered after the change of properties occurred:

```kotlin
v.spatial().scale = Vector3f(3.0f, 10.0f, 1.0f)
System.out.println("v.model: " + v.spatial().model)
// Output (Matrix is not immediately updated and is still unity):
// 1.000E+0  0.000E+0  0.000E+0  0.000E+0
// 0.000E+0  1.000E+0  0.000E+0  0.000E+0
// 0.000E+0  0.000E+0  1.000E+0  0.000E+0
// 0.000E+0  0.000E+0  0.000E+0  1.000E+0
```

Any manual changes to the world and model matrix will be overwritten by `composeModel()`.

_Again, do not rely on the model and world matrices to be up-to-date after changing transform properties._

### Overriding default matrix composition behaviour

As stated above, scenery by default takes the position, rotation, and scale properties into account when constructing a Node's model matrix. These properties were chosen as they are the most common ones used. However, you might feel the need to introduce additional transforms into the world matrix, such as skew. This is possible in two ways:

1. Setting `wantsComposeModel = false`: This will cause scenery to not run the `composeModel()` routine, but it will use whatever matrix you have provided as the Node's `model` matrix property. The world matrix will still be the parent's world matrix times your custom model matrix.
2. Overriding `composeModel()`: This is possible when introducing a new [Attribute](attributes-api.md) type, and will integrate your custom composeModel() routine within scenery's default update cycle.

### Manually updating the model and world matrices

As said, the model and world matrices are only updated before the next frame is rendered. This behaviour can be overridden as well, but is discouraged, as it goes outside of scenery's expected update cycle (inconsistencies should not be expected though, as the `updateWorld()` method is only called as [`@Synchronized` method](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html)). Only use if strictly necessary, or for debug purposes.

The above example then changes to:

```kotlin
v.spatial().position = Vector3f(-3.0f, 10.0f, 0.0f)
v.spatial().updateWorld()
System.out.println("v.model: " + v.spatial().model)
// Output (Matrix is updated to what's expected, if updated immediately):
// 3.000E+0  0.000E+0  0.000E+0  0.000E+0
// 0.000E+0  1.000E+1  0.000E+0  0.000E+0
// 0.000E+0  0.000E+0  1.000E+0  0.000E+0
// 0.000E+0  0.000E+0  0.000E+0  1.000E+0
```

Which is what would be expected if updates to the matrices happed immediately.
