---
title: open3DCV - A 3D Computer Vision Library
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
  - Computer Graphics
---

As I'm approaching to the end of my Master, I'll organize all the codes that I've written and write a 3D computer vision libray for future use. I'll try to write a post for some of the main methods, in the hope that it can help others and as a reminder to myself. The source code is [here](https://github.com/imkaywu/open3DCV).

## Math
[Matrix]({{ site.url }}{{ site.baseurl }}/blog/2017/06/numeric-cpp/)

---

## Basis
[Camera]({{ site.url }}{{ site.baseurl }}/blog/2017/05/camera-class/)\\
[Image]({{ site.url }}{{ site.baseurl }}/blog/2017/06/image-class/)

---

## Feature detector and descriptor
[DoG]()\\
[Harris]()\\
[SIFT (wrapper)]({{ site.url }}{{ site.baseurl }}/blog/2017/07/vlfeat-wrapper/)

---

## Matching
[Brute Force Matching]({{ site.url }}{{ site.baseurl }}/blog/2017/09/feature-matching/)\\
[Flann Matching]({{ site.url }}{{ site.baseurl }}/blog/2017/09/feature-matching/)

---

## Transformation
[Transformation]({{ site.url }}{{ site.baseurl }}/blog/2017/02/transformation/)

---

## Triangulation
[Triangulation]({{ site.url }}{{ site.baseurl }}/blog/2017/07/triangulation/)

---

## Estimator
[Perspective N-Point (PnP)]({{ site.url }}{{ site.baseurl }}/blog/2017/08/pnp/)\\
[Fundamental Matrix: Seven Point Algorithm]({{ site.url }}{{ site.baseurl }}/blog/2017/06/fundamental-matrix/)\\
[Fundamental Matrix: Eight Point Algorithm]({{ site.url }}{{ site.baseurl }}/blog/2017/06/fundamental-matrix/)\\
[Robust Fundamental Matrix]({{ site.url }}{{ site.baseurl }}/blog/2017/09/robust-fundamental-matrix/)\\
[Essential Matrix]({{ site.url }}{{ site.baseurl }}/blog/2017/08/essential-matrix/)\\
[Relative Pose from Essential Matrix]({{ site.url }}{{ site.baseurl }})\\
[Perspective Three Point (PnP)]()\\
[Five Point Relative Pose]()\\
[Four Point Algorithm for Homography]()
<!-- 
[Four Point Focal Length]()\\
[Five Point Focal Length and Radial Distortion]()\\
[Three Point Relative Pose with a Partially Known Rotation]()\\
[Four Point Relative Pose with a Partially Known Rotation]()\\
[Two Point Absolute Pose with a Partially Known Rotation]()\\
[Source](http://www.theia-sfm.org/features.html) -->

---

## Visualization
[Camera]({{ site.url }}{{ site.baseurl }}/blog/2017/05/draw-camera/)\\
[Multi-view Intersect]({{ site.url }}{{ site.baseurl }}/blog/2017/07/multiview-intersect/)

---

## Miscellaneous
[RANSAC]({{ site.url }}{{ site.baseurl }}/blog/2017/08/ransac-framework/)

---

## Application
[SfM]({{ site.url }}{{ site.baseurl }}/tutorials/sfm/)