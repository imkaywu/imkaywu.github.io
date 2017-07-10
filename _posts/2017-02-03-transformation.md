---
title: Interpretation of transformations and the relation with the coordinate system
categories: 
  - Research
tags:
  - Computer Vision
  - Computer Graphics
---

This is a post of useful notes and tricks on transformation for future reference.

## Compostion of Transformations

Transformations can be thought of as a change of object or a change in coordinate system.

**Rule of thumb**: The transformations necessary to convert a point from CS2 to CS1 are given by the transformations necessary to align the CS1 frame with the CS2 frame.

<!-- <div class="img_row">
	<img class="col three" src="/assets/img/research/transform/examp_trans.png">
</div>

$$
p''=T(2, 3, 0)R(z, -90)p
$$ -->

### Transform the coordinates
<div class="img_row">
	<img class="col three" src="/assets/img/research/transform/transform.pdf">
</div>

* right to left:
	* interpret operations w.r.t fixed global coordinates
	* moving object
		* draw object
		* rotate object by 45 degrees w.r.t. fixed global coordinates
		* translate it (1, 1) over w.r.t fixed global coordinates
	* Premultiplication effects a transformation in 'world' or 'fixed' coordinates

### Transform the coordinate frame
<div class="img_row">
	<img class="col three" src="/assets/img/research/transform/transform_1.pdf">
</div>

* left to right:
	* interprete operations w.r.t. local coordinates
	* changing coordinate system
		* translate coordinate system (1, 1) over
		* rotate coordinate system 45 degrees w.r.t. local origin
		* draw object in current coordinate system
	* Postmultiplication effects a transformation in 'local' coodinates.
	* OpenGL postmultiplies transformation, i.e., applies them in 'local' coordinates.

## Transformation as a change of basis
There is another way of viewing transformations which can let us construct transformations between two coordinate systems directly, without having to express the transformations in terms of one or more rotation and translation operations.

Suppose we have two coordinate systems, $$CS1$$ and $$CS2$$, having coincident origins, but having different orientations: