# Instancing

Instancing is a very cool feature, added to make the process of rendering a large number of objects a lot quicker, by submitting the object geometry only once, and rendering it many times in a single draw call:  Imagine you're making an educational movie and you want to animate thousands of blood cells moving through vasculature. If you would like to render all of the thousands of blood cells separately, who basically look the same, it would take a great amount of draw calls and therefore, resources. So instead, you create only one copy of each type of blood cells, and render many instances of it in one go.

On this page, we describe an example on how you can do it in scenery. The complete example can be found at `src/test/tests/graphics/scenery/tests/examples/advanced/BloodCellExample.kt`.

First, we need to create a mesh:

```kotlin
val erythrocyte = Mesh()
erythrocyte.readFromOBJ(Mesh::class.java.getResource("models/erythrocyte.obj").file)
erythrocyte.material = ShaderMaterial.fromFiles("DefaultDeferredInstanced.vert", "DefaultDeferred.frag")
erythrocyte.material.ambient = GLVector(0.1f, 0.0f, 0.0f)
erythrocyte.material.diffuse = GLVector(0.9f, 0.0f, 0.02f)
erythrocyte.material.specular = GLVector(0.05f, 0f, 0f)
erythrocyte.material.metallic = 0.01f
erythrocyte.material.roughness = 0.9f
erythrocyte.name = "Erythrocyte_Master"
erythrocyte.instancedProperties["ModelMatrix"] = { erythrocyte.model }

scene.addChild(erythrocyte)
```

Important here is the, you guessed it, `instancedProperties`. So lets have a look at what this actually is. `instancedProperties` is a parameter of the node class \(please do not get confused, the mesh class inherits from the node class\):

```kotlin
var instancedProperties = LinkedHashMap<String, () -> Any>()
```

As you can see, we store in the instanced properties the `model` matrix of our erythrocyte. The model matrix is the matrix governing how this object is positioned in 3D space.  
  
Now we can proceed. We will map through 40 erythrocytes and make them children of a cell container:

```kotlin
//This is the node we use to store our instances of the object
val container = Node("Cell Container")

val erythrocytes = (0 until 40).map {
  val v = Mesh()
  v.name = "erythrocyte_$it"
  v.instancedProperties["ModelMatrix"] = { v.world }
  v.metadata["axis"] = GLVector(sin(0.1 * it).toFloat(), -cos(0.1 * it).toFloat(), sin(1.0f*it)*cos(1.0f*it)).normalized
  v.parent = container

  //This part is only important for visualization; it makes the cells floating in space
  val scale = Random.randomFromRange(0.5f, 1.2f)
  v.scale = GLVector(scale, scale, scale)
  v.position = Random.randomVectorFromRange(3, -positionRange, positionRange)
  v.rotation.setFromEuler(
    Random.randomFromRange(0.01f, 0.9f),
    Random.randomFromRange(0.01f, 0.9f),
    Random.randomFromRange(0.01f, 0.9f)
  )

  v
}

// Here comes the important part: we add all the objects created to the 
// instances list of the master erythrocyte. 
erythrocyte.instances.addAll(erythrocytes)
```

Two things are of main interest here: First we have the `instancedProperties` again. But this time with the world matrix, which brings our individual model into world space. Then secondly, `instances` which  is a `CopyOnWriteArrayList` of Nodes. Meaning, it will copy the mesh data from the master object \(erythrocyte\) to each of the erythrocytes 0..40. Now all that's left to do is adding our erythrocytes to a scene:

```kotlin
scene.addChild(container)
```

Now you should be able to render the objects in much less time.

