# Instancing

Instancing is a very cool feature, added to make the whole process of rendering a lot quicker. If your rendering process is too slow, this might be the page you're looking for.

So, what is Instancing?   
_Feel free to skip this part if you're familiar with the concept and just want to know, how to use it in scenery_   
Basically, Instancing is rendering multiple copies of a mesh in a scene at once. Imagine you're making a movie and you want to animate a legion of the roman empire. If you would like to render all of the 5000 soldiers, who basically look the same, it would take a great amount of computational ressources. So you render only one of them and copy this instance of a soldier.

Now we will offer an example on how you can do it in scenery. The complete class can be found at /test/examples/advanced/BloodCellExample.kt.

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

Important here is the, you guessed it, "instancedProperties". So lets have a look at what this actually is. "InstancedProperties" is a parameter of the node class \(please do not get confused, the mesh class inherits from the node class\):

```kotlin
    var instancedProperties = LinkedHashMap<String, () -> Any>()
```

As you can see, we store in the instanced properties the "model" of our erythrocyte. This model essentially is a Matrix used to transform the our geometry object from 3D to a 2D representation.  
  
Now we can proceed. We will create a map with 40 erythrocytes and make them children of a cell container.

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
            //Here comes the substantial part: we add all the objects created to the instances of erythrocyte. 
            erythrocyte.instances.addAll(erythrocytes)
```

Two things are of main interest here: First we have the "InstancedProperties" again. But this time with the world matrix, which brings our individual model into world space. Then secondly, "Instances" which  is a CopyOnWriteArrayList of Nodes. Meaning, it will copy the mesh data from the master object \(erythrocyte\) to each of the erythrocytes 0..40. Now all thats left to do is adding our erythrocytes to a scene:

```kotlin
    scene.addChild(container)
```

Now you should be able to render the objects in much less time.

