---
title: "Paper list (Constantly updating)"
categories:
  - Reading
tags:
  - Paper
---

This is the main page for the papers that I've read and summrized, the format of summary is as below

## Authors, title, keyword(s)

{% capture notice_info %}
### Related work

### Contributions

### Pros & Cons

### Details

### Results

### Take-away message or Comment
{% endcapture %}

<div class="notice--info">
  {{ notice_info | markdownify }}
</div>

Below is the list of papers sorted by category

## 3D Reconstruction

### Shape from Shading 

### [Photometric Stereo]({{ site.url }}{{ site.baseurl }}/2016/05/10/photometric-stereo)

### Binocular Stereo

### Mulit-view Stereo

### Structure from Motion

### Simultaneous Localisation and Mapping

### Model-based methods


## Physics-based Vision

### [Reflection]({{ site.url }}{{ site.baseurl }}/2016/05/10/physics-based-vision)

## TBR

### Misc
* [Ma, et al, 07] Rapid Acquisition of Specular and Diffuse Normal Maps from Polarized Spherical Gradient Illumination
* [Agarwal 07] Generalized Non-metric Multidimensional Scaling

### Reflectance

* [Lawrence, et al, 06] Inverse Shade Trees for Non-Parametric Material Representation
* [Weyrich, Lawrence, Lensch, Rusinkiewicz, Zickler, 09] Principles of Appearance Acquisition and Representation

### Photometric stereo

