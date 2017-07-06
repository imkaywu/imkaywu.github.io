---
title: A skeleton code for data conversion for the mex file
categories: 
  - Dev
---

I use mostly Matlab to test my algorithms, it easy to implement and fast enough for matrix computation. However, for some computationally heavy tasks, I would normally turn to C++ for its efficiency. Thus I need an additional `mex` file so that my Matlab code can use the C++ code. To do so, most of the job required is to convert matlab data into C++ data, and I create some methods just for this purpose so that I don't need to do this all over again next time. So here it is.
