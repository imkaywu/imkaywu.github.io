---
title: Estimate focal length from Homography
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

This post discusses estimating focal length from Homography. This technique tries to estimate focal lengths from the given homography under the assumption that the camera undergoes rotations around its centre only. The original paper is *Construction of Panoramic Image Mosaics with Global and Local Alignment*. The [OpenCV](https://opencv.org/) implementation can be found [here](https://github.com/opencv/opencv/blob/05b15943d6a42c99e5f921b7dbaa8323f3c042c6/modules/stitching/src/autocalib.cpp).

### Theory
We assume that the camera center is fixed, and goes through pure rotation, then the Homography between two views is:

$$
\begin{align}
H &= \lambda K_1 R K_0^{-1}\\
\begin{bmatrix}
h_0 & h_1 & h_2\\
h_3 & h_4 & h_5\\
h_6 & h_7 & h_8\\
\end{bmatrix} &\sim
\begin{bmatrix}
r_{00} & r_{01} & r_{02}f_0\\
r_{10} & r_{11} & r_{12}f_0\\
r_{20}/f_1 & r_{21}/f_1 & r_{22}f_0/f_1
\end{bmatrix}
\end{align}
$$

We observe that the first two rows (or columns) of $$R$$ must have the same norm and be orthogonal, i.e.,

$$
\begin{align}
h_0^2 + h_1^2 + \frac{h_2^2}{f_0^2} &= h_3^2 + h_4^2 + \frac{h_5^2}{f_0^2}\\
h_0h_3+h_1h_4+\frac{h_2h_5}{f_0^2} &=0\\
h_0^2 + h_3^2 + \frac{h_6^2}{f_1^2} &= h_1^2 + h_4^2 + \frac{h_7^2}{f_0^2}\\
h_0h_1+h_3h_4+\frac{h_6h_7}{f_1^2} &=0\\
\end{align}
$$

From this, we can compute the estimates

$$
f_0^2 = \frac{h_5^2-h_2^2}{h_0^2+h_1^2-h_3^2-h_4^2} \quad \text{if } h_0^2+h_1^2\neq h_3^2+h_4^2
$$

or 

$$
f_0^2 = -\frac{h_2h_5}{h_0h_3+h_1h_4} \quad \text{if } h_0h_3\neq -h_1h_4
$$

Similar result can be obtained for $$f_1$$ as well. If the focal length is fixed for two images, we can take the geometric mean of $$f_0$$ and $$f_1$$ as the estimated focal length $$f=\sqrt(f_1f_0)$$. When multiple estimates of $$f$$ are available, the median value is used as the final estimate.

### OpenCV implementation
The implementation can be found in `autocalib.cpp`.
```cpp
void focalsFromHomography(const Mat& H, double &f0, double &f1, bool &f0_ok, bool &f1_ok)
{
    CV_Assert(H.type() == CV_64F && H.size() == Size(3, 3));

    const double* h = H.ptr<double>();

    double d1, d2; // Denominators
    double v1, v2; // Focal squares value candidates

    f1_ok = true;
    d1 = h[6] * h[7];
    d2 = (h[7] - h[6]) * (h[7] + h[6]);
    v1 = -(h[0] * h[1] + h[3] * h[4]) / d1;
    v2 = (h[0] * h[0] + h[3] * h[3] - h[1] * h[1] - h[4] * h[4]) / d2;
    if (v1 < v2) std::swap(v1, v2);
    if (v1 > 0 && v2 > 0) f1 = std::sqrt(std::abs(d1) > std::abs(d2) ? v1 : v2);
    else if (v1 > 0) f1 = std::sqrt(v1);
    else f1_ok = false;

    f0_ok = true;
    d1 = h[0] * h[3] + h[1] * h[4];
    d2 = h[0] * h[0] + h[1] * h[1] - h[3] * h[3] - h[4] * h[4];
    v1 = -h[2] * h[5] / d1;
    v2 = (h[5] * h[5] - h[2] * h[2]) / d2;
    if (v1 < v2) std::swap(v1, v2);
    if (v1 > 0 && v2 > 0) f0 = std::sqrt(std::abs(d1) > std::abs(d2) ? v1 : v2);
    else if (v1 > 0) f0 = std::sqrt(v1);
    else f0_ok = false;
}
```

### Open3DCV implementation