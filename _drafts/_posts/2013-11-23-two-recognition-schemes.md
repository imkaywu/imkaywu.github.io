---
title: Two Recognition Schemes
categories:
  - Research
tags:
  - Computer Vision
  - Hand Gesture Recognition
---

### Scheme 1
After we obtain the accurate hand direction, we can find a relatively fixed reference point in the hand gesture. The reference point is chosen using the following rule. As is shown in the following image.

> The line crosses the reference point and the centroid is perpendicular to the hand direction.

![reference point]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/reference_point.png)

Then we can calculate the degree between the fingertips (or fingerroots) and the reference point. Because the hand direction is rotation invariant and the reference point is relatively fixed, this should suffice to recognition the gestures.

### Scheme 2
We transform the contour points above the centroid into a time-series curve again. Now this time, we don't need to find the fingertips and fingerroots, we take samples in this curve. Because the time-series is the description of the hand profile, this extracted samples also serve as a coarse description of the hand profile. See the following two images.

**Samples in the original image**

![Samples in the original image]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/sample_original.png)

**Samples in the time-series curve**

![edge_finder_unmodified]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/sample_time_series.png)

#### Logistic Regression
We then use machine learning algorithm(Logistic Regression) to train the data for recognition. The source code is as follows

```matlab
function  result_table = main_logistic_regression_hand
	times = 1;
	result_table = 0;
	for ii = 1 : times
	    [ result_table_temp , theta ]= logistic_regression_hand;
	    result_table = result_table + result_table_temp;
	    save( strcat('theta',num2str(ii),'.mat'),'theta');
	end
	result_table = result_table / times;
end

function [ result_table , theta ] = logistic_regression_hand
	%% Read in data & select data
	str = 'data\Test data\gesture';
	file_index = 2 : 14;
	[ x_all , groung_truth_gesture_index_all , same_gesture_number_all ] = read_in_distance_sample_map_data( str , file_index );
	[ y , train_set , test_set , test_index ] = data_select( x_all , same_gesture_number_all , 0.8 , 'random_on'  );
	%% Traing
	theta = zeros( size( y , 2 ) , size( train_set , 2 ) );%theta(number of gestures, number of features)
	for kk = 1 : size( y , 2 )
	    theta(kk,:) = Logistic_Regression_Gradient_Descent_Hand( y( : , kk ) , train_set);
	end
	%% Result
	test_gesture_index = groung_truth_gesture_index_all( test_index );
	test_gesture_number = zeros( length( same_gesture_number_all ) , 1 );
	init_gesture = [ 1 , 1 , 1 , 4 , 4 , 4 , 4 , 8 , 8 , 8 , 8 , 12 , 13 ];
	recognized_gesture_index = zeros( length( test_gesture_index ) ,1 );
	result_table = zeros( length( same_gesture_number_all ) , length( same_gesture_number_all ) );
	result = test_set * ( theta' );
	for ii = 1 : length( same_gesture_number_all )
	    test_gesture_number( ii ) = sum( test_gesture_index == ii );
	end
	for ii = 1 : length( same_gesture_number_all )
	    [ maxVal , maxInd ] = max( result( sum( test_gesture_number( 1 : ii-1 ) ) + 1 : sum( test_gesture_number( 1 : ii ) ) , init_gesture == init_gesture( ii ) ) , [] , 2 );
	    recognized_gesture_index( sum( test_gesture_number( 1 : ii-1 ) ) + 1 : sum( test_gesture_number( 1 : ii ) ) ) = maxInd + init_gesture( ii ) - 1;
	end
	
	for ii = 1 : length( recognized_gesture_index )
	    result_table( recognized_gesture_index(ii) , test_gesture_index( ii ) ) = result_table( recognized_gesture_index(ii) , test_gesture_index( ii ) ) + 1;
	end
	for ii = 1 : length( same_gesture_number_all )
	    result_table( : , ii ) = result_table( : , ii ) ./ sum( test_gesture_number( ii ) );
	end
end
%% Logistic Regression and Gradient Descent
function theta = Logistic_Regression_Gradient_Descent_Hand( y , x )
	epsilon = 1e-5;
	num_feature = size( x , 2 );
	theta = zeros( 1 , num_feature );
	jVal = 0;
	jVal_old = 100 * epsilon;
	while( abs( jVal_old - jVal ) >= epsilon )
	    jVal_old = jVal;
	    theta = Gradient_Descent_hand( y , x , theta );
	    jVal = y .* log( h_theta( x , theta) )+ ( 1 - y ) .* log( 1 - h_theta( x , theta) );
	    jVal = sum( jVal );
	end
end

function theta = Gradient_Descent_hand( y , x , theta )
	num_feature = size( x , 2 );
	alpha = 0.001;
	tempVal1 = y - h_theta( x , theta );
	tempVal2 = repmat( tempVal1 ,1 , num_feature );
	tempVal3 = tempVal2 .* x;
	tempVal4 = sum( tempVal3 );
	theta = theta + alpha * tempVal4;
end

function hVal = h_theta( x , theta)
	hVal = 1 ./ ( 1 + exp( - x * theta' ) );
end
%% Read in the data of each type of gesture
function [ x , gesture_index , same_gesture_number ] = read_in_distance_sample_map_data( str , file_index )
	x = []; gesture_index = []; same_gesture_number = [];
	new_gesture_index_num = 1;
	for ii = file_index
	    load( strcat( str , num2str(ii) , '_test_data') );
	    m = size(distance_sample_map,1);
	    same_gesture_number = [ same_gesture_number ; m ];
	    gesture_index = [ gesture_index ; new_gesture_index_num*ones(m,1)];
	    new_gesture_index_num = new_gesture_index_num + 1;
	    x = [ x ; distance_sample_map ]; 
	end
	clear ii jj m;
end
%% Select the training set and test set, store separately in x and z; x_all is the gesture set, gesture_number_all is the number of each gesture type, percent is the percentage of training set, CW_random=='random_on' means select the training set randomly.
function [ y , x , z ,index ] = data_select( x_all , gesture_number_all , percent , CW_random )
	dif_gesture_num = size( gesture_number_all , 1 );%Size of each gesture
	feature_num = size( x_all , 2 );
	selected_same_gesture_num = floor( percent * gesture_number_all );%Size of each gesture in the training set
	unselected_same_gesture_num = gesture_number_all - selected_same_gesture_num;%Size of each gesture in the test set
	
	y = zeros( sum( selected_same_gesture_num ) , dif_gesture_num );
	x = zeros( sum( selected_same_gesture_num ) , feature_num );
	index = zeros( sum( unselected_same_gesture_num ) , 1 );
	
	for ii = 1 : dif_gesture_num
	    if strcmp( CW_random , 'random_on' )
	        index_all_random = randperm( gesture_number_all(ii) );
	        index_select = index_all_random( 1 : selected_same_gesture_num(ii) );
	        index_unselect = index_all_random( selected_same_gesture_num(ii)+1 : end );
	    else
	        index_select = 1 : selected_same_gesture_num(ii);
	        index_unselect = selected_same_gesture_num(ii)+1 : gesture_number_all(ii);
	    end
	    
	    x( sum( selected_same_gesture_num( 1 : ii -1 ) ) + 1 : sum( selected_same_gesture_num( 1 : ii  ) ) , : ) = x_all( sum( gesture_number_all( 1 : ii -1 ) ) +  index_select , : );
	    y( sum( selected_same_gesture_num( 1 : ii -1 ) ) + 1 : sum( selected_same_gesture_num( 1 : ii  ) ) , ii ) = 1;
	    index( sum( unselected_same_gesture_num( 1 : ii -1 ) ) + 1 : sum( unselected_same_gesture_num( 1 : ii ) ) ) = sum( gesture_number_all( 1 : ii -1 ) ) + index_unselect;
	end
	z = x_all( index , : );
end
```

