---
title: The Math of Stereo Triangulation
categories: 
  - Research
tags:
  - Computer Vision
---

This post discusses the problem of stereo triangulation, which is estimating the position of a 3D point from two corresponding image points. We present the derivation in a coordinate-free fashion first, then introduce coordinates later.

## Geometric Representations

### Lines and Rays

**Parametric form**: \\[L = \\{p = q + \lambda v \| \lambda \in \mathbb{R}\\}\\]

**Implicit form**: A line can be thought of as the intersection of two planes.

\\[L = \\{p \| n_1^t(p-q) = n_2^t(p-q) = 0\\}\\]

**Parametric form**: \\[R = \\{p = q + \lambda v \| \lambda \geq 0\\}\\]

### Planes

**Parametric form**: \\[P = \\{p = q + \lambda_1 v_1 + \lambda_2 v_2 \| \lambda_1, \lambda_2 \in \mathbb{R}\\}\\]
**Implicit form**: \\[P = \\{p \| n^t (p-q) = 0\\}\\]

## Reconstruction by Triangulation

We assume that the locations and orientations of projector and camera are known with respect to the global coordinate system. Under this assumption, the equations of projected planes and rays, as well as the equations of camera rays corresponding to illuminated points, can be obtained. The location of illuminated points can be recovered by intersecting the planes or rays of light with the camera rays corresponding to the illuminated points.

### Line-Plane Intersection

Computing the intersection of a line and a plane is strightforward when the line is represented in parametric form:
\\[L = \\{p = q + \lambda v \| \lambda \in \mathbb{R}\\}\\]

and the plane is represented in implicit form
\\[P = \\{p \| n^t (p-q) = 0\\}\\]

When the line and the plane are parallel (co-plane included), there is no intersection (infinite intersections). In this case, $$v$$ and $$n$$ are orthogonal $$v^t n=0$$. If the vector $$v$$ and $$n$$ are nor orthogonal, the intersection of the line and the plane contains exactly one point $$p$$.

\\[n^t (p-q_P)=n^t (\lambda v + q_L - q_P\\]
\\[\lambda = \frac{n^t(q_P - q_L)}{n^tv}\\]

### Line-Line Intersection

Here we consider the case of two arbitrary lines $$L_1$$ and $$L_2$$
\\[L_1=\\{p=q_1+\lambda_1v_1\|\lambda_1\in\mathbb{R}\\}\\]
\\[L_2=\\{p=q_2+\lambda_2v_2\|\lambda_2\in\mathbb{R}\\}\\]

If vectors $$v_1$$ and $$v_2$$ are linearly dependent, i.e., one is the scalar multiple of the other. In addition, suppose $$q_2 - q_1$$ is also the multiple of $$v_1$$ or $$v_2$$, then these two lines are essentially a single line. Otherwise, these two lines are parallel and do not intersect.

If vector $$v_1$$ and $$v_2$$ are linearly independent, these two lines may or may not intersect. the sufficient and necessary condition for two lines to intersect is that scalar values $$\lambda_1$$ and $$lambda_2$$ exist so that

\\[q_1 + \lambda_1v_1=q_2+\lambda_2v_2\\]

If two lines do not intersect, we find the *approximate intersection* as the point that is *closest* to the two lines. More specifically, we define the approximate intersection as the point $$p$$ which minimizes the sum of the square distances to both lines

\\[\phi(p, \lambda_1, \lambda_2)=\\|q_1+\lambda_1 v_1-p\\|^2 + \\|q_2+\lambda_2 v_2-p\\|^2\\]

The function $$\phi(p, \lambda_1, \lambda_2)$$ is a quadratic non-negative definite function of five variables, the three coordinates of the point $$p$$ and the two scalars $$\lambda_1$$ and $$\lambda_2$$. Let $$p_1 = q_1 + \lambda_1 v_1$$ be a point on the line $$L_1$$, and $$p_2 = q_2 + \lambda_2 v_2$$ be a point on the line $$L_2$$. A necessary condition for the minimizer $$(p, \lambda_1, \lambda2)$$ is that the partial derivative of $$\phi$$, with respect to the five variables, all vanish at the minimizer. In particular, the derivatives w.r.t. the three coordinates of the point $$p$$ must vanish

\\[\frac{\partial \phi}{\partial p} = (p-p_1)+(p-p_2)=0\\]

or equivalently, it is necessary for the minimizer point $$p$$ to be the midpoint of the line segment joining $$p_1$$ and $$p_2$$.

As a result, the problem reduces to the minimization of the squared distance from a point $$p_1$$ on line $$L_1$$ to a point $$p_2$$ on line $$L_2$$. The original cost function becomes a quadratic non-negative definite function of two variables

\\[\psi(\lambda_1, \lambda_2)= 2\phi(p, \lambda_1, \lambda_2) = \\|(q_2+\lambda_2 v_2) - (q_1 + \lambda_1 v_1)\\|^2\\]

It is necessary for the partial derivatives of $$\psi$$, w.r.t. $$\lambda_1$$ and $$\lambda_2$$ to be equal to zeros at the minimum, as follows

\\[\frac{\partial \psi}{\partial \lambda_1} = v_1^t(\lambda_1v_1-\lambda_2v_2+q_1-q_2) = \lambda_1\\|v_1\\|^2 - \lambda_2v_1^tv_2+v_1^t(q_1-q_2) = 0\\]
\\[\frac{\partial \psi}{\partial \lambda_2} = v_2^t(\lambda_2v_2-\lambda_1v_1+q_2-q_1) = \lambda_2\\|v_2\\|^2 - \lambda_2v_2^tv_1+v_2^t(q_2-q_1) = 0\\]

These provide two linear equations in $$\lambda_1$$ and $$\lambda_2$$, which can be expressed in the matrix form as

[mathjax doesn't work right]
\\[\begin{pmatrix} 
	\\|v_1\\|^2 & -v_1^tv_2 \\\\ 
	-v_2^tv_1 & |v_2\\|^2
	\end{pmatrix} \left(\begin{array}{c}
					\lambda_1 \\
					\lambda_2
				  \end{array}\right) = \left(\begin{array}{c}
				  						v_1^t(q_2-q_1) & \\
				  						v_2^t(q_1-q_2)
				  				  	   \end{array}\right)\\]


## Coordinate Systems

### Lines from Image Points

An image point with coordinates $$\mathbf{x}=(x, y, 1)$$ defines a unique line containing this point and the center of projection. Assume $$\mathbf{X}$$ is the world coordinates of this point, then we have

$$\lambda \mathbf{x} = R \mathbf{X} +T$$

Since R is a rotation matrix, which is orthonormal, we have $$R^{-1}=R^T$$, the line of sight can be written as

$$\mathbf{X} = (-R^T T)+\lambda (R^T\mathbf{x})$$

### Planes from Image Lines

A straight line $$l$$ on the image plane can be expressed in the parametric or implicit form w.r.t the image coordinates. There is a unique plane $$P$$ containing this line $$l$$ and the center of projection. Let $$\mathbf{X}$$ be the point on the plane $$P$$ projecting onto an image point $$\mathbf{x}$$. We have $$\mathbf{x}=R\mathbf{X}+T$$, and the point $$\mathbf{x}$$ satisfies the implicit equation defining the line $$l$$, we have

$$
0=l^T\mathbf{x}=l^T(R\mathbf{X}+T)=(R^Tl)^T(\mathbf{X}-(-R^TT))
$$

Another interpretation is: $$R^Tl$$ is the normal of the plane $$P$$, and $$-R^TT$$ is the center of projection in the world coordinate system.

