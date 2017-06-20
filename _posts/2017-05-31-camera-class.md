---
title: A C++ implmentation of camera class
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
  - Computer Graphics
---
Camera is the starting point of perceiving the world, and it's probably the single most component in the computer vision library.

The camera class should have the following methods:
- IO: read to/write from file: 
	- intrinsic and extrinsic parameters: type 0;
	- projection matrix.
- set intrinsic and extrinsic parameters;
- camera coordinate system: camera center and axes;
- set/get matrix `K`, `R`, `t`, `Rt`, and `P`;
- project/unproject;
- compute depth
- possiblly more