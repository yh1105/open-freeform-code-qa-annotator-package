
# Post \#54137790 [Link](https://stackoverflow.com/questions/54137790/)

## Convert from '_io.BytesIO' to a bytes-like object in python3.6?

**Vote**: 68 (96/702) **Views**: 75626 (113/702) 

**Internal ID** \#1-3-230

Created at 2019-01-10 22:26:47

Tags: `python` `python-3.x` `typeerror` `zlib` `bytesio`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am using this function to uncompress the body of a HTTP response if it is compressed with gzip, compress or deflate.
```
def uncompress_body(self, compression_type, body):
    if compression_type == 'gzip' or compression_type == 'compress':
        return zlib.decompress(body)
    elif compression_type == 'deflate':
        compressor = zlib.compressobj(9, zlib.DEFLATED, -zlib.MAX_WBITS)
        compressed = compressor.compress(body)
        compressed += compressor.flush()
        return base64.b64encode(compressed)

    return body
```

However python throws this error message.
```
TypeError: a bytes-like object is required, not '_io.BytesIO'
```

on this line:
```
return zlib.decompress(body)
```

Essentially, how do I convert from '_io.BytesIO' to a bytes-like object?


----------
        
## Answer \#0

**Accepted** Vote: 84

Created at 2019-01-10 22:28:50

------------

It's a file-like object.  Read them:

```
>>> b = io.BytesIO(b'hello')
>>> b.read()
b'hello'
```


If the data coming in from `body` is too large to read into memory, you'll want to refactor your code and use [zlib.decompressobj](https://docs.python.org/3/library/zlib.html#zlib.decompressobj) instead of `zlib.decompress`.


------------
    
    
## Answer \#1

 Vote: 54

Created at 2019-09-19 07:46:33

------------

In case you write into the object first, make sure to reset the stream before reading:

```
>>> b = io.BytesIO()
>>> image = PIL.Image.open(path_to_image)
>>> image.save(b, format='PNG')
>>> b.seek(0)
>>> b.read()
b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR\x00\x00\x06\xcf\x00\x00\x03W\x08\x02\x00'
```


or directly get the data with `getvalue`

```
>>> b.getvalue()
b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR\x00\x00\x06\xcf\x00\x00\x03W\x08\x02\x00'
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-03-28 13:09:53

------------

```
b = io.BytesIO()
image = PIL.Image.open(path_to_image) # ie 'image.png'
image.save(b, format='PNG')
b.getbuffer().tobytes() # b'\x89PNG\r\n\x1a\n\x00 ...
```



------------
    
    