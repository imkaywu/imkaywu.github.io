---
title: Estimation of essential and fundamental matrix
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

## Fundamental matrix from point correspondences
The epipolar geometry is the intrinsic projective geometry between two views, which is independent of scene structure, and only depends on the camera's internal parameters and relative pose.

The fundamental matrix $$F$$ encapsulates this intrinsic geometry, which is a $$3\times 3$$ matrix of rank 2. If a 3D point $$X$$ is projected as $$\mathbf{x}$$ in the first view, and $$\mathbf{x'}$$ in the second, then we have the following relation

$$
x'Fx = 0
$$

Each point correspondence gives rise to one linear equation. Specifically, writing $$\mathbf{x}=(x, y, 1)$$, and $$\mathbf{x'}=(x', y', 1)$$, this equation can be written in terms of the known coordinates $$x$$ and $$x'$$.

$$
x'xf_{11}+x'yf_{12}+x'f_{13}+y'xf_{21}+y'yf_{22}+y'f_{23}+xf_{31}+yf_{32}+f_{33}=0
$$

where $$f_{ij}$$ is the component of $$F$$ in $$(i,j)$$ position. Organize all $$f_{ij}$$ into the vector $$\mathbf{f}$$, we have

$$
\left[x'x, x'y, x', y'x, y'y, y', x, y, 1\right]\mathbf{f}=0
$$

From a set of $$n$$ point correspondences, we obtain a set of linear equations of the form

$$
Af = \begin{bmatrix}
x_1'x_1, x'_1y_1, x_1', y_1'x_1, y_1'y_1, y_1', x_1, y_1, 1\\
\vdots\\
x_n'x_n, x_n'y_n, x_n', y_n'x_n, y_n'y_n, y_n', x_n, y_n, 1\\
\end{bmatrix} \mathbf{f} = \mathbf{0}.
$$

The least squares solution for $$\mathbf{f}$$ is the singular vector corresponding to the smallest singular value of $$A$$, that is, the last column of $$V$$ in the SVD $$A=UDV^\top$$. The solution minimizes the algebraic error $$\|A\mathbf{f}\|$$ subject to $$\|\mathbf{f}\|=1$$. This algorithm is call "8-point" algorithm.

To make algorithm perform better, we typically need to do a normalization step: the center of the points are moved to the origin, and the RMS distance of the points to the origin is $$\sqrt{2}$$. 

Peter Kovesi implemented an amazing [projective geometry](http://www.peterkovesi.com/matlabfns/#projective) library in Matlab, including estimation of the fundamental matrix.

### 8-point algorithm: C++ version
```cpp
inline void fund_eight_pts (const vector<Vec2f>& x1, const vector<Vec2f>& x2, Mat3f& F)
{
    vector<Vec2f> nx1, nx2;
    Mat3f T1, T2;
    T1 = normalize_pts(x1, nx1);
    T2 = normalize_pts(x2, nx2);
    
    Matf matx1, matx2;
    vector2mat<float>(nx1, matx1);
    vector2mat<float>(nx2, matx2);
    
    Matf A(9, x1.size());
    A << matx2.row(0).array() * matx1.row(0).array(),
         matx2.row(0).array() * matx1.row(1).array(),
         matx2.row(0).array(),
         matx2.row(1).array() * matx1.row(0).array(),
         matx2.row(1).array() * matx1.row(1).array(),
         matx2.row(1).array(),
         matx1.row(0),
         matx1.row(1),
         Matf::Ones(1, x1.size());
    A = A.transpose().eval();
    
    // Solve the constraint equation for F from nullspace extraction.
    // An LU decomposition is efficient for the minimally constrained case.
    // Otherwise, use an SVD.
    Vec9f fvector;
    if (0) // x1.size() == 8
    {
        const auto lu_decomp = A.fullPivLu();
        if(lu_decomp.dimensionOfKernel() == 1)
        {
            fvector = lu_decomp.kernel();
        }
    }
    else
    {
        JacobiSVD<Eigen::Matrix<float, Eigen::Dynamic, 9> > amatrix_svd(A, Eigen::ComputeFullV);
        fvector = amatrix_svd.matrixV().col(8);
    }
    
    F << fvector(0), fvector(1), fvector(2),
         fvector(3), fvector(4), fvector(5),
         fvector(6), fvector(7), fvector(8);
    
    // enforce the constraint that F is of rank 2
    // Find the closest singular matrix to F under frobenius norm.
    // We can compute this matrix with SVD.
    JacobiSVD<Mat3f> fmatrix_svd(F, Eigen::ComputeFullU | Eigen::ComputeFullV);
    Vec3f singular_values = fmatrix_svd.singularValues();
    singular_values(2) = 0.0f;
    F = fmatrix_svd.matrixU() * singular_values.asDiagonal() * fmatrix_svd.matrixV().transpose();

    F = T2.transpose() * F * T1;
}
```

### normalization: C++ version
```cpp
inline Mat3f normalize_pts(const vector<Vec2f>& pts, vector<Vec2f>& newpts)
{
    newpts.resize(pts.size());
    Vec2f c(0, 0);
    for (int i = 0; i < pts.size(); ++i)
    {
        c += pts[i];
    }
    c /= pts.size();
    
    float dist = 0.0f;
    for (int i = 0; i < pts.size(); ++i)
    {
        newpts[i] = pts[i] - c;
        dist += newpts[i].norm();
    }
    dist /= pts.size();
    
    float scale = sqrt(2) / dist;
    for (int i = 0; i < pts.size(); ++i)
    {
        newpts[i] *= scale;
    }
    
    Mat3f T;
    T << scale, 0,     -scale * c(0),
         0,     scale, -scale * c(1),
         0,     0,      1;
    
    return T;
}
```

## Fundamental matrix from projection matrices


## Essential Matrix
