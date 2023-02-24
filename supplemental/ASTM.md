# ASTM Standard {#astm}

libE57Format is not sanctioned or approved in any way by ASTM. The designer of the API and author of the original Reference Implementation (Kevin Ackley) was heavily involved in the drafting of the E57 format standard. However, the E57 Standard for 3D Imaging Data Exchange is the final word on what is a legal/valid .e57 file.

# Do I need to get the ASTM standard?

You should. This API provides the building blocks (the E57 primitive elements) for constructing and interpreting an E57 file, but the data structures that are built with these building blocks are up to the user of the API. The required names and types of these data structures (not to mention their meanings) are specified in the ASTM standard, so you need a copy.

ASTM owns the standard which may be purchased on the [ASTM website](https://www.astm.org/Standards/E2807.htm). Only a very few of the 100+ ASTM committees make money, and E57 isn't one of them. So support your industry and buy a copy.

# Is the E57 format XML?

Only partially, but it's a highly specialized XML. It's better think of an E57 file as hierarchical tree of eight data types, that happens to be partly encoded in XML on the disk. The E57 format is a hybrid of XML (encoding the tree) and binary sections (which efficiently encode the records of point data). For efficiency, the binary sections must be read/written in large blocks of records. The XML section, although it holds most of the complexity in the file, is much smaller and objects can be accessed individually. The binary sections aren't embedded in the XML section. The XML section makes references to the binary sections, which are stored separately within the E57 file.
