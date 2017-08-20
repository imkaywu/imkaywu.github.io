---
title: Triangulation
categories: 
  - Research
  - Dev
tag:
  - Computer Vision
  - Computer Graphics
---

## Problem definition
Given camera matrices ($$P, P'$$), or fundamental matrix $$F$$, and *measured* image points $$x$$ and $$x'$$, estimate the 3D point $$X$$ that minimizes some cost function.

Since the rays back-projected from the points are skew. There will not be a point $$X$$ that satisfies $$x=PX, x'=P'X$$, the the image points do not satisfy the epipolar constraint $$x'^\top F x= 0$$.

Since from a fundamental matrix and a set of corresponding points, the scene can only be reconstructed up to a projective transformation. Thus, it is crucial to have a triangulation method that is invariant to projective transformation.

Denote $$\tau$$ as the triangulation method used to compute a 3D point $$X$$ from a point correspondence $$x\leftrightarrow x'$$ and a pair of camera matrices $$P, P'$$. We have

$$
X = \tau(x, x', P, P')
$$

The invariance to projective transformation $$H$$ is expressed as

$$
\tau(x, x', P, P') = H^{-1}\tau(x, x', PH^{-1}, P'H^{-1})
$$

## Linear triangulation
We first discuss a simple linear triangulation method, which is similar to the DLT method for homography estimation.

By definition, we have $$x = PX, x'=P'X$$. We can get rid of the homogeneous scale factory by a cross product. Therefore, $$x\times PX=0$$. This can be rewritten as

$$
\begin{align}
x(\mathbf{p^{3\top}}\mathbf{X})-(\mathbf{p^{1\top}}\mathbf{X}) &=0\\
y(\mathbf{p^{3\top}}\mathbf{X})-(\mathbf{p^{2\top}}\mathbf{X}) &=0\\
x(\mathbf{p^{2\top}}\mathbf{X})-y(\mathbf{p^{1\top}}\mathbf{X}) &=0\\
\end{align}
$$

where $$\mathbf{p^{i\top}}$$ is the $$i$$th row of $$P$$. Only two of these equations are linear independent.

An equation of the form $$A\mathbf{X} = 0$$ can be formed from the image points, with

$$
A=
\begin{bmatrix}
x(\mathbf{p^{3\top}})-(\mathbf{p^{1\top}})\\
y(\mathbf{p^{3\top}})-(\mathbf{p^{2\top}})\\
x'(\mathbf{p^{3\top}})-(\mathbf{p^{1\top}})\\
y'(\mathbf{p^{3\top}})-(\mathbf{p^{2\top}})\\
\end{bmatrix}
$$

There are typically two ways of solving this problem:

### Homogeneous method (DLT)
The solution is the unit singular vector corresponding to the smallest singular value of $$A$$.

### Inhomogeneous method
Set $$\mathbf{X} = [X, Y, Z, 1]$$, $$A\mathbf{X}=0$$ can be converted to a set of four inhomogeneous equations with three unknowns. This technique fails to work when the last coordinate of $$\mathbf{X}$$ is close to 0.

### NOT projective invariant
Unfortunately, this linear method is NOT projective invariant. Let's consider another camera matrix pair: $$PH^{-1}, P'H^{-1}$$. The matrix A, in this case, becomes $$AH^{-1}$$. It's easy to verify that the 3D point $$X$$ that satisfies $$A\mathbf{X}=\epsilon$$ also satisfies the new linear system $$(AH^{-1})(H\mathbf{X})=\epsilon$$. Thus, there is a one-to-one correspondence between point $$X$$ and $$HX$$ that gives the same error. However, the constraints for both the homogeneous method ($$\|X\|=1$$) and inhomogeneous method ($$\mathbf{X}=[X,Y,Z,1]^\top$$) are not invariant under projective transformation. Thus, in general the point $$\mathbf{X}$$ solving the original problem will not correspond to a solution $$H\mathbf{X}$$ for the transformed problem. However, there is a special case: for affine reconstruction, the inhomogeneous method is affine transformation invariant. Please refer to the MVG book by Hartley and Zisserman for details.

Here is a the C++ implementation of the linear triangulation solved by the homogeneous method.

```cpp
StructurePoint Triangulation::linear(const vector<Camera> &cameras,
      const FeatureTrack &track) const
{
  MatrixXd A(2*track.size(), 4);
  Vector3i color(0,0,0);
  for(unsigned int k = 0; k < track.size(); ++k) {
    double x = track[k].coords().x();
    double y = track[k].coords().y();
    color += track[k].color();
    unsigned int index = track[k].index();
    const MatrixXd &Pi = cameras[index].projection();
    Vector4d pi1t(Pi(0,0),Pi(0,1),Pi(0,2),Pi(0,3));
    Vector4d pi2t(Pi(1,0),Pi(1,1),Pi(1,2),Pi(1,3));
    Vector4d pi3t(Pi(2,0),Pi(2,1),Pi(2,2),Pi(2,3));
    Vector4d a = x*pi3t - pi1t;
    a.normalize();
    Vector4d b = y*pi3t - pi2t;
    b.normalize();
    A(2*k,0) = a.x();A(2*k,1) = a(1);A(2*k,2) = a(2);A(2*k,3) = a(3);
    A(2*k+1,0) = b(0);A(2*k+1,1) = b(1);A(2*k+1,2) = b(2);A(2*k+1,3) = b(3);
  }
  SelfAdjointEigenSolver<MatrixXd> eigensolver(A.transpose()*A);
  MatrixXd evectors = eigensolver.eigenvectors();
  double denom = 1.0/evectors(3,0);
  Vector3d p(evectors(0,0)*denom, evectors(1,0)*denom, evectors(2,0)*denom);
  return StructurePoint(p, color);
}
```

## Midpoint triangulation
This method computes the 3D scene structure using midpoint triangulation. Richard I. Hartley and Peter Sturm mentioned in their paper "Triangulation" that this method is neither affine nor projective invariant, since concepts such as perpendicular or mid-point are not affine concepts. It is seen to behave very poorly indeed under projective and affine transformation, and is by far the worst of the methods considered here in this regard. We include this for the sake of completeness.

<div class="img_row">
    <img class="col three" src="/assets/img/open3DCV/triangulation/triangulation_midpoint.png" alt="midpoint triangulation" title="midpoint triangulation"/>
</div>
<div class="col three caption">
    Midpoint triangulation
</div>

Here we consider the case of two arbitrary lines $$L_1$$ and $$L_2$$
\\[L_1=\\{p=q_1+\lambda_1v_1\|\lambda_1\in\mathbb{R}\\}\\]
\\[L_2=\\{p=q_2+\lambda_2v_2\|\lambda_2\in\mathbb{R}\\}\\]

If vectors $$v_1$$ and $$v_2$$ are linearly dependent, i.e., one is the scalar multiple of the other. In addition, suppose $$q_2 - q_1$$ is also the multiple of $$v_1$$ or $$v_2$$, then these two lines are essentially a single line. Otherwise, these two lines are parallel and do not intersect.

If vector $$v_1$$ and $$v_2$$ are linearly independent, these two lines may or may not intersect. the sufficient and necessary condition for two lines to intersect is that scalar values $$\lambda_1$$ and $$\lambda_2$$ exist so that

\\[q_1 + \lambda_1v_1=q_2+\lambda_2v_2\\]

If two lines do not intersect, we find the *approximate intersection* as the point that is *closest* to the two lines. More specifically, we define the approximate intersection as the point $$p$$ which minimizes the sum of the square distances to both lines

\\[\phi(p, \lambda_1, \lambda_2)=\\|q_1+\lambda_1 v_1-p\\|^2 + \\|q_2+\lambda_2 v_2-p\\|^2\\]

The function $$\phi(p, \lambda_1, \lambda_2)$$ is a quadratic non-negative definite function of five variables, the three coordinates of the point $$p$$ and the two scalars $$\lambda_1$$ and $$\lambda_2$$. Let $$p_1 = q_1 + \lambda_1 v_1$$ be a point on the line $$L_1$$, and $$p_2 = q_2 + \lambda_2 v_2$$ be a point on the line $$L_2$$. A necessary condition for the minimizer $$(p, \lambda_1, \lambda2)$$ is that the partial derivative of $$\phi$$, with respect to the five variables, all vanish at the minimizer. In particular, the derivatives w.r.t. the three coordinates of the point $$p$$ must vanish

\\[\frac{\partial \phi}{\partial p} = (p-p_1)+(p-p_2)=0\\]

or equivalently, it is necessary for the minimizer point $$p$$ to be the midpoint of the line segment joining $$p_1$$ and $$p_2$$.

As a result, the problem reduces to the minimization of the squared distance from a point $$p_1$$ on line $$L_1$$ to a point $$p_2$$ on line $$L_2$$. The original cost function becomes a quadratic non-negative definite function of two variables

\\[\psi(\lambda_1, \lambda_2)= 2\phi(p, \lambda_1, \lambda_2) = \\|(q_2+\lambda_2 v_2) - (q_1 + \lambda_1 v_1)\\|^2\\]

It is necessary for the partial derivatives of $$\psi$$, w.r.t. $$\lambda_1$$ and $$\lambda_2$$ to be equal to zeros at the minimum, as follows

\\[\frac{\partial \psi}{\partial \lambda_1} = v_1^\top (\lambda_1v_1-\lambda_2v_2+q_1-q_2) = \lambda_1\\|v_1\\|^2 - \lambda_2v_1^\top v_2+v_1^\top (q_1-q_2) = 0\\]
\\[\frac{\partial \psi}{\partial \lambda_2} = v_2^\top (\lambda_2v_2-\lambda_1v_1+q_2-q_1) = \lambda_2\\|v_2\\|^2 - \lambda_2v_2^\top v_1+v_2^\top (q_2-q_1) = 0\\]

These provide two linear equations in $$\lambda_1$$ and $$\lambda_2$$, which can be expressed in the matrix form as

$$
\begin{bmatrix} 
  \|v_1\|^2 & -v_1^\top v_2\\
  -v_2^\top v_1 & \|v_2\|^2\\
\end{bmatrix}
\begin{bmatrix}
  \lambda_1 \\
  \lambda_2 \\
\end{bmatrix}= 
\begin{bmatrix}
  v_1^\top (q_2-q_1)\\
  v_2^\top (q_1-q_2)
\end{bmatrix}
$$

Or equivalently

$$
\begin{bmatrix}
  \lambda_1 \\
  \lambda_2 \\
\end{bmatrix}= 
\frac{1}{\|v_1\|^2\|v_2\|^2-(v_1^\top v_2)^2}
\begin{bmatrix} 
  \|v_1\|^2 & v_1^\top v_2\\
  v_2^\top v_1 & \|v_2\|^2\\
\end{bmatrix}
\begin{bmatrix}
  v_1^\top (q_2-q_1)\\
  v_2^\top (q_1-q_2)
\end{bmatrix}
$$

Here is the C++ implementation of the midpoint triangulation
```cpp
StructurePoint Triangulation::midpoint(const vector<Camera> &cameras,
     const FeatureTrack &track) const
{
  double min_dist = numeric_limits<double>::max();
  Vector3d min_mp;
  unsigned int size = track.size();
  Vector3i color(0,0,0);
  for(unsigned int i = 0; i < size; ++i) {
    unsigned int i1 = track[i].index();
    Vector3d pos1 = cameras[i1].position();
    Vector3d dir1 = cameras[i1].direction(track[i].coords());
    for(unsigned int j = i+1; j < size; ++j) {
      unsigned int i2 = track[j].index();
      Vector3d pos2 = cameras[i2].position();
      Vector3d dir2 = cameras[i2].direction(track[j].coords());
      Vector3d c = dir1.cross(dir2);
      double x = c.x(), y = c.y(), z = c.z();
      double denom = 1.0/(x*x + y*y + z*z);
      double a1 = ((pos2-pos1).cross(dir2)).dot(c)*denom;
      double a2 = ((pos2-pos1).cross(dir1)).dot(c)*denom;
      Vector3d p1 = pos1 + a1 * dir1;
      Vector3d p2 = pos2 + a2 * dir2;
      Vector3d d = p1-p2;
      double dist = sqrt(d.dot(d));
      Vector3d cd = pos1-pos2;
      double cdist = 1/sqrt(cd.dot(cd));
      Vector3d mp = (p1+p2)*0.5;
      min_mp = dist*cdist < min_dist ? (min_dist = dist*cdist), mp : min_mp;
      if(dist*cdist < .1) {
        color = track[i].color() + track[j].color();
        color /= 2;
        return StructurePoint(min_mp, color);
      }
    }
  }
  return StructurePoint(min_mp, track[0].color());
}
```
## Geometric error triangulation
Let $$x\leftrightarrow x'$$ be the measured point correspondence, and $$\hat{x}\leftrightarrow \hat{x'}$$ be the estimated point correspondence that satisfy the epipolar constraint, *i.e.*, $$\hat{x'}^\top F \hat{x}=0$$. This method minimizes the geometric distance between the measured and estimated points

$$
C(x, x')=d(x, \hat{x}) + d(x', \hat{x'})
$$

## MLE triangulation (non-iterative)


## Angular triangulation
```cpp
StructurePoint Triangulation::angular(const vector<Camera> &cameras,
      const FeatureTrack &track) const
{
  double precision = 1e-25;
  StructurePoint point = midpoint(cameras, track);
  Vector3d g_old;
  Vector3d x_old;
  Vector3d x_new = point.coords();
  Vector3d grad = angular_gradient(cameras, track, x_new);
  double epsilon = .001;
  double diff;
  int count = 150;
  do {
    x_old = x_new;
    g_old = grad;
    x_new = x_old - epsilon * g_old;
    grad = angular_gradient(cameras, track, x_new);
    Vector3d sk = x_new - x_old;
    Vector3d yk = grad - g_old;
    double skx = sk.x();
    double sky = sk.y();
    double skz = sk.z();
    diff = skx*skx+sky*sky+skz*skz;
    //Compute adaptive step size (sometimes get a divide by zero hence
    //the subsequent check)
    epsilon = diff/(skx*yk.x()+sky*yk.y()+skz*yk.z());
    epsilon = (epsilon != epsilon) ||
      (epsilon == numeric_limits<double>::infinity()) ? .001 : epsilon;
    --count;
  } while(diff > precision && count-- > 0);
  if(isnan(x_new.x()) || isnan(x_new.y()) || isnan(x_new.z())) {
    return point;
  }
  return StructurePoint(x_new, point.color());
}
```

## Angular gradient triangulation

```cpp
Vector3d Triangulation::angular_gradient(const vector<Camera> &cameras,
      const FeatureTrack &track, const Vector3d &point) const
{
  Vector3d g = Vector3d(0,0,0);
  for(unsigned int i = 0; i < track.size(); ++i) {
    const Keypoint& f = track[i];
    const Camera& cam = cameras[f.index()];
    Vector3d w = cam.direction(f.coords());
    Vector3d v = point - cam.position();
    double denom2 = v.dot(v);
    double denom = sqrt(denom2);
    double denom15 = pow(denom2, 1.5);
    double vdotw = v.dot(w);
    g.x() += (-w.x()/denom) + ((v.x()*vdotw)/denom15);
    g.y() += (-w.y()/denom) + ((v.y()*vdotw)/denom15);
    g.z() += (-w.z()/denom) + ((v.z()*vdotw)/denom15);
  }

  return g;
}
```