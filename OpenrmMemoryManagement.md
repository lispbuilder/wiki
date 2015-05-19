# Introduction #

LISPBUILDER-OPENRM attempts to hide much of the manual memory management cruft that is necessary if one interfaces to OpenRM using a language like C. Many of the objects defined in LISPBUILDER-OPENRM place a Lispy shell around a foreign object; for example the NODE, PRIMITIVE, CAMERA and LIGHT objects. We use TRIVIAL-GARBAGE to free foreign objects automatically at object finalization, reducing the need to manage references to objects and explicitly free memory when objects go out of scope.

# Assigning Vertex, Pixel, Color & Normal Data #

There are two mechansims for assigning datasets to objects in the scene graph (vertex data, color data, normal data and pixel data); **copy** and **share**.

## Copy ##

The object makes an internal copy of the dataset. Deallocation of memory becomes the responsibility of the foreign OpenRM object. For example, an OpenRM sprite primitive will copy image data from an external pixel array into an internal frame buffer. The memory managed by the primitive object is automatically freed when the object is removed from the scene graph.

Advantages:

  * Garbage collection is straighforward as memory is managed by the Scene Graph.
  * A Lisp vector can be used to represent vertex, color, pixel and normal data. The vector is a lightweight lisp object that allows very efficient lookups. Thus a CFFI layer is not necessary (that would be necessary when reading/writing to a foreign array.)

Disadvantages;

  * Memory is copied. This is inefficient when very large datasets are involved. The contents of the Lisp vector are copied into a foreign array and the OpenRM object again creates an internal copy of this array.
  * Datasets are not shared between application and scene graph. Modifying a single element in a data set requires that the entire dataset be again copied into the scene graph.
  * PRIMITIVE objects allow data to be written but not read. For example Vertex data once copied into a PRIMITIVE cannot be read out (This is not true for vertex and color data assigned to NODES however).

## Share ##

The object stores a reference to the dataset is stored. Deallocation of memory becomes the responsibility of the application. For example, a sprite primitive will not create an internal frame buffer but will instead store a reference to external pixel data. The object will not free memory when it is removed from the scene graph.

Advantages:

  * Memory is shared. Efficient as large data sets are not copied.
  * Because datasets are shared, any modification of the dataset by the application becomes immediately visible in the Scene Graph. For example, the application can update the pixel data in a texture that then becomes immediately visible when next the scene graph is rendered.
  * Data can be written and read at will inside objects. For example the Vertex in a PRIMITIVE can be easily read and updated.

Disadvantages:

  * Garbage collection and memory management is not straightforward. The Scene Graph uses foreign callbacks into Lisp to free foreign memory. This is inefficient when many small foreign objects must be freed.
  * A foreign object that contains vertex, color, pixel or normal data is not a lightweight object like a Lisp VECTOR and access is via the CFFI. If the dataset requires many frequent updates then it may be more efficient to store the dataset in a lisp vector and copy the data into the object.

# Garbage Collection of foreign objects (Object Finalization) #

Objects that inherit from FOREIGN-OBJECT are responsible for managing the lifecycle of a foreign object. These objects include NODE, PRIMITIVE, CAMERA, LIGHT, Vertex data, Color data etc.

The NODE object, once added to the scene graph, will have finalization disabled. A NODE object may be added to the scene graph many times. The scene graph increments a reference for the NODE each time it is added. The NODE is deleted when all it is no longer attached to the scene graph and the reference count reached zero. When a NODE is deleted all PRIMITIVES attached to the NODE are also deleted.

The PRIMITIVE object, once added to a NODE, will have finalization disabled. A PRIMITIVE may be added to the scene graph only once as the scene graph does not maintain a reference count for the PRIMITIVE as it does the NODE. As described above, all PRIMITIVES attached to a NODE are deleted when the NODE is deleted. If a PRIMITIVE had been added to the scene graph a second time then a crash would more than likely result. To duplicate PRIMTIVES, add the primitive to a NODE and then add the NODE to the scene graph multiple times.

The IMAGE object, once added to a PRIMITIVE, will have finalization disabled. An IMAGE object may be added only once to a scene graph as the scene graph does not maintain a reference count for the IMAGE as it does the NODE. To add multiple images to the scene graph, duplicate an image using IMAGE-DUPLICATE. If the image pixel data is contained in a VECTOR then each IMAGE will maintain an internal frame buffer and a copy of the pixel data. If the pixel data is contained in a foreign array, then this data is shared across IMAGES.

The Vertex, Color and Normal objects, once added to an IMAGE or PRIMITIVE will have finalization disabled. A copy of the datasets is made when contained in a VECTOR, otherwise only the reference to the dataset is stored.


There are two types of objects for the purposes of garbage collection. There are those objects that will automatically memory assigned to the foreign object freed when teh object goes out of scope, and there are objects

There are two types of objects in L

As described in 'Assigning Vertex, Pixel, Color & Normal Data' datasets may be copied or shared. When a dataset is shared finalization is immediately disabled to stop an object from freeing the shared memory when when it is finalized (almost certainly causing a crash).

PRIMITIVES may not be shared. Any time a primitive is added to a

S


Certain objects Objects that are added to the scene graph, such as a PIPE and NODE, will immedaitely

Prior to adding the object to the scene graph the default object behaviour is to free the allocated foreign object when the object goes out of scope. Object finalization is immediately disabled when the object is added to the scene graph or the object is added to another object. When finalization is disabled, the system will not attempt to free memory allocated to the foreign object when the object goes out of scope.

Finalization is disabled for the following objects when added to the scene graph;

  * PIPE
  * NODE

Finalization is disabled for the following objects when added to a NODE;

  * PRIMITIVE

Finalization is disabled for the following objects when added to a PRIMITIVE;

  * IMAGE
  * V2D, V2d**, V3D, V3D**, C3D, C3D**, C4D, C4D** (Vertex, color and normal data)

Finalization is disabled for the following ojbects when added to an IMAGE;

  * Pixel data

Finalization can be explicitely enabled or disabled by calling GC-P. It is not recommended to do this unless you know what you are doing.
