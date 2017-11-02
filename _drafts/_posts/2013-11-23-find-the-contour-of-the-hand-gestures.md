---
title: Find the contour of the hand gestures
categories:
  - Research
tags:
  - Computer Vision
  - Hand Gesture Recognition
---

Our algorithm of hand gesture recognition begins with the detection of the hand contour and connect these points in a consecutive manner. This problem may sound intuitive and simple for human beings, when it comes to the computer, the issue is a little more complicated. Here is why, because the contour is no longer smooth as we expect it would be, possibility is high that we may run into a deadend before we reach the end point, then what we got is no long the whole but part of the hand contour. The contour detection algorithm should be able to ignore all the noise points and obtain the profile of hand.

So what are exactly these noisy points we are talking about. Well, here are an image showing one typical scenario. It's straightforward that when the computer runs into these points, it runs into a deadend.

**Image that has noisy points**

![edge_finder_unmodified]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/edge_finder_unmodified.png)

Here is my solution to this problem, it's quite clear these points have two neighboring points up and down/left and right that have different values from themselves (the points in the hand region is 1 while the points outside is 0). So we can use a variable called edge_direction to indicate where their neighboring points are (up-down/left-down). If the points in the hand have both neighboring up-down/left-down points, it's the '*deadend*' points we are looking for. The result of the algorithm is shown below.

**Image that has eliminated the noisy points**

![edge_finder_modified]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/edge_finder_modified.png)

The above is just a simple depict of the hand contour detection algorithm, I think no other words are more convincing than the code, so check out my source code.

```matlab
function edge_map=edge_finder(binary_map,row,colum)
    %-------------------------------------------------------------------------
    % find the edge pixels of the hand gestures, stored in edge_map(row * col)
    %-------------------------------------------------------------------------
    edge_map = zeros(size(binary_map));
    edge_direction = zeros(size(binary_map));
    for i = 1 : row
        j = 1;
        while(true)
            if(j == colum + 1)
                break;
            elseif(i ~= 1 && j ~= 1 && i ~= row && j ~= colum)
                if(xor(binary_map(i, j), binary_map(i, j + 1)))
                    if(edge_map(i, j + binary_map(i, j + 1)) == 1 && (edge_direction(i, j + binary_map(i, j + 1)) == 1 || edge_direction(i, j + binary_map(i, j + 1)) == 3))
                        edge_map(i, j + binary_map(i, j + 1)) = 0;
                        edge_direction(i, j + binary_map(i, j + 1)) = 0;
                        binary_map(i, j + binary_map(i, j + 1)) = 0;
                        continue;
                    else
                        edge_map(i, j + binary_map(i, j + 1)) = 1;
                        edge_direction(i, j + binary_map(i, j + 1)) = edge_direction(i, j + binary_map(i, j + 1)) + 1;
                    end
                end
                if(xor(binary_map(i, j), binary_map(i + 1, j)))
                    if(edge_map(i + binary_map(i + 1, j), j) == 1 && (edge_direction(i + binary_map(i + 1, j), j) == 2 || edge_direction(i + binary_map(i + 1, j), j) == 3))
                        edge_map(i + binary_map(i + 1, j), j) = 0;
                        binary_map(i + binary_map(i + 1, j), j) = 0;
                        if(binary_map(i + binary_map(i + 1, j), j - 1))
                            j = j - 1;
                        end
                        edge_direction(i + binary_map(i + 1, j), j) = 0;
                        continue;
                    else
                        edge_map(i + binary_map(i + 1, j), j) = 1;
                        edge_direction(i + binary_map(i + 1, j), j) = edge_direction(i + binary_map(i + 1, j), j) + 2;
                    end
                end
            else
                if(binary_map(i, j))
                   edge_map(i, j) = 1;
                end
            end
            j = j + 1;
        end
    end
    edge_pixel = zeros(1, 2);
    n = 1;
    for j = 2 : colum - 1
        if(edge_map(row, j) > 0)
            if(sum([edge_map(row, j - 1), edge_map(row - 1, j), edge_map(row, j + 1)]) == 3)
                edge_pixel(n) = j;
                n = n + 1;
            end
        end
    end
    if(find(edge_pixel, 1, 'last') == 2)
        edge_map(row, edge_pixel(1) - 1) = 0;
        edge_map(row, edge_pixel(2) + 1) = 0;
    elseif(find(edge_pixel, 1, 'last') == 1)
        if(sum(edge_map(row, 1 : edge_pixel(1) - 1)) > sum(edge_map(row, edge_pixel(1) + 1 : end)))
            edge_map(row, edge_pixel(1) + 1 : end) = 0;
        else
            edge_map(row, 1 : edge_pixel(1) - 1) = 0;
        end
    end
    
    edge_pixel = zeros(1, 2);
    n = 1;
    for i = 2 : row - 1
        if(edge_map(i, colum) > 0)
            if(sum([edge_map(i - 1, colum), edge_map(i, colum - 1), edge_map(i + 1, colum)]) == 3)
                edge_pixel(n) = i;
                n = n + 1;
            end
        end
    end
    if(find(edge_pixel, 1, 'last') == 2)
        edge_map(1 : edge_pixel(1) - 1, colum) = 0;
        edge_map(edge_pixel(2) + 1, colum) = 0;
    elseif(find(edge_pixel, 1, 'last') == 1)
        if(sum(edge_map(1 : edge_pixel(1) - 1, colum)) > sum(edge_map(edge_pixel(1) + 1 : end, colum)))
            edge_map(edge_pixel(1) + 1 : end, colum) = 0;
        else
            edge_map(1 : edge_pixel(1) - 1, colum) = 0;
        end
    end
end
```

