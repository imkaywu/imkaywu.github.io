---
title: Source code build in Mac
categories:
  - Dev
tags:
  - Compile
---

### Build flann

```bash
$ cd flann-x.y.z-src
$ mkdir build && cd build
$ cmake -DBUILD_C_BINDINGS:BOOL=ON -DBUILD_MATLAB_BINDINGS:BOOL=ON -DBUILD_PYTHON_BINDINGS:BOOL=ON -DCMAKE_BUILD_TYPE:STRING=Release -DCMAKE_INSTALL_PREFIX:PATH=/usr/local ..
$ make
$ make install
```

#### Building flann for Matlab

```bash
>> cd flann-x.y.z-src
>> mex -L/usr/local/lib -lflann -I/usr/local/include nearest_neighbors.cpp
```

### Build VTK
```bash
$ cd VTK-x.y.z
$ mkdir build && cd build
$ cmake -DBUILD_SHARED_LIBS:BOOL=ON -DBUILD_TESTING:BOOL=OFF -DCMAKE_BUILD_TYPE:STRING=Debug -DCMAKE_INSTALL_PREFIX:PATH=/usr/local ..
make
make install
```