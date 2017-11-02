---
title: Find the direction of hand
categories:
  - Research
tags:
  - Computer Vision
  - Hand Gesture Recognition
---

After detecting the contour of the hand gestures, we need to find the direction(also called as orientation or slope) of the hand in order to make the feature vector we extracted later be rotation invariant.

### Step 1

Intuitively, the contour of hand represent the direction of the hand (though this is not accurate, it's suffice right now), so we can check the neighboring contour vertex and their relative positions, then calculate the direction of hand using this information.

I decide to predefine 16 different directions and refer to the image for visualization :

**16 different directions between neighboring contour points**

![16_directions]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/16_directions.png)

```matlab
function direction_angle=direction_detector1(x_array,y_array)
	%----------------------------------------------------------------------
	% find the orientation of the hand gestures using orientation histogram
	% of the contour pixels
	%----------------------------------------------------------------------
	direction=zeros(1, 17);
	direction=zeros(1,17);
	for i=1:size(x_array,2);
	    index1=i;
	    if(i<size(x_array,2)-2)
	        index2=i+3;
	    elseif(i<size(x_array,2)-1)
	        index2=1;
	    elseif(i<size(x_array,2))
	        index2=2;
	    else
	        index2=3;
	    end
	    if(x_array(index2)~=x_array(index1))
	        k=(y_array(index2)-y_array(index1))/(x_array(index2)-x_array(index1));
	        switch k
	            case 0
	                if(x_array(index2)>x_array(index1))
	                    direction(1)=direction(1)+1;
	                else
	                    direction(17)=direction(17)+1;
	                end
	            case -1/3
	                direction(2)=direction(2)+1;
	            case -1/2
	                direction(3)=direction(3)+1;
	            case -2/3
	                direction(4)=direction(4)+1;
	            case -1
	                direction(5)=direction(5)+1;
	            case -3/2
	                direction(6)=direction(6)+1;
	            case -2
	                direction(7)=direction(7)+1;
	            case -3
	                direction(8)=direction(8)+1;
	                
	            case 3
	                direction(10)=direction(10)+1;
	            case 2
	                direction(11)=direction(11)+1;
	            case 3/2
	                direction(12)=direction(12)+1;
	            case 1
	                direction(13)=direction(13)+1;
	            case 2/3
	                direction(14)=direction(14)+1;
	            case 1/2
	                direction(15)=direction(15)+1;
	            case 1/3
	                direction(16)=direction(16)+1;
	        end
	    else
	        direction(9)=direction(9)+1;
	    end
	end

	angle=-[atan2(0,1),atan2(-1,3),atan2(-1,2),atan2(-2,3),atan2(-1,1),atan2(-3,2),atan2(-2,1),atan2(-3,1),atan2(-1,-0),atan2(-3,-1),atan2(-2,-1),atan2(-3,-2),atan2(-1,-1),atan2(-2,-3),atan2(-1,-2),atan2(-1,-3)]/pi*180;
	direction_temp=zeros(1,15);
	direction([1,17])=[];
	angle(1)=[];
	[a,index]=max(direction);
	for i=1:15
	    direction_temp(i)=i-index;
	end
	direction=direction/sum(direction);
	direction=direction.*direction_temp;
	direction=sum(direction);
	index=find(direction_temp==0);
	direction_fix=fix(direction);
	if(direction<0)
	    direction_angle=angle(index+direction_fix)-abs(direction-direction_fix)*(angle(index+direction_fix)-angle(index+direction_fix-1));
	else
	    direction_angle=abs(direction-direction_fix)*(angle(index+direction_fix+1)-angle(index+direction_fix))+angle(index+direction_fix);
	end
end
```

### Step 2

This method mentioned above is not all that accurate, especially when the hands are tilted. So I came up with something more accurate. We assume that the direction of the hand can be presented by the overall direction of the stretched fingers. So we first detect the directions of the fingers, then the direction of the hand. Besides, because it's intuitive that the contour points that are below the centroid actually contribute less to the direction of hand, and even worse have nothing to do with the direction(e.g. the contour points in the wrist). So I only use those above the centroid to calculate the directione.

> direction_hand = mean(direction_fingers);

First, I need to find out the points that are above the centroid which contribute more to hand direction with the aid of the initially detected hand direction.

