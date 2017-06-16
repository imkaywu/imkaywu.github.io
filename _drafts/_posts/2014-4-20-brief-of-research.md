---
title: About Hand Gesture Recognition
tags: [Hand Gesture Recognition]
categories: blog
---

This post is a brief introduction of the solution and result of the hand gesture recognition algorithm. The implementation details are [here](http://imkaywu.com/2014/01/16/Hand-gesture-recognition.html).

###High-level features extraction

High-level features include the centoid(red circle), hand orientation(red line: general orientation, green line: accurate orientation), fingertips(red triangle), fingerroots(red star), contour pixels that include fingers(green dot)

####Gesture 2
![Gesture 2](/assets/images/gesture 2.png)

####Gesture 3
![Gesture 3](/assets/images/gesture 3.png)

####Gesture 4
![Gesture 4](/assets/images/gesture 4.png)

####Gesture 5
![Gesture 5](/assets/images/gesture 5.png)

####Gesture 6
![Gesture 6](/assets/images/gesture 6.png)

####Gesture 7
![Gesture 7](/assets/images/gesture 7.png)

####Gesture 8
![Gesture 8](/assets/images/gesture 8.png)

####Gesture 9
![Gesture 9](/assets/images/gesture 9.png)

####Gesture 10
![Gesture 10](/assets/images/gesture 10.png)

####Gesture 11
![Gesture 11](/assets/images/gesture 11.png)

####Gesture 12
![Gesture 12](/assets/images/gesture 12.png)

####Gesture 13
![Gesture 13](/assets/images/gesture 13.png)

####Gesture 14
![Gesture 14](/assets/images/gesture 14.png)

###Feature Vector for Recognition

After the extraction of high-level features, feature vector for recognition is calculated in two ways:

####Distance between the discretized contour pixels and the centroid

![Sample](/assets/images/sample_original.png)

####LCS(Local Contour Sequence)

The `ith` sample `h(i)` of the LCS of the gesture is obtained by computing the perpendicular Euclidean distance between `hi` and the chord connecting the end-points `h[i−(w−1)/2]` and `h[i−(w−1)/2]` of a window of size `w` boundary pixels (w odd) centered on hi .

![LCS](/assets/images/LCS.png)

###Result of the algorithm

![result](/assets/images/result.png)
