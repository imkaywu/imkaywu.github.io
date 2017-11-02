---
title: Paper Summary - Photometric Stereo
categories:
  - Reading
tags:
  - Paper
  - Computer Vision
header:
  teaser: reading.jpg
---

## [Horn, Woodham, Silver 78] Determining shape and reflectance using multiple images

{% capture notice_info %}
### Details

The scene radiance seen by the image sensor is the product of the scene irradiance, and the reflectance. The reflectance is a function of the surface normal relative to the direction to the light source and the direction to the image sensor. The reflectance can be related to BRDF, which depends on the material of which the surface is composed and its microstructure.

Let \\(R(p, q)\\) be the scene radiance of a surface element with gradient \\((p, q)\\), where \\(p\\) and \\(q\\) are the first partial derivatives of the surface elevation w.r.t coordinate axes parallel to the image axes. We have called \\(R(p, q)\\) the "reflectance map". It gives scene radiance as a function of surface gradient and can be determined from the BRDF for the surface material and the distribution of incident radiance.

#### Determining the shape from a single image

If the distribution of lightsources is known, and the object has a uniform surface covering, its shape can be calculated from the image irradiances in a single image. If \\(I(x, y)\\) is the image irradiance at the image point \\((x, y)\\), the the relevant equation is

\\[I(x, y) = R(p, q)\\]

This shape from shading problem can be solved by the above nonlinear first-order partial differential equation, using method like characteristic strip expansion. 

#### Determining shape from two images obtained with different lighting

If two images taken from the same point of view are available, then two constraints apply at every image point. The possible directions of the local surface normal form a two parameter family. This method, which has been called photometric stereo, produces information about the shape of an object in the form of a distribution of surface normals.

$$
I_1(x, y) = R_1(p, q)\\
I_2(x, y) = R_2(p, q)
$$

The calculation of surface normal from the two image irradiances at each point is local and can be implemented by means of a simple 2D look-up table. This table is the inverse of a table containing the two image irradiances indexed on the 2 components of the surface normal. There may be some ambiguity that cannot be resolved locally, since more than one surface normal orientation may produce the same pair of scene radiances. This difficulty may be resolved by an iterative "relaxation" computation in which compatible solutions for neighbouring points are reinforced while conflicting assignments are eliminated.

It is possible to produce inconsistent surface normals as a result of noise in the scene radiance measurements or as a result of depth discontinuities (perhaps even due to discontinuities in gradient). This possibility arises because the surface normal is perpendicular to a local tangent plane, which in turn is defined by the partial derivatives of distance to the object's surface w.r.t. two orthogonal directions parallel to the coordinate axes of the image plane (\\((z_x, z_y, -1)\\)).

#### Determining shape and reflectance using two images

In many real situations the objects do not have uniform surface properties. It's thus interesting to explore methods that can solve for surface reflectance factor and shape simultaneously. There is not enough information in a single image to accomplish this task in general.

Two images do provide enough information. If the surface reflectance factor or "albedo" is \\(\rho(x, y)\\) then one obtains two equations

$$
I_1(x, y) = \rho(x, y) \times R_1(p, q)\\
I_2(x, y) = \rho(x, y) \times R_2(p, q)
$$

The variable surface reflectance factor can be removed by dividing the two images (provided that reflectance factor or "albedo" appears as a multiplicative factor). If we let \\(I_{12}\\) be the ratio of \\(I_1\\) and \\(I_2\\), and \\(R_{12}\\) be the ratio of \\(R_1\\) and \\(R_2\\), then

\\[I_{12}(x, y) = R_{12}(p, q)\\]

One can then apply the shape from shading methods for the determination of shape from a single image. Finally, one can recover the reflectance factor by dividing one of the real images by a synthetic image \\(S_1(x, y)\\).

\\[\rho(x, y) = I_1(x, y)/S_1(x, y)\\]

Where \\(S_1(x, y)\\) is a synthetic image computed using the reflectance map \\(R_1(p, q)\\). The spatial distribution of surface gradient and reflectance factor, are examples of "intrinsic images", distributions of underlying information that can be extracted from the raw image irradiances.

#### Determining shape and reflectance using three images

When 3 images taken under different lighting conditions are available, enough constraint is available to solve for the surface normal and reflectance factor locally. If the reflectance function has a particularly simple form, an analytic solution for the surface normal and the reflectance factor is possible. If this is not the case, one may use a simple algorithm based on a 3D look-up table indexed by the 3 gray levels found in the images at corresponding image points. The equations are

$$
I_1(x, y) = \rho(x, y) \times R_1(p, q)\\
I_2(x, y) = \rho(x, y) \times R_2(p, q)\\
I_3(x, y) = \rho(x, y) \times R_3(p, q)
$$

A better method uses ratios of images to remove the variable surface reflectance factor. If two new "images" are generated by dividing two pairs of the original 3 images, these can be used to determine the shape using photometric stereo. If \\(I_{12}\\) is the ratio of \\(I_1\\) and \\(I_2\\) and so on, then the relevant equations are

$$
I_{12}(x, y) = R_{12}(p, q)\\
I_{23}(x, y) = R_{23}(p, q)
$$

