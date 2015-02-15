# Definition SciSerialize
SciSerialize is a serialization format focusing on scientific data. It is *based on the JSON format* and adds some kind of a type-coding-system. All supported datatypes are converted to valid JSON types and can be saved to JSON and MesssagePack file-formats. The goal of this serialization format is to be cross-platform and -language.
This definition will show the representations of all required data types to be supported by a serializer implemented in any language.
To every implementation it is free to deside how the representation inside the specific language looks like, but it is recommendet to keep it simple and similar to the reference implementation. There are no restrictions to extend the serializer with language specific types and representations, however they should be switched off by default so there will be no conflicts between cross language serializers by using defaults. An implementation example of SciSerialize will be given in python.

##Formats

+ [JSON](https://www.json.com/)
+ [MessagePack](http://msgpack.org/) "It's like JSON but fast and small"

All data types supported by ScySerialize will be reduced to the data types supported by JSON:

+ Number: 123.987 (double-precision floating-point)
+ String: "Hello" (dubblequted UTF-8, escaped with backslashes) 
+ Boolean: true/false
+ Array: [1.0, 2.0, "a", "b"] (like lists)
+ Object: {"keyString": "value", "Object containing Numbers array": [1.1, 1.2, 1.3]};(java-script objects like key-value stores)
+ Null: null (empty values

Binary data will be handled differently for JSON and MessagePack. In JSON, binary data will be stored as base64 strings and in MessagePack the raw binary data well be packed.

##Data Types
**TODO**
+ Date Time
+ Time Delta
+ Multidimensional Array (Bytes) (N-dimensional Matrix for MATLAB Users)
+ Data Frame (containing columns and rows array and Multidimensional Array)
+ Boolean Array (Bytes)

##Functional Requirements

+ Encoder for each data type
+ Decoder for each data type
+ JSON serializer functions:
  + `dumps()`
  + `loads()`
+ MessagePack serializer functions:
  + `packb()`
  + `unpackb()`

##Architecture

+ sciserialize
  + coders
    + Date Time
    + Time Delta
    + Multidimensional Array (Bytes)
    + Boolean Array (Bytes)
    + ... and already supported JSON types
    + `encode_types()` ... recursive encoder function
    + `decode_types()` ... recursive decoder function
    
  + serializers
    + `dumps()`
    + `loads()`
    + `packb()`
    + `unpackb()`
