---
layout: page
title: Structure from Motion Tutorial
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

Structure from Motion is the holy grail of multiple view geometry. It is a process of estimating camera pose and retrieving a sparse reconstruction. In this tutorial, I'll explain every step of this technique and provide detailed implementation using [open3DCV]({{site.url}}{{site.baseurl}}/open3DCV/). The source code can be found [here](https://github.com/imkaywu/open3DCV/blob/master/test/sfm.cc).

### Table of Content
0. [Test images](#test_image)
1. [Image IO](#image_io)
2. [Feature detection](#feature_detection)
3. [Descriptor extraction](#descriptor_extraction)
4. [Feature matching](#feature_matching)
    * [class `pair`](#class_pair)
5. [Relative pose estimation](#relative_pose)
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
    * [Two-view filtering](#2v_filter)
9. [N-view SfM](#nv_sfm)
10. [Output](#output)
11. [Results](#result)

### 0. Test images <a name="test_image"></a>
I used the `bust` images from Jianxiong Xiao's SfM tutorial, and `templeRing` dataset from Middlebury mview datasets. The directory of the test images is

```cpp
string idir = "/Users/BlacKay/Documents/Projects/Images/test/bust/";
string idir = "/Users/BlacKay/Documents/Projects/Images/test/templeRing/";
```

### 1. Image IO <a name="image_io"></a>

```cpp
const int nimages = 10;
char iname[100];
vector<Image> images(nimages);
for (int i = 0; i < nimages; ++i)
{
    sprintf(iname, "%s/%08d.jpg", idir.c_str(), i);
    images[i].read(iname);
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

#### class `Pair` <a name="class_pair"></a>
This is the most important data structure in pairwise image matching. It stores everything regaring the two-view geometry, including matching features, fundamental matrix, essential matrix, relative rotation and translation, and so on. A brief summary of the class `Pair` and the declaration is as follows:
* camera indexes: `ind_cam_`;
* matching features: `matches_`;
* fundamental and essential matrix: `F_`, `E_`;
* intrinsics and extrinsics of the corresponding cameras: `intrinsics_mat_`, `extrinsics_mat_`.

```cpp
class Pair
{
public:
    Pair();
    Pair(const int cam1, const int cam2);
    Pair(const int cam1, const int cam2, const std::vector<std::pair<Vec2f, Vec2f> >& matches);
    Pair(const int cam1, const int cam2, const std::vector<DMatch>& matches);
    ~Pair();
    
    void init(const int ind_cam1, const int ind_cam2);
    void update_matches(const int* vote_inlier);
    void update_intrinsics(const float f, const int w, const int h);
    bool operator<(const Pair& rhs) const;
    float baseline_angle() const;
    
    std::vector<int> cams_;
    std::vector<DMatch> matches_;
    Mat3f F_;
    Mat3f E_;
    std::vector<Mat3f> intrinsics_mat_;
    std::vector<Mat34f> extrinsics_mat_;
    
};
```

The matching is an exhaustive matching between any two images in the set. See the matching results below
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
vector<Pair> pairs;
Matcher_Param matcher_param(0.6*0.6, 50);
Matcher_Flann matcher(matcher_param);
vector<vector<vector<DMatch> > > matches_pairwise(nimages-1, vector<vector<DMatch> >());
for (int i = 0; i < nimages-1; ++i)
{
  matches_pairwise[i].resize(nimages-(i+1));
  for (int j = i+1; j < nimages; ++j)
  {
    vector<DMatch>& matches = matches_pairwise[i][j-(i+1)];
    matcher.match(descs[i], descs[j], matches);
    matcher.matching_keys(keys[i], keys[j], matches);
    pairs.push_back(Pair(i, j, matches));
    string fname = odir+"matching"+to_string(i+1)+"_"+to_string(j+1)+".txt";
    write_matches(fname, matches);
    if ((false))
    {
      draw_matches(images[i], keys[i], images[j], keys[j], matches, odir+"matching"+to_string(i+1)+"_"+to_string(j+1));
    }
    cout << "Image (" << i+1 << ", " << j+1 << "): matches number: " << matches.size() << endl;
  }
}
```

### 5. Relative pose estimation <a name="relative_pose"></a>

#### Known intrinsic parameters
Assuming the cameras have been calibrated, thus euclidean (metric) reconstruction is possible.

##### Estimate essential matrix <a name="essential"></a>
5-point algorithm + RANSAC.

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
for (int i = 0; i < pairs.size(); ++i)
{
    // ------ image pair ------
    Pair& pair = pairs[i];
    int nmatches = static_cast<int>(pair.matches_.size());
    const int& ind1 = pair.cams_[0];
    const int& ind2 = pair.cams_[1];
    cout << "*******************************" << endl;
    cout << " 2-View SfM of image " << ind1+1 << " and " << ind2+1 << endl;
    cout << "*******************************" << endl;

    // ------ estimate Fundamental matrix ------
    vector<float> params(9);
    int *vote_inlier = new int[nmatches];
    Param_Estimator<DMatch, float>* fund_esti = new open3DCV::Fundamental_Estimator(1e-8);
    float ratio_inlier = Ransac<DMatch, float>::estimate(fund_esti, pair.matches_, params, 0.99, vote_inlier);
    std::cout << "ratio of matching inliers: " << ratio_inlier << std::endl;
    if (ratio_inlier < thresh_inlier_ratio)
    {
        delete [] vote_inlier;
        continue;
    }
    pair.F_ << params[0], params[3], params[6],
               params[1], params[4], params[7],
               params[2], params[5], params[8];

    // ------ other more code ------
}
```

##### Estimate pose and orientation of camera pair <a name="rt_from_e"></a>
We can estimate the relative position and orientation of two cameras from Essential matrix, the detail is discussed in [this post]({{site.url}}/{{site.baseurl}}/blog/2017/09/relative-pose/).

```cpp
// ------ estimate relative pose ------
const float f = 1520.4;
const int w = 302.32*2, h = 246.87*2;
pair.update_intrinsics(f, 0, 0);
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
Open3DCVBundleAdjustment(graph, BUNDLE_NO_INTRINSICS);
if (update_focal)
    Open3DCVBundleAdjustment(graph, BUNDLE_FOCAL_LENGTH);
else if (update_intrinsic)
    Open3DCVBundleAdjustment(graph, BUNDLE_INTRINSICS);
cout << "------ end bundle adjustment ------" << endl;
error = reprojection_error(graph);
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
reprojection error (after bundle adjustment): 0.43175
*******************************
 2-View SfM of image 1 and 2
*******************************
ratio of matching inliers: 0.916667
number of matches: 110
reprojection error (before bundle adjustment): 2.25564
reprojection error (after bundle adjustment): 0.381238
*******************************
 2-View SfM of image 2 and 3
*******************************
ratio of matching inliers: 0.967742
number of matches: 120
reprojection error (before bundle adjustment): 2.01501
reprojection error (after bundle adjustment): 0.238207
*******************************
 2-View SfM of image 3 and 4
*******************************
ratio of matching inliers: 0.840426
number of matches: 79
reprojection error (before bundle adjustment): 1.19674
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
reprojection error (after bundle adjustment): 0.340547
*******************************
 N-View SfM: merging graph 0-2
*******************************
reprojection error (before bundle adjustment): 3.91939
reprojection error (after bundle adjustment): 0.328052
*******************************
 N-View SfM: merging graph 0-3
*******************************
reprojection error (before bundle adjustment): 4.75183
reprojection error (after bundle adjustment): 0.321569
```

### 10. Output <a name="output"></a>
For now, the camera parameters are written to PMVS-compatible files.

```cpp
write_sfm(global_graph);
```

### 11. Results <a name="result"></a>
The lack of detail in the first dataset could be due to the lack of surface texture.
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/sfm_camera_pose_bust.png" alt="" title="sfm results"/>
    <img class="col two" src="/assets/img/open3DCV/sfm/bust.png" alt="" title="sfm results"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/sfm_camera_pose_temple_front.png" alt="" title="sfm results"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/sfm_camera_pose_temple_top.png" alt="" title="sfm results"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/temple.png" alt="" title="sfm results"/>
</div>
<div class="col three caption">
    Estimated camera position and orientation, and the dense reconstruction result using PMVS
</div>
