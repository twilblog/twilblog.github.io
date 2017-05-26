---
layout: post
title:  "Importing sibling directories in Python"
date:   2017-05-26 12:00:00:00
author: shaun
categories: python python2.7
---

*Please note that I'm referring to Python 2.x here, not Python 3.x.*

I have a simple Python project laid out like this:

```
my-project/
    src/
        __init__.py
        main.py
    tests/
        test_main.py
```

I'd like to use `test_main.py` to test `main.py` (unsurprisingly). But how do I import `main.py` into my test? If you're a NodeJS fanboy like myself, you might think you can do something this:

```
import '../src/main'
```

Right? WRONG. Take your sensible, Bash style imports back to Nodeland, you glorified jQuery developer.

In Python, you need to do two things. First, create a blank `__init__.py` file inside `tests/`. This makes it into a package or something. Then, you can use this syntax to import the file:

```
from src import main
```

And then you're good.
