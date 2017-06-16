---
title: Transform to Time-series curve
categories:
  - Research
tags:
  - Computer Vision
  - Hand Gesture Recognition
---

After obtaining the contour vertex and the direction of hand, we can transform the contour vertex into a *time-series curve*. The *time-series curve* records the relative distance between each contour vertex to the centroid starting from the reference point(a relatively fixed point in hand gestures). The horizontal axis denotes the angle between each contour vertex and the reference point relative to the centroid, normalized by 360. The vertical axis denotes the Euclidean distance between the contour vertices and the centroid, normalized by the radius of the inscribed circle. Here is how it's done.

```matlab
function [degree, norm_distance] = trans_graph1(x_array, y_array, x_center, y_center, norm_on_off)
    %----------------------------------------------------------------------
    % transform the original gesture into the time-series curve
    %----------------------------------------------------------------------
    length = find(x_array, 1, 'last');
    radius = atan2(y_array(1 : length) - y_center, x_array(1 : length) - x_center);
    degree = radius ./ pi * 180;
    degree(degree > 0) = 360 - degree(degree > 0);    % -180~+180=>0~360
    degree(degree < 0) = abs(degree(degree < 0));
    degree = degree - degree(1);
    degree(degree > 0) = 360 - degree(degree > 0);
    degree(degree < 0) = -degree(degree < 0);
    degree = degree / 360;

    x_distance = x_array - x_center;
    y_distance = y_array - y_center;
    distance = zeros(length, 1);
    for i = 1 : length
        distance(i) = norm([x_distance(i), y_distance(i)]);
    end
    if(strcmp(norm_on_off, 'norm_on'))
        min_distance = min(distance);
    elseif(strcmp(norm_on_off, 'norm_off'))
        min_distance = 1;
    end
    norm_distance = distance ./ min_distance;
end
```

This *time-series curve* is used to find the positions of [fingertips and fingerroots]({{ site.url }}{{ site.baseurl }}/research/find-the-fingertips-and-fingerroots.html) to further determine the accurate direction of hand or serve as high-level features for recognition phase. Here are the original and transformed images of a hand gesture.

**Original Image**

![original image]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/original_image.png)

**Time-series curve**

![Time-series curve]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/time-series.png)
