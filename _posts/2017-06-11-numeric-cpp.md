---
title: C++ implementation of a linear algebra class
categories: 
  - Dev
---

One of the most widely used matrix and linear algebra library is [Eigen](http://eigen.tuxfamily.org). This is a header only library, thus no compiling or installation needed. I decided to write a wrapper class based on this library, so here it is.

```c++
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