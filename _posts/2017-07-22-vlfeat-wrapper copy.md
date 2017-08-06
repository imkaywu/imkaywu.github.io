---
title: VLFeat wrapper
categories: 
  - Research
  - Dev
tag:
  - Computer Vision
---

The keypoint detector and descriptor classes implemented are a wrapper of the amazing [vlfeat](http://www.vlfeat.org) functionalities. There are three separated classes, they are:

* Keypoint
	* member variables: coords_, scale_, orientation_, index_, color_, keypoint_type_
	* member methods: setters/getters
* Detector
	* detect_keypoints
* Descriptor
	* extract_descriptor;

The specific keypoint class is the subclass of both `Detector` and `Descriptor`.

### Results
<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sift_pgm.pgm" alt="sift detector" title="sift detector"/>
    <img class="col two" src="/assets/img/open3DCV/sift_jpg.jpg" alt="sift detector" title="sift detector"/>
</div>
<div class="col three caption">
    Detection results returned by the implemented C++ wrapper
</div>

<div class="img_row">
    <img class="col one" src="/assets/img/open3DCV/sift_pgm_mat.pgm" alt="sift detector" title="sift detector"/>
    <img class="col two" src="/assets/img/open3DCV/sift_jpg_mat.jpg" alt="sift detector" title="sift detector"/>
</div>
<div class="col three caption">
    Detection results returned by the SIFT Mex file
</div>

### `Keypoint` class: header file
The header file of the `Keypoint` class
```
#ifndef keypoint_h
#define keypoint_h

#include "numeric.h"

namespace open3DCV {
    //! Keypoint class
    /*!
     * This class encapsulates the data associated with a 2D image keypoint.
     * This class stores the x and y coordinates of the image location, along
     * with the index (i) of the image (camera) it is found, and color value
     * (stored in a 0-255 format).
     */
    enum KeypointType
    {
        INVALID = -1,
        DOG = 0,
        HARRIS = 1,
        SIFT = 2,
        SURF = 3,
    };
    
    class Keypoint {
    public:
        Keypoint(const Vec2 &r_x, KeypointType &r_t);
        Keypoint(const Vec2 &r_x, KeypointType &r_t, unsigned int r_i);
        Keypoint(const Vec2 &r_x, KeypointType &r_t, unsigned int r_i, const Vec3i &r_c);
        virtual ~Keypoint() { };
        
        const Vec2 &coords() const;
        void coords(const Vec2 r_coords);
        const unsigned int index() const;
        void index(const unsigned int r_i);
        const Vec3i &color() const;
        void color(const Vec3i r_c);
        const double scale() const;
        void scale(const double r_s);
        const double orientation() const;
        void orientation(const double r_o);

    private:
        Vec2 coords_;                   // coordinates
        unsigned int index_;            // Image index
        Vec3i color_;                   // color
        KeypointType keypoint_type_;
        double scale_;
        double orientation_;
        
    };
    // inline code
}
#endif // keypoint_h

```

### `Detector` class: header file
The header file of the `Detector`
```
#ifndef detector_h_
#define detector_h_

#include "image/image.h"
#include "keypoint/keypoint.h"

namespace open3DCV {
    class Detector {
    public:
        Detector();
        virtual ~Detector();
        
        virtual int detect_keypoints(const Image& image, vector<Keypoint>& keypoints);
    };
} // namespace open3DCV

#endif
```

### `Descriptor` class: header file
```

```

### `Sift` detector class: header file
The header file
```
#ifndef sift_h_
#define sift_h_

#include "vl/sift.h"
#include "keypoint/keypoint.h"
#include "keypoint/detector.h"
#include "keypoint/descriptor.h"

namespace open3DCV {

class Sift_Params {
    
};

class Sift : public Detector, public Descriptor {
    
public:
    
    Sift() { type_ = SIFT };
    ~Sift() { delete data_; };
    
    int convert(Image &img);
    int detect_keypoints(const Image &image, vector<Keypoint> &keypoints, int verbose);
    int extract_descript();
    void transpose_descriptor (vl_sift_pix* dst, vl_sift_pix* src);
    static bool ksort(const Keypoint &a, const Keypoint &b);
    vl_bool check_sorted(const vector<Keypoint> &keys, vl_size nkeys);
    
private:
    vl_sift_pix* data_;
    int width_, height_, channel_;
    KeypointType type_;
    
}; // end of class Sift

} // end of namespace open3DCV

#endif // sift_h

```

### `Sift` detector class: implementation file
```
int Sift::detect_keypoints_simp(Image &image, vector<Keypoint> &keypoints, int verbose)
{
    // convert image
    convert(image);
    
    // sift setting
    int O = 3;
    int S = 3;
    int o_min = 0;
    double edge_thresh = 10;
    double peak_thresh = 0;
    double norm_thresh = -INFINITY;
    double magnif = 3;
    double window_size = 2;
    
    VlSiftFilt* filt = 0;
    
    if (filt == nullptr || (filt->width != image.width() ||
                            filt->height != image.height()))
    {
        vl_sift_delete(filt);
        filt = vl_sift_new(image.width(), image.height(),
                           O, S, o_min);
        vl_sift_set_edge_thresh(filt, edge_thresh);
        vl_sift_set_peak_thresh(filt, peak_thresh);
    }
    
    int vl_status = vl_sift_process_first_octave(filt, data_);
    
    keypoints.reserve(2000);
    
    while (vl_status != VL_ERR_EOF)
    {
        vl_sift_detect(filt);
        
        const VlSiftKeypoint* vl_keypoints = vl_sift_get_keypoints(filt);
        int nkeys = vl_sift_get_nkeypoints(filt);
        
        for (int i = 0; i < nkeys; ++i)
        {
            double angles[4];
            int nangles = vl_sift_calc_keypoint_orientations(filt, angles, &vl_keypoints[i]);
            
            for (int j = 0; j < nangles; ++j)
            {
                Keypoint keypoint(Vec2(vl_keypoints[i].x + 1, vl_keypoints[i].y + 1), type_);
                keypoint.scale(vl_keypoints[i].sigma);
                keypoint.orientation(angles[j]);
                keypoints.push_back(keypoint);
            }
        }
        vl_status = vl_sift_process_next_octave(filt);
    }
    
    return 0;
}
```