* [Horn] Understanding Image Intensities
* [Lensch, Goesele, Heidrich, 01] Image-Based Reconstruction of Spatially Varying Materials
* [Alldrin 08] Photometric Stereo With Non-Parametric and Spatially-Varying Reflectance
* [Tagare] A Theory of Photometric Stereo for a Class of Diffuse Non-Lambertian Surfaces
* [Georghiades 03] Recovering 3-D Shape and Reflectance From a Small Number of Photographs
* [Basri 01] Photometric Stereo with General, Unknown Lighting (from normals to surfaces)
* [Ackermann] Removing the Example from Example-based Photometric Stereo
* [Ackermann] A Survey of Photometric Stereo Techniques
* [Ikehata] Robust Photometric Stereo using Sparse Regression [code]()
* [Ikehata] Photometric stereo using constrained bivariate regression for general isotropic surfaces [code]()
* [Ying Xiong] From Shading to Local Shape(http://vision.seas.harvard.edu/qsfs/index.html)

### Surface integration
* [Stolfi] Depth from Slope by Weighted Multi-Scale Integration
* [Stolfi] A robust multi-scale integration method to obtain the depth from gradient maps
* [Durou] Normal Integration – Part I: A Survey
* [Durou] Integration of a Normal Field without Boundary Condition
* [A. Agrawal, R. Chellappa, and R. Raskar] An Algebraic Ap- proach to Surface Reconstruction from Gradient Fields.
* [I. Horowitz and N. Kiryati] Depth from Gradient Fields and Control Points: Bias Correction in Photometric Stereo.
* [R. Klette and K. Schluns] Height data from gradient fields.
* [A. Robles-Kelly and E. R. Hancock] A Graph-Spectral Method for Surface Height Recovery from Needle-maps

### Multi-view photometric stereo

* [Hernandez] Multi-view photometric stereo (calibrated photometric stereo)
* [Wu] High-quality shape from multi-view stereo and shading under general illumination
* [Beljan] Consensus Multi-View Photometric Stereo
* [Ackermann] Multi-View Photometric Stereo by Example (time-space stereo, orientation consistency)
* [Vlasic, CSAIL, USC] Dynamic Shape Capture using Multi-View Photometric Stereo
* [Zhou, CVPR13] Multi-view Photometric Stereo with Spatially Varying Isotropic Materials
* [Grochulla] Combining Photometric Normals and Multi-View Stereo for 3D Reconstruction
* [Haque, CVPR14] High Quality Photometric Reconstruction using a Depth Camera
* [Du Seitz] Binocular Photometric Stereo
* [Galliani] Just look at the image: viewpoint-specific surface normal prediction for improved multi-view reconstruction
* [Wang !] BRDF Invari- ant Stereo using Light Transport Constancy
* [Park] Multiview Photometric Stereo using Planar Mesh Parameterization
* [Langguth] Shading-aware Multi-view Stereo
* [Heise] Variational PatchMatch MultiView Reconstruction and Refinement

### 3D reconstruction

* [Snavely] Photo Tourism: Exploring Photo Collections in 3D
* [Xiao] 3D reconstruction is not just a low-level task: retrospect and survey
* [Maier - Master thesis] Out-of-Core Bundle Adjustment for 3D Workpiece Reconstruction
* [Treuille, Hertzmann, Seitz] Example-Based Stereo with General BRDFs
* [Holroyd] A Coaxial Optical Scanner for Synchronous Acquisition of 3D Geometry and Surface Reflectance
* [Fanello] HyperDepth: Learning Depth from Structured Light Without Matching
* [Habbecke] LaserBrush: A Flexible Device for 3D Reconstruction of Indoor Scenes
* [Sabzevari] PiMPeR: Piecewise Dense 3D Reconstruction from Multi-View and Multi-Illumination Images
* [Zhang, 03] Shape and motion under varying illumination: Unifying structure from motion, photometric stereo, and multi-view stereo
* [Lim, 05] Passive Photometric Stereo from Motion

### MVS

* [Goesele 07] Multi-view Stereo for Community Photo Collections
* [Delaunoy, Pollefeys] Photometric Bundle Adjustment for Dense Multi-View 3D Modeling
* [Galliani - ETH, 15] Massively Parallel Multiview Stereopsis by Surface Normal Diffusion
* [Langguth] Guided Capturing of Multi-view Stereo Datasets
* [Jason Mak] Efficient Dense Reconstruction Using Geometry and Image Consistency Constraints
* [Lhuillier, 02] Match Propagation for Image-Based Modeling and Rendering
* [Uh] Efficient Multiview Stereo by Random-Search and Propagation
* [Uh] Multi-view 3D reconstruction by random-search and propagation with view-dependent patch maps
* [Shuhan Shen] Accurate Multiple View 3D Reconstruction Using Patch-Based Stereo for Large-Scale Scenes
* [Zheng] PatchMatch Based Joint View Selection and Depthmap Estimation
* [Baek] Multiview Image Completion with Space Structure Propagation

### SfM

* [Schönberger] Structure-from-Motion Revisited

## MVS + PS
* [Cryer, Tsai, Shah] [Combining Shape from Shading and Stereo Using Human Vision Model](http://vision.eecs.ucf.edu/combsrc.html)

#### Quasi-dense Stereo Correspondence

* [Kannala] Quasi-Dense Wide Baseline Matching Using Match Propagation
* [Koskenkorva, Kannala] Quasi-Dense Wide Baseline Matching for Three Views
* [Cech] Efficient Sampling of Disparity Space for Fast And Accurate Matching
* [Geiger] StereoScan: Dense 3d Reconstruction in Real-time
* [Zhang] Cross-scale cost aggregation for stereo matching

### Calibration

* [Herrera 12] Joint depth and color camera calibration with distortion correction
* [Ackermann 13] Geometric Point Light Source Calibration
* [Zhang] CALIBRATION BETWEEN DEPTH AND COLOR SENSORS FOR COMMODITY DEPTH CAMERAS
* [Hartley 09] Global Optimization through Rotation Space Search
* [Marc Pollefeys] Euclidean 3D reconstruction from stereo sequences wth variable focal lengths

### Closed-form 2-view registration (correspondence known)
* K. S. Arun, T. S. Huang, and S. D. Blostein. Least-squares fitting of two 3-d point sets. IEEE Transactions on Pattern Analysis and Machine Intelligence, 9(5):690–700, 1987. [done]
* B. K. P. Horn. Closed-form solution of absolute orientation using unit quaternions. Journal of the Optical Society of America A, 4(4):629–642, 1987.
* B. K. P. Horn, H. M. Hilden, and S. Negahdaripour. Closed form solution of absolute orientation using orthonormal matrices. Journal of the Optical Society of America A, 5(7):1127–1135, July 1988.
* M. W. Walker, L. Shao, and R. A. Volz. Estimating 3-d location parameters using dual number quaternions. CVGIP: Image Understanding, 54(3):358–367, November 1991.
[Umeyama 91] Least-Squares Estimation of Transformation Parameters Between Two Point Patterns
* [Lorusso] A comparison of Four Algorithms for Estimating 3-D Rigid Transformations
* [Horn 2001] Some Notes on Unit Quaternions and Rotation

### Iterative 2-view registration (correspondence unknown)

* [Chen 91] Object Modeling by Registration of Multiple Range Images
* [Besl, McKay 92] A method for registration of 3-d shapes
* [Johnson 97] Surface Registration by Matching Oriented Points
* [Rusinkiewicz, Levoy] Efficient Variants of the ICP Algorithm
* [Granger 02] Multi-scale EM-ICP: A Fast and Robust Approach for Surface Registration
* [Du 07] AN EXTENSION OF THE ICP ALGORITHM CONSIDERING SCALE FACTOR
* [Fitzgibbon] Robust Registration of 2D and 3D Point Sets(LM-ICP)
* The Trimmed Iterative Closest Point Algorithm
* [Bouaziz 13] Sparse Iterative Closest Point
* [Daniel 11] A modified ICP algorithm for normal-guided surface registration
* [Wild 10] Recent Development of the Iterative Closest Point (ICP) Algorithm
* [Oswald 10] Recent development of the Iterative Closest Point algorithm
* [thesis](http://www2.imm.dtu.dk/~jakw/publications/bscthesis.pdf)
* [Yang 13] Go-ICP: Solving 3D Registration Efficiently and Globally Optimally
* [Campbell 16] GOGMA: Globally-Optimal Gaussian Mixture Alignment
* [Straub 16] Efficient Globally Optimal Point Cloud Alignment using Bayesian Nonparametric Mixtures

### Multi-view registration

* [Pooja A.] A Multi-View Extension of the ICP algorithm
* [Pulli] Multiview Registration for Large Data Sets
* [Bergevin] Towards a general multi-view registration technique
* [Tam 2007] Registration of 3D Point Clouds and Meshes: A Survey From Rigid to Non-Rigid
* [Evangelidis] A Generative Model for the Joint Registration of Multiple Point Sets
* [Zhou] Fast Global Registration

### GMM Registration

* [Tsin, Kanade] A Correlation-Based Approach to Robust Point Set Registration
* [Jian]Robust Point Set Registration Using Gaussian Mixture Models
* [Dylan 15] An Adaptive Data Representation for Robust Point-Set Registration and Merging
* [Rigas] Hierarchical Similarity Transformations Between Gaussian Mixtures
* [Eckart] REM-Seg: A Robust EM Algorithm for Parallel Segmentation and Registration of Point Clouds
* [Chui] A Feature registration framework using mixture models

### Evaluation

* [Strecha] On benchmarking camera calibration and multi-view stereo for high resolution imagery
* [Jensen] Large Scale Multi-view Stereopsis Evaluation

### Light stage

* [Einarsson] Relighting human locomotion with flowed reflectance fields

### 3D Capture system

* Fusing Multiview and Photometric Stereo for 3D Reconstruction under Uncalibrated Illumination
* Building a Digital Model of Michelangelo’s Florentine Pieta

### visual properties
Visual perception of materials and surfaces
Visual perception of materials and their properties
Human Perception: Visual Heuristics in the Perception of Glossiness
Perception of the material properties of wood based on vision, audition, and touch
Visual Cues and 'pseudocues' to the Perception of 3D Surface Texture