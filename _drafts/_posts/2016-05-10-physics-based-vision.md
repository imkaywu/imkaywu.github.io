---
title: Paper Summary - Physics-Based Vision
categories:
  - Reading
tags:
  - Paper
  - Computer Vision
header:
  teaser: reading.jpg
---

## [Nayar, Ikeuchi, Kanade, 89] Surface Reflection: Physical and Geometrical Perspectives (keywords: Surface models, Reflection models: Beckmann-Spizzichino model, Torrance-Sparrow model)

<!-- ===================================== -->

## [Dana, 99] Reflectance and Texture of Real-World Surfaces (keywords: visual appearance, BRDF, BTF)

<!-- ===================================== -->

## [Rusinkiewicz 99] A New Change of Viriables for Efficient BRDF Representation

<!-- ===================================== -->

## [Rusinkiewicz] A New Change of Variables for Efficient BRDF Representation

<!-- ===================================== -->

## [Matusik 03] A data-driven reflectance model (keyword: MERL database)

{% capture notice_info %}
### Related work

* Traditionally, physically inspired analytic reflection models are only approximations of reflectance of real materials, limited to describing only particular subclasses of materials. The parameters of many models are in principle measurable, but in practice difficult to acquire.
* The measure-and-fit approach: fit the measured data to an analytic model using various optimisation techniques.
* Dense measurements of the BRDF, preserves subtleties, time-consuming.

### Contributions

* A sampling based approach for modeling surface reflectance, which is data-driven - it interpolates/extrapolates new BRDFs from the representative BRDF data.
* Introduce a set of perceptually-based parameters for this model, let users specify their own parameters. This way of specifying model parameters makes the model easier to use than the analytic models in which the meaning of parameters is non-intuitive.
* Analyze both linear and non-linear dimensionality of the space of isotropic BRDFs

### Pros

* The produced BRDFs look reaslistic since they are based on the measured BRDFs.
* Provide intuitive parameters to change the properties of the output BRDF.
* Allow users to specify their own parameters by labeling a few representative BRDFs.

### Cons

### Details

#### Data acquisition

Image-based acquisition

#### Data representation

The goal is to generate novel, yet plausible reflectance functions directly from this database.

Assumption: treat each BRDF sample as a high dimensional vector in an abstract BRDF space, all physical BRDFs should lie upon a lower dimensional manifold within this space indicative of their inherent dimensionality.

2-phase approach: discover this lower dimensional model, define an interpolation scheme within this lower-dimensional subspace.

* Linear analysis
	* manifold discovery: Principal Component Analysis
	* interpolation: linear combinations of samples
	* When the plateau occurs on the \\(k\\)th eigenvalue, model the data as a \\(k-\\)dimensional linear subspace
	* Problem: the 45-dimensional space is bigger than the space of all possible BRDFs, thus we can find BRDFs that don't correspond to any physical materials. This suggests the space of all possible BRDFs lies on a lower-dimensional manifold that is non-linearly embedded in the 45D linear space.
* Non-linear analysis
	* [TBD]

#### Model construction

* Define a direction corresponding to a desired trait. We pick some arbitrary point on the manifold and then move in the direction defined by the vector by adding it to the current position to increase the trait, or subtracting it to decrease the trait.
* Trait vector identification: binary classification

### Results


### Take-away message or Comment

This method is proposed to generate physically valid BRDF for rendering. The idea of 'trait' and BRDF generation by increasing/decreasing trait vector can be utilized to define object reflectance property in a continuous and intuitive manner.

{% endcapture %}

<div class="notice--info">
  {{ notice_info | markdownify }}
</div>

<!-- ===================================== -->

## [Lawrence, et al, 06] Inverse Shade Trees for Non-Parametric Material Representation

<!-- ===================================== -->

## [Hui, Sankaranarayanan, 15] A Dictionary-based Approach for Estimating Shape and Spatially-Varying Reflectance

{% capture notice_info %}
### Related work

* The diffuse + specular model: ?
* Parametric BRDFs: limited in their ability to provide precise approximation to the true BRDFs
* Isotropic BRDFs: the surface normal is restricted to a plane
* Reference basis model: SV-BRDF is generated from a few unknown reference BRDFs
* Example-based photometric stereo, reference objects. *orientation consistency*. Objects with the same or spatially varying BRDFs

### Contributions

* A method for recovering the surface normals and the reflectance of opaque objects with complex spatially-varying reflectance without introducing reference objects.
* Estimate surface normals in a coarse-to-fine fashion.
* Recover the BRDF at each pixel independently by solving a linear inverse problem

### Pros

### Cons

* Assumption: orthographic projection, distance light source with known direction, shodows and inter-reflections are negligible, camera is radiometrically calibrated, the unknown BRDF at each pixel lies in the non-negative span of the dictionary atoms

* Require light calibration due to the use of virtual spheres

### Details

#### Problem formulation

$$
\{\hat{\textbf{n}}_p, \hat{\textbf{c}}_p\} = argmin_{\textbf{n}, \textbf{c}}\|\textbf{I}_p-A(\textbf{n})D\textbf{n})\|_2^2 + \lambda \|\textbf{c}\|_1\\
s.t. \textbf{c} \geq 0, \|\textbf{n}\|_2=1
$$

#### Surface normal estimation

Render virtual spheres

$$
b_{ij}(\tilde{\textbf{n}})=max\{0, \tilde{\textbf{n}} \textbf{l}_i\}\cdot \textbf{s}_{\{\textbf{l}_i, \textbf{v}; \tilde{\textbf{n}}\}}^T \rho_j
$$

\\(b_{ij}(\tilde{\textbf{n}})\\) is teh intensity observed at a surface with normal \\(\tilde{\textbf{n}}\\) and BRDF \\(\rho_j\\), under lighting \\( \textbf{l_i} \\). \\(B(\tilde{\textbf{n}})=[b_{ij}(\tilde{\textbf{n}})]\\), we render one such matrix \\(B(\cdot)\\) for each candidate normal.

#### Reflectance estimation

$$
\hat{\textbf{c}_p}=argmin_{\textbf{c}\geq 0}\|\textbf{I}_p-B(\hat{\textbf{n}}_p) \textbf{c}\|_2^2+\lambda \|\textbf{c}\|_1
$$

The use of \\(l_1\\)-regularizer promotes sparse solutions and avoids over-fitting.

### Results

Test on both synthetic and real data.

### Take-away message or Comment
{% endcapture %}

<div class="notice--info">
  {{ notice_info | markdownify }}
</div>