---
title: Camera visualization in Matlab
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
  - Computer Graphics
---
I read somewhere that to be more efficient in a long run when it comes to implementing a library, the first thing you should implement is the code that does visualization. To 3D computer vision, the single most important or common place component is probably the camera. This is what we are going to do here.

### First implementation
The first implementation comes from the SFMedu2 by Dr. Jianxiong Xiao, let's assume that we're in the camera coordinate system, and the focal length is $f$, then the coordinate of the center of projection and the four corners of the images plane is: $$(0, 0, 0)$$, $$(-\frac{w}{2}, -\frac{h}{2}, f)$$, $$(\frac{w}{2}, -\frac{h}{2}, f)$$, $$(\frac{w}{2}, \frac{h}{2}, f)$$, $$(-\frac{w}{2}, \frac{h}{2}, f)$$. Special points on the axes of the camera coordinate system are: $$(f, 0, 0)$$, $$(0, f, 0)$$, $$(0, 0, f)$$.

To get the coordinates of all these points that are in the camera coordinate system to that in the world coordinate system, we need to

$$
\begin{align}
\left[\begin{matrix} X_w \\1 \end{matrix}\right] &= \left[\begin{matrix} R_{wc} & T_{wc} \\ \vec{0}^T & 1 \end{matrix}\right]' * \left[\begin{matrix} X_c \\1 \end{matrix}\right]\\
&= \left[\begin{matrix} R_{wc}' & -R_{wc}'T_{wc} \\ \vec{0}^T & 1 \end{matrix}\right] * \left[\begin{matrix} X_c \\1 \end{matrix}\right]\\
\leftrightarrow X_w &= R_{wc}'*(X_c-T_{wc})
\end{align}
$$

<!-- The inverse transform of vector is different from that of point:

$$
l^T x = l^T P^{-1} P x = (P^{-T} l)^T \cdot (P x)
$$ -->

To get the coordinates of the axes in the world coordinate system, we need to

```matlab
V=[...
0 0 0 f -w/2  w/2 w/2 -w/2
0 0 f 0 -h/2 -h/2 h/2  h/2
0 f 0 0  f    f    f   f];

V = R'*(V-repmat(T, 1, size(V, 2)));

hold on;
plot3(V(1,[1 4]),V(2,[1 4]),V(3,[1 4]),'-r','LineWidth',lineWidth); % plot x-axis
plot3(V(1,[1 3]),V(2,[1 3]),V(3,[1 3]),'-g','LineWidth',lineWidth); % plot y-axis
plot3(V(1,[1 2]),V(2,[1 2]),V(3,[1 2]),'-b','LineWidth',lineWidth); % plot z-axis

plot3(V(1,[1 5]),V(2,[1 5]),V(3,[1 5]),'-k','LineWidth',lineWidth); % center to up-left corner
plot3(V(1,[1 6]),V(2,[1 6]),V(3,[1 6]),'-k','LineWidth',lineWidth); % center to up-right corner
plot3(V(1,[1 7]),V(2,[1 7]),V(3,[1 7]),'-k','LineWidth',lineWidth); % center to lower-right corner
plot3(V(1,[1 8]),V(2,[1 8]),V(3,[1 8]),'-k','LineWidth',lineWidth); % center to lower-left corner

plot3(V(1,[5 6 7 8 5]),V(2,[5 6 7 8 5]),V(3,[5 6 7 8 5]),'-k','LineWidth',lineWidth); % image plane
hold off;

```

### Second implementation
This implementation comes from the camera calibration toolbox implemented by Dr. Jean-Yves Bouguet. The key idea is the same, it's just the representation of the points is a bit different.

For example, the pixel coordinates are defined such that $$[0;0]$$ is the center of the upper left pixel of the image. As a result, $$[nx-1;0]$$ is center of the upper right corner pixel, $$[0;ny-1]$$ is the center of the lower left corner pixel and $$[nx-1;ny-1]$$ is the center of the lower right corner pixel where nx and ny are the width and height of the image (for the images of the first example, $$nx=640$$ and $$ny=480$$). This is in the image plane based coordinate system, so the first step is to transformed back to the camera coordinate system by

```matlab
IP = 5*dX*[1 -alpha_c 0;...
           0 1        0;...
           0 0        1]...
         *[1/fc(1) 0       0;...
           0       1/fc(2) 0;...
           0       0       1]...
         *[1 0 -cc(1);...
           0 1 -cc(2);...
           0 0 1]...
         *[0 nx-1 nx-1 0    0;...
           0 0    ny-1 ny-1 0;...
           1 1    1    1    1];
```

Notice that $$\left[\begin{matrix} 1 & -\alpha_c & 0;\\
                                   0 & 1         & 0;\\
                                   0 & 0         & 1\end{matrix}\right]$$ 
is the inverse of $$\left[\begin{matrix}1 & \alpha_c & 0;\\
                                         0 & 1        & 0;\\
                                         0 & 0        & 1\end{matrix}\right]$$.
Thus the purpose of this step is to convert from image plane coordinate system to camera coordinate system. Once this is done, the rest is the same.

```matlab
function drawCamera(K, R, t)
  alpha_c = K(1, 2);
  fc(1) = K(1, 1);
  fc(2) = K(2, 2);
  cc(1) = K(1, 3);
  cc(2) = K(2, 3);
  % for test purpose
  nx = 640;
  ny = 480;
  dX = 30;
  dY = 30;

  IP = 5*dX*[1 -alpha_c 0;0 1 0;0 0 1]*[1/fc(1) 0 0;0 1/fc(2) 0;0 0 1]*[1 0 -cc(1);0 1 -cc(2);0 0 1]*[0 nx-1 nx-1 0 0 ; 0 0 ny-1 ny-1 0;1 1 1 1 1];
  BASE = 5*dX*([0 1 0 0 0 0;0 0 0 1 0 0;0 0 0 0 0 1]);
  IP = reshape([IP;BASE(:,1)*ones(1,5);IP],3,15);

  IP = R' * (IP - t * ones(1, 5));
  BASE = R' * (IP - t * ones(1, 15));

  plot3(BASE(1,:),BASE(3,:),-BASE(2,:),'b-','linewidth',2');
  hold on;
  plot3(IP(1,:),IP(3,:),-IP(2,:),'r-','linewidth',2);
  text(BASE(1,2),BASE(3,2),-BASE(2,2),'X','HorizontalAlignment','center','FontWeight','bold');
  text(BASE(1,6),BASE(3,6),-BASE(2,6),'Z','HorizontalAlignment','center','FontWeight','bold');
  text(BASE(1,4),BASE(3,4),-BASE(2,4),'Y','HorizontalAlignment','center','FontWeight','bold');
  text(BASE(1,1),BASE(3,1),-BASE(2,1),'Left Camera','HorizontalAlignment','center','FontWeight','bold');
  hold off;
end
```