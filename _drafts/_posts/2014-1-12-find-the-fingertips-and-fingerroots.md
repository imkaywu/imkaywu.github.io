---
title: Find the position of fingertips and fingervalleys
categories:
  - Research
tags:
  - Computer Vision
  - Hand Gesture Recognition

---
### Step 1
After obtaining the *time-series curve*, we can find the positions of the fingertips and fingerroots.

The fingertips are definded as the locally highest point and the fingervalleys as locally lowest ones. The result of the initial detection of fingertips and fingervalleys is shown below.

**The initial detection of fingertips and fingervalleys**

![initially detected fingertips and fingervalleys]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/fingertip&fingervalley.png)

Then the valid fingertips and their corresponding fingerroots are selected from these candidate points. This is done as follows:

```
for each fingertip
	find out all the fingervalleys that are on the left and right side of this fingertip (ft), denoted as fv_l and fv_r;
	eliminate invalid fingervalleys using prior knowledge of hand geometry;
	optimal_function(ft,fv_l);
	optimal_function(ft,fv_r);
	find out the fingervalley that maximize the optimal function *optimal_function* and this is regarded as the two fingerroots of this fingertip
endfor
```

The result of the algorithm is shown as follows. The optimal function I adopt is 

> optimal_function = (distance_ft - distance_fv) ./ distance_fv - 2.5 * (degree_ft - degree_fv) ./ degree_fv;

**The modified detection of fingertips and fingervalleys in both original image and time-series curve**

![detection fingertips and fingerroots]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/fingertip&fingerroot2.png)

![detection fingertips and fingerroots]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/fingertip&fingerroot1.png)
For some images, there are still some refinement needed. For instance, more than 1 detected fingertips may share one or both the fingerroot(s), so there is another optimal function needed to select one fingertip, the optimal function is as follows

> optimal_function = distance_ft - 0.5 * (distance_fr_l + distance_fr_r);

Here is the source code of the algorithm written in MATLAB

