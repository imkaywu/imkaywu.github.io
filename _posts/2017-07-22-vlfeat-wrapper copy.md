---
title: vlfeat wrapper
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

### Use case


### Implementation of `Keypoint`
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
        unsigned int index_;                // Image index
        Vec3i color_;                       // color
        KeypointType keypoint_type_;
        double scale_;
        double orientation_;
        
    };

    inline const Vec2& Keypoint::coords() const {
        return coords_;
    }
    
    inline void Keypoint::coords(const Vec2 r_coords) {
        coords_ = r_coords;
    }

    inline const unsigned int Keypoint::index() const {
        return index_;
    }
    
    inline void Keypoint::index(const unsigned int r_i) {
        index_ = r_i;
    }

    inline const Vec3i& Keypoint::color() const {
        return color_;
    }
    
    inline void Keypoint::color(const Vec3i r_c) {
        color_ = r_c;
    }
    
    inline const double Keypoint::scale() const {
        return scale_;
    }
    
    inline void Keypoint::scale(const double r_s) {
        scale_ = r_s;
    }
    
    inline const double Keypoint::orientation() const {
        return orientation_;
    }
    
    inline void Keypoint::orientation(const double r_o) {
        orientation_ = r_o;
    }
}
#endif // keypoint_h

```

### Implementation of `Detector`
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

### Implementation of `Sift`
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
    
    Sift();
    ~Sift();
    
    int convert(Image &img);
    int detect_keypoints(const Image &image, vector<Keypoint> &keypoints, int verbose);
    int extract_descript();
    void transpose_descriptor (vl_sift_pix* dst, vl_sift_pix* src);
    static bool ksort(const Keypoint &a, const Keypoint &b);
    vl_bool check_sorted(const vector<Keypoint> &keys, vl_size nkeys);
    
private:
    vl_sift_pix* data_;
    int width_, height_, channel_;
    
}; // end of class Sift

} // end of namespace open3DCV

#endif // sift_h

```

