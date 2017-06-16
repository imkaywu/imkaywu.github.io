---
title: Object Categorisation Based on Various Properties
categories: 
  - Research
tags:
  - Computer Vision
  - 3D Reconstruction
header:
  teaser: research.jpg
---

Current literature of 3D reconstruction focuses mainly on the algorithmic development and improvement. Very often, the proposed method make some assumptions about the object of interest, like Lambertian reflectance model, textured surface, static in nature, etc. Very few work has been devoted to the development of a framework that caters varied categories of objects.

Object:
	appearance: determined by reflectance. We use basis materials and trait vectors to create physically-plausible BRDFs, which is used to render virtual reference objects.
	shape: basic geometric primitives

3D reconstruction algorithms
	Data-driven photometric stereo
	MVS
	Kinect Fusion (RGB-D camera)

The novelty is not which category of algorithms works best for any specific object category, rather this can be readily adopted by many exiting 3D algorithms and have the potential to improve the reconstruction results. I think it also suits the purpose of OpenVL (OpenVL doesn't favour one algorithm of deprecate another), instead, it tries to find a common high level abstraction to hide the algorithmic details, a high level starting point for low level implementations. The user can choose freely any category they want, and they will determine the best reconstruciton themselves.


How does this description fits for MVS and SLAM systems?

Can we elliminate the specular component using those reference objects?

MVS
	scene representation
	Photo-consistency measure
	Visibility model - probably can use reference object to infer visible views
	Shape prior
	Reconstruction algorithm
	Initialization requirements