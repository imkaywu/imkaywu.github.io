---
layout: page
title: Structure from Motion - a tutorial
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

Structure from Motion is like the holy grail of multiple view geometry. It is a process of estimating camera pose and retrieving a sparse reconstruction. In this tutorial, I'll explain every step of this technique and provide detailed implementation using [open3DCV]({{site.url}}/{{site.baseurl}}blog/2017/05/3d-vision-lib/).

### 1. Image IO
[done]

### 2. Feature detection
[done]

### 3. Descriptor extraction
[done]

### 4. Feature matching
[done]

### 5. Relative pose estimation

#### Known intrinsic parameters

##### Estimate essential matrix

##### Estimate fundamental matrix
[done]

#### Unknown intrinsic parameters

##### Estimate homography

##### Estimate focal length from homography

### 6. N-view triangulation

### 7. Bundle adjustment

### 8. Two-view SfM
One graph represents two views with sufficient correspondences. This sub-section is for all possible graphs.

#### 8.1. Two-view matching

#### 8.2. Two-view relative pose estimation

#### 8.3. Two-view triangulation

#### 8.4. Two-view bundle adjustment

### 9. Sequential SfM 

#### 9.1. merge two graphs

#### 9.2. N-view triangulation

#### 9.3. N-view bundle adjustment

### 10. Output
