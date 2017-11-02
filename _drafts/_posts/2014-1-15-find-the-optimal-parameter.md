---
title: Find the optimal parameters for LIBSVM using cross-validation
categories:
  - Dev
tags:
  - Machine Learning
  - SVM
---

### 目标
在SVM算法中，通过交叉验证来确定最佳的参数`c`和`g`。

### 想法：
让c和g在一定的范围里跑(比如 c = 2^(-5),2^(-4),...,2^(5),g = 2^(-5),2^(-4),...,2^(5)),然后用cross validation的想法找到准确率最高的c和g,在这里我做了一点修改(纯粹是个人的一点小经验和想法),我改进的是: 因为会有不同的c和g都对应最高的的准确率,我把具有最小c的那组c和g认为是最佳的c和g,因为惩罚参数不能设置太高,很高的惩罚参数能使得validation数据的准确率提高,但过高的惩罚参数c会造成过学习状态,反正从我用SVM到现在,往往都是惩罚参数c过高会导致最终测试集合的准确率并不是很理想。

### Code:

寻找最优化参数的函数(implemented using 10-fold cross-validation)：

```matlab
function [bestacc,bestc,bestg] = SVMcg(train_label,train,cmin,cmax,gmin,gmax,v,cstep,gstep,accstep)
    % train_label:训练集标签.要求与libsvm工具箱中要求一致.
    % train:训练集.要求与libsvm工具箱中要求一致.
    % cmin:惩罚参数c的变化范围的最小值(取以2为底的对数后),即 c_min = 2^(cmin).默认为 -5
    % cmax:惩罚参数c的变化范围的最大值(取以2为底的对数后),即 c_max = 2^(cmax).默认为 5
    % gmin:参数g的变化范围的最小值(取以2为底的对数后),即 g_min = 2^(gmin).默认为 -5
    % gmax:参数g的变化范围的最小值(取以2为底的对数后),即 g_min = 2^(gmax).默认为 5
    % 
    % v:cross validation的参数,即给测试集分为几部分进行cross validation.默认为 3
    % cstep:参数c步进的大小.默认为 1
    % gstep:参数g步进的大小.默认为 1
    % accstep:最后显示准确率图时的步进大小. 默认为 1.5
    %% about the parameters of SVMcg
    v_def = 10;
    if nargin < 10
        accstep = 1.5;
    end
    if nargin < 8
        accstep = 1.5;
        cstep = 1;
        gstep = 1;
    end
    if nargin < 7
        accstep = 1.5;
        v = v_def;
        cstep = 1;
        gstep = 1;
    end
    if nargin < 6
        accstep = 1.5;
        v = v_def;
        cstep = 1;
        gstep = 1;
        gmax = 5;
    end
    if nargin < 5
        accstep = 1.5;
        v = v_def;
        cstep = 1;
        gstep = 1;
        gmax = 5;
        gmin = -5;
    end
    if nargin < 4
        accstep = 1.5;
        v = v_def;
        cstep = 1;
        gstep = 1;
        gmax = 5;
        gmin = -5;
        cmax = 5;
    end
    if nargin < 3
        accstep = 1.5;
        v = v_def;
        cstep = 1;
        gstep = 1;
        gmax = 5;
        gmin = -5;
        cmax = 5;
        cmin = -5;
    end
    %% X:c Y:g cg:acc
    [X,Y] = meshgrid(cmin:cstep:cmax,gmin:gstep:gmax);
    [m,n] = size(X);
    cg = zeros(m,n);
    %% record acc with different c & g,and find the bestacc with the smallest c
    bestc = 0;
    bestg = 0;
    bestacc = 0;
    basenum = 2;
    for i = 1:m
        for j = 1:n
            cmd = ['-v ',num2str(v),' -c ',num2str( basenum^X(i,j) ),' -g ',num2str( basenum^Y(i,j) )];
            cg(i,j) = svmtrain(train_label, train, cmd);

            if ( cg(i,j) > bestacc || ( cg(i,j) == bestacc && bestc > basenum^X(i,j) ) )
                bestacc = cg(i,j);
                bestc = basenum^X(i,j);
                bestg = basenum^Y(i,j);
            end
        end
    end
    %% to draw the acc with different c & g
    [C,h] = contour(X,Y,cg,60:accstep:100);
    clabel(C,h,'FontSize',10,'Color','r');
    xlabel('log2c','FontSize',10);
    ylabel('log2g','FontSize',10);
    grid on;
end
```

函数的测试程序：

```matlab
[ bestacc , bestc , bestg ] = SVMcg( input_feature , train_set );
cmd = [ '-c ' , num2str(bestc) , ' -g ' , num2str(bestg) ];
model = svmtrain( input_feature , train_set , cmd );
```

[原文链接](http://www.ilovematlab.cn/thread-47819-1-1.html)

