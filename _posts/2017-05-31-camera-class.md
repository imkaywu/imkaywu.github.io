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
- set/get matrix $$K$$, $$R$$, $$t$$, $$Rt$$, and $$P$$;
- project/unproject;
- compute depth
- possiblly more

## Intrinsic and extrinsic parameter
The camera matrix, or often named projection matrix is a $$3\times 4$$ matrix. Sometimes a fourth row $$[0, 0, 0, 1]$$ is added to make a $$4\times 4$$ matrix. Let's denote the camera matrix as $$P$$, and $$P$$ is often decomposed into

$$
P=\left[ M \vert -MC \right]
$$

where $$M$$ is an invertible $$3\times 3$$ matrix, and $$C$$ is the camera center in the world coordinates. The camera matrix can be further decomposed into an intrinsic matrix $$K$$, and an extrinsic one, $$\left[ R \vert - RC \right]$$:

$$
P = K\left[R | -RC\right]
$$

The properties of these matrices are:
* $$K$$ is a upper-triangular matrix, which is usually represented as $$\begin{bmatrix}
	f_x & s & c_x \\
	0 & f_y & c_y \\
	0 & 0 & 1
\end{bmatrix}$$, where $$f_x$$, and $$f_y$$ are the focal length, and $$c_x$$ and $$c_y$$ are the center of the image plane, all in pixel;
* $$R$$ is a orthonormal matrix, where each column is the axes of the world coordinates in the camera's coordinate system. It's apprent in the following example: $$[r_1^\top \vert r_2^\top \vert r_3^\top]\begin{bmatrix}
	   1 \\
       0 \\
       0
     \end{bmatrix} = r_1^\top$$;
* $$C$$ is the camera center in the world coordinate system, while $$t=-RC$$ is the world origin in the camera coordinate system since $$\left[ R\vert -RC \right]\begin{bmatrix}
	   0 \\
       0 \\
       0 \\
       1
     \end{bmatrix}=-RC$$.

### Recovering $$C$$
Decompose $$C$$ is the easiest, since $$M$$ is invertible, and $$-MC$$ is the fourth column of the camera matrix $$P$$, thus

$$
C = -M^{-1}P(:, 4)
$$

### Recovering $$K$$ and $$R$$
We know that all full rank matrices can be decomposed into an upper-triangular matrix and an orthogonal matrix by using [RQ-decomposition](https://en.wikipedia.org/wiki/QR_decomposition), and this is exactly the case with $$K$$ and $$R$$. RQ decomposition is normally unavailable in many numeric libraries. A nice [post](http://www.janeriksolem.net/rq-factorization-of-camera-matrices.html) in [Solem's vision blog](http://www.janeriksolem.net/blog.html) gives an implementation of this decomposition.

The problem is RQ-decomposition is not unique, since the sign of the diagonal elements can be flipped. Let's first assume that the diagonal elements of $$K$$ are positive, which is not necessarily true. Nonetheless, we can achieve a unique solution by enforcing non-negative diagonal elements. An elegant solution is

```matlab
% non-negative diagonal elements
T = sign(K);
K = K * T;
R = T * R % the inverse of T is itself
```