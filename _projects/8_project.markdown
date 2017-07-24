---
title: Hand Gesture Recognition
description: Research project done in Tsinghua University
img: /assets/img/project/thumbnail/hand_gesture_recognition.png
---

This is the research project I did in Tsinghua University during my senior year at college.

This is a post that gives a brief description of the algorithm of hand gesture recognition that I developed. It serves as a reminder of what has been done and what remains to be done. You can download the codes in [Github](https://github.com/imkaywu/Gesture-Recognition-High-Low-Level-Features), feel free to try out and change the code.Let me introduce our algorithm step by step.

> 1. [Find the contour points and connect them in a consecutive manner]({{ site.url }}/research/find-the-contour-of-the-hand-gestures).
> 2. [Find the centroid of hand gesture using **distance transform**.]({{ site.url }}/research/find-the-centroid-of-the-hand-gestures)  
> 3. [Find the direction of the hand gesture using the relative positions of contour points (using just Step 1).]({{ site.url }}/research/find-the-direction-of-hand-gestures)  
> 4. [Transform the hand contour (points above the centroid, which contribute more to the hand direction) into a *time-series curve*.]({{ site.url }}/research/transform-to-time-series-curve)  
> 5. [Find the initial positions of the fingertips and fingerroots (using just Step 1).]({{ site.url }}/research/find-the-fingertips-and-fingerroots)  
> 6. [Refine the positions of fingertips and fingerroots(using Step 2).]({{ site.url }}/research/find-the-fingertips-and-fingerroots)  
> 7. [Find the accurate direction of the hand gesture using the directions of fingers(using Step 2).]({{ site.url }}/research/find-the-direction-of-hand-gestures)  
> 7. [Recognition scheme 1: Use the positions of the fingertips and fingerroots for recognition.]({{ site.url }}/research/two-recognition-schemes)  
> 8. [Recognition scheme 2: Transform the contour points into the time-series curve again and take samples. For each gesture, we can obtain a feature vector. Use Machine Learning algorithm to train the data for recognition.]({{ site.url }}/research/two-recognition-schemes)

This is pretty much everything about our hand gesture recognition algorithm, the source code is constantly changing because I am still refining the algorithm. If you are interested, please go to my [Github page](https://github.com/imkaywu/) to download the [latest version](https://github.com/imkaywu/Gesture-Recognition-High-Low-Level-Features).