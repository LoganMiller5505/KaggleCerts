Encodings are rules for mapping from raw binary bytes to human-readable text.

If you try to read text with a different encoding than it was originally written in, you may get random characters or a string of unknown characters

The standard text encoding is UTF-8

The string data type is text by default, and the otehr data type is the bytes type (a sequence of integers). You can convert a string into bytes by specifying the encoding:
```python
before.encode("utf-8", errors="replace")
```

When the bytes are converted back into a string with the correct encoding, all text is there correctly

However, using a different encoding results in an error (strings are UTF-8 by default in Python 3)

---
Most files will be encoded with UTF-8, but sometimes, there will be an error (`UnicodeDecodeError`) if this is not the case

To automatically guess what the encoding is, use `charset_normalizer` on a subset of the bytes in the file to input

```python
with open("../input/kickstarter-projects/ks-projects-201801.csv", 'rb') as rawdata:
    result = charset_normalizer.detect(rawdata.read(10000))
```

This isn't guaranteed to be right, but is better than guessing manually

Files will be saved in UTF-8 by default