```matlab
function [fingertip, fingerroot] = finger_finder2(degree, norm_distance)
    %----------------------------------------------------------------------
    % find the fingertips and their corresponding roots of fingers
    % 
    % variable:
    %  - fingertip: n * 1 (n: fingertip number)
    %  - fingerroot: n * 2 (n: fingertip number)
    %----------------------------------------------------------------------
    j = 1;
    firstTime = 1;
    finger_edge = 1;
    length = size(degree, 1);
    fingertip = zeros(5, 1);
    fingertip_remain = zeros(5, 1);
    fingervalley = zeros(6, 1);
    fingerroot = zeros(5, 2);
    fingerroot_remain = zeros(5, 2);
    min_distance = norm_distance(1);
    max_distance = norm_distance(1);
    
    % detect all the highest and lowest points as fingertips and fingervalleys
    for i = 1 : length
        if(norm_distance(i) < min_distance)
            min_distance = norm_distance(i);
            max_distance = norm_distance(i);
            if(min_distance < norm_distance(finger_edge))
                finger_edge = i;
            end
        elseif(norm_distance(i) < max_distance)
            fingertip(j) = i - 1;
            fingervalley(j) = finger_edge;
            j = j + 1;
            firstTime = ~firstTime;
            min_distance = norm_distance(i);
            max_distance = norm_distance(i);
        elseif(norm_distance(i) > max_distance)
            if(firstTime)
                finger_edge = i - 1;
                firstTime = ~firstTime;
            end
            max_distance = norm_distance(i);
        end
    end
    
    % eliminate invalid fingertips and fingervalleys
    j = 1;
    fingertip_length = find(fingertip, 1, 'last');
    fingervalley(fingertip_length + 1) = size(degree, 1);
    for i = 1 : fingertip_length
        index_left = i;
        index_right = i + 1;
%         if(i == 7)
%         end
        if(sum(fingertip_remain) == 0 || max(norm_distance(fingertip(fingertip_remain(1 : find(fingertip_remain, 1, 'last'))))) < norm_distance(fingertip(i)))
            fv_left_all = fingervalley(1 : index_left);
        else
            [ft_highest, ft_highest_index] = max(norm_distance(fingertip(fingertip_remain(1 : find(fingertip_remain, 1, 'last')))));
            ft_highest_index = fingertip_remain(ft_highest_index);
            fv_left_all = fingervalley(ft_highest_index + 1 : index_left);
        end
        fv_right_all = fingervalley(index_right : fingertip_length + 1);
%         fv_left_all(norm_distance(fv_left_all) > 1.85) = [];
%         fv_right_all(norm_distance(fv_right_all) > 1.85) = [];
        fv_left_index = (norm_distance(fingertip(i)) - norm_distance(fv_left_all)) ./ norm_distance(fv_left_all) > 0.49;
        fv_right_index = (norm_distance(fingertip(i)) - norm_distance(fv_right_all)) ./ norm_distance(fv_right_all) > 0.49;
        fv_left_all = fv_left_all(fv_left_index);
        fv_right_all = fv_right_all(fv_right_index);
        
        if(sum(fv_left_index) ~= 0 && size(fv_left_all, 1) > 1)
            optim_func_left = (norm_distance(fingertip(i)) - norm_distance(fv_left_all)) ./ norm_distance(fv_left_all) - 2.5 * (degree(fingertip(i)) - degree(fv_left_all)) ./ degree(fv_left_all);
            [maxVal, maxInd] = max(optim_func_left);
            fv_left_all = fv_left_all(maxInd);
        end

        if(sum(fv_right_index) ~= 0 && size(fv_right_index, 1) > 1)
            optim_func_right = (norm_distance(fingertip(i)) - norm_distance(fv_right_index)) ./ norm_distance(fv_right_index) - 2.5 * (degree(fv_right_index) - degree(fingertip(i))) ./ degree(fv_right_index);
            [maxVal, maxInd] = max(optim_func_right);
            fv_right_all = fv_right_all(maxInd);
        end
        
        if(sum(fv_left_index) == 0 || sum(fv_right_index) == 0)
            continue;
        else
            fingertip_remain(j) = i;
            fingerroot_remain(j, :) = [fv_left_all, fv_right_all];
            j = j + 1;
        end
    end
    index = find(fingertip_remain == 0);
    fingertip_remain(index) = [];
    fingertip_remain = fingertip(fingertip_remain);
    fingerroot_remain(index, :) = [];
    
    % choose one fingertip among >= 2 fingertips that have overlapped fingervalleys, an example: 
    % {fingertip: 100, fingervalley: (50, 150)}, {fingertip: 200, fingervalley: (120, 250)}
    n = 1;
    fingertip_remove = zeros(1,size(fingertip_remain,1));
    for i = 1 : size(fingertip_remain, 1) - 1
        for j = i + 1 : size(fingertip_remain, 1)
            if(fingerroot_remain(i, 1) >= fingerroot_remain(j, 1) || fingerroot_remain(i, 2) > fingerroot_remain(j, 1))    % between the two numbers
                if(sum((norm_distance(fingertip_remain(i)) - norm_distance(fingerroot_remain(i, :))) ./ norm_distance(fingerroot_remain(i, :))) > sum((norm_distance(fingertip_remain(j)) - norm_distance(fingerroot_remain(j, :))) ./ norm_distance(fingerroot_remain(j, :))))
                    fingertip_remove(n) = j;
                else
                    fingertip_remove(n) = i;
                end
                n = n + 1;
            end
        end
    end
    fingertip_remove(fingertip_remove == 0) = [];
    fingertip_remove = unique(fingertip_remove);
    fingertip_remain(fingertip_remove) = [];
%     fingertip_remain = unique(fingertip_remain);
    fingerroot_remain(fingertip_remove, :) = [];
    
    % choose the fingertip among >= 2 fingertips that have the same fingervalleys, an example:
    % {fingertip: 100, fingervalley: (50, 150)}, {fingertip: 120, fingervalley: (80, 150)}
    fingervalley_cato = zeros(size(fingertip_remain, 1));
    flag=zeros(1, size(fingertip_remain, 1));
    for i = 1 : size(fingertip_remain, 1) - 1
        for j = i + 1 : size(fingertip_remain, 1)
            if(sum(fingerroot_remain(i, :) == fingerroot_remain(j, :)) && flag(j) == 0)
                fingervalley_cato(i, [2 * (j - i) - 1, 2 * (j - i)]) = [i, j];
                flag([i, j]) = 1;
            end
        end
    end
    
    n = 1;
    fingertip = zeros(5, 1);
    if(size(fingertip_remain, 1) ~= 1 && sum(sum(fingervalley_cato)) ~= 0)
        for i = 1 : size(fingervalley_cato, 1)
            fingervalley_cato_temp = fingervalley_cato(i, :);
            if(sum(fingervalley_cato_temp) ~= 0)
                fingertip_index = unique(fingervalley_cato_temp(fingervalley_cato_temp ~= 0));
                optim_func = zeros(size(fingertip_index, 2), 1);
                for j = 1 : size(fingertip_index, 2)
                    optim_func(j) = norm_distance(fingertip_remain(fingertip_index(j))) - mean(norm_distance(fingerroot_remain(fingertip_index(j), :)));
                end
                [~, fingertip_pos] = max(optim_func);
                fingertip_pos = fingertip_index(fingertip_pos);
                fingertip(n) = fingertip_remain(fingertip_pos);
                fingerroot(n, :) = fingerroot_remain(fingertip_pos, :);
                n = n + 1;
            elseif(flag(i) == 0)
                fingertip(n) = fingertip_remain(i);
                fingerroot(n, :) = fingerroot_remain(i, :);
                n = n + 1;
            end
        end
    else
        fingertip = fingertip_remain;
        fingerroot = fingerroot_remain;
    end
    
    finger_remove = fingertip == 0;
    fingertip(finger_remove) = [];
    fingerroot(finger_remove, :) = [];
    [fingertip, ft_index] = sort(fingertip);
    fingerroot = fingerroot(ft_index, :);
    
    % used to solve noise peak in gesture4 image7
    if(find(fingertip, 1, 'last') > 1 && (degree(fingerroot(1, 2)) - degree(fingerroot(1, 1))) / (norm_distance(fingertip(1)) - mean(norm_distance(fingerroot(1, :)))) > 0.3)
        fingertip(1) = [];
        fingerroot(1, :) = [];
    end
    
%     figure;
%     plot(degree, norm_distance);
%     hold on;
%     plot(degree(fingertip), norm_distance(fingertip), 'r^');
%     plot(degree(fingerroot), norm_distance(fingerroot), 'ro');
end
```

