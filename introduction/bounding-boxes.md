# Bounding Boxes

## What are Bounding Boxes?

You want to check off the box on bounding boxes, so let's cut the bad puns and dive right into it. To understand bounding boxes, consider this stock photo of the sherlock holmes among penguins and its bounding box:

![](<../.gitbook/assets/sherlock penguin (1).jpg>)

In short, a bounding box is the smallest box that contains every feature of an object. This makes them a powerful tool to realize intersections.

## How are bounding boxes handled in scenery?

\
There are many ways to define such a bounding box mathematically, in _scenery_, we do it via a min vector and a max vector:

```kotlin
open class OrientedBoundingBox(val n: Node, val min: Vector3f, val max: Vector3f)
```

Here is a visual explanation of what this looks like:

![](<../.gitbook/assets/BoundingBoxSketch - 2021-06-22 22.53.16.png>)

Note that both of these vectors are local coordinates so that the bounding box remains flexible when translating or rescaling a node.

Accessing the bounding box of a node is rather easy:

```kotlin
val someNode = ThisIsSomeNodeClass()
val boundingBox = node.getMaximumBoundingBox()
```

In case you are dealing with a more sphere-like object, e.g. a biological cell, you should consider using a bounding sphere instead. Simply use the function:

```kotlin
BoundingBox.getBoundingSphere()
```

