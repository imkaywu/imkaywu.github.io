---
title: Estimation of essential and fundamental matrix
categories: 
  - Research
  - Dev
tags:
  - Computer Vision
---

## Fundamental matrix
```matlab
function [F,e1,e2] = fundmatrix(varargin)
    
    [x1, x2, npts] = checkargs(varargin(:));
    Octave = exist('OCTAVE_VERSION') ~= 0;  % Are we running under Octave?    
    
    % Normalise each set of points so that the origin 
    % is at centroid and mean distance from origin is sqrt(2). 
    % normalise2dpts also ensures the scale parameter is 1.
    [x1, T1] = normalise2dpts(x1);
    [x2, T2] = normalise2dpts(x2);
    
    % Build the constraint matrix
    A = [x2(1,:)'.*x1(1,:)'   x2(1,:)'.*x1(2,:)'  x2(1,:)' ...
         x2(2,:)'.*x1(1,:)'   x2(2,:)'.*x1(2,:)'  x2(2,:)' ...
         x1(1,:)'             x1(2,:)'            ones(npts,1) ];       

    if Octave
	[U,D,V] = svd(A);   % Don't seem to be able to use the economy
                            % decomposition under Octave here
    else
	[U,D,V] = svd(A,0); % Under MATLAB use the economy decomposition
    end

    % Extract fundamental matrix from the column of V corresponding to
    % smallest singular value.
    F = reshape(V(:,9),3,3)';
    
    % Enforce constraint that fundamental matrix has rank 2 by performing
    % a svd and then reconstructing with the two largest singular values.
    [U,D,V] = svd(F,0);
    F = U*diag([D(1,1) D(2,2) 0])*V';
    
    % Denormalise
    F = T2'*F*T1;
    
    if nargout == 3  	% Solve for epipoles
	[U,D,V] = svd(F,0);
	e1 = hnormalise(V(:,3));
	e2 = hnormalise(U(:,3));
    end
    
%--------------------------------------------------------------------------
% Function to check argument values and set defaults

function [x1, x2, npts] = checkargs(arg);
    
    if length(arg) == 2
        x1 = arg{1};
        x2 = arg{2};
        if ~all(size(x1)==size(x2))
            error('x1 and x2 must have the same size');
        elseif size(x1,1) ~= 3
            error('x1 and x2 must be 3xN');
        end
        
    elseif length(arg) == 1
        if size(arg{1},1) ~= 6
            error('Single argument x must be 6xN');
        else
            x1 = arg{1}(1:3,:);
            x2 = arg{1}(4:6,:);
        end
    else
        error('Wrong number of arguments supplied');
    end
      
    npts = size(x1,2);
    if npts < 8
        error('At least 8 points are needed to compute the fundamental matrix');
    end
```