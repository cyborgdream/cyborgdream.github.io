+++
title = "How to create mixed build packages for conda"
description = "C++, Python and Pybind11 on conda-forge"
date = 2023-11-22
draft = false
slug = "mixed-builds"

[taxonomies]
categories = ["tutorial"]
tags = ["python", "cpp", "software-tools"]

[extra]
comments = true
+++

This is part of a series where I write down things I know or I've learned while trying to keep myself sharp in various subjects that I don't use so often anymore.

The simplest example on how to build a Python package using PyBind11 that I could come up with is the following:

#### Step 1: Writing the C++ Code

First, we write our C++ function. We'll create a file named `hello.cpp`:

```cpp
#include <iostream>void say_hello(const char* name) {
    std::cout << "Hello, " << name << "!" << std::endl;
}
```

#### Step 2: Creating C++ Python Bindings

Next, we'll use PyBind11 to create Python bindings for our C++ code. We will create a file named `pybind_wrapper.cpp`:

```cpp
#include <pybind11/pybind11.h>
#include "hello.cpp"
namespace py = pybind11;

PYBIND11_MODULE(hello_ext, m) {
    m.def("say_hello", &say_hello, "A function that says hello");
}
```

For this to work, you'll need to have PyBind11 installed. It can be included in your project as a submodule or installed globally.

#### Step 3: Writing the `setup.py` File

Now, we need to write `setup.py`, which will build our extension module using setuptools:

```python
from setuptools import setup, Extension
from pybind11.setup_helpers import Pybind11Extension, build_ext

ext_modules = [
    Pybind11Extension("hello_ext",
                      ["pybind_wrapper.cpp"],
                      # Example: passing in the version of the Python extension
                      define_macros = [('VERSION_INFO', '"0.0.1"')],
                     ),
]

setup(
    name="hello-package",
    version="0.0.1",
    author="Mari",
    description="A test package with a C++ extension",
    ext_modules=ext_modules,
    cmdclass={"build_ext": build_ext},
)
```

#### Step 4: Building the Wheel

To build the wheel, you'll need to install the `wheel` package if you haven't already:

```
pip install wheel
```

Then, you run:

```
python setup.py bdist_wheel
```

This will compile the C++ code and create a `.whl` file in the `dist` directory.

#### Step 5: Creating a Conda Package

To create a Conda package, you'll need to create a `meta.yaml` file that Conda-build can use to build your package. The `meta.yaml` file might look something like this:

```yaml
package:
  name: hello-package
  version: "0.0.1"

source:
  path: .

requirements:
  build:
    - {{ compiler('c') }}
    - {{ compiler('cxx') }}
    - python
    - setuptools
    - pybind11

test:
  imports:
    - hello_ext

about:
  home: "The URL to the homepage"
  license: MIT
  summary: "A simple example package with C++ extension
```

To build the Conda package, you'll use the `conda-build` command:

```
conda build .
```

This will create a Conda package that you can then upload to Anaconda Cloud or install directly.

To run it:

```python
pip install /path/to/dist/hello_package-0.0.1-py3-none-any.whl
```

And to use it:

```python
import hello_ext

hello_ext.say_hello("Mari")
```

---

This project could be extended to use CMAKE, in case one has a more complex build than just one C++ file.

In a typical Python project without C++ extensions, `setup.py` is responsible for the entire build process. However, when integrating C++ extensions with CMake, `setup.py` often delegates the building of the extension to CMake.

- You need to specify if you want your build to be static or dynamically linked: `add_library(simplemath STATIC simplemath/simplemath.cpp)` if you don’t specify, CMAKE will make it shared by default. You can also use the following flag `set(BUILD_SHARED_LIBS ON)  # Set the default build type to shared libraries.`