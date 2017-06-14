---
title: Transformations
categories: 
  - Research
tags:
  - Computer Vision
  - Computer Graphics
---

This is a post of useful notes and tricks on transformation for future reference.

### Composing Transformations

![example of transformation](/assets/img/research/cv/examp_trans.png)

**Which direction to read?**

* right to left
	* interpret operations w.r.t fixed global coordinates
	* moving object
		* draw object
		* rotate object by 45 degrees w.r.t. fixed global coordinates
		* translate it (2, 3) over w.r.t fixed global coordinates
![right to left](/assets/img/research/cv/right2left.png)

* left to right
	* interprete operations w.r.t. local coordinates
	* changing coordinate system
		* translate coordinate system (2, 3) over
		* rotate coordinate system 45 degrees w.r.t. local origin
		* draw object in current coordinate system
![left to right](/assets/img/research/cv/left2right.png)