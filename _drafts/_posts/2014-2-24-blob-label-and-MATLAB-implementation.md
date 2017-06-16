---
title: Blob label and MATLAB implementation
tags: [Computer Vision]
categories: blog
---

##Graphical example of the algorithm
###The array from which connected regions are to be extracted is given below (8-connectivity based).
We first assign different binary values to elements in the graph. Attention should be paid that the "0~1" values write on the center of the elements in the following graph are elements' values. While, the "1,2,...,7" values in the next two graphs are the elements' labels. The two concepts should not be confused.
![blob label 1]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/blob label 1.png)

###After the first pass, the following labels are generated. A total of 7 labels are generated in accordance with the conditions highlighted above.
![blob label 2]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/blob label 2.png)

The label equivalence relationships generated are,
![blob label 3]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/blob label 3.png)

###Array generated after the merging of labels is carried out. Here, the label value that was the smallest for a given region "floods" throughout the connected region and gives two distinct labels, and hence two distinct labels.
![blob label 4]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/blob label 4.png)

###Final result in color to clearly see two different regions that have been found in the array.
![blob label 5]({{ site.url }}{{ site.baseurl }}/assets/images/portfolio/hand_gesture_recognition/blob label 5.png)

##Source Code

{% highlight matlab linenos %}
function label_map = blob_detector( binary_map )
    [ y , x ] = find( binary_map == 1 );
    y_start = min( y );
    y_end = max( y );
    x_end = max( x );
    label_map = zeros( size( binary_map ) );
    label = 1;
    addRow = 0;
    addCol = 0;
    if( y_end == size( binary_map , 1 ) )
        label_map( y_end + 1 , : ) = 0;
        addRow = 1;
    end
    if( x_end == size( binary_map , 2 ) )
        label_map( : , x_end + 1 ) = 0;
        addCol = 1;
    end
    % blob label
    for i = y_start : y_end
        for j = find( binary_map( i ,: ) , 1 ) : find( binary_map( i ,: ) , 1 , 'last' )
            if( binary_map( i , j ) > 0 )
                label_neighbor = label_map( i - 1 : i + 1 , j - 1 : j + 1 );
                if( sum( sum( label_neighbor ) ) == 0 )
                    label_map( i , j ) = label( end );
                    label = [ label , label(end)+1 ];
                else
                    label_temp = unique( label_neighbor( label_neighbor > 0 ) );
                    label_map( i , j ) =label_temp( 1 );
                    if( size( label_temp , 1 ) > 1 )
                        label_temp = label_temp( 2 : end );
                        label( label_temp( label( label_temp ) > label_map( i , j ) ) ) = label_map( i , j );
                    end
                end
            end
        end
    end
    % combine labels
    if( addRow )
        label_map( end , : ) = [];
    end
    if( addCol )
        label_map( : , end ) = [];
    end
    label = label( 1 : end - 1 );
    for i = 1 : size( label , 2 )
        while( 1 )
            if( label( label( i ) ) == label( i ) )
                break;
            else
                label( i ) = label( label( i ) );
            end
        end
    end
    
    for i = 1 : size(label , 2 )
        label_map( label_map == i ) = label( i );
    end
    % find the dominant blob
    label_temp = unique( label );
    blob_max = 0;
    for i = 1 : size(label_temp , 2)
        if( blob_max < sum( sum( label_map == label_temp( i ) ) ) )
            blob_max = sum( sum( label_map == label_temp( i ) ) );
            label = label_temp( i );
        end
    end
    
    label_map( label_map ~= label ) = 0;
    label_map( label_map == label ) = 1;
end
{% endhighlight %}