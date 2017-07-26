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

## Interpretation of 3D rotation matrix

### Geometric interpretation
* Rows of matrix are 3 unit vector of the new coordinate frame (?);
* Can construct rotation matrix from 3 orthonormal vectors;
* 3D rotation can be interpretated projections of point into new coordinate frame;
* New coordinate frame $$uvw$$ taken to cartesian components $$xyz$$ (Let $$\vec{p}$$ be $$\vec{u}$$, then $$Rp=\vec{x}=\begin{bmatrix} 1\\ 0\\ 0\\ \end{bmatrix}$$);
* Inverse or transpose takes $$xyz$$ cartesian to $$uvw$$.

$$
Rp = 
\begin{bmatrix}
x_u & y_u & z_u\\
x_v & y_v & z_v\\
x_w & y_w & z_w\\
\end{bmatrix} 
\begin{bmatrix}
x_p\\
y_p\\
z_p\\
\end{bmatrix} =
\begin{bmatrix}
\vec{u} \cdot \vec{p}\\
\vec{v} \cdot \vec{p}\\
\vec{w} \cdot \vec{p}\\
\end{bmatrix}
$$

## Derivation of Axis-Angle formula
Rotate vector $$\vec{b}$$ $$\theta$$ about vector $$\vec{a}$$, both $$\vec{a}$$ and $$\vec{b}$$ are unit vectors.

* Step 1: $$\vec{b}$$ has component paralle to $$\vec{a}$$, which is unchanged, and component perpendicular to $$\vec{a}$$.

$$
\begin{align*}
\vec{b_\parallel} &= (\vec{a}\cdot\vec{b})\vec{a}\\
\vec{b_\perp} &= \vec{b}-\vec{b_\parallel}
\end{align*}
$$

* Step 2: Define $$\vec{c}$$ orthogonal to both $$\vec{a}$$ and $$\vec{b}$$:

$$
\vec{c}=\vec{a}\times\vec{b}
$$

* Step 3: the perpendicular component is decomposed into two parts after rotating $$\theta$$

$$
\vec{c}\sin\theta\\
\vec{b_\perp}\cos\theta
$$

Therefore, the final rotated vector are:

$$
\begin{align*}
\text{parallel component: } & (\vec{a}\cdot\vec{b})\vec{a} \\ 
							=& (\vec{a}^\top\vec{b})\vec{a}\\
							=& (\vec{a}\vec{a}^\top)\vec{b} \quad (\vec{a}^\top\vec{b} \text{ is a scalar})\\
\text{perpendicular component 1: } & \vec{c}\sin\theta\\
								   =&\vec{a}\times\vec{b}\\
								   =&([\vec{a}]_\times\sin\theta)\vec{b}\\
\text{perpendicular component 2: } & \vec{b_\perp}\cos\theta\\
								   =&(\vec{b}-\vec{b_\parallel})\cos\theta\\
								   =&(\vec{b}-(\vec{a}\vec{a}^\top)\vec{b})\cos\theta\\
								   =&(I_{3\times 3}\cos\theta-\vec{a}\vec{a}^\top\cos\theta)\vec{b}
\end{align*}
$$

Putting all these together, the rotation matrix can be written as

$$
R(\vec{a}, \theta)=I_{3\times 3}\cos\theta+\vec{a}\vec{a}^\top(1-\cos\theta)+[\vec{a}]_\times\sin\theta
$$
This is generally refered to as the `Rodrigues formular`.