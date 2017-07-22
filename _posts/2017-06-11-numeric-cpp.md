---
title: C++ implementation of a numeric class
categories: 
  - Dev
---

One of the most widely used matrix and linear algebra library is [Eigen](http://eigen.tuxfamily.org). This is a header only library, thus no compiling or installation needed. I decided to write a wrapper class based on this library, so here it is.

## Overview

### Data Types
* Vector: 
	* type: int, float, double;
	* dimension: $$2\times 1$$, $$3\times 1$$, $$4\times 1$$, $$6\times 1$$, $$1\times 2$$, $$1\times 3$$, $$1\times 4$$, $$1\times 6$$
* Matrix: 
	* type: int, float, double
	* dimension: $$2\times 2$$, $$3\times 3$$, $$3\times 4$$, $$4\times 4$$

#### Fixed-size vectorizable
An Eigen object is called "fixed-size vectorizable" if it has fixed size and that size is a multiple of 16 bytes. Eigen will then request 16-byte alignment for these objects, and henceforth rely on these objects being aligned so no runtime check for alignment is performed. For more info, please go to [here](https://eigen.tuxfamily.org/dox/group__TopicFixedSizeVectorizable.html).

### Operations
* addition, minus, matrix multiplication, element-wise multiplication

## Implemetation

### Headers
```cpp
#include <Eigen/Core>
```

### Data Types
```cpp
// Check MSVC
#if _WIN32 || _WIN64
  #if _WIN64
    #define ENV64BIT
  #else
    #define ENV32BIT
  #endif
#endif

// Check GCC
#if __GNUC__
  #if __x86_64__ || __ppc64__ || _LP64
    #define ENV64BIT
  #else
    #define ENV32BIT
  #endif
#endif

typedef Eigen::Vector2i Vec2i;
typedef Eigen::Vector2f Vec2f;
typedef Eigen::Vector3i Vec3i;
typedef Eigen::Vector3f Vec3f;
typedef Eigen::Vector3d Vec3;
typedef Eigen::Vector4i Vec4i;

#if defined(ENV32BIT)
	typedef Eigen::Matrix<double, 2, 1, Eigen::DontAlign> Vec2;
	typedef Eigen::Matrix<float, 4, 1, Eigen::DontAlign> Vec4f;
	typedef Eigen::Matrix<double, 4, 1, Eigen::DontAlign> Vec4;
	typedef Eigen::Matrix<double, 6, 1, Eigen::DontAlign> Vec6;

#else
	typedef Eigen::Vector2d Vec2;
	typedef Eigen::Vector4f Vec4f;
	typedef Eigen::Vector4d Vec4;
	typedef Eigen::Vector6d Vec6;
#endif

typedef Eigen::Matrix2i Mat2i;
typedef Eigen::Matrix3i Mat3i;
typedef Eigen::Matrix3f Mat3f;
typedef Eigen::Matrix3d Mat3;

#if defined(ENV32BIT)
	typedef Eigen::Matrix<float, 2, 2, Eigen::DontAlign> Mat2f;
	typedef Eigen::Matrix<double, 2, 2, Eigen::DontAlign> Mat2;
	typedef Eigen::Matrix<int, 4, 4, Eigen::DontAlign> Mat4i;
	typedef Eigen::Matrix<float, 4, 4, Eigen::DontAlign> Mat4f;
	typedef Eigen::Matrix<double, 4, 4, Eigen::DontAlign> Mat4;
	typedef Eigen::Matrix<float, 3, 4, Eigen::DontAlign> Mat34f;
	typedef Eigen::Matrix<double, 3, 4, Eigen::DontAlign> Mat34;
#else
	typedef Eigen::Matrix2f Mat2f;
	typedef Eigen::Matrix2d Mat2;
	typedef Eigen::Matrix4i Mat4i;
	typedef Eigen::Matrix4f Mat4f;
	typedef Eigen::Matrix4d Mat4;
	typedef Eigen::Matrix<float, 3, 4> Mat34f;
	typedef Eigen::Matrix<double, 3, 4> Mat34;
#endif

//-- General purpose Matrix and Vector
typedef Eigen::Matrix<unsigned int, Eigen::Dynamic, 1> Vecu;
typedef Eigen::VectorXf Vecf;
typedef Eigen::VectorXd Vec;
typedef Eigen::Matrix<unsigned int, Eigen::Dynamic, Eigen::Dynamic> Matu;
typedef Eigen::MatrixXf Matf;
typedef Eigen::MatrixXd Mat;
typedef Eigen::Matrix<double, 2, Eigen::Dynamic> Mat2X;
typedef Eigen::Matrix<double, 3, Eigen::Dynamic> Mat3X;
typedef Eigen::Matrix<double, 4, Eigen::Dynamic> Mat4X;
typedef Eigen::Matrix<double, 9, Eigen::Dynamic> Mat9X;

/// Quaternion type
typedef Eigen::Quaternion<double> Quaternion;
```

```cpp
#include <Eigen/Core>

namespace open3DCV
{
	/// 2d vector using int internal format
	typedef Eigen::Vector2i Vec2i;

	/// 2d vector using float internal format
	typedef Eigen::Vector2f Vec2f;

	/// 3d vector using double internal format
	typedef Eigen::Vector3d Vec3;
}
```