A 2D dimensional look-up table is appropriate for this computation. The surface reflectance factor can be determined after the surface shape has been found, by division of one of the real images with a synthetic image created using the shape information. For reasons of numerical accuracy, it's more appropriate to use other combinations of 2 images than their ratio, for example

$$
[I_1 - I_2]/[I_1 + I_2] = [R_1 - R_2]/[R_1 + R_2]
$$

#### Comparison with ordinary stereo methods


{% endcapture %}

<div class="notice--info">
  {{ notice_info | markdownify }}
</div>

<!-- ===================================== -->

## [Woodham 80] Photometric method for determining surface orientation from multiple images

<!-- ===================================== -->

## [Georghiades 03] Recovering 3-D Shape and Reflectance From a Small Number of Photographs

<!-- ===================================== -->

## [Alldrin 08] Photometric Stereo With Non-Parametric and Spatially-Varying Reflectance

{% capture notice_info %}
### Related work

* Woodham and Silver: strong assumption on the reflectance function, explicit knowledge of BRDF
* Later research focused on weakening constraints and enabling photometric stereo to work on broader classes of objects.
* Hertzmann and Seitz: Reference objects. can handle spatially-varying BRDFs by assuming that the BRDF at each point is a linear combination of the "basis" BRDFs defined by reference objects.
* Goldman: Remove the need for reference objects by iteratively estimating basis BRDFs and surface normal + material weight maps.
* Exploit physical properties of BRDFs: energy conservation, non-negativity, Helmholtz, and isotropy.
* Lawrence: Factorize sampled BRDF values into the product of a material weight matrix and a BRDF matrix

### Contributions

* Simultaneously recover shape and non-parametric reflectance
* Do not impose a parametric model on the reflectance function, introduce bi-variate representations of reflectance.

### Pros & Cons

* Generality: doesn't rely on a specific parametric reflectance model and is purely "data-driven". employs bi-variate approximations of isotropic reflectance functions.

### Details

#### Features

* Exploit isotropy to constrain surface normals to a single degree of freedom.
* use a non-parametric bi-variate approximation of the BRDF
* assume the surfaces are composed of a small number of "basis" materials and solve a factorization problem

#### Imaging setup

Fixed object, fixed orthographic camera, and \\(m\\) images taken under distant point source illumination, with known source positions

* Azimuth angle: assume BRDF at each point is isotropic
* Zenith angle: assume (1) that the surface is composed of a small set of fundamental materials, and (2) that the BRDF at each point is approximated by a bi-variate function.

#### Bivariate BRDF assumption

* Stark, Arvo and Smits: \\(\alpha \sigma\\)-parameterization for bi-variate BRDFs
* This paper: bi-variate parameterization based on the half-way and difference angles, \\(\rho(\theta_h, \theta_d)\\).

#### Image formation model

$$
\begin{align}
e_{ij} &= \textbf{H}_i^T \Phi_{ij} max\{0, \textbf{n}_i^T \textbf{s}_j\}\\
&= \textbf{H}_i^T \tilde{\Phi}_{ij}\\
&= \textbf{w}_i^T \textbf{B}^T \tilde{\Phi}_{ij}
\end{align}
$$

where \\(max{0, \textbf{n}_i^T \textbf{s}_j}\\) accounts for shading and \\(\textbf{\Phi}_{ij} \in \mathbb{R}^{d \times 1}\\) is an interpolation vector mapping the domain of BRDF \\(\textbf{H}_i\\) to the half-angle/difference angle of the \\(ij\\)th measurement.

#### Alternating Constrained Least Squares

* Recover the azimuth angle of the surface normals
* Update \\(\textbf{B}\\) with Fixed \\(\textbf{n}\\) and \\(\textbf{W}\\): minimize the \\(\textit{L}_2\\) error between image measurements \\(e_{ij}\\) and image formation model \\(\textbf{w}_i^T \textbf{B}^T \tilde{\Phi}_{ij}\\)
* Update \\(\textbf{n}\\) and \\(\textbf{W}\\) with Fixed \\(\textbf{B}\\)

#### Additional Constraints

* Additional regularization constraints: impose smoothness and monotonicity over the BRDF domain, re-weight the constraints to prevent sepcular highlights from dominating the solution.
* 

### Results

### Take-away message or Comment
{% endcapture %}

<div class="notice--info">
  {{ notice_info | markdownify }}
</div>

<!-- ===================================== -->

## [Hertzman, Seitz, 05] Example-Based Photometric Stereo: Shape Reconstruction with General, Varying BRDFs (keyword: orientation consistency)

<!-- ===================================== -->

## [Goldman, Curless, Seitz, 05] Shape and Spatially-Varying BRDFs From Photometric Stereo

{% capture notice_info %}
### Related work

Digital creation of 3D shape and material model is a manual and laborous process, which requires reconstruction of shape (2D manifold) and spatially-varying reflectance (6D). Previous research limited the scope of the problem by assuming either known shape or known reflectance.

### Contributions

