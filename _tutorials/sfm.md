---
layout: page
title: Structure from Motion Tutorial
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

Structure from Motion is the holy grail of multiple view geometry. It is a process of estimating camera pose and retrieving a sparse reconstruction. In this tutorial, I'll explain every step of this technique and provide detailed implementation using [open3DCV]({{site.url}}/{{site.baseurl}}blog/2017/05/3d-vision-lib/). The source code can be found [here](https://github.com/imkaywu/open3DCV/blob/master/test/sfm.cc).

### Table of Content
0. [Test images](#test_image)
1. [Image IO](#image_io)
2. [Feature detection](#feature_detection)
3. [Descriptor extraction](#descriptor_extraction)
4. [Feature matching](#feature_matching)
5. [Relative pose estimation](#relative_pose)
    * [class `pair`](#class_pair)
    * Known intrinsic parameters
        * [Essential matrix estimation](#essential)
        * [Fundamental matrix estimation](#fundamental)
    * Unknown intrinsic parameters
        * [Homography estimation](#homography)
        * [focal length from Homography](#f_from_homog)
6. [N-view triangulation](#triangulation)
    * [class `Graph`](#class_graph)
7. [Bundle adjustment](#bundle_adjustment)
8. [Two-view SfM](#2v_sfm)
9. [N-view SfM](#nv_sfm)
10. [Output](#output)

### 0. Test images <a name="test_image"></a>
I used the `bust` images from Jianxiong Xiao's SfM tutorial, and `templeSparseRing` dataset from Middlebury mview datasets. The directory of the test images is

```cpp
string idir = "/Users/BlacKay/Documents/Projects/Images/test/bust/";
// string idir = "/Users/BlacKay/Documents/Projects/Images/test/templeSparseRing/";
```

### 1. Image IO <a name="image_io"></a>

```cpp
const int nimages = 5;
vector<string> inames(nimages);
inames[0] = idir + "B21.jpg";
inames[1] = idir + "B22.jpg";
inames[2] = idir + "B23.jpg";
inames[3] = idir + "B24.jpg";
inames[4] = idir + "B25.jpg";
vector<Image> images(nimages);
for (int i = 0; i < nimages; ++i)
{
    images[i].read(inames[i]);
}
```

### 2. Feature detection <a name="feature_detection"></a>
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/feature1.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/feature2.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/feature3.jpg" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/feature4.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/feature5.jpg" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of feature detection.
</div>

```cpp
Sift_Params sift_param(3, 3, 0, 10.0f / 255.0, 0, -INFINITY, 3, 2);
Sift sift(sift_param);
vector<vector<Keypoint> > keys(nimages, vector<Keypoint>());
vector<vector<Vecf> > descs(nimages, vector<Vecf>());
for (int i = 0; i < nimages; ++i)
{
    sift.detect_keypoints(images[i], keys[i]);
    sift.extract_descriptors(images[i], keys[i], descs[i]);
    sift.clear(); // don't forget this
    if (is_vis)
    {
        draw_cross(images[i], keys[i], "feature_"+to_string(i+1));
    }
}
```

### 3. Descriptor extraction <a href="descriptor_extraction"></a>
The code above also extract descriptors from detected features.

### 4. Feature matching <a name="feature_matching"></a>
See the matching results below
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/matching_inlier1_2.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/matching_inlier2_3.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/matching_inlier3_4.jpg" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/matching_inlier4_5.jpg" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of feature matching.
</div>

```cpp
Matcher_Param matcher_param;
Matcher_Flann matcher(matcher_param);
vector<vector<Match> > matches(nimages - 1, vector<Match>());
for (int i = 0; i < nimages - 1; ++i)
    for (int j = i + 1; i < nimages; ++j)
    {
        matcher.match(descs[i], descs[j], matches[i]);
        if (is_vis)
        {
            draw_matches(images[i], keys[i], images[j], keys[j], matches[i], "matching"+to_string(i+1));
        }
    }
```

### 5. Relative pose estimation <a name="relative_pose"></a>

#### class `pair` <a name="class_pair"></a>
We first need to define a class `pair` to store information of an image pair, which is defined as follows:
* camera indexes: `ind_cam_`;
* matching features: `matches_`;
* fundamental and essential matrix: `F_`, `E_`;
* intrinsics and extrinsics of the corresponding cameras: `intrinsics_mat_`, `extrinsics_mat_`.

```cpp
class Pair
{
public:
    Pair(const int ind_cam1, const int ind_cam2);
    Pair(const int ind_cam1, const int ind_cam2, const std::vector<std::pair<Vec2f, Vec2f> >& matches);
    ~Pair();
    
    void update_matches(const std::vector<std::pair<Vec2f, Vec2f> >& matches, const int* vote_inlier);
    void update_intrinsics(const float f, const int w, const int h);
    
    std::vector<int> ind_cam_;
    std::vector<std::pair<Vec2f, Vec2f> > matches_;
    Mat3f F_;
    Mat3f E_;
    std::vector<Mat3f> intrinsics_mat_;
    std::vector<Mat34f> extrinsics_mat_;
    
private:
    void init(const int ind_cam1, const int ind_cam2);
};
```

#### Known intrinsic parameters
Assuming the cameras have been calibrated, thus euclidean (metric) reconstruction is possible.

##### Estimate essential matrix <a name="essential"></a>
[TBD]

##### Estimate fundamental matrix <a name="fundamental"></a>
I use the `7-point algorithm` + `RANSAC` to estimate fundamental matrix. See the results below.
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/epipolar1_2.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/epipolar2_3.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/epipolar3_4.jpg" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/epipolar4_5.jpg" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of fundamental matrix.
</div>

```cpp
// variable to store the coordinates of matching features
vector<std::pair<Vec2f, Vec2f> > data;
for (int i = 0; i < nimages - 1; ++i)
{
    cout << "*******************************" << endl;
    cout << " 2-View SfM of image " << i << " and " << i + 1 << endl;
    cout << "*******************************" << endl;

    // ------ image pair ------
    Pair pair(i, i+1);
    int nmatches = static_cast<int>(matches[i].size());
    for (int j = 0; j < nmatches; ++j)
    {
        Vec2f x1 = keys[i][matches[i][j].ikey1_].coords();
        Vec2f x2 = keys[i+1][matches[i][j].ikey2_].coords();
        std::pair<Vec2f, Vec2f> pair_data;
        pair_data.first = x1;
        pair_data.second = x2;
        data.push_back(pair_data);
    }
    int *vote_inlier = new int[nmatches];
    std::fill(vote_inlier, vote_inlier + nmatches, 1);
    pair.update_matches(data, vote_inlier);
    
    // ------ estimate Fundamental matrix ------
    vector<float> params(9);
    Param_Estimator<std::pair<Vec2f, Vec2f>, float>* fund_esti = new open3DCV::Fundamental_Estimator(10e-8);
    float ratio_inlier = Ransac<std::pair<Vec2f, Vec2f>, float>::estimate(fund_esti, pair.matches_, params, 0.99, vote_inlier);
    std::cout << "ratio of matching inliers: " << ratio_inlier << std::endl;
    pair.F_ << params[0], params[3], params[6],
               params[1], params[4], params[7],
               params[2], params[5], params[8];
    
    // remove outliers
    pair.update_matches(data, vote_inlier);
    std::cout << "number of matches: " << pair.matches_.size() << std::endl;

    // ------ other code ------

    delete [] vote_inlier;
    data.clear();
}
```

##### Estimate pose and orientation of camera pair <a name="rt_from_e"></a>
We can estimate the relative position and orientation of two cameras from Essential matrix, the detail is discussed in [this post]({{site.url}}/{{site.baseurl}}/blog/2017/09/relative-pose/).

```cpp
// ------ estimate relative pose ------
// ugly, but works for now
const float f = 719.5459;
const int w = 480, h = 640;
pair.update_intrinsics(f, w, h);
pair.E_ = pair.intrinsics_mat_[1].transpose() * pair.F_ * pair.intrinsics_mat_[0];
Rt_from_E(pair);
```

#### Unknown intrinsic parameters
Assuming that the cameras have not been calibrated, in this case, the scene is reconstructed up to a projective projection.

##### Estimate homography <a name="homography"></a>
[TBD]

##### Estimate focal length from homography <a name="f_from_homog"></a>
[TBD]

### 6. N-view triangulation <a name="triangulation"></a>
The N-view triangulation is discussed in [this pose]({{site.url}}/{{site.baseurl}}/blog/2017/07/triangulation/). I use the `triangulate_nonlinear()` method to compute the 3D points.

#### class `Graph` <a name="class_graph"></a>
The class `Graph` is heavily used hereafter, it is a data type that stores multiple camera views with sufficient amount of matching features, the corresponding camera poses/orientations, and sparse 3D points, the detailed definition is as follows

* number of cameras: `ncams_`;
* indexes of cameras: `ind_cam_`;
* Fundamental and Essential matrix: `F_`, `E_`;
* intrinsics and extrinsics of cameras: `intrinsics_mat_`, `extrinsics_mat_`;
* point tracks: `tracks_`. Each set of matching pixels across multiple images should correspond to a single point in 3D, i.e., each individual pixel in a matched set should be the projection of the same 3D point.
* structure points: `structure_points_`, a fancier version of 3D point.

```cpp
class Graph
{
public:
    Graph();
    Graph(const Pair& pair);
    ~Graph();
    
    void init(const Pair& pair);
    int index(int icam) const;
    int size() const;
    static std::vector<int> intersect(const std::vector<Track>& tracks1, const std::vector<Track>& tracks2);
    
    int ncams_;
    std::vector<int> ind_cam_;
    Mat3f F_;
    Mat3f E_;
    float f_;
    std::vector<Mat3f> intrinsics_mat_;
    std::vector<Mat34f> extrinsics_mat_;
    std::vector<Track> tracks_;
    std::vector<Structure_Point> structure_points_;
};
```

The `triangulate_nonlinear` takes a `Graph` instance as input and compute the `structure_points_` from feature tracks and relative poses.
```cpp
triangulate_nonlinear(graph[i]);
// compute reprojection error
float error = reprojection_error(graph[i]);
std::cout << "reprojection error (before bundle adjustment): " << error << std::endl;
```

### 7. Bundle adjustment <a name="bundle_adjustment"></a>
The detailed bundle adjustment for SfM is discussed in [this post]({{site.url}}/{{site.baseurl}}/blog/2017/09/sfm-bundle-adjustment/).

```cpp
// ------ bundle adjustment ------
cout << "------ start bundle adjustment ------" << endl;
Open3DCVBundleAdjustment(graph[i], BUNDLE_PRINCIPAL_POINT);
cout << "------ end bundle adjustment ------" << endl;
error = reprojection_error(graph[i]);
std::cout << "reprojection error (after bundle adjustment): " << error << std::endl;
```

### 8. Two-view SfM <a name="2v_sfm"></a>
For each `Graph` instance, we repeat the step 1-7 to compute both camera parameters, poses, orientations, and a sparse set of 3D points. This process is called two-view Structure from Motion. See the running results below:

```cpp
*******************************
 2-View SfM of image 0 and 1
*******************************
ratio of matching inliers: 0.988506
number of matches: 172
reprojection error (before bundle adjustment): 3.87837
------ start bundle adjustment ------
iter      cost      cost_change  |gradient|   |step|    tr_ratio  tr_radius  ls_iter  iter_time  total_time
   0  2.885275e+03    0.00e+00    4.96e+05   0.00e+00   0.00e+00  1.00e+04        0    3.84e-02    4.09e-02
   1  1.967958e+02    2.69e+03    2.71e+02   6.75e+01   1.00e+00  3.00e+04        2    2.11e-01    2.52e-01
   2  1.930865e+02    3.71e+00    1.84e+02   7.41e+01   1.00e+00  9.00e+04        4    2.11e-01    4.64e-01
   3  1.922473e+02    8.39e-01    1.40e+03   1.44e+01   1.00e+00  2.70e+05        4    3.42e-02    4.98e-01
   4  2.242285e+02   -3.20e+01    1.95e+04   5.30e+01   9.87e-01  8.10e+05        9    3.54e-02    5.33e-01
   5  1.879325e+02    3.63e+01    1.50e+02   3.25e+01   1.00e+00  2.43e+06        3    3.41e-02    5.67e-01
   6  1.947733e+02   -6.84e+00    9.32e+03   4.59e+01   9.84e-01  7.29e+06        9    3.85e-02    6.06e-01
   7  1.873827e+02    7.39e+00    3.45e+01   1.17e+01   1.00e+00  2.19e+07        3    3.73e-02    6.43e-01
   8  1.873775e+02    5.22e-03    1.56e+02   5.25e+00   9.84e-01  6.56e+07        9    4.01e-02    6.84e-01
   9  1.873755e+02    1.94e-03    3.41e-01   2.34e-01   1.00e+00  1.97e+08        9    4.21e-02    7.26e-01
Ceres Solver Report: Iterations: 9, Initial cost: 2.885275e+03, Final cost: 1.873755e+02, Termination: CONVERGENCE
------ end bundle adjustment ------
reprojection error (after bundle adjustment): 0.43175
*******************************
 2-View SfM of image 1 and 2
*******************************
ratio of matching inliers: 0.916667
number of matches: 110
reprojection error (before bundle adjustment): 2.25564
------ start bundle adjustment ------
iter      cost      cost_change  |gradient|   |step|    tr_ratio  tr_radius  ls_iter  iter_time  total_time
   0  7.923923e+02    0.00e+00    1.81e+05   0.00e+00   0.00e+00  1.00e+04        0    1.92e-02    2.02e-02
   1  8.861546e+01    7.04e+02    2.30e+02   1.79e+01   9.87e-01  3.00e+04        2    1.44e-01    1.64e-01
   2  8.837636e+01    2.39e-01    2.77e+02   1.17e+00   9.87e-01  9.00e+04        3    1.21e-01    2.85e-01
   3  8.811263e+01    2.64e-01    5.18e+03   1.05e+01   9.85e-01  2.70e+05        8    1.92e-02    3.04e-01
   4  9.234304e+01   -4.23e+00    1.23e+04   1.62e+01   9.77e-01  8.10e+05        7    2.07e-02    3.25e-01
   5  9.005855e+01    2.28e+00    7.36e+03   1.24e+01   9.73e-01  2.43e+06        7    1.99e-02    3.45e-01
   6  8.677939e+01    3.28e+00    1.15e+03   4.87e+00   9.73e-01  7.29e+06        7    1.96e-02    3.65e-01
   7  8.661048e+01    1.69e-01    4.21e+01   1.31e+00   9.99e-01  2.19e+07        7    2.20e-02    3.87e-01
   8  8.661011e+01    3.66e-04    6.15e+01   1.26e+00   9.73e-01  6.56e+07        7    2.20e-02    4.09e-01
   9  8.660974e+01    3.73e-04    6.49e+00   4.53e-01   9.73e-01  1.97e+08        7    2.26e-02    4.31e-01
Ceres Solver Report: Iterations: 9, Initial cost: 7.923923e+02, Final cost: 8.660974e+01, Termination: CONVERGENCE
------ end bundle adjustment ------
reprojection error (after bundle adjustment): 0.381238
*******************************
 2-View SfM of image 2 and 3
*******************************
ratio of matching inliers: 0.967742
number of matches: 120
reprojection error (before bundle adjustment): 2.01501
------ start bundle adjustment ------
iter      cost      cost_change  |gradient|   |step|    tr_ratio  tr_radius  ls_iter  iter_time  total_time
   0  5.076611e+02    0.00e+00    1.77e+05   0.00e+00   0.00e+00  1.00e+04        0    2.20e-02    2.31e-02
   1  1.520502e+01    4.92e+02    1.94e+02   2.60e+00   1.00e+00  3.00e+04        2    1.39e-01    1.62e-01
   2  1.435592e+01    8.49e-01    1.50e+02   4.68e+00   1.00e+00  9.00e+04       10    2.49e-02    1.87e-01
   3  1.431633e+01    3.96e-02    4.29e+01   2.89e+00   1.00e+00  2.70e+05        9    2.89e-02    2.16e-01
   4  1.430186e+01    1.45e-02    5.72e+01   3.77e+00   1.00e+00  8.10e+05        7    3.23e-02    2.48e-01
   5  1.429232e+01    9.54e-03    4.04e+01   2.46e+00   1.00e+00  2.43e+06        7    2.29e-02    2.71e-01
   6  1.428941e+01    2.91e-03    4.77e+00   1.09e+00   1.00e+00  7.29e+06        9    2.17e-02    2.93e-01
   7  1.428935e+01    5.70e-05    4.03e-01   4.41e-02   1.00e+00  2.19e+07        2    2.10e-02    3.14e-01
Ceres Solver Report: Iterations: 7, Initial cost: 5.076611e+02, Final cost: 1.428935e+01, Termination: CONVERGENCE
------ end bundle adjustment ------
reprojection error (after bundle adjustment): 0.238207
*******************************
 2-View SfM of image 3 and 4
*******************************
ratio of matching inliers: 0.840426
number of matches: 79
reprojection error (before bundle adjustment): 1.19674
------ start bundle adjustment ------
iter      cost      cost_change  |gradient|   |step|    tr_ratio  tr_radius  ls_iter  iter_time  total_time
   0  1.606857e+02    0.00e+00    7.20e+04   0.00e+00   0.00e+00  1.00e+04        0    1.39e-02    1.48e-02
   1  6.570561e+00    1.54e+02    1.25e+01   6.56e+00   1.00e+00  3.00e+04        3    8.53e-02    1.00e-01
   2  6.563225e+00    7.34e-03    4.53e-01   8.34e-01   1.00e+00  9.00e+04        7    7.00e-02    1.70e-01
   3  6.553658e+00    9.57e-03    1.51e+01   2.09e+00   1.00e+00  2.70e+05        5    1.48e-02    1.85e-01
   4  6.541664e+00    1.20e-02    6.03e+01   5.22e+00   1.00e+00  8.10e+05        8    1.43e-02    1.99e-01
   5  6.561159e+00   -1.95e-02    5.65e+01   7.57e+00   9.99e-01  2.43e+06        8    1.39e-02    2.13e-01
   6  6.520189e+00    4.10e-02    2.92e+01   5.26e+00   9.99e-01  7.29e+06        8    1.43e-02    2.28e-01
   7  6.506048e+00    1.41e-02    3.96e+00   1.42e+00   9.99e-01  2.19e+07        9    1.41e-02    2.42e-01
   8  6.505955e+00    9.28e-05    4.77e-02   1.43e-01   1.00e+00  6.56e+07        9    1.45e-02    2.56e-01
Ceres Solver Report: Iterations: 8, Initial cost: 1.606857e+02, Final cost: 6.505955e+00, Termination: CONVERGENCE
------ end bundle adjustment ------
reprojection error (after bundle adjustment): 0.178282
```

### 9. Sequential SfM <a name="nv_sfm"></a>
The sequential SfM can be summarized as follows:
* merge two graphs
* N-view triangulation
* N-view bundle adjustment

The code for N-view triangulation and N-view bundle adjustment are exactly the same as those of the 2-view counterparts. The main issue is to merge multiple pairwise graphs into a global graph containing all camera parameters, feature tracks, and 3D structure points.

#### N-view SfM
```cpp
Graph global_graph(graph[0]);
for (int i = 1; i < nimages - 1; ++i)
{
    cout << "*******************************" << endl;
    cout << " N-View SfM: merging graph 0-" << i << endl;
    cout << "*******************************" << endl;
    // ------ merge graphs ------
    Graph::merge_graph(global_graph, graph[i]);
    
    // ------ N-view triangulation ------
    triangulate_nonlinear(global_graph);
    float error = reprojection_error(global_graph);
    std::cout << "reprojection error (before bundle adjustment): " << error << std::endl;
    
    // ------ N-view bundle adjustment ------
    cout << "------ start bundle adjustment ------" << endl;
    Open3DCVBundleAdjustment(global_graph, BUNDLE_PRINCIPAL_POINT);
    cout << "------ end bundle adjustment ------" << endl;
    error = reprojection_error(global_graph);
    std::cout << "reprojection error (after bundle adjustment): " << error << std::endl;
}
```

#### Merge graphs
```cpp
void Graph::merge_graph(Graph &graph1, Graph &graph2)
{
    vector<int>::iterator iter;
    
    // find overlapping cameras
    vector<int> cams_common(std::max(graph1.ncams_, graph2.ncams_));
    iter = std::set_intersection(graph1.cams_.begin(), graph1.cams_.end(), graph2.cams_.begin(), graph2.cams_.end(), cams_common.begin());
    cams_common.resize(iter - cams_common.begin());
    
    // find distinct cameras
    vector<int> cams_diff(std::max(graph1.ncams_, graph2.ncams_));
    iter = std::set_difference(graph2.cams_.begin(), graph2.cams_.end(), graph1.cams_.begin(), graph1.cams_.end(), cams_diff.begin());
    cams_diff.resize(iter - cams_diff.begin());
    
    if (cams_common.empty())
        return;
    
    // merge the camera intrinsic and extrinsic parameters
    int ind_cam1 = graph1.index(cams_common[0]);
    int ind_cam2 = graph2.index(cams_common[0]);
    Mat34f Rt1 = graph1.extrinsics_mat_[ind_cam1];
    Mat34f Rt2 = graph2.extrinsics_mat_[ind_cam2];
    Mat34f Rt21 = concat_Rt(inv_Rt(Rt1), Rt2);
    for (int i = 0; i < graph2.structure_points_.size(); ++i)
    {
        Vec3f& pt3d = graph2.structure_points_[i].coords();
        pt3d = Rt21 * pt3d.homogeneous();
    }
    Mat34f Rt21t = inv_Rt(Rt21);
    for (int i = 0; i < cams_diff.size(); ++i)
    {
        graph1.cams_.push_back(cams_diff[i]);
        graph1.intrinsics_mat_.push_back(graph2.intrinsics_mat_[graph2.index(cams_diff[i])]);
        graph1.extrinsics_mat_.push_back(concat_Rt(graph2.extrinsics_mat_[graph2.index(cams_diff[i])], Rt21t));
    }
    graph1.ncams_ = static_cast<int>(graph1.cams_.size());
    vector<size_t> indexes;
    sort<int>(graph1.cams_, graph1.cams_, indexes);
    reorder<Mat3f>(graph1.intrinsics_mat_, indexes, graph1.intrinsics_mat_);
    reorder<Mat34f>(graph1.extrinsics_mat_, indexes, graph1.extrinsics_mat_);
    
    const int ntracks = static_cast<int>(graph1.tracks_.size());
    for (int j = 0; j < graph2.tracks_.size(); ++j)
    {
        Track& track2 = graph2.tracks_[j];
        bool is_track_connected = false;
        for (int i = 0; i < ntracks; ++i)
        {
            Track& track1 = graph1.tracks_[i];
            vector<pair<int, int> > feats_common;
            Track::find_overlapping_keypoints(track1, track2, feats_common);
            // find overlapping feature tracks from common cameras
            if (!feats_common.empty())
            {
                Graph::merge_tracks(track1, track2, feats_common);
                is_track_connected = true;
                // check if the structur_points are close, for debug purpose
                if (false)
                {
                    std::cout << "graph1 structure_point: " << std::endl << graph1.structure_points_[i].coords() << std::endl;
                    std::cout << "graph2 structure_point: " << std::endl << graph2.structure_points_[j].coords() << std::endl;
                }
                break;
            }
        }
        // find non-overlapping feature tracks from common cameras
        if (!is_track_connected)
        {
            graph1.add_track(track2);
            graph1.add_struct_pt(graph2.structure_points_[j]);
        }
    }

    // find new features from non-overlapping cameras, this part is not needed for now
}
```

#### Results
```cpp
*******************************
 N-View SfM: merging graph 0-1
*******************************
reprojection error (before bundle adjustment): 1.76187
------ start bundle adjustment ------
iter      cost      cost_change  |gradient|   |step|    tr_ratio  tr_radius  ls_iter  iter_time  total_time
   0  1.982806e+03    0.00e+00    2.31e+05   0.00e+00   0.00e+00  1.00e+04        0    7.02e-02    7.25e-02
   [iterations omitted]
  13  1.143924e+02    6.80e-03    1.26e+01   3.70e-01   9.99e-01  1.59e+10        9    8.41e-02    2.26e+00
Ceres Solver Report: Iterations: 13, Initial cost: 1.982806e+03, Final cost: 1.143924e+02, Termination: CONVERGENCE
------ end bundle adjustment ------
reprojection error (after bundle adjustment): 0.340547
*******************************
 N-View SfM: merging graph 0-2
*******************************
reprojection error (before bundle adjustment): 3.91939
------ start bundle adjustment ------
iter      cost      cost_change  |gradient|   |step|    tr_ratio  tr_radius  ls_iter  iter_time  total_time
   0  1.464648e+04    0.00e+00    7.46e+05   0.00e+00   0.00e+00  1.00e+04        0    1.24e-01    1.32e-01
   [iterations omitted]
  34  1.442173e+02    2.78e-03    8.02e+00   3.16e+06   9.77e-01  1.00e+16       14    1.07e-01    8.95e+00
Ceres Solver Report: Iterations: 34, Initial cost: 1.464648e+04, Final cost: 1.442173e+02, Termination: CONVERGENCE
------ end bundle adjustment ------
reprojection error (after bundle adjustment): 0.328052
*******************************
 N-View SfM: merging graph 0-3
*******************************
reprojection error (before bundle adjustment): 4.75183
------ start bundle adjustment ------
iter      cost      cost_change  |gradient|   |step|    tr_ratio  tr_radius  ls_iter  iter_time  total_time
   0  5.316645e+04    0.00e+00    3.86e+05   0.00e+00   0.00e+00  1.00e+04        0    1.27e-01    1.33e-01
   [iterations omitted]
  26  1.586235e+02    3.25e-03    1.79e+01   4.73e+06   9.11e-01  5.93e+13       25    1.35e-01    9.55e+00
Ceres Solver Report: Iterations: 26, Initial cost: 5.316645e+04, Final cost: 1.586235e+02, Termination: CONVERGENCE
------ end bundle adjustment ------
reprojection error (after bundle adjustment): 0.321569
```

### 10. Output <a name="output"></a>
