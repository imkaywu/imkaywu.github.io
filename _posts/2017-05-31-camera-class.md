---
title: Pinhole camera model - a C++ implementation
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
  - Computer Graphics
---
Camera is the starting point of perceiving the world, and it is one of the most components in the computer vision/graphics library. This post discusses the implementation of a pinhole camera model WITHOUT lens distortion. Please refer to [camera part 3]({{site.url}}{{site.baseurl}}/blog/2017/09/camera-distortion/) for camera with lens distortion.

The camera class should have the following methods:
- constructor
  - projection matrix;
  - intrinsic matrix ($$K$$), rotation matrix ($$R$$), translation ($$t$$);
  - parameters: intrinsics ($$f_x, f_y, c_x, c_w, \alpha$$), extrinsics ($$om_1, om_2, om_3, t_1, t_2, t_3$$).
- update projection matrix ($$P$$): `update_projection()`;
- update intrinsic matrix ($$K$$), rotation matrix ($$R$$), translation ($$t$$): `update_matrices()`;
- update parameters: `update_parameters()`;
- update camera center: `update_center()`;
- update axes: `update_axes()`;
- pixel to ray: `pixel2ray()`;
- project from 3D point to 2D point: `Vec2f project(const Vec3f&)`;
- re-project from 2D point to 3D point: `Vec3f unproject(const Vec2f&, const int d)`
- compute depth: `compute_depth()`
- possiblly more

### header file
The header file of the `camera` class

```cpp
class Camera
{
public:
    Camera();
    Camera(const std::vector<float>& r_intrinsics, const std::vector<float>& r_extrinsics);
    Camera(const Mat34f& r_projection);
    Camera(const Mat3f& R, const Vec3f& t);
    Camera(const Mat3f& K, const Mat3f& R, const Vec3f& t);
    virtual ~Camera();
    
    void update_projection();
    void update_parameters();
    void update_matrices(const int is_proj = 1);
    void update_center();
    void update_axes();
    
    int decompose(Mat3f & K, Mat3f& R) const;

    
    const Mat34f& projection() const;
    Mat34f& projection();
    const Vec3f& direction() const;
    Vec3f& direction();
    const Vec3f& center() const;
    Vec3f& center();
    
    Vec3f pixel2ray(const Vec2f& pixel) const;
    Vec3f project(const Vec4f& coord) const;
    Vec4f unproject(const Vec3f& icoord) const;
    float compute_depth(const Vec4f& coord) const;
    
private:
    Vec3f center_; // optical center
    Vec3f oaxis_; // optical axis
    Vec3f xaxis_; // x-axis of the camera-centered coordinate system
    Vec3f yaxis_; // y-axis of the camera-centered coordinate system
    Vec3f zaxis_; // z-axis of the camera-centered coordinate system
    Mat34f projection_; // projection matrix
    Mat3f K_; // intrinsic matrix
    Mat3f R_; // rotation
    Vec3f om_; // axis-angle
    Vec3f t_; // translation
    std::vector<float> intrinsics_; // intrinsic params: f_x, f_y, c_x, c_y, alpha
    std::vector<float> extrinsics_; // extrinsic params: om(0), om(1), om(2), t(0), t(1), t(2);
};
```

### Decompose projection matrix $$P$$
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

### Estimate $$K$$ and $$Rt$$ from projection matrix $$P$$
We know that all full rank matrices can be decomposed into an upper-triangular matrix and an orthogonal matrix by using [RQ-decomposition](https://en.wikipedia.org/wiki/QR_decomposition), and this is exactly the case with $$K$$ and $$R$$. RQ decomposition is normally unavailable in many numeric libraries. A nice [post](http://www.janeriksolem.net/rq-factorization-of-camera-matrices.html) in [Solem's vision blog](http://www.janeriksolem.net/blog.html) gives an implementation of this decomposition in Python, a Matlab implementation is given in [this post](http://ksimek.github.io/2012/08/14/decompose/).

```matlab
function [R Q] = rq(M)
    [Q,R] = qr(flipud(M)')
    R = flipud(R');
    R = fliplr(R);

    Q = Q';   
    Q = flipud(Q);
```

The problem of RQ-decomposition is that the solution is not unique, since negating the sign of any column of K and the corresponding row of R gives the exactly same projection matrix. We may enforce the diagonal elements of $$K$$ to be non-negative, but it is true under the following conditions:

* the X/Y axes of the image coordinate system are in the same direction as those of the camera coordinate system;
* the camera looks down in the positive $$z-$$ direction.

[Solem's post](http://www.janeriksolem.net/rq-factorization-of-camera-matrices.html) gives the positive diagonal elements in the following code:

```matlab
% non-negative diagonal elements
T = sign(K);
K = K * T;
R = T * R % the inverse of T is itself
```

Enforcing this assumption might be unjustified for cases mentioned above. Thus, we need to carry out several steps to correct $$K$$ and $$R$$ from the non-negative decomposition.

1. If the image $$x-$$axis and camera $$x-$$axis point in opposite directions, negate the first column of $$K$$ and the first row of $$R$$.
2. If the image $$y-$$axis and camera $$y-$$axis point in opposite directions, negate the second column of $$K$$ and the second row of $$R$$.
3. If the camera looks down the negative$$-z$$ axis, negate the third column of $$K$$ and negate the third column of $$R$$.
4. If the determinant of $$R$$ is -1, negate it.

#### Conventions of image coordinate system
* **Matlab-style**: 
  * origin: top-left
  * $$x-$$axis: point right
  * $$y-$$axis: point down
* **Math-style**:
  * origin: bottom-left
  * $$x-$$axis: point right
  * $$y-$$axis: point up

#### Conventions of camera coordinate system
* **OpenGL-style**: 
    * $$x-$$axis: point right
    * $$y-$$axis: point up
    * camera looks down the negative $$z-$$axis
* **Hartley&Zisserman-style**: 
  * $$x-$$axis: point right
  * $$y-$$axis: point down
  * camera looks down the positive $$z-$$axis

### Update $$C$$
Decompose $$C$$ is the easiest, since $$M$$ is invertible, and $$-MC$$ is the fourth column of the camera matrix $$P$$, thus

$$
C = -M^{-1}P(:, 4)
$$

### Update axes


### Pixel to ray


### Project from 3D to 2D


### Re-project from 2D to 3D


### Compute depth