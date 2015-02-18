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
###Date Time Iso String Coder
Serialized Representation:
```json
{
    "__type__": "datetime",
    "isostr": "2015-02-18T21:36:32.528617",
}
```

Example from reference implementation:
```python
In [1]: import sciserialize
In [2]: import datetime
In [3]: s = sciserialize.dumps(datetime.datetime.now())
In [4]: s
Out[4]: '{"isostr": "2015-02-18T21:40:23.511717", "__type__": "datetime"}'
In [5]: t = sciserialize.loads(s)
In [6]: t
Out[6]: datetime.datetime(2015, 2, 18, 21, 40, 23, 511717)
```

###Time Delta Coder
Serialized Representation

```
JSON:
{
    "__type__": "timedelta",
    "days": 0,
    "seconds": 11,
    "microsec": 626512,
}

MSGPACK
b'\x84\xa8microsec\xce\x00\t\x8fP\xa7seconds\x0b\xa8__type__\xa9timedelta\xa4days\x00'
```

Example from reference implementation:
```python
In [11]: t1 = datetime.datetime.now()
In [12]: t2 = datetime.datetime.now()
In [13]: td = t2 - t1
In [14]: td
Out[14]: datetime.timedelta(0, 11, 626512)
In [15]: s = sciserialize.dumps(td)
In [16]: s
Out[16]: '{"microsec": 626512, "seconds": 11, "__type__": "timedelta", "days": 0}'
In [17]: decoded = sciserialize.loads(s)
In [18]: decoded
Out[18]: datetime.timedelta(0, 11, 626512)
In [19]: b = sciserialize.packb(td)
In [20]: b
Out[20]: b'\x84\xa8microsec\xce\x00\t\x8fP\xa7seconds\x0b\xa8__type__\xa9timedelta\xa4days\x00'
In [22]: sys.getsizeof(b)
Out[22]: 82
In [23]: sys.getsizeof(s)
Out[23]: 120
```

###N dimensional Array Coder



