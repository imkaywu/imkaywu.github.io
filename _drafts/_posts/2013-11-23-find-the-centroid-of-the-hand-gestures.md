---
title: Find the centroid of the hand gestures
categories:
  - Research
tags:
  - Computer Vision
  - Hand Gesture Recognition
---

For the next step, we need to find out the centroid of the hand. We employ two methods to this end, a simple one using all the points in the hand region, and another one called **Distance Transform**.

Because we know the points in the hand region have value 1 while others are 0. So we can easily use the points in the hand to calculate the centroid. Here is the code.

```matlab
function [x_center,y_center]=center_finder(edge_map,binary_map,x_array,y_array)
    %% ----find the center point, solution 1---- %%
    [y_binary,x_binary]=ind2sub(size(binary_map),find(binary_map));
    x_center=round(mean(x_binary));
    y_center=round(mean(y_binary));
end
```

Another method is called the **Distance Transform**. You can refer to my other post to check out concept or the algorithm I developed. Here is the code of centroid finger using **Distance Transform**

```matlab
function [x_center,y_center]=center_finder(edge_map,binary_map,x_array,y_array)
    %% ----find the center point, solution 2---- %%
    max_distance=0;
    dist_trans_map=bwdist(edge_map);
    % dist_trans_map1=dist_trans(edge_map);    %distance transform I implement
    dist_trans_map(binary_map==0)=0;
    x_left=min(x_array(x_array~=0));
    x_right=max(x_array);
    y_up=min(y_array(y_array~=0));
    y_bottom=max(y_array);
    for i=x_left:x_right
        for j=y_up:y_bottom
            if(dist_trans_map(j,i)>max_distance)
                x_center=i;
                y_center=j;
                max_distance=dist_trans_map(j,i);
            end
        end
    end
end
```

After testing on more than 600+ images we decide to choose the latter method.