After the fingertips and fingerroots are found, they are still in need of some adjustment in order to achievement the best performance. Therefore Step 2 is employed to further refine the positions of fingertips and fingerroots.

### Step 2

It's intuitive to us that if we draw a external rectangle to each finger, the ratio of the area of the finger and the rectangular area should surpass a certain threshold. So this is the main idea of the algorithm to precisely locate the positions of the fingerroots.

> area_ratio = area_finger / area_external_rec;

First, with the positions of the fingertip and fingerroots, we can know the direction of the finger, which is obtained as follows

> finger_direction = 0.5 * (direction_ft_fr_l + direction_ft_fr_r);

Then we find the mirror point for each of the fingerroot(the line crosses one fingerroot and its mirror point is perpendicular to finger direction). And whichever combination(fingerroot and its mirror point) is closer to the fingertip, we treat them as the updated fingerroots. Then we calculate the *area_ratio*. This process repeats until the *area_ratio* surpass a certain threshold. In our code, I use 0.65 as the threshold.

Here is the refined positions of fingertips and fingerroot, as you can see the differences when compared to the initial ones above. Source code is as follows.

**The refined detection of fingertips and fingervalleys**

![refined detection fingertips and fingervalleys]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/fingertip&fingerroot_refined.png)

```matlab
function [fingerroot, finger_direction] = fingerroot_detector(x_array, y_array, fingertip, fingerroot_init)
    %------------------------------------------------------------------------
    % calculate the accurate positions of fingerroots using the ratio between 
    % the area of the finger and that of the external rectangle
    %
    % function:
    %  - direction_detector3: use the positions of fingertip and fingerroots
    %                         to determine the direction of finger
    %  - fingerroot_update: update the positions of fingerroots using the
    %                       newly detected direction
    %------------------------------------------------------------------------
    fingertip_length = size(fingertip, 1);
    fingerroot = zeros(1, 2 * fingertip_length);
    finger_direction = zeros(1, fingertip_length);
    for i = 1 : fingertip_length
        P = [y_array(fingertip(i)), x_array(fingertip(i))];
        fingerroot_start = fingerroot_init(i, 1);
        fingerroot_end = fingerroot_init(i, 2);
        while(true)
            direction_angle = direction_detector3(x_array, y_array, fingertip(i), [fingerroot_start, fingerroot_end]);
            [fingerroot_start, fingerroot_end] = fingerroot_update(x_array, y_array, fingertip(i), [fingerroot_start, fingerroot_end], direction_angle);
            %calculate the area of the external rectangle
            Q = [P(1) + 10, P(2) - 10 / tand(direction_angle)];
            n = 1;
            dist = zeros(1, fingerroot_end - fingerroot_start + 1);
            for j = fingerroot_start : fingerroot_end
                dist(n) = abs(det([P - Q; [y_array(j), x_array(j)] - Q])) / norm(P - Q);
                n = n + 1;
            end
            fingerroot1 = [y_array(fingerroot_start), x_array(fingerroot_start)];
            fingerroot2 = [y_array(fingerroot_end), x_array(fingerroot_end)];
            length = max(dist(1 : fingertip(i) - fingerroot_start + 1)) + max(dist(fingertip(i) - fingerroot_start + 1 : fingerroot_end - fingerroot_start + 1));
            height = abs(det([fingerroot2 - fingerroot1; P - fingerroot1])) / norm(fingerroot2 - fingerroot1);
            area = length * height;
            %calculate the area of the finger
            finger_area = 0;
            finger_top = min(y_array(fingerroot_start : fingerroot_end));
            up = min(y_array([fingerroot_start, fingerroot_end]));
            low = max(y_array([fingerroot_start, fingerroot_end]));
            for j = finger_top : 1 : low
                if(j <= up)
                    x = x_array(fingerroot_start + find(j == y_array(fingerroot_start : fingerroot_end)) - 1);
                    finger_area = finger_area + max(x) - min(x) + 1;
                else
                    if(up == y_array(fingerroot_end))
                        x = x_array(fingerroot_end) + (x_array(fingerroot_end) - x_array(fingerroot_start)) / (up - low) * (j - up);
                        finger_area = finger_area + x - min(x_array(fingerroot_start + find(j == y_array(fingerroot_start : fingerroot_end)) - 1)) + 1;
                    else
                        x = x_array(fingerroot_start) + (x_array(fingerroot_start) - x_array(fingerroot_end)) / (up - low) * (j - up);
                        finger_area = finger_area + max(x_array(fingerroot_start + find(j == y_array(fingerroot_start : fingerroot_end)) - 1)) - x + 1;
                    end
                end
            end
%             imshow(edge_map);
%             hold on;
%             x_line1 = zeros(1, row);
%             y_line = 1 : row;
%             x_line1(1 : y_array(fingertip(i))) = x_array(fingertip(i)) + round((y_array(fingertip(i)) - (1 : y_array(fingertip(i)))) ./ tand(direction_angle));
%             x_line1(y_array(fingertip(i)) + 1 : row) = x_array(fingertip(i)) - round(((y_array(fingertip(i)) + 1 : row) - y_array(fingertip(i))) ./ tand(direction_angle));
%             plot(x_line1, y_line);
%             plot([x_array(fingerroot_start), x_array(fingerroot_end)], [y_array(fingerroot_start), y_array(fingerroot_end)], 'ro');
%             plot([x_array(fingerroot_start), x_array(fingerroot_end)], [y_array(fingerroot_start), y_array(fingerroot_end)], 'r-');
            
            area_percent = finger_area / area;
            if(area_percent > 0.65 || (fingerroot_start > fingertip(i) || fingerroot_end < fingertip(i)))
                fingerroot([2 * i - 1, 2 * i]) = [fingerroot_start, fingerroot_end];
                finger_direction(i) = direction_angle;
                break;
            end
        end
    end
end
```

