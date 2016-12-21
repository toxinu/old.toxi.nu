---
published: true
layout: post
title: How to store your Python package metadata
---

A small, but full of snippets, post about how to store your package metadata cleanly.


Let's define a simple layout.

```bash
$ tree .
.
├── my_package
│   ├── __about__.py
│   └── __init__.py
└── setup.py

1 directory, 3 files
```

All your metadata lives in `__about__.py` like that:

```python
__all__ = [
    "__title__", "__summary__", "__uri__", "__version__", "__author__",
    "__email__", "__license__", "__copyright__",
]

__title__ = "my-package"
__summary__ = "My package is something."
__uri__ = "https://example.com"

__version__ = "0.1.0"

__author__ = u"Geoffrey Lehée"
__email__ = "contact@toxi.nu"

__license__ = "BSD"
__copyright__ = "Copyright 2016 %s" % __author__
```

Expose all your metadata at package root.

```python
cat my_package/__init__.py

from .__about__ import (
    __author__, __copyright__, __email__, __license__, __summary__, __title__,
    __uri__, __version__
)

__all__ = [
    "__title__", "__summary__", "__uri__", "__version__", "__author__",
    "__email__", "__license__", "__copyright__",
]
```

This setup allow you to avoid editing your `setup.py` too frequently and keep it up to date easily.

```python
import os

from setuptools import setup

base_dir = os.path.dirname(__file__)

about = {}
with open(os.path.join(base_dir, "my_package", "__about__.py")) as f:
    exec(f.read(), about)

setup(
    name=about["__title__"],
    version=about["__version__"],
    description=about["__summary__"],
    author=about["__author__"],
    author_email=about["__email__"],
    url=about["__uri__"],
    license=about["__license__"]
)

```

Happy coding and don't eat too much turkeys!
