---
layout: page
title: Structure from Motion - a tutorial
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

Structure from Motion is the holy grail of multiple view geometry. It is a process of estimating camera pose and retrieving a sparse reconstruction. In this tutorial, I'll explain every step of this technique and provide detailed implementation using [open3DCV]({{site.url}}/{{site.baseurl}}blog/2017/05/3d-vision-lib/).

### 0. Test images
I used the `bust` images from Jianxiong Xiao's SfM tutorial, and `templeSparseRing` dataset from Middlebury mview datasets. The directory of the test images is

```cpp
string idir = "/Users/BlacKay/Documents/Projects/Images/test/bust/";
// string idir = "/Users/BlacKay/Documents/Projects/Images/test/templeSparseRing/";
```

### 1. Image IO

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

### 2. Feature detection
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

### 3. Descriptor extraction
See the code above

### 4. Feature matching
See the matching results below
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/matching1_2.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/matching2_3.jpg" alt="" title="example image"/>
    <img class="col one" src="/assets/img/open3DCV/sfm/matching3_4.jpg" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sfm/matching4_5.jpg" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of feature matching.
</div>

```cpp
Matcher_Param matcher_param(0.8, 128, 10, 3);
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

### 5. Relative pose estimation

#### Known intrinsic parameters
Assuming the cameras have been calibrated, thus euclidean (metric) reconstruction is possible.

##### Estimate essential matrix
[TBD]

##### Estimate fundamental matrix
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
vector<std::pair<Vec2f, Vec2f> > data;
for (int i = 0; i < nimages - 1; ++i)
{
    for (int j = 0; j < matches[i].size(); ++j)
    {
        Vec2f x1 = keys[i][matches[i][j].ikey1_].coords();
        Vec2f x2 = keys[i+1][matches[i][j].ikey2_].coords();
        std::pair<Vec2f, Vec2f> pair_data;
        pair_data.first = x1;
        pair_data.second = x2;
        data.push_back(pair_data);
    }
    vector<float> params(9);
    Param_Estimator<std::pair<Vec2f, Vec2f>, float>* fund_esti = new open3DCV::Fundamental_Estimator(0.2f);
    Ransac<std::pair<Vec2f, Vec2f>, float>::estimate(fund_esti, data, params, 0.99);
    
    Mat3f F;
    F << params[0], params[3], params[6],
         params[1], params[4], params[7],
         params[2], params[5], params[8];
    
    // visualize epipolar geometry
    if (is_vis)
    {
        draw_epipolar_geometry(images[i], images[i+1], F, data, "epipolar"+to_string(i+1)+"_"+to_string(i+2));
    }
    data.clear();
}
```

#### Unknown intrinsic parameters
Assuming that the cameras have not been calibrated, in this case, the scene is reconstructed up to a projective projection.

##### Estimate homography
[TBD]

##### Estimate focal length from homography
[TBD]

### 6. N-view triangulation
[Done]

### 7. Bundle adjustment

### 8. Two-view SfM
One graph represents two views with sufficient correspondences. This sub-section is for all possible graphs.

#### 8.1. Two-view matching

#### 8.2. Two-view relative pose estimation

#### 8.3. Two-view triangulation

#### 8.4. Two-view bundle adjustment

### 9. Sequential SfM 

#### 9.1. merge two graphs

#### 9.2. N-view triangulation

#### 9.3. N-view bundle adjustment

### 10. Output
