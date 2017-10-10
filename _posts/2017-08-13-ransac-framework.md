---
title: A framework for RANSAC
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

RANSAC is short for Random SAmple Consensus, which is an iterative method to estimate parameters of a mathematical model from a set of observed data that contains outliers. It can be incorporated to estimate many key matrices in vision, such as homography, fundamental/essential matrix, etc. This post aims to develop a general framework so that RANSAC could be easier applied to existing estimation algorithms. The code is modified from the code originally written by [Ziv Yaniv](http://yanivresearch.info).

There are two separate classes:
* Parameter_Estimator: this is a super-class that each estimation class need to extend. It defines the interfaces for estimating parameters of the model. Classes inherit from this class are used by the `Ransac` class to perform robust parameter estimation.
* Ransac: this class implements the RANSAC framework, a framework for robust parameter estimation.

## Header files
### Parameter_Estimator
This class has the following methods:
* `estimate`: exact estimation, which uses the minimal amount of data
* `ls_estimate`: estimation of parameters using over-determined data, so that the solution minimizes a least squares cost function.

```cpp
#ifndef param_estimator_
#define param_estimator_

#include <vector>

using std::vector;

namespace open3DCV
{

template<class T, class S>
class Param_Estimator
{
public:
    
    Param_Estimator(unsigned int min_data) : min_num_data_(min_data) { }
    
    virtual void estimate(vector<T>& data, vector<S>& params) = 0;
    
    virtual void ls_estimate(vector<T>& data, vector<S>& params) = 0;
    
    virtual int check_inliers(vector<S>& params, T& data) = 0;
    
    unsigned int num_data() const { return min_num_data_; }
    
private:
    
    unsigned int min_num_data_;
};

}
#endif
```

### Ransac
This class takes an instance of the Param_Estimator as one of the parameters, and estimate the parameters using RANSAC.

```cpp
#ifndef ransac_h_
#define ransac_h_

#include "estimator/param_estimator.h"
#include "estimator/fundamental.h"

namespace open3DCV
{
    
template<class T, class S>
class Ransac
{
public:
    // - params:            a vector containing the estimated parameters
    // - param_estimator:   an instance which can estimate the desired parameters by either an exact
    //                      fit or a least squares fit
    // - data:              the input from which the parameters will be estimated
    // - prob_wo_outlieres: the probability that at least one of the selected subsets doens't contain an outlier,
    //                      must be in (0, 1).
    static float estimate(Param_Estimator<T, S>* param_estimator,
                          std::vector<T>& data,
                          std::vector<S>& params,
                          float prob_wo_outliers);
    
private:
    
    // has to be static
    static unsigned int choose(unsigned int n, unsigned int m);
    
    class Subset_Ind_Cmp
    {
    private:
        int length_;
    public:
        Subset_Ind_Cmp(int length) : length_(length) { }
        bool operator() (const int* arr1, const int* arr2) const
        {
            for (int i = 0; i < length_; ++i)
            {
                if (arr1[i] < arr2[i])
                    return true;
                else if (arr1[i] > arr2[i])
                    return false;
            }
            return false;
        }
    };

};
}
#endif
```

### Select random data


### Uniqueness of selected data


###