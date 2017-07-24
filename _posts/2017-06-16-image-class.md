---
title: C++ implementation of a image class
categories: 
  - Dev
---

The internal data structure used to store image is a vector of `unsigned char`. Here are a list of basic functionalities included in the `Image` class

* read from grey-scale or color images
    * currently supported formats include: pbm, pgm, ppm, and jpg
* write to grey-scale or color images
    * currently supported formats include: pbm, pgm, ppm, and jpg
* getter methods
    * width, height, channel
* color space conversions
    * color image to grey-scale image: rgb2grey
    * more to come
* access to intensity/color of a pixel
* plot and drawing
    * draw line
    * draw dot (denoted by `+`, or `x`)

I use the [CImg](http://cimg.eu) library for read/write `ppm`, and `jpeg` images. The additional support of `jpeg` depends on the [libjpeg](http://www.ijg.org) library. Support of `png` and `tiff` is possible, but relies on external libraries. The source code can be found in the `src/image` directory of [open3DCV]({{ site.url }}{{ site.baseurl }}/blog/2017/05/3d-vision-lib/). Below are the implementation

Here is the header file
```cpp
#ifndef open3dcv_image_h
#define open3dcv_image_h

#include <iostream>
#include <vector>
#include "Eigen/Dense"

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
    Image(const string r_name);
    virtual ~Image();
    int init(const string r_name);
    int check_format(const string r_fmt);
    void free();
    
    int read(const string r_name);
    int write(const string r_name);
    static int read_any_image(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height, int& r_channel);
    static int read_pbm(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height);
    static int write_pbm(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height);
    static int read_pgm(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height);
    static int write_pgm(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height);
    static int read_ppm(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height, int& r_channel);
    static int write_ppm(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height, int& r_channel);
    static int read_jpeg(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height, int& r_channel);
    static void write_jpeg(const string r_name, vector<unsigned char>& r_image, int& r_width, int& r_height, int& r_channel, const int flip = 0);
    
    void rgb2grey();
    int width() const;
    int height() const;
    int channel() const;

    void draw_line(Vec2i r_p1, Vec2i r_p2);
    void draw_plus(const Vec2i r_p, const int scale = 3);
    void draw_cross(const Vec2i r_p, const int scale = 3);
    
    Vector3f color(const float fx, const float fy) const;
    Vector3f color(const int ix, const int iy) const;
    
    // try to make this private
    vector<unsigned char> m_image;
    vector<unsigned char> m_gimage;
    
private:
    // 0: nothing allocated, 1: width/height allocated, 2: memory allocated;
    int alloc_;
    string name_;
    int width_;
    int height_;
    int channel_;
};
    
} // end of namespace open3DCV

#endif // end of image_hpp
```