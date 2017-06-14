---
title: Methods for non-linear least squares problems
categories: 
  - Research
tags:
  - Optimisation
---

The content in this post is not original, the reference list is at the end of the post.

## Definition of Least Squares Problem

{% capture notice %}
### Definition 1.1. Least Squares Problem

Find \\(\mathbf{x}^*\\), a local minimizer for

$$
F(x)=\frac{1}{2}\sum_{i=1}^m (f_i(\mathbf{x}))^2
$$

where \\(f_i: \mathbf{R}^n \mapsto \mathbf{R}, i=1,...,m\\) are given functions, and \\(m\geq n\\)
{% endcapture %}

<div class="notice--primary">
  {{ notice | markdownify }}
</div>

The least squares problem is a special variant of the more general problem: Given a function \\(F: \mathbf{R}^n \mapsto \mathbf{R}\\), find an argument of \\(F\\) that gives the minimum value of this so-called *objective function* or *cost function*.

{% capture notice %}
### Definition 1.2. Global Minimizer

Given \\(F: \mathbf{R}^n \mapsto \mathbf{R}\\). Find

$$
\mathbf{x}^+ = argmin_\mathbf{x}{F(\mathbf{x})}.
$$
{% endcapture %}

<div class="notice--primary">
  {{ notice | markdownify }}
</div>

This problem is very hard to solve in general, and we only present methods for solving the simpler problem of finding a local minimizer for \\(F\\), an argument vector which gives a minimum value of \\(F\\) inside a certain region whose size is given by \\(\sigma\\), where \\(\sigma\\) is a small, positive number.

{% capture notice %}
### Definition 1.3. Local Minimizer

Given \\(F: \mathbf{R}^n \mapsto \mathbf{R}\\). Find

\\(F(\mathbf{x}^*) \leq F(\mathbf{x})\\)

for \\(\\|\mathbf{x}-\mathbf{x}^*\\| < \delta\\)
{% endcapture %}

<div class="notice--primary">
  {{ notice | markdownify }}
</div>

We assume that the cost function \\(F\\) is differentiable and smooth, which makes the following *Taylor expansion* valid

\\[F(\mathbf{x} + \mathbf{h}) = F(\mathbf{x}) + \mathbf{h}^T \mathbf{g} + \frac{1}{2}\mathbf{h}^T \mathbf{H} \mathbf{h} + \mathit{O}(\\|\mathbf{h}\\|^3)\\]

where \\(\mathbf{g}\\) is the gradient,

$$
\mathbf{g} = F'(\mathbf{x}) = \begin{bmatrix}
								\frac{\partial F}{\partial x_1}(\mathbf{x})\\
								\vdots\\
								\frac{\partial F}{\partial x_n}(\mathbf{x})
								\end{bmatrix}
$$

and \\(\mathbf{H}\\) is the *Hessian*

$$
\mathbf{H}=F''(\mathbf{x}) = \begin{bmatrix}
								\frac{\partial^2 F}{\partial x_i \partial x_j}(\mathbf{x})
								\end{bmatrix}
$$

If \\(\mathbf{x}^*\\) is a local minimizer and \\(\|\mathbf{h}\|\\) is sufficiently small, then we cannot find a point \\(\mathbf{x}^* + \mathbf{h}\\) with a smaller \\(F\\)-value. We get

{% capture notice %}

### Theorem 1.4. Necessary condition for a local minimizer

If \\(\mathbf{x}^*\\) is a local minimizer, then

$$
\mathbf{g}^* = F'(\mathbf{x}^*) = 0
$$
{% endcapture %}

<div class="notice--primary">
  {{ notice | markdownify }}
</div>

We use a special name for arguments that satisfy the necessary condition:

{% capture notice %}

### Definition 1.5. Stationary point

If \\(\mathbf{g}_s = F'(\mathbf{x}_s)=0\\)

then \\(\mathbf{x}_s\\) is said to be a *stationary point* for \\(F\\).
{% endcapture %}

<div class="notice--primary">
  {{ notice | markdownify }}
</div>

Thus, a local minimizer is a stationary point, but so is a local maximizer. A stationary point which is neither a local maximizer nor a local minimizer is called a saddle point. In order to determine whether a given stationary point is a local minimizer or not, we need to include the second order term in the Taylor series. Insert \\(\mathbf{x}_s\\) we see that

$$
F(\mathbf{x}_s+\mathbf{h})=F(\mathbf{x}_s)+\frac{1}{2}\mathbf{h}^T \mathbf{H}_s \mathbf{h} + \mathit{O}(\|\mathbf{h}\|^3)
$$

with \\(\mathbf{H}_s=F''(\mathbf{x}_s)\\)

Because any Hessian is a symmetric matrix. If we request that \\(\mathbf{H}_s\\) is *positive definite*, then its eigenvalues are greater than some number \\(\delta > 0\\), and

\\[\mathbf{h}^T \mathbf{H}_s \mathbf{h} > \delta \\|\mathbf{h}\\|^2\\]

For \\(\mathbf{h}\\) that sufficiently small, the third term \\(\mathit{O}(\|\mathbf{h}\|^3)\\) will be dominated by \\(\frac{1}{2}\mathbf{h}^T \mathbf{H}_s \mathbf{h}\\). This term is positive, so we get

{% capture notice %}

### Theorem 1.6. Sufficient condition for a local minimizer

Assume that \\(\mathbf{x}_s\\) is the stationary point and that \\(F''(\mathbf{x}_s\\) is positive definite. Then \\(\mathbf{x}_s\\) is a local minimizer.
{% endcapture %}

<div class="notice--primary">
  {{ notice | markdownify }}
</div>

If \\(\mathbf{H}_s\\) is negative definite, then \\(\mathbf{x}_s\\) is a local maximizer. If \\(\mathbf{H}_s\\) is *indefinite* (i.e. it has both positive and negative eigenvalues), then \\(\mathbf{x}_s\\) is a saddle point.

## Descent methods

All methods for non-linear optimization are iterative: from a starting point \\(\mathbf{x}_0\\), the method produces a series of vectors \\(\mathbf{x}_1, \mathbf{x}_2, ...\\), which hopefully converges to \\(\mathbf{x}^*\\), a local minimizer for the given function. Most methods have measures which enforce the *descending condition*

\\[F(\mathbf{x}_{k+1}) < F(\mathbf{x}_k)\\]

This prevents convergence to a maximizer and also makes it less probable that we converge towards a saddle point. If the given function has several minimizers, the result will depend on the starting point \\(\mathbf{x}_0\\). We don't know which of the minimizers that will be found, it is not necessarily the one closest to \\(\mathbf{x}_0\\).

In many cases, the method produces vectors which converge towards the minimizer in two different stages

* When \\(\mathbf{x}_0\\) is far from the solution, we want the method to produce iterates which move steadily towards \\(\mathbf{x}^*\\). 


## Reference

[1] K.Madsen, H.B.Nielsen, O.Tingleff, Methods for Non-linear Least Squares Problems