Here is the code to connect the contour points in a consecutive manner. We first consider the points in the neighborhood regions than that in the 8-neighborhood region.

```
function [x_array, y_array]=edge_connector2(edge_map, row, colum)
    %----------------------------------------------------------------------
    % connect the contour pixels using 8-neighbor connectivity, the 
    % coordinates are stored individually in x_array, y_array
    %----------------------------------------------------------------------
    edge_map(row + 1, :) = 0;
    edge_map(:, colum + 1) = 0;
    % [y_origin, x_origin] = ind2sub(size(edge_map), min(find(edge_map)));
    [y_index, x_index] = find(edge_map == 1);
    [y_present, index] = max(y_index); % the start is the bottom left pixel
    x_present = min(x_index(index));
    x_array = zeros(sum(sum(edge_map)), 1);
    y_array = zeros(sum(sum(edge_map)), 1);
    x_array(1) = x_present;
    y_array(1) = y_present;
    i = 1;
    first_time = 1;
    while(1)
%         if(x_present == 233 && y_present == 198)
%         end
        x_present = x_array(i);
        y_present = y_array(i);
        if(first_time)
            i = i + 1;
            [x_array(i), y_array(i)] = first_detect(edge_map, x_present, y_present);
            first_time = ~first_time;
        else
            if((x_present > 1 && x_present < max(x_index) && sum(edge_map(y_present, [x_present - 1, x_present + 1])) > 0) || (x_present > 1 && edge_map(y_present, x_present - 1) == 1) || (x_present < max(x_index) && edge_map(y_present, x_present + 1) == 1))
                i = i + 1;
                y_array(i) = y_present;
                if(x_present > 1 && x_present < max(x_index) && sum(edge_map(y_present, [x_present - 1, x_present + 1])) == 2)
                    if(x_present - min(x_index) > max(x_index) - x_present)
                        x_array(i) = x_present - 1;
                    else
                        x_array(i) = x_present + 1;
                    end
                elseif(x_present > 1 && edge_map(y_present, x_present - 1) == 1)
                    x_array(i) = x_present - 1;
                else
                    x_array(i) = x_present + 1;
                end
            elseif((y_present > 1 && y_present < max(y_index) && sum(edge_map([y_present - 1 , y_present + 1], x_present)) > 0) || (y_present > 1 && edge_map(y_present - 1, x_present) == 1) || (y_present < max(y_index) && edge_map(y_present + 1, x_present) == 1))
                i = i + 1;
                x_array(i) = x_present;
                if(y_present > 1 && y_present < max(y_index) && sum(edge_map([y_present - 1 , y_present + 1], x_present)) == 2)
                    if(y_present - min(y_index) > max(y_index) - y_present)
                        y_array(i) = y_present - 1;
                    else
                        y_array(i) = y_present + 1;
                    end
                elseif(y_present > 1 && edge_map(y_present - 1, x_present) == 1)
                    y_array(i) = y_present - 1;
                else
                    y_array(i) = y_present + 1;
                end
            elseif(sum(edge_map([y_present - 1, y_present + 1], x_present - 1)) > 0)
                i = i + 1;
                x_array(i) = x_present - 1;
                if(sum(edge_map([y_present - 1, y_present + 1], x_present - 1)) == 2)
                    if(y_present - min(y_index) > max(y_index) - y_present)
                        y_array(i) = y_present - 1;
                    else
                        y_array(i) = y_present + 1;
                    end
                elseif(edge_map(y_present - 1, x_present - 1))
                    y_array(i) = y_present - 1;
                else
                    y_array(i) = y_present + 1;
                end
            elseif(sum(edge_map([y_present - 1, y_present + 1], x_present + 1)) > 0)
                i = i + 1;
                x_array(i) = x_present + 1;
                if(sum(edge_map([y_present - 1, y_present + 1], x_present + 1)) == 2)
                    if(y_present - min(y_index) > max(y_index) - y_present)
                        y_array(i) = y_present - 1;
                    else
                        y_array(i) = y_present + 1;
                    end
                elseif(edge_map(y_present - 1, x_present + 1) == 1)
                    y_array(i) = y_present - 1;
                else
                    y_array(i) = y_present + 1;
                end
            else
                edge_map(y_present, x_present) = 0;
                dist = norm([x_present - x_array(1), y_present - y_array(1)]);
                if(dist == 1 || dist == sqrt(2))%size(x_array(x_array > 0), 1) > 0.95 * sum_point
                    break;
                else
                    i = i - 1;
                    continue;
                end
            end
        end
%         hold on;
%         plot(x_present, y_present, 'r.');
        edge_map(y_present, x_present) = 0;
    end
    
    x_array(x_array == 0) = [];
    y_array(y_array == 0) = [];
end

function [x, y] = first_detect(edge_map, x_present, y_present)
    y = y_present - 1;
    if(edge_map(y_present - 1, x_present) == 1)
        x = x_present;
    elseif(edge_map(y_present - 1, x_present - 1) == 1)
        x = x_present - 1;
    elseif(edge_map(y_present - 1, x_present + 1) == 1)
        x = x_present + 1;
    end 
end

% not used now
function direction = choose_direction(edge_map, x_present, y_present) 
    n = 1;
    while(1)
    end
end
```

So this the the result of our algorithm, and we've tested it on 600+ images and we will take more pictures to test the robustness of the algorithm.
