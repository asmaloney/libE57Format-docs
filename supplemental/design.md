# Library Design {#design}

## The API objects

This API is described in C++, and is object-oriented. There are 15 types of objects, each described in a C++ class. There is a class that encapsulates the E57 file (ImageFile), a class for each of the eight E57 primitive elements described in the standard (IntegerNode, ScaledIntegerNode, FloatNode, StringNode, BlobNode, StructureNode, VectorNode, and CompressedVectorNode) and a base class that encapsulates the common functionality of all nodes (Node). There are two classes that keep track of the block reads/writes to the binary sections (CompressedVectorReader, CompressedVectorWriter) and a class to manage buffers for these block transfers (SourceDestBuffer). Finally, there is a class to organize the reporting of errors (E57Exception), and a class for miscellaneous functions that are not associated with any of the other objects (E57Utilities).

## Set-once design

The primary motivation for the E57 file is to be a conduit between the proprietary formats of two different vendors' software (the reader and the writer). It is not a general-purpose 3D database. The API design reflects this use case by not allowing modifications of data. This simplifies the implementation. For example, it is not possible to change a value of a node, or delete an attached child node, and it is an error to attempt to attach a node to the tree using an already existing name.

## Handle semantics

All but one of the classes (the exception being E57Exception) have handle semantics, which means that there is a level of indirection. The API classes are actually just pointers to an underlying object. The pointers are smart (they have reference counts), so the user doesn't have to explicitly free them. If two handles exist to the same underlying object, the object is not deleted until both handles are destroyed. So the programmer is not burdened with keeping track of who is going to delete an object, the object "deletes itself" when all the references to it are gone. This simplifies the use of the API, especially in the creation of trees of objects. After you attach some object into a tree, there will be two references to the object, yours and the parent of the object in the tree. So when you delete your reference, the parent's
reference will keep the object alive (until the parent is destroyed).

A second benefit of smart pointers is that the copy and assignment functions in the classes are very cheap. They just copy or assign the handle, not the underlying object. So function call arguments (that are API objects) are always "call-by-reference", and the underlying object is not copied. It is impossible to assign the underlying state of one object directly into another. This restriction fits well into the set-once scheme described above.

The bottom line is that you should not use new/delete with the E57 objects, nor reference types (e.g. ImageFile&). You don't have to keep track of who will delete the object, nor try to speed up the passing of handles to functions. And the API objects clean up after themselves when an exception occurs.

## No default constructors

None of the API classes contain default constructors, so there is no equivalent to a NULL handle (with no underlying object). So if you have an API handle, you know it points to a valid object.
