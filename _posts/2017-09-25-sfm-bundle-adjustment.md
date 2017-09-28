---
title: Bundle adjustment of Structure from Motion in Ceres Solver
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

Bundle adjustment is used ubiquitously as the last step of most feature based estimation problems. The goal is to simultaneously optimize the motion and data (estimated 2D feature or 3D point position). Some popular open source libraries are: [sparse bundle adjustment](http://users.ics.forth.gr/~lourakis/sba/) (sba), [ceres solver](http://ceres-solver.org/index.html), [Multicore Bundle Adjustment](http://grail.cs.washington.edu/projects/mcba/), [Simple Sparse Bundle Adjustment (SSBA)](http://www.cvg.ethz.ch/research/chzach/opensource.html). In this post, I focus only on ceres solver. I'll add support for [sba](http://users.ics.forth.gr/~lourakis/sba/) in future release of [open3DCV]({{site.url}}{{site.baseurl}}/open3DCV/).

There are multiple ways to formulate the optimization problem, one way to write the cost function is as follows:

$$
E_{BA\_2D}=\sum_i \sum_j \|f(X_i; K_j, R_j, t_j)-x_{ij}\|
$$

The parameters that need to be updated are:
* camera intrinsic parameters: $$f_x$$, $$f_x$$, $$c_x$$, $$c_y$$
* camera extrinsic parameters: $$om[0,1,2]$$, and $$t[0,1,2]$$, where $$om$$ is the axis-angle representation of rotation matrix
* camera distortion parameters: $$k1$$, $$k2$$, $$k3$$, $$p1$$, $$p2$$.
* a set of 3D point positions

The first step is to define a templated functor that computes the reprojection error/residual. This cost functor depends on the 3D points and the parameters listed above.

``` cpp
struct Open3DCVReprojectionError
{
    Open3DCVReprojectionError(const double observed_x, const double observed_y)
        : observed_x_(observed_x), observed_y_(observed_y) {}
    
    template<typename T>
    bool operator()(const T* const intrinsics,
                    const T* const extrinsics,
                    const T* const point,
                    T* residules) const
    {
        const T& focal_length       = intrinsics[OFFSET_FOCAL_LENGTH];
        const T& principal_point_x  = intrinsics[OFFSET_PRINCIPAL_POINT_X];
        const T& principal_point_y  = intrinsics[OFFSET_PRINCIPAL_POINT_Y];
        const T& k1                 = intrinsics[OFFSET_K1];
        const T& k2                 = intrinsics[OFFSET_K2];
        const T& k3                 = intrinsics[OFFSET_K3];
        const T& p1                 = intrinsics[OFFSET_P1];
        const T& p2                 = intrinsics[OFFSET_P2];
        
        // compute projective coordinates: x = RX + t.
        // extrinsics[0, 1, 2]: axis-angle
        // extrinsics[3, 4, 5]: translation
        T x[3];
        ceres::AngleAxisRotatePoint(extrinsics, point, x);
        x[0] += extrinsics[3];
        x[1] += extrinsics[4];
        x[2] += extrinsics[5];
        
        // compute normalized coordinates
        T xn = x[0] / x[2];
        T yn = x[1] / x[2];
        
        T predicted_x, predicted_y;
        
        // apply distortion to the normalized points to get (xd, yd)
        // do something for zero distortion
        apply_radio_distortion_camera_intrinsics(focal_length,
                                                 focal_length,
                                                 principal_point_x,
                                                 principal_point_y,
                                                 k1, k2, k3,
                                                 p1, p2,
                                                 xn, yn,
                                                 &predicted_x,
                                                 &predicted_y);
        
        residules[0] = predicted_x - T(observed_x_);
        residules[1] = predicted_y - T(observed_y_);
        return true;
    }
    
    // Factory to hide the construction of the CostFunction object from the client code
    static ceres::CostFunction* create(const float observed_x,
                                       const float observed_y)
    {
        return (new ceres::AutoDiffCostFunction<Open3DCVReprojectionError, 2, 8, 6, 3>(
                    new Open3DCVReprojectionError(observed_x, observed_y)));
    }
    
    double observed_x_;
    double observed_y_;
};
```

### Data conversion
In the [open3DCV]({{site.url}}/{{site.baseurl}}/open3DCV/) implementation of SfM, these information are originally stored in a class call `Graph`, thus we need methods for data conversion.

```cpp
// Graph class
class Graph
{
public:
    Graph();
    Graph(const Pair& pair);
    ~Graph();
    
    void init(const Pair& pair);
    int index(int icam) const; // return the index of camera i in the array
    int size() const;
    
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

// data conversion methods
vector<Vec6> pack_camera_extrinsics(const Graph& graph);
void unpack_camera_extrinsics(Graph& graph, const vector<Vec6>& extrinsics);
vector<Vec8> pack_camera_intrinsics(const Graph& graph);
void unpack_camera_intrinsics(Graph& graph, const vector<Vec8>& intrinsics);
vector<Vec3> pack_3d_pts(const Graph&graph);
void unpack_3d_pts(Graph &graph, const vector<Vec3>& pts3d)
```

We need to convert the data stored in `Graph` to data used in bundle adjustment functor, which is two arrays of camera parameters, and positions of 3D points.

```cpp
// convert camera rotation to angle axis and merge with translation
vector<Vec6> extrinsics = pack_camera_extrinsics(graph);
vector<Vec8> intrinsics = pack_camera_intrinsics(graph);
vector<Vec3> pts3d = pack_3d_pts(graph);

// for each 3D point
for (int m = 0; m < pts3d.size(); ++m)
{
    Vec3& pt3d = pts3d[m];
    
    for (int n = 0; n < graph.tracks_[m].size(); ++n)
    {
        Keypoint key = graph.tracks_[m][n];
        double x = (double)key.coords().x();
        double y = (double)key.coords().y();
        ceres::CostFunction* cost_function = Open3DCVReprojectionError::create(x, y);
        
        int idx = graph.index(key.index());
        problem.AddResidualBlock(cost_function, NULL, &intrinsics[idx](0), &extrinsics[idx](0), &pt3d(0));
        
        // lock the first camera to better deal with scene orientation ambiguity
        if (!is_camera_locked)
        {
            problem.SetParameterBlockConstant(&extrinsics[idx](0));
            is_camera_locked = true;
        }
    }
}

// other bundle adjustment code

// copy intrinsics and extrinsics back
unpack_camera_extrinsics(graph, extrinsics);
unpack_camera_intrinsics(graph, intrinsics);
unpack_3d_pts(graph, pts3d);
```

### Fixing parameters
To provide more flexibility, the user is allowed to specified which part of intrinsics remains fixed. This is done by setting `SubsetParameterization`. For instance, there are some cases where translation is fixed (rotating camera), thus the translation parameters should be specified as fixed.

```cpp
std::vector<int> constant_translation;
// First three elements are rotation, last three are translation.
constant_translation.push_back(3);
constant_translation.push_back(4);
constant_translation.push_back(5);
constant_transform_parameterization =
	new ceres::SubsetParameterization(6, constant_translation);
problem.SetParameterization(current_camera_R_t,
	constant_transform_parameterization);
```

### Bundle adjustment solver

```cpp
void Open3DCVBundleAdjustment(Graph& graph,
                                  const int bundle_intrinsics)
{
    ceres::Problem::Options problem_options;
    ceres::Problem problem(problem_options);
    
    // convert camera rotation to angle axis and merge with translation
    vector<Vec6> extrinsics = pack_camera_extrinsics(graph);
    vector<Vec8> intrinsics = pack_camera_intrinsics(graph);
    vector<Vec3> pts3d = pack_3d_pts(graph);
    
    // construct the problem
    bool is_camera_locked = false;
    for (int m = 0; m < pts3d.size(); ++m)
    {
        Vec3& pt3d = pts3d[m];
        
        for (int n = 0; n < graph.tracks_[m].size(); ++n)
        {
            Keypoint key = graph.tracks_[m][n];
            double x = (double)key.coords().x();
            double y = (double)key.coords().y();
            ceres::CostFunction* cost_function = Open3DCVReprojectionError::create(x, y);
            
            int idx = graph.index(key.index());
            problem.AddResidualBlock(cost_function, NULL, &intrinsics[idx](0), &extrinsics[idx](0), &pt3d(0));
            
            // lock the first camera to better deal with scene orientation ambiguity
            if (!is_camera_locked)
            {
                problem.SetParameterBlockConstant(&extrinsics[idx](0));
                is_camera_locked = true;
            }
        }
    }
    
    // set part of parameters constant
    if (bundle_intrinsics == BUNDLE_NO_INTRINSICS)
    {
        for (int i = 0; i < intrinsics.size(); ++i)
            problem.SetParameterBlockConstant(&intrinsics[i](0));
    }
    else
    {
        vector<int> constant_intrinsics;
#define MAYBE_SET_CONSTANT(bundle_enum, offset) \
        if (!(bundle_intrinsics & bundle_enum)) { \
            constant_intrinsics.push_back(offset); \
        }
        MAYBE_SET_CONSTANT(BUNDLE_FOCAL_LENGTH,    OFFSET_FOCAL_LENGTH);
        MAYBE_SET_CONSTANT(BUNDLE_PRINCIPAL_POINT, OFFSET_PRINCIPAL_POINT_X);
        MAYBE_SET_CONSTANT(BUNDLE_PRINCIPAL_POINT, OFFSET_PRINCIPAL_POINT_Y);
        MAYBE_SET_CONSTANT(BUNDLE_RADIAL_K1,       OFFSET_K1);
        MAYBE_SET_CONSTANT(BUNDLE_RADIAL_K2,       OFFSET_K2);
        MAYBE_SET_CONSTANT(BUNDLE_TANGENTIAL_P1,   OFFSET_P1);
        MAYBE_SET_CONSTANT(BUNDLE_TANGENTIAL_P2,   OFFSET_P2);
#undef MAYBE_SET_CONSTANT
        
        // always set K3 constant, it's not used at the moment
        constant_intrinsics.push_back(OFFSET_K3);
        ceres::SubsetParameterization *subset_parameterizaiton =
            new ceres::SubsetParameterization(8, constant_intrinsics);
        
        for (int i = 0; i < intrinsics.size(); ++i)
            problem.SetParameterization(&intrinsics[i](0), subset_parameterizaiton);
    }
    
    // configure the solver
    ceres::Solver::Options options;
    options.use_nonmonotonic_steps = true;
    options.preconditioner_type = ceres::SCHUR_JACOBI;
    options.linear_solver_type = ceres::ITERATIVE_SCHUR;
    options.use_inner_iterations = true;
    options.max_num_iterations = 100;
    options.minimizer_progress_to_stdout = true;

    // solve
    ceres::Solver::Summary summary;
    ceres::Solve(options, &problem, &summary);
    std::cout << summary.BriefReport() << std::endl;
    
    // copy intrinsics and extrinsics back
    unpack_camera_extrinsics(graph, extrinsics);
    unpack_camera_intrinsics(graph, intrinsics);
    unpack_3d_pts(graph, pts3d);
}
```