```matlab
function [fingeredge_start, fingeredge_end] = fingerroot_update(x_array, y_array, fingertip, fingerroot, direction_angle)
    %----------------------------------------------------------------------
    % update the positions of fingerroots using the newly detected finger
    % direction
    %----------------------------------------------------------------------
    for i = 1 : 2
        if(i == 1)    %左边的点
            edge_degree = atan2(y_array(fingertip : fingerroot(2)) - y_array(fingerroot(1)), x_array(fingertip : fingerroot(2)) - x_array(fingerroot(1))) / pi * 180;
            edge_degree(edge_degree > 0) = 360 - edge_degree(edge_degree > 0);
            edge_degree(edge_degree < 0) = -edge_degree(edge_degree < 0);
            edge_degree(edge_degree > 180) = edge_degree(edge_degree > 180) - 360;
        else    %右边的点
            edge_degree1 = atan2(y_array(fingerroot(1) : fingertip(1)) - y_array(fingerroot(2)), x_array(fingerroot(1) : fingertip(1)) - x_array(fingerroot(2))) / pi * 180;
            edge_degree1(edge_degree1 > 0) = 360 - edge_degree1(edge_degree1 > 0);
            edge_degree1(edge_degree1 < 0) = -edge_degree1(edge_degree1 < 0);
        end
        if(i == 2)
            [a, index1] = min(abs(edge_degree - direction_angle + 90));%开始选左边为起点
            [a, index2] = min(abs(edge_degree1 - direction_angle - 90));%开始选右边为终点
            if(fingertip + index1 - 1 < fingerroot(2) && index2 == 1)
                fingeredge_start = fingerroot(1);
                fingeredge_end = fingertip + index1 - 1;
            elseif(index2 > 1 && fingertip + index1 - 1 == fingerroot(2))
                fingeredge_start = fingerroot(1) + index2 - 1;
                fingeredge_end = fingerroot(2);
            else
                fingeredge_start = fingerroot(1) + 1;
                fingeredge_end = fingerroot(2) - 1;
            end
%             if(min(edge_degree) <= 0 && max(edge_degree1) <= 180)%选左边
%                 [a, index1] = min(abs(edge_degree - direction(index) / 2 + 90));
%                 fingeredge_start = fingerroot(1);
%                 fingeredge_end = start_index + fingertip + index1 - 2;
%             else
%                 [a, index1] = min(abs(edge_degree1 - direction(index) / 2 - 90));%选右边
%                 fingeredge_start = fingerroot(1) + index1 - 1;
%                 fingeredge_end = fingerroot(2);
%             end
        end
    end
end
```