---
title: Depth map estimation using multi-view stereo
categories: 
  - Research
tags:
  - Computer Vision
  - 3D Reconstruction
header:
  teaser: research.jpg
---

This post talks about the algorithmic details and implementation of depth map estimation using multi-view stereo technique. The method is proposed in Secion 5, 6 of the paper Multi-View Stereo for Community Photo Collections ([pdf](http://grail.cs.washington.edu/projects/mvscpc/download/Goesele-2007-MVS.pdf)). The MVE has a C++ implementation of the algorithms ([source code](https://github.com/simonfuhrmann/mve/tree/master/libs/dmrecon)).

## View Selection

### Global View Selection

* The number of shared feature points reconstructed in the SfM phase/success in SIFT matching is a good indicator that pixel-level matching will also succeed
* The number of shared feature points is not sufficient to ensure good reconstructions
	* The views with the most shared feature points tend to be nearly collocated, thus do not provide a large enough baseline.
	* SIFT is scale invariant, which causes images of different resolution to match well.

We compute a global score \\(g_R\\) for each view \\(V\\) within a candidate neighbourhood \\(\textbf{N}\\) (which include \\(R\\), and grow incrementally by adding to \\(\textbf{N}\\) the highest scoring view given the current \\(\textbf{N}\\)) as a weighted sum over features shared with \\(R\\):

$$
g_R(V)=\sum_{f \in \textbf{F}_V \cap \textbf{F}_R} \omega_\textbf{N}(f) \cdot \omega_s(f)
$$

The weight function \\(\omega_\textbf{N}(f)\\) is defined as a product over all pairs of views in \\(\textbf{N}\\):

$$
\omega_\textbf{N}(f) = \Pi_{V_i, V_j \in \textbf{N}, s.t. i \neq j, f \in \textbf{F}_V \cap \textbf{F}_R} \omega_\alpha (f, V_i, V_j)
$$

where \\(\omega_\alpha (f, V_i, V_j) = \min ((\frac{\alpha}{\alpha_{max}})^2, 1)\\) and \\(\alpha\\) is the angle between the lines of sight from \\(V_i\\) and \\(V_j\\) to \\(f\\). The quadratic weight function serves to counteract the trend of greater numbers of features in common with decreasing angle. Meanwhile, excessively large triangulation angles are automatically discouraged by the associated scarcity of shared SIFT features.

The weighting function \\(\omega_s(f)\\) measures similarity in resolution of images \\(R\\) and \\(V\\) at feature \\(f\\). To estimate the 3D sampling rate of \\(V\\) in the vicinity of the feature \\(f\\), we compute the diameter \\(s_V(f)\\) of a sphere centered at \\(f\\) whose projected diameter in \\(V\\) equals the pixel spacing in \\(V\\). \\(s_R(f)\\) is computed similarly for \\(R\\) and define the scale weight \\(\omega_s\\) based on the ratio \\(r=\frac{s_R(f)}{s_V(f)}\\) using

$$
\omega_s(f)=
\begin{cases}
    \frac{2}{r}  & \quad 2 \leq r\\
    1  & \quad 1 \leq r < 2\\
    r  & \quad r < 1
  \end{cases}
$$

The weight function favors views with equal or higher resolution than the reference view.

### Local View Selection

Instead of using all of the views determined by global view selection, only a subset \\(\textbf{A} \in \textbf{N}\\) is used to speed up the depth computation, which speeds up the depth computation.

\\(\textbf{A}\\) is iteratively updated using a set of local view selection criteria that favours views that are photometrically consistent and provide a sufficiently wide range of observation directions (?).


### Multi-View Stereo Reconstruction

#### Region-growing

The idea behind the region growing approach is that a successfully matched depth sample provides a good initial estimate for depth, normal, and matching confidence for neighboring pixel locations in \\(\mathit{R}\\). The optimisation process is non-linear with numerous local minima, which makes initialisation critical. This heuristic may fail for non-smooth surfaces or at silhouettes.


#### Stereo Matching as Optimisation

We interpret an \\(n \times n\\) pixel window centered on a pixel in the reference view \\(R\\) as the projection of a small planar patch in the scene. The goal is to optimize over the depth and orientation of this patch to maximize photometric consistency with its projections into the neighbouring views.

##### Scene Geometry Model

![orthographic projection]({{ site.url }}{{ site.baseurl }}/images/depth map estimation/param-stereo-matching.png)

We assume that scene geometry visible in the \\(n \times n\\) pixel window centered at a pixel location \\((s, t)\\) in the reference view is well modeled by a planar, oriented window at depth \\(h(s, t)\\). The 3D position \\(\textbf{x}_R(s, t)\\) of the point projecting to the central pixel is

$$
\textbf{x}_R(s, t)=\textbf{o}_R+h(s, t)\cdot \vec{r}_R(s, t)
$$

where \\(\textbf{o}_R\\) is the center of projection of view \\(R\\) and \\(\vec{r}_R(s, t)\\) is the normalized ray direction through the pixel. We encode the window orientation using per-pixel distance offsets \\(h_s(s, t)\\) and \\(h_t(s, t)\\), corresponding to the per-pixel rate of change of depth in the \\(s\\) and \\(t\\) directions. The 3D position of a point projecting to a pixel inside the matching window is then

$$
\textbf{x}_R(s + i, t + j) = \textbf{o}_R + [h(s, t) + ih_s(s, t) + jh_t(s, t)] \cdot \vec{r}_R (s + i, t + j)
$$

with \\(i, j = -\frac{n-1}{2}, ..., \frac{n-1}{2}\\). We can now determine the corresponding locations in a neighbouring view \\(k\\) with sub-pixel accuracy using that view's projection \\(\textbf{P}_k(x_R(s + i, t + j))\\)

##### Photometric Model

A simple model for reflectance effects: a colour scale factor \\(c_k\\) for each patch projected into the \\(k\\)-th neighbouring view. The model fails when the illumination changes within the patch or when the patch contains a specular highlight or when the local contrast changes between views.

We can relate the pixel intensities within a patch in \\(R\\) to the intensities in the \\(k\\)-th neighbouring view

$$
I_R(s + i, t + j) = c_k(s, t) \cdot I_K(\textbf{P}_k(\textbf{x}_R(s + i, t + j)))
$$

with \\(i, j = -\frac{n-1}{2}, ..., \frac{n-1}{2}, k = 1, ..., m\\), where \\(m=(\|\textbf{A}\|)\\). Omitting the pixel coordinates \\((s, t)\\), we get

$$
I_R(i, j) = c_k \cdot I_k(\textbf{P}_k(\textbf{o}_R + \vec{r}_R(i, j) \cdot (h + ih_s + jh_t)))
$$

In the case of a 3-channel colour image, the equation above represents 3 equations, one per colour channel. Consider all pixels in the window (\\(n^2\\)) and all neighbouring views (\\(m\\)), we have \\(3n^2m\\) equations to solve for \\(3 + 3m\\): \\(h, h_s, h_t\\) and the per-view color scale \\(c_k\\). To solve this overdetermined non-linear system, we follow the standard **MPGC** approach and linearize the above equation

$$
I_R(i, j) = c_k \cdot I_k(\textbf{P}_k(\textbf{o}_R + \vec{r}_R(i, j) \cdot (h + ih_s + jh_t))) + \frac{\partial I_k(i, j)}{\partial h} \cdot (dh + i \cdot dh_s + j \cdot dh_t)
$$

Given an initial value for \\(h, h_s, \text{ and } h_t\\), we can solve for \\(dh, dh_s, dh_t\\), and the \\(c_k\\) using linear least squares. Then we update \\(h, h_s, h_t\\) by adding to them \\(h, h_s, \text{ and } h_t\\), respectively and iterate.
