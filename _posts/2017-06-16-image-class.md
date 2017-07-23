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

```cpp
//----------------------------------------------------------------------
// Jpeg (from image.cpp)
//----------------------------------------------------------------------
struct my_error_mgr {
    struct jpeg_error_mgr pub;  /* "public" fields */
    
    jmp_buf setjmp_buffer;  /* for return to caller */
};
typedef struct my_error_mgr * my_error_ptr;

METHODDEF(void)
my_error_exit (j_common_ptr cinfo)
{
    /* cinfo->err really points to a my_error_mgr struct, so coerce pointer */
    my_error_ptr myerr = (my_error_ptr) cinfo->err;
    
    /* Always display the message. */
    /* We could postpone this until after returning, if we chose. */
    (*cinfo->err->output_message) (cinfo);
    
    /* Return control to the setjmp point */
    longjmp(myerr->setjmp_buffer, 1);
}

int Image::readJpegImage(const std::string file, vector<unsigned char>& image, int& width, int& height, int& channel) {
    if (file.substr(file.length() - 3, file.length()) != "jpg") {
        cerr << file.substr(file.length() - 3, file.length()) << endl;
        return -1;
    }
    
    // Use CImg for image loading
    cimg::imagemagick_path("/opt/local/bin/convert");
    CImg<unsigned char> cimage;
    try {
        cimage.load_jpeg(file.c_str());
        if(!cimage.is_empty()) {
            if(cimage.spectrum() != 1 && cimage.spectrum() != 3) {
                cerr << "Cannot handle this component. Component num is " << cimage.spectrum() << endl;
                return 1;
            }
            width = cimage.width();
            height = cimage.height();
            channel = cimage.spectrum();
            
            // CImg holds the image internally in a different order, so we need to reorder it here
            int i = 0;
            if (channel == 1) {
                image.resize(3 * cimage.size());
                
                for(int y = 0; y < cimage.height(); y++) {
                    for(int x = 0; x < cimage.width(); x++) {
                        image[i] = image[i + 1] = image[i + 2] = cimage(x, y, 0, 0);
                        i += 3;
                    }
                }
            }
            else if (channel == 3) {
                image.resize(cimage.size());
                
                for(int y = 0; y < cimage.height(); y++) {
                    for(int x = 0; x < cimage.width(); x++) {
                        for(int c = 0; c < cimage.spectrum(); c++) {
                            image[i] = cimage(x, y, 0, c);
                            i++;
                        }
                    }
                }
            }
        }
    }
    catch(cimg_library::CImgException& e) {
        std::cerr << "Couldn't read image " << file.c_str() << std::endl;
        return -1;
    }
    
    return 0;
}

void Image::writeJpegImage(const string filename, vector<unsigned char>& buffer, const int width, const int height, const int flip) {
    const int quality = 100;
    
    struct jpeg_compress_struct cinfo;
    struct jpeg_error_mgr jerr;
    /* More stuff */
    FILE * outfile;     /* target file */
    JSAMPROW row_pointer[1];    /* pointer to JSAMPLE row[s] */
    int row_stride;     /* physical row width in image buffer */
    
    cinfo.err = jpeg_std_error(&jerr);
    jpeg_create_compress(&cinfo);
    
    if ((outfile = fopen(filename.c_str(), "wb")) == NULL) {
        fprintf(stderr, "can't open %s\n", filename.c_str());
        exit(1);
    }
    jpeg_stdio_dest(&cinfo, outfile);
    
    cinfo.image_width = width;
    cinfo.image_height = height;
    cinfo.input_components = 3;
    cinfo.in_color_space = JCS_RGB;
    jpeg_set_defaults(&cinfo);
    jpeg_set_quality(&cinfo, quality, TRUE /* limit to baseline-JPEG values */);
    
    jpeg_start_compress(&cinfo, TRUE);
    
    row_stride = width * 3; /* JSAMPLEs per row in image_buffer */
    
    while (cinfo.next_scanline < cinfo.image_height) {
        if (flip)
            row_pointer[0] = (JSAMPROW)& buffer[(cinfo.image_height - 1 - cinfo.next_scanline) * row_stride];
        else
            row_pointer[0] = (JSAMPROW)& buffer[cinfo.next_scanline * row_stride];
        (void) jpeg_write_scanlines(&cinfo, row_pointer, 1);
    }
    jpeg_finish_compress(&cinfo);
    fclose(outfile);
    
    jpeg_destroy_compress(&cinfo);
}
;
    
    int getWidth() const;
    int getHeight() const;
    
    Vector3f getColor(const float fx, const float fy) const;
    Vector3f getColor(const int ix, const int iy) const;
    
    // 0: nothing allocated, 1: width/height allocated, 2: memory allocated;
    int m_alloc;
    string m_iname;
    vector<unsigned char> m_image;
    int m_width;
    int m_height;
    int m_nchanl;
};
    
} // end of namespace open3DCV

#endif // end of image_hpp

```

