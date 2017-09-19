---
title: Robust Fundamental Matrix
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

### Results


### Implementation
This post is the results of two previous post: [Estimation of fundamental matrix]({{site.url}}/{{site.baseurl}}/blog/2017/06/fundamental-matrix/) and [A framework for RANSAC]({{site.url}}/{{site.baseurl}}/blog/2017/08/ransac-framework/).

In this specific example, type `T` is `std::pair<Vec2f, Vec2f>`, `S` is `float`.

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

class Fundamental_Estimator : public Param_Estimator<std::pair<Vec2f, Vec2f>, float>
{
public:
    
    Fundamental_Estimator(const float thresh);
    
    void estimate(std::vector<std::pair<Vec2f, Vec2f> >& data, std::vector<float>& params);
    
    void ls_estimate(std::vector<std::pair<Vec2f, Vec2f> >& data, std::vector<float>& params);
    
    int check_inliers(std::vector<float>& params, std::pair<Vec2f, Vec2f>& data);

    
private:
    
    void fund_seven_pts (const std::vector<Vec2f>& x1, const std::vector<Vec2f>& x2, Mat3f& F);
    
    void fund_eight_pts(const std::vector<Vec2f>& x1, const std::vector<Vec2f>& x2, Mat3f& F);
    
    float error_thresh_;

};
    
}

#endif
```