---
layout: post
title:  "sitecustomize: executing code when loading python"
date:   2022-01-04 16:00:52 +0100
categories: blog
tags: [python]
---

I recently installed the version 3.9 of Python in my computer. For the moment, I want to retain the version 3.8 alongside it (probably the best way to do it would be using virtualenvs, but I don't have time to learn it now). Obviously, some packages needed new versions: the usual suspects `numpy`, `scipy`, `matplotlib`, etc. But for the rest of packages, I wanted to keep the old ones, so I don't have to manage two versions of the same packages.

<!--more-->

## PYTHONPATH

Python keeps their modules in several folders across the computer. The list of search directories can be checked with

```python
import sys
sys.path
```

By default, `sys.path` contains the current directory, and standard folders as `/usr/lib/python3.X` and `~/.local/lib/python3.X/dist-packages` (for Linux). Folders are checked in the order they appear on `path`, so Python imports the module from the first module available. During runtime, this `sys.path` can be modified as any normal `list`, appending or removing elements. Another way is using

```python
import site
site.addsitepackages('/my/modules/folder/')
```

Both methods modify the search path for the current session only. For a more permanent solution, the usual suggestion is to modify the enviroment varaible `$PYTHONPATH`, for example in `~/.bashrc`. Sounds good, right?

The problem is that the folders in `$PYTHONPATH` are added *at the start* of `sys.path`. In my case, python tried to import the 3.8 versions of `numpy` and `scipy` instead of their 3.9 versions, resulting in an error.

## sitecustomize

The file `sitecustomize.py` allows to execute some code when python loads. In my computer, this file is located at `/usr/lib/python3.X/sitecustomize.py`, which was in fact a symlink to `/etc/python3.X/sitecustomize`. I modified that file to add the search directories to the end of `sys.path` every time that I open python3.9.

You can check that the code is really executed at the start of the python session by adding a `print("Hello world")` to `sitecustomize.py`. You'll see the greeting at the very first line of the python session, even before the Python and GCC version numbers.
