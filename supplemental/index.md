# libE57Format Documentation {#mainpage}

# Introduction
This browser-based document describes the [libE57Format](https://github.com/asmaloney/libE57Format) API (Application Programmer Interface), which is a collection of functions that help a C++ programmer read and write ASTM E57 format files. The API acts as a documented software connector between an application (above the interface) and an implementation of the API.

## This Documentation
This documentation was generated from the comments in the code that existed in the reference implementation ([see below](#fork)). These docs are probably out of date and may not be complete, so consider them a starting point. Please submit any updates and fixes (for [these docs](https://github.com/asmaloney/libE57Format-docs/issues) or [in the code](https://github.com/asmaloney/libE57Format/issues)) so they may be improved over time.

# Fork {#fork}
libE57Format is a fork of [E57RefImpl](https://sourceforge.net/projects/e57-3d-imgfmt/) v1.1.332.

The original source is from [E57RefImpl 1.1.332](https://sourceforge.net/projects/e57-3d-imgfmt/files/E57Refimpl-src/) and then everything was stripped out except the main implementation for reading and writing E57. This version also removes the dependency on [Boost](http://www.boost.org/) and requires C++11.

Many, many other changes were made prior to the first release of this fork. See the [CHANGELOG](https://github.com/asmaloney/libE57Format/CHANGELOG.md) and git history for details.

## Why Fork?
The E57RefImpl code hasn't been touched in years and I ([Andy Maloney](https://github.com/asmaloney/)) wanted to make changes to compile this library with macOS. Forking it gave me more freedom to update the code and make changes as required.

I changed the name of the project so that it is not confused with the **E57RefImpl** project. I have also changed the main include file's name from `E57Foundation.h` to `E57Format.h` to make sure there is no inclusion confusion.

Versions of **libE57Format** started at 2.0.

# ASTM standard
libE57Format is not sanctioned or approved in any way by ASTM. The designer of the API and author of the original Reference Implementation (Kevin Ackley) was heavily involved in the drafting of the E57 format standard. However, the E57 Standard for 3D Imaging Data Exchange is the final word on what is a legal/valid .e57 file.

# Do I need to get the ASTM standard?
You should. This API provides the building blocks (the E57 primitive elements) for constructing and interpreting an E57 file, but the data structures that are built with these building blocks are up to the user of the API. The required names and types of these data structures (not to mention their meanings) are specified in the ASTM standard, so you need a copy.

ASTM owns the standard which may be purchased on the [ASTM website](https://www.astm.org/Standards/E2807.htm). Only a very few of the 100+ ASTM committees make money, and E57 isn't one of them. So support your industry and buy a copy.

# Is the E57 format XML?
Only partially, but it's a highly specialized XML. It's better think of an E57 file as hierarchical tree of eight data types, that happens to be partly encoded in XML on the disk. The E57 format is a hybrid of
XML (encoding the tree) and binary sections (which efficiently encode the records of point data). For efficiency, the binary sections must be read/written in large blocks of records. The XML section, although it holds most of the complexity in the file, is much smaller and objects can be accessed individually. The binary sections aren't embedded in the XML section. The XML section makes references to the binary sections, which are stored separately within the E57 file.

# The API objects
This API is described in C++, and is object-oriented. There are 15 types of objects, each described in a C++ class. There is a class that encapsulates the E57 file (ImageFile), a class for each of the eight E57 primitive elements described in the standard (IntegerNode, ScaledIntegerNode, FloatNode, StringNode, BlobNode, StructureNode, VectorNode, and CompressedVectorNode) and a base class that encapsulates the common functionality of all nodes (Node). There are two classes that keep track of the block reads/writes to the binary sections (CompressedVectorReader, CompressedVectorWriter) and a class to manage buffers for these block transfers (SourceDestBuffer). Finally, there is a class to organize the reporting of errors (E57Exception), and a class for miscellaneous functions that are not associated with any of the other objects (E57Utilities).

# Set-once design
The primary motivation for the E57 file is to be a conduit between the proprietary formats of two different vendors' software (the reader and the writer). It is not a general-purpose 3D database. The API design reflects this use case by not allowing modifications of data. This simplifies the implementation. For example, it is not possible to change a value of a node, or delete an attached child node, and it is an error to attempt to attach a node to the tree using an already existing name.

# Handle semantics
All but one of the classes (the exception being E57Exception) have handle semantics, which means that there is a level of indirection. The API classes are actually just pointers to an underlying object. The pointers are smart (they have reference counts), so the user doesn't have to explicitly free them. If two handles exist to the same underlying object, the object is not deleted until both handles are destroyed. So the programmer is not burdened with keeping track of who is going to delete an object, the object "deletes itself" when all the references to it are gone. This simplifies the use of the API, especially in the creation of trees of objects. After you attach some object into a tree, there will be two references to the object, yours and the parent of the object in the tree. So when you delete your reference, the parent's
reference will keep the object alive (until the parent is destroyed).

A second benefit of smart pointers is that the copy and assignment functions in the classes are very cheap. They just copy or assign the handle, not the underlying object. So function call arguments (that are API objects) are always "call-by-reference", and the underlying object is not copied. It is impossible to assign the underlying state of one object directly into another. This restriction fits well into the set-once scheme described above.

The bottom line is that you should not use new/delete with the E57 objects, nor reference types (e.g. ImageFile&). You don't have to keep track of who will delete the object, nor try to speed up the passing of handles to functions. And the API objects clean up after themselves when an exception occurs.

# No default constructors
None of the API classes contain default constructors, so there is no equivalent to a NULL handle (with no underlying object). So if you have an API handle, you know it points to a valid object.

# How do I get a copy of the source?
The full source of libE57Format is available on [GitHub](https://github.com/asmaloney/libE57Format). The libE57Format source is distributed under the very liberal [Boost Software License](https://opensource.org/licenses/BSL-1.0).

# How can I get help?
Help is available by submitting an issue on [GitHub](https://github.com/asmaloney/libE57Format/issues).

# How can I help?
- Fixing or reporting problems with this documentation via [GitHub issues](https://github.com/asmaloney/libE57Format-docs/issues).
- Fixing or reporting bugs in the library via [libE57Format GitHub](https://github.com/asmaloney/libE57Format/issues).
- Support the project using the support link on the [libE57Format GitHub page](https://github.com/asmaloney/libE57Format).
