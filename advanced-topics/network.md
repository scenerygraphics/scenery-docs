# Network

The network synchronization of scenery has two targets. First for CAVE setups the scene needs to be rendered by multiple nodes from different perspectives and second multi user volume viewing sessions using networked computers.

### How to Start

The networking capabilities of scenery are enabled via VM parameters. The server has to be started with `-Dscenery.Server=true` and the client needs to be pointed to the server via `-Dscenery.ServerAddress=tcp://127.0.0.1`

The server can be pretty much any scenery scene (maybe with some adjusments, see below). For a simple client use \[SlimClient].

**Additional VM Parameters**

* For server and client
  * `scenery.MainPort` - Port for the main channel. Default: `6040`
  * `scenery.BackchannelPort` - Port for the Backchannel. Default: `6041`

### Relevant Examples

* \[SimpleNetworkExample]
* \[NetworkVolumeExample]
* \[SlimClient]

### How it works

Once the server application is started it begins scanning and registering the scene graph for objects which implement the \[Networkable] interface. By default that are all nodes connected to the root node and their attributes.

When a client requests a resync all registered objects are serialized and send to all connected clients. Upon receiving an object the client checks whether it has seen one with that id before and if not adds its to its scene. If the objects parent has not been synced yet it is put in a waiting queue and added once the parent has been synced it is added. If the object is already there the existing object is simply updated with the values of the new object.

If the server registers a change in a registered object it sends an update with the new object to all clients.

**Relevant Classes**

* \[Networkable]
* \[NodePublisher]
* \[NodeSubscriber]

### Slim Client

The \[SlimClient] is a scenery application which offers a mostly bare scene in which scenes from a server might be loaded. In addition to that it offers its own camera if desired. If the VM parameter `-Dscenery.RemoteCamera=true` is set cameras from the server scene are disregarded and a local one is used. If it is not or to false set the server scene has to provide a camera.

### Network Adjustments

* Camera
  * \[Camera.wantsSync] - allows disabling the registration of the camera for syncing. Default: true
* Material
  * \[DefaultMaterial.syncronizeTextures] - if set to true trys to transmit the textures over the network. Should be disabled for large textures. Default: True
* Volume
  * \[Volume.forNetwork] - Volume creation method for networkable volumes. For more see section below.

### Implementing the Networkable Interface

#### New Objects

There are two ways to add objects for the client. The default way is to take the deserialized object from the server and simply add it to its corresponding parent. This works for most cases. But some objects require the constructor be run locally or with specific parameters. For those cases there are the `getConstructorParameters()` and `constructWithParameters(parameters)` functions in the `Networkable` interface. No matter how the object was created afterwards the update method is called with the same object from the server again. The update happens after the object has been added to the scene graph.

**Example**

\[PointLight]

#### Updating Objects

Every `Networkable` object needs to implement the `update` method. In the `update` method a fresh copy from the server is given as parameter and should be used to copy over relevant values to the client-side object.

Properties which are marked `@Transient` are not serialized and therefore not available on the client-side copy of a object from the server. If those properties need to be synced anyway they should be returned in a serializable form in an overwrite of the `getAdditionalUpdateData()` method. This method is called on the server at the serialization and the results are transmitted next to the object. The transmitted result of the `getAdditionalUpdateData()` function is another parameter of the update function. Special attention has to be paid to parent classes which might also have additional data. This data has to be handled manually in the overriding methods.

References to other `Networkable` objects besides parent/child relations of the scene graph and node/attribute relations can't be synced automatically. For those the `Networkable.networkID` needs to be saved as additional data on the server side and resolved in the `update` method with the `getNetworkable` lambda parameter.

**Attention: The first Update**

If the object was not created with a local constructor but deserialization then the first update will be "with itself". This might be a source of sneaky bugs when for example lists which where cleared on the "client" object are now also empty on the "server" object.

**Examples**

\[DefaultMaterial] - AdditionalUpdateData \[DefaultSpatial] - GetNetworkable, First Update

#### Making Changes Known

For the server to register a change the `modifiedAt` property need to be updated. For convenience the `updateModifiedAt` method simply may be called like in \[DefaultSpatial].

#### Disabling Syncing

It might be needed to prevent the server from registering an object. If `wantsSync` returns false the object will be skipped in the registration like in \[Camera].

#### Registering Subcomponents

For the server to register objects out of the scene graph they need to be returned by `getSubcomponents`. The registration of attributes is handled that way for \[DefaultNode]. One could extends this for rarely changing data objects to reduce the amount of times they are transmitted.

#### On transmitting lambdas

At the time of writing serializing lambdas is wonky. Don't expect it to work. An alternative is to generate them new on the client side in the `constructWithParameters` or `update` method.

### Volumes

Syncing volume data is currently not possible. Therefore the data has to be available locally.

To initialize a volume node with sync support the `Volume.forNetwork` create method should be used. It takes an implementation of the \[VolumeInitalizer] interface.

There are already two implementation in this repo. The first is part of scenery it self \[VolumeFileSource].

`VolumeFileSource` has two parameters with each two options:

* `path`
  * `Given(val filePath: String)` - for fixed file path witch are the same on every machine (eg. network drive or something like "C://Volume")
  * `Settings(val settingsName: String = "VolumeFile")` - the file path is taken from the VM parameter "-DVolumeFile=$path$" of each individual application
  * `Resource(val path:String)` - the volume is a resource reachable by the java loader
* `type`
  * `TIFF` - tiff file format
  * `SPIM` - Spim xml data format

The other implementation is \[IJVolumeInitializer] which can be found in the \[NetworkVolumeExample]. It takes a path/url and opens it using the ImageJ framework.

**Examples**

\[NetworkVolumeExample]
