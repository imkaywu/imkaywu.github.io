---
title: C++ implementation of a image class
categories: 
  - Dev
---

The image class should be able to read/write image, get and set pixel color, and create a image pyramid to save computational and memory cost.

`image.hpp`
```cpp
#ifndef open3dcv_image_hpp
#define open3dcv_image_hpp

#include <iostream>
#include <vector>
#include "Eigen/Dense"
#include "CImg.h"

using namespace cimg_library;

using std::cerr;
using std::endl;
using std::string;
using std::vector;
using Eigen::Vector3f;
using Eigen::Vector3i;

namespace open3DCV
{
class Image
{
public:
    Image();
    virtual ~Image();
    
    virtual void init(const string name);
    
    void alloc(const int fast = 0, const int filter = 0);
    void free();
    
    static int readImage(const string file, vector<unsigned char>& image, int& width, int& height, int& channel, const int fast);
    static int readJpeg(const string file, vector<unsigned char>& image, int& width, int& height, int& channel, const int fast);
    static void writeJpeg(const string file, vector<unsigned char>& image, const int width, const int height, const int flip = 0);
    static int readPBMImage(const string file, vector<unsigned char>& image, int& width, int& height, const int fast);
    static int writePGMImage(const std::string file, const std::vector<unsigned char>& image, const int width, const int height);
    static int readPGMImage(const string file, vector<unsigned char>& image, int& width, int& height, const int fast);
    
    int getWidth(const int level) const;
    int getHeight(const int level) const;
    
    Vector3f getColor(const float fx, const float fy) const;
    Vector3f getColor(const int ix, const int iy) const;
    
    // 0: nothing allocated, 1: width/height allocated, 2: memory allocated;
    int m_alloc;
    string m_iname;
    vector<unsigned char> m_image;
    int m_width;
    int m_height;
    int m_nchanl;
    

#ifdef PMMVPS_IMAGE_GAMMA
    // gamma decoded images
    vector<float> m_dimage;
#endif
};
    
} // end of namespace open3DCV

#endif // end of image_hpp
    

```