```python
In [25]: x = randn(3, 4, 5)
In [26]: x
Out[26]:
array([[[ 1.05344858,  0.0291041 ,  0.38289843, -0.98107994,  0.3139919 ],
        [ 0.73767174,  2.12341246, -2.04463471,  0.32919843,  0.67947691],
        [-1.96649368, -1.81407016, -0.95784464, -1.68542805,  0.87043148],
        [ 0.71586721, -1.56975362, -1.74745641,  1.05711026,  1.14616359]],

       [[-1.30953889, -1.42069983,  0.24024504,  0.67984107, -0.11183973],
        [-0.83789866, -0.04427722,  0.18577593, -0.12175868,  0.34695681],
        [ 0.93977527, -1.07114551,  0.06305212, -0.82623412, -2.11478308],
        [-0.08274785,  0.18192578, -0.14476797, -1.24072587,  1.03656297]],

       [[ 1.03339121,  0.14559811, -0.69360654,  1.17136044,  1.24462986],
        [ 0.82171074,  1.1502607 , -0.69005395, -0.81639491,  0.82231422],
        [-1.53028073,  1.04233296, -0.62064313,  2.30226706,  1.0182505 ],
        [-0.04815908,  0.55817617,  0.07667377, -0.82511246,  0.44404845]]])
In [27]: x.shape
Out[27]: (3, 4, 5)
In [28]: s = sciserialize.dumps(x)
In [29]: s
Out[29]: '{"shape": [3, 4, 5], "dtype": "float64", "bytes": {"__base64__": "K4Ik5eza8D93oqobd82dP4eaOm9ogdg/HsiHwAFl778bk4x2cRjUP80XhMIBm+c/hzvqq7/8AECOCQBxaVsAwBeBeE2WEdU/IdaoXUa+5T+DjjkUwnb/v8iSkm9uBv2/lKZJzqmm7r/bimhng/f6vw0bRx+T2us/jQ1cWGLo5j/yvhL4tR35vxgOZ9mU9fu/sC35c+zp8D8boDejr1byPw7c5Azf8/S/6Lk/vi+79r/X9Sd+WcDOP0W7jA1CweU/ApkfUoehvL8Gfj/cENDqv+TZvOWAq6a/sgWPbIHHxz8k7B6rkyu/vxDCJVGKNNY/Slfhl6MS7j+Kh3t5aSPxv5bfTwYvJLA/Zs6hiYJw6r8erJlkE+sAwJCckI/2LrW/geMkCFhJxz8Qp5m5wYfCvwn5pF4D2vO/A1dRDMOV8D8R84I5xYjwP8SViXH1osI/LkXgVgYy5r9E1wNy5L3yP5husAAB6vM/H7iWUnRL6j+36B/Ed2fyP7c1PAfsFOa/TiUpNugf6r8btp/rZVDqP/SpZqUHfPi/YzIcUmWt8D9bQeP9Ttzjvxnopv0KawJAZ6ZECMFK8D95WPSlTqiov5B2NUSU3OE/wTz9X+Sgsz/aEcI9Umfqv3UCWDRKa9w/"}, "__type__": "ndarray"}'
In [30]: xdec = sciserialize.loads(s)
In [31]: x == xdec
Out[31]:
array([[[ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True]],

       [[ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True]],

       [[ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True]]], dtype=bool)

An with msgpack:

In [32]: b = sciserialize.packb(x)
In [33]: b
Out[33]: b"\x84\xa5shape\x93\x03\x04\x05\xa5dtype\xa7float64\xa5bytes\xc5\x01\xe0+\x82$\xe5\xec\xda\xf0?w\xa2\xaa\x1bw\xcd\x9d?\x87\x9a:oh\x81\xd8?\x1e\xc8\x87\xc0\x01e\xef\xbf\x1b\x93\x8cvq\x18\xd4?\xcd\x17\x84\xc2\x01\x9b\xe7?\x87;\xea\xab\xbf\xfc\x00@\x8e\t\x00qi[\x00\xc0\x17\x81xM\x96\x11\xd5?!\xd6\xa8]F\xbe\xe5?\x83\x8e9\x14\xc2v\xff\xbf\xc8\x92\x92on\x06\xfd\xbf\x94\xa6I\xce\xa9\xa6\xee\xbf\xdb\x8ahg\x83\xf7\xfa\xbf\r\x1bG\x1f\x93\xda\xeb?\x8d\r\\Xb\xe8\xe6?\xf2\xbe\x12\xf8\xb5\x1d\xf9\xbf\x18\x0eg\xd9\x94\xf5\xfb\xbf\xb0-\xf9s\xec\xe9\xf0?\x1b\xa07\xa3\xafV\xf2?\x0e\xdc\xe4\x0c\xdf\xf3\xf4\xbf\xe8\xb9?\xbe/\xbb\xf6\xbf\xd7\xf5'~Y\xc0\xce?E\xbb\x8c\rB\xc1\xe5?\x02\x99\x1fR\x87\xa1\xbc\xbf\x06~?\xdc\x10\xd0\xea\xbf\xe4\xd9\xbc\xe5\x80\xab\xa6\xbf\xb2\x05\x8fl\x81\xc7\xc7?$\xec\x1e\xab\x93+\xbf\xbf\x10\xc2%Q\x8a4\xd6?JW\xe1\x97\xa3\x12\xee?\x8a\x87{yi#\xf1\xbf\x96\xdfO\x06/$\xb0?f\xce\xa1\x89\x82p\xea\xbf\x1e\xac\x99d\x13\xeb\x00\xc0\x90\x9c\x90\x8f\xf6.\xb5\xbf\x81\xe3$\x08XI\xc7?\x10\xa7\x99\xb9\xc1\x87\xc2\xbf\t\xf9\xa4^\x03\xda\xf3\xbf\x03WQ\x0c\xc3\x95\xf0?\x11\xf3\x829\xc5\x88\xf0?\xc4\x95\x89q\xf5\xa2\xc2?.E\xe0V\x062\xe6\xbfD\xd7\x03r\xe4\xbd\xf2?\x98n\xb0\x00\x01\xea\xf3?\x1f\xb8\x96RtK\xea?\xb7\xe8\x1f\xc4wg\xf2?\xb75<\x07\xec\x14\xe6\xbfN%)6\xe8\x1f\xea\xbf\x1b\xb6\x9f\xebeP\xea?\xf4\xa9f\xa5\x07|\xf8\xbfc2\x1cRe\xad\xf0?[A\xe3\xfdN\xdc\xe3\xbf\x19\xe8\xa6\xfd\nk\x02@g\xa6D\x08\xc1J\xf0?yX\xf4\xa5N\xa8\xa8\xbf\x90v5D\x94\xdc\xe1?\xc1<\xfd_\xe4\xa0\xb3?\xda\x11\xc2=Rg\xea\xbfu\x02X4Jk\xdc?\xa8__type__\xa7ndarray"
In [34]: xunp = sciserialize.unpackb(b)
In [35]: x==xunp
Out[35]:
array([[[ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True]],

       [[ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True]],

       [[ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True],
        [ True,  True,  True,  True,  True]]], dtype=bool)

In [37]: sys.getsizeof(b)
Out[37]: 564
In [38]: sys.getsizeof(s)
Out[38]: 781
```

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