```matlab
function [start_index, end_index] = start_end_points(x_array, y_array, x_center, y_center, direction_angle, start_degree, end_degree)
    %----------------------------------------------------------------------
    % find the index of the start and end contour point. the contour points
    % between the two points contribute most to the hand orientation
    %
    % vairable:
    %  - start_degree, end_degree: 边缘上的起、终点和中心连线和手势方向夹角
    %----------------------------------------------------------------------
    flag1 = 1;
    flag2 = 1;
    direction_angle1 = atan2((y_array - y_center), (x_array - x_center)) / pi * 180;
    direction_angle1(direction_angle1 > 0) = 360 - direction_angle1(direction_angle1 > 0);    % -180~+180=>0~360
    direction_angle1(direction_angle1 < 0) = -direction_angle1(direction_angle1 < 0);
    direction_angle2 = direction_angle1;
    if(direction_angle < 90)
        direction_angle1 = direction_angle + 360 - end_degree - direction_angle1;   %end point,-90+360
    else
        direction_angle1 = direction_angle1 - direction_angle + end_degree;
    end
    direction_angle2 = direction_angle2 - direction_angle - start_degree;    %start point
    
    while(true)
        [a, end_index] = min(abs(direction_angle1));
        [a, start_index] = min(abs(direction_angle2));
        
%         hold on;
%         plot(x_center, y_center, 'ro');
%         plot(x_array([start_index, end_index]), y_array([start_index, end_index]), 'ro');
%         plot(x_array([start_index, end_index]), y_array([start_index, end_index]));
%         row = 640;
%         y_line = 1 : row;
%         x_line(1 : y_center) = x_center + round((y_center - (1 : y_center)) ./ tand(direction_angle));
%         x_line(y_center + 1 : row) = x_center - round(((y_center + 1 : row) - y_center) ./ tand(direction_angle));
%         plot(x_line, y_line, 'r');
        
        if(x_array(end_index) > x_center)   %abs(y_center-y_array(end_index)<1.5*abs(y_center-y_array(start_index))is for gesture2 image13,14, is contradictory to gesture6 image12
            flag1 = 0;
        else
            direction_angle1(end_index) = 360;
        end
        if(x_array(start_index) < x_center)
            flag2 = 0;
        else
            direction_angle2(start_index) = 360;
        end
        if(~(flag1 || flag2))
            break;
        end
    end
end
```

Then, I transform the hand contour to a [time-series graph]({{ site.url }}{{ site.baseurl }}/research/transform-to-time-series-curve) to [find the fingertip and fingerroots]({{ site.url }}{{ site.baseurl }}/research/find-the-fingertips-and-fingerroots). These positions of fingertips and fingerroots(two points located at the root of each finger) are the key to find out the accurate positions of each finger, and we assume that the directions of the fingers represent the direction of the hand, thus the direction detected using this method is much more accurate and reliable.

Here is the code to find the accurate direction of fingers and hand and the image is shown below.

**Direction of each finger**

![finger_directions]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/finger_directions.png)

```matlab
%% ----detect the direction again---- %%

function direction_angle=direction_detector3(x_array,y_array,fingertip,fingerroots)
	direction_angle=0;
	for i=1:2
		if(i==1)
		    direction_temp=atan2(y_array(fingertip)-y_array(fingerroots(1)),x_array(fingertip)-x_array(fingerroots(1)))/pi*180;
		else
		    direction_temp=atan2(y_array(fingertip)-y_array(fingerroots(2)),x_array(fingertip)-x_array(fingerroots(2)))/pi*180;
		end
		direction_temp=(direction_temp<=0)*abs(direction_temp)+(direction_temp>0)*(360-direction_temp);
		direction_temp=(direction_temp>270)*(direction_temp-360)+(direction_temp<270)*direction_temp;
		direction_angle=direction_angle+direction_temp;
	end
	direction_angle=direction_angle/2;
end
```

After finding out the accurate direction of the hand, we need to locate the reference point in the image, the reference point is perpendicular to the hand direction. In this way, the degree between the fingertips(or fingerroots) and the reference point relative to the centroid is scale and orientation invariant. This is the key to robust recognition result.

```matlab
function [x_refer,y_refer,index]=refer_point(x_array,y_array,x_center,y_center,direction_angle)
	direction_angle1=atan2((y_array-y_center),(x_array-x_center))/pi*180;
	direction_angle1(direction_angle1>0)=360-direction_angle1(direction_angle1>0);    % -180~+180=>0~360
	direction_angle1(direction_angle1<0)=-direction_angle1(direction_angle1<0);
	direction_angle1=direction_angle1-direction_angle-180;%this may have problems
	while(true)
		[a,index]=min(abs(direction_angle1));
		x_refer=x_array(index);
		y_refer=y_array(index);
		if(y_refer>y_center)
		    break;
		end
		direction_angle1(index)=360;
	end
end
```