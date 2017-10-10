---
title: Robust Estimation of Fundamental Matrix
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

This post is the results of two previous post: [Estimation of fundamental matrix]({{site.url}}/{{site.baseurl}}/blog/2017/06/fundamental-matrix/) and [A framework for RANSAC]({{site.url}}/{{site.baseurl}}/blog/2017/08/ransac-framework/).

### Results
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/fundamental/bust_left.jpg" alt="epipolar lines" title="epipolar lines"/>
    <img class="col one" src="/assets/img/open3DCV/fundamental/bust_right.jpg" alt="epipolar lines" title="epipolar lines"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/fundamental/building_left.jpg" alt="epipolar lines" title="epipolar lines"/>
    <img class="col one" src="/assets/img/open3DCV/fundamental/building_right.jpg" alt="epipolar lines" title="epipolar lines"/>
</div>
<div class="col three caption">
    Demonstrative result of estimation of fundamental matrix.
</div>

### Implementation
In this specific example, type `T` is `DMatch`, `S` is `float`, where `DMatch` is a datatype defined in open3DCV, that is designed to handle pairwise matching results. The definition of this datatype is as follows:

* `std::pair<int, int> ind_key_` stores the index of the matching keypoints;
* `std::pair<Vec2f, Vec2f> point_` stores the positions of the matching keypoints.

```cpp
class DMatch
{
public:
    DMatch () {};
    DMatch (const int r_ikey1, const int r_ikey2, const float dist) :
        ind_key_(r_ikey1, r_ikey2), dist_(dist) {};
    DMatch (const int r_ikey1, const int r_ikey2, const Vec2f r_pt1, const Vec2f r_pt2, const float dist) :
        ind_key_(r_ikey1, r_ikey2), point_(r_pt1, r_pt2), dist_(dist) {};
    DMatch (const DMatch& match) :
        ind_key_(match.ind_key_), point_(match.point_), dist_(match.dist_) {};
    
    const float& dist() const;
    
    std::pair<int, int> ind_key_;
    std::pair<Vec2f, Vec2f> point_;
    float dist_;
};
```

### Header file
```cpp
#ifndef fundamental_h_
#define fundamental_h_

#include "math/numeric.h"
#include "estimator/preprocess.h"
#include "estimator/param_estimator.h"

using Eigen::JacobiSVD;

namespace open3DCV
{
    class Fundamental_Estimator : public Param_Estimator<DMatch, float>
    {
    public:
        Fundamental_Estimator();
        Fundamental_Estimator(const float thresh);
        
        void estimate(std::vector<DMatch>& data, std::vector<float>& params);
        
        void ls_estimate(std::vector<DMatch>& data, std::vector<float>& params);
        
        int check_inlier(DMatch& data, std::vector<float>& params);

    private:
        
        void fund_seven_pts(const std::vector<Vec2f>& x1, const std::vector<Vec2f>& x2, std::vector<Mat3f>& F);
        
        void fund_eight_pts(const std::vector<Vec2f>& x1, const std::vector<Vec2f>& x2, Mat3f& F);
        
        float error_thresh_;
    };    
}

#endif
```