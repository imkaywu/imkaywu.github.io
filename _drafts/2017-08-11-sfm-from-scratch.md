---
title: Structure from Motion from scratch
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

Structure from Motion is the problem of estimating the parameters of cameras, including position, orientation, and intrinsic parameters, and reconstructing a sparse scene at the same time. This is the holy grail of 3D computer vision, requiring an understanding of feature detector/descriptor, feature matching, estimation of fundamental/essential matrix, estimation of relative pose, triangulation, bundle adjustment, etc. In this post, I will try to tackle this problem by implementing a most basic SfM system from scratch using [open3DCV](), and hopefully, this tutorial can help anyone who was interested but intimidated by this field to have a better understanding of the details of this technique.

## Feature detector/descriptor