---
title: PYTHON环境常见问题
date: 2025-05-12 09:15:00
tags: [PYTHON, FQA]
categories: PYTHON
---

##### gcc error

```shell
# 找到包含正确版本的gcc的.so
shell> find /usr -name libgcc_s.so*
shell> vi ~/.bash_profile
shell> source ~/.bash_profile
shell> ldconfig
```

```bash
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH
```

##### _lamz

```shell
(venv) shell> pip3 install backports.lzma
(venv) shell> vi /usr/local/python3/lib/python3.10/lzma.py
```

###### lzma.py

```python
 from _lzma import *
    from _lzma import _encode_filter_properties, _decode_filter_properti
```

- 替换成下面的

```python
try:
    from _lzma import *
    from _lzma import _encode_filter_properties, _decode_filter_properties
except ImportError:
    from backports.lzma import *
    from backports.lzma import _encode_filter_properties, _decode_filter_properties
import _compression
```