#### Support Vector Machine
I recently turned to SVM and thanks to the open code provided by LIBSVM, I implemented the recognition algorithm with quite convience and it achieves much better recognition  results. I used one-versus-one multi-class SVM.

```matlab
% one-versus-one
function [ accuracy_train , accuracy_test ,false_post, wrong_gesture, fal_pos_gesture ] = main_svm_classifier_v4
    tic;
    train_pct = 0.7;
    count = 5;
    accuracy_train = zeros( 13 );
    accuracy_test = zeros( 13 );
    false_post = zeros( 13 , 1 );
    wrong_gesture = [];
    fal_pos_gesture = [];
    for i = 1 : count
        [ accuracy_train_temp , accuracy_test_temp , false_post_temp, wrong_gesture_temp, fal_pos_gesture_temp ] = svm_classifier(train_pct);
        accuracy_train = accuracy_train + accuracy_train_temp;
        accuracy_test = accuracy_test + accuracy_test_temp;
        false_post = false_post + false_post_temp;
        wrong_gesture = [wrong_gesture, wrong_gesture_temp];
        fal_pos_gesture = [fal_pos_gesture, fal_pos_gesture_temp];
    end
    accuracy_train = accuracy_train / count;
    accuracy_test = accuracy_test / count;
    false_post = false_post / count;
    max_len = 0;
    for i = 1 : size(wrong_gesture, 1)
        len = size(wrong_gesture(i, wrong_gesture(i, :) > 0), 2);
        wrong_gesture(i, 1 : len) = wrong_gesture(i, wrong_gesture(i, :) > 0);
        wrong_gesture(i, len + 1 : end) = 0;
        if(len > max_len)
            max_len = len;
        end
    end
    wrong_gesture(:, max_len + 1 : end) = [];
    fal_pos_gesture(:, max_len + 1 : end) = [];
%     cd '..\data\Train data\accuracy';
    cd '..\data\projection\accuracy';
%     cd '..\data\dist_smp(50)\accuracy';
    save( strcat('accuracy_train',num2str(train_pct),'.mat'),'accuracy_train');
    save( strcat('accuracy_test',num2str(train_pct),'.mat'),'accuracy_test');
    save( strcat('false_positive',num2str(train_pct),'.mat'),'false_post');
    toc;
end
function [ accuracy_train , accuracy_test , false_post, wrong_gesture, fal_pos_gesture ] = svm_classifier(train_pct)
    %% Read in data and choose the training set
%     str = '..\data\Test data\gesture';
    str = '..\data\projection\distance_sample_map(100)\';
%     str = '..\data\projection\dist_smp(50)\normalized_min_max\';
    
    [gesture_data, gesture_num] = read_in_data(str);
    %x_all = x_all / 100;
    [train_set, test_set,train_label, test_label, test_index] = data_select(gesture_data, gesture_num, train_pct, 'random_on');   %gesture_distance_map, gesture_dist_smp
    %% Training
    cd 'E:\Program Files\MATLAB\R2010b\toolbox\libsvm-3.17\matlab';
    [ bestc , bestg ] = SVMcg( train_label, train_set );%(train_label,train,cmin,cmax,gmin,gmax,v,cstep,gstep,accstep)
    cmd = [ '-c ' , num2str(bestc) , ' -g ' , num2str(bestg) ];
    cd 'E:\Program Files\MATLAB\R2010b\toolbox\libsvm-3.17\matlab';
    model = svmtrain( train_label , train_set , cmd );
    %% Test
    gesture_type = size(gesture_num, 1);
    accuracy_train = zeros( gesture_type );
    accuracy_test = zeros( gesture_type );
    wrong_gesture = zeros(gesture_type, 1);
    fal_pos_gesture = zeros(gesture_type, 1);
    % Predict the training set
    cd 'E:\Program Files\MATLAB\R2010b\toolbox\libsvm-3.17\matlab';
    [ predict_label , accuracy_rate , prob_estimates ] = svmpredict( train_label , train_set , model , '-q' );
    cd 'G:\Projects\Hand Gesture\Kay''s code';
    for i = 1 : size( train_label , 1 )
        accuracy_train( predict_label( i ) , train_label( i ) ) = accuracy_train( predict_label( i ) , train_label( i ) ) + 1;
    end
    for i = 1 : gesture_type
        accuracy_train( : , i ) = accuracy_train( : , i ) / sum( accuracy_train( : , i ) );
    end
    
    % Predict the test set
    cd 'E:\Program Files\MATLAB\R2010b\toolbox\libsvm-3.17\matlab';
    [ predict_label , accuracy_rate , prob_estimates ] = svmpredict( test_label , test_set , model , '-q' );
    cd 'G:\Projects\Hand Gesture\Kay''s code';
    for i = 1 : size( test_label , 1 )
        accuracy_test( predict_label( i ) , test_label( i ) ) = accuracy_test( predict_label( i ) , test_label( i ) ) + 1;
        if(predict_label(i) ~= test_label(i))
            wrong_gesture(test_label( i ), end + 1) = test_index(i) - sum(gesture_num(1 : test_label( i ) - 1));
            fal_pos_gesture(test_label( i ), end + 1) = predict_label(i) + 1;
        end
    end
    % False positive
    false_post = zeros( gesture_type , 1 );
    for i = 1 : gesture_type
        false_post( i ) = ( sum( accuracy_test( i , : ) ) - accuracy_test( i , i ) ) / sum( accuracy_test( i , : ) );
    end
    % True positive
    for i = 1 : gesture_type
        accuracy_test( : , i ) = accuracy_test( : , i ) / sum( accuracy_test( : , i ) );
    end
end
%% Read in the data of each type of gesture
function [gesture_data, gesture_num] = read_in_data(str)
    load(strcat(str, 'gesture_distance_map'));
%     load(strcat(str, 'gesture_dist_smp'));
    gesture_data = distance_sample_map;    %distance_sample_map, distance_LCS_map
    load(strcat(str, 'gesture_num'));
    gesture_num = gesture_num(2 : end);
end
%% Select the training set and test set
function [train_set, test_set,train_label, test_label, test_index] = data_select( gesture_data , gesture_num , percent , CW_random )
    train_num = round(gesture_num * percent);
    test_num = gesture_num - train_num;
    train_index = [];
    test_index = [];
    for i = 1 : size(gesture_num, 1)
        if strcmp( CW_random , 'random_on' )
            index = randperm(gesture_num(i))';
            train_index = [train_index; sum(gesture_num(1 : i - 1)) + index(1 : train_num(i))];
            test_index = [test_index; sum(gesture_num(1 : i - 1)) + index(train_num(i) + 1 : end)];
        else
            train_index = [train_set; sum(gesture_num(1 : i - 1)) + (1 : train_num(i))'];
            test_index = [test_index; sum(gesture_num(1 : i - 1)) + (train_num(i) + 1 : gesture_num(i))'];
        end
    end
    train_set = gesture_data(train_index, :);
    test_set = gesture_data(test_index, :);
    
    train_label = zeros(sum(train_num), 1);
    test_label = zeros(sum(test_num), 1);
    for i = 1 : size(gesture_num, 1)
        train_label(sum(train_num(1 : i - 1)) + 1 : sum(train_num(1 : i - 1)) + train_num(i)) = i;
        test_label(sum(test_num(1 : i - 1)) + 1 : sum(test_num(1 : i - 1)) + test_num(i)) = i;
    end
end
```