* Material model: real world variations in BRDF are a result of the surface's composition from several different substances, i.e. complex surfaces are the spatially-varying mixtures of uniform materials (called *fundamental materials*). The mixtures of these materials at each pixel are specified by *material weight maps*. 

### Pros & Cons

### Details

#### Assumption

* A static target object imaged from a distant camera under different illuminaiton with known direction.
* Only local illumination effects are present (no cast shadows, inter-reflections, transparency, or translucency)
* The surface does not contain significant depth discontinuities in the camera's view.
* The materials can be described as a convex combination of a small number of fundamental materials.

#### Problem formulation

Model the color at pixel \\(p\\) as if generated from a convex combination of fundamental materials:

\\[I_{i, p, c} \Leftarrow \sum_m \gamma_{p, m} f_c(\textbf{n}_p, \textbf{L}_i, \alpha_m)\\]

Where \\(I_{i, p, c}\\) represents the color of channel \\(c\\) of pixel \\(p\\) of image \\(i\\). The function \\(f_c\\) represents color channel \\(c\\) of the parametrized lighting model with model \\(\textbf{n}_p\\), lighting condition \\(\textbf{L}_i\\) and BRDF parameter vector \\(\alpha_m\\); there is one \\(\alpha_m\\) for each fundamental material.

Based on this model, the objective function to solve for shape and materials:

$$
Q(\textbf{n}, \alpha, \gamma) = \sum_{i, p, c}(\textbf{I}_{i, p, c}-\sum_m \gamma_{p, m} f_c(\textbf{n}_p, \textbf{L}_i, \alpha_m))^2
$$

in which \\(Q\\) is to be minimized w.r.t. the normals, material parameters, and material weight maps (denoted by \\(\textbf{n}, \alpha, \text{ and } \gamma\\)) without subscripts, respectively). 

Optimization of the objective function is non-trivial. Most optimization algorithms may easily become trapped in local minima. The objective function also tends to get overfitted if the material weights \\(\gamma\\) are left unconstrained. The use of pairwise convexity constraints for these material weights can largely prevent this overfitting.

#### Light calibration (useful)

The light sources' directions and intensities are estimated using chrome and diffuse grey spheres captured under the same illumination as the target object. First direction is recovered as the reflection of the viewing direction about the normal at the point of greatest brightness. The relative intensity is recovered by solving \\(l_i \rho = \sum_p I_{i, p} / \sum_p \textbf{n}_p^T \textbf{L}_i\\).

#### Initialization

Two approaches to initialize normals and materials. One is appropriate for objects whose materials are similar and vary continuously, without uniform regions, and the other is appropriate for objects with more widely-varying and distinct regions.

#### Optimisation

* **Compute surface normals and material weight maps**: first precompute the function \\(f_c\\) over a discrete sampling of normals \\(\textbf{n}\\) for each of the lighting samples \\(\textbf{L}_i\\) and fundamental material parameters \\(\alpha_m\\), then compute \\(\gamma_{p, m}\\) by linear projection and brute force search over all normals and all pairwise combinations of fundamental materials.
* **Enforce integrability**: compute a 3D surface from the estimated surface orientations, then recompute normals from this surface approximation.
* **Optimize BRDF parameters**

### Results

#### Capture mechanism

* Light source: programmed Lutron lighting control system, Camera: Canon 10D camera. Long focal length, distances from target to camera and lights 5 feet.
* Multiple exposures of each lighting sample are combined into high-dynamic range images

#### Editing operations

* **Direct manipulation**: change the parameters of one BRDF to that of another
* **Material Transfer**: BRDF parameters captured from one object can easily be transferred to another.
* **Material/shape extraction and transfer**: 

### Take-away message or Comment
{% endcapture %}

<div class="notice--info">
  {{ notice_info | markdownify }}
</div>

<!-- ===================================== -->

## Multi-View Photometric Stereo by Example

{% capture notice_info %}
### Related work

#### Photometric Stereo

* Woodham: recover shape from varying image intensities
* Jointly recover unknown shape and reflectances
* Less restrained capture setup with arbitrary and unknown illumination. Silver, Hertzmann and Seitz

#### Single Image Reconstruction

* Shape from shading and intrinsic image decomposition methods

#### Multi-view Photometric Reconstruction

* Challenge: find correspondences between pixels in different images
* Most techniques rely on some proxy geometry that gets refined using shading information
* Few approaches use photometric cues for depth estimation

### Contributions

* Present a novel multi-view photometric stereo technique based on matching per-pixel appearance profiles, which makes no assumption about the placement of distant light sources or cameras.
* Analyze the relation between matching ambiguity and normal errors in the multi-view setting and develop an energy formulation that exploits the fact that normals can be recovered more reliably than depth.
* Use an example object to handle arbitrary uniform BRDFs and avoid any light or radiometric camera calibration.

### Pros & Cons

### Details

* Goal: recover the surface of a textureless object solely from a set of images under varying illumination and from different viewpoints
* Project a candidate 3D point into all images to obtain projected pixels. Concatenate 3 color channels to get the *appearance profile*.


### Results

### Take-away message or Comment
{% endcapture %}

<div class="notice--info">
  {{ notice_info | markdownify }}
</div>