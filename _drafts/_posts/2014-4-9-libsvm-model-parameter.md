---
title: LIBSVM model parameters
tags: [SVM, LIBSVM]
categories: blog
---

本帖子主要就是讲解利用libsvm-mat工具箱建立分类（回归模型）后，得到的模型model里面参数的意义都是神马？以及如果通过model得到相应模型的表达式，这里主要以分类问题为例子。

测试数据使用的是libsvm-mat自带的heart_scale.mat数据（270*13的一个属性据矩阵，共有270个样本，每个样本有13个属性），方便大家自己测试学习。

首先上一个简短的测试代码：

{% highlight matlab linenos %}
%% ModelDecryption
% by faruto @ faruto's Studio~
% http://blog.sina.com.cn/faruto
% Email:faruto@163.com
% http://www.matlabsky.com
% http://www.mfun.la
% http://video.ourmatlab.com
% last modified by 2011.01.06
%% a litte clean work
tic;
close all;
clear;
clc;
format compact;
%% 
% 首先载入数据
load heart_scale;
data = heart_scale_inst;
label = heart_scale_label;
% 建立分类模型
model = svmtrain(label,data,'-s 0 -t 2 -c 1.2 -g 2.8');
model
% 利用建立的模型看其在训练集合上的分类效果
[PredictLabel,accuracy] = svmpredict(label,data,model);
accuracy
%%
toc;
{% endhighlight %}

运行结果：
{% highlight matlab linenos %}
model = 
    Parameters: [5x1 double]
      nr_class: 2
       totalSV: 259
           rho: 0.0514
         Label: [2x1 double]
         ProbA: []
         ProbB: []
           nSV: [2x1 double]
       sv_coef: [259x1 double]
           SVs: [259x13 double]
Accuracy = 99.6296% (269/270) (classification)
accuracy =
   99.6296
    0.0148
    0.9851
Elapsed time is 0.040155 seconds.
{% endhighlight %}

这里面为了简单起见没有将测试数据进行训练集和测试集的划分，这里仅仅是为了简单明了而已，分类结果估计可以不要管，[参数优化](http://imkaywu.com/2014/01/15/find-the-optimal-parameter.html)也不要管，另有帖子讲解。

下面我们就看看 model这个结构体里面的各种参数的意义都是神马，model如下：
{% highlight matlab linenos %}
model = 
Parameters: [5x1 double]
  nr_class: 2
   totalSV: 259
       rho: 0.0514
     Label: [2x1 double]
     ProbA: []
     ProbB: []
       nSV: [2x1 double]
   sv_coef: [259x1 double]
       SVs: [259x13 double]
{% endhighlight %}

###Model Parameters
{% highlight matlab linenos %}
>model.Parameters
ans =
 0
2.0000
3.0000
2.8000
 0
{% endhighlight %}

model.Parameters参数意义从上到下依次为：
> * -s svm类型：SVM设置类型(默认0)
> * -t 核函数类型：核函数设置类型(默认2)
> * -d degree：核函数中的degree设置(针对多项式核函数)(默认3)
> * -g r(gama)：核函数中的gamma函数设置(针对多项式/rbf/sigmoid核函数) (默认类别数目的倒数)
> * -r coef0：核函数中的coef0设置(针对多项式/sigmoid核函数)((默认0)

即在本例中通过model.Parameters我们可以得知 –s 参数为0；-t 参数为 2；-d 参数为3；-g 参数为2.8（这也是我们自己的输入）；-r 参数为0。

关于libsvm参数的一点小说明：

> Libsvm中参数设置可以按照SVM的类型和核函数所支持的参数进行任意组合，如果设置的参数在函数或SVM类型中没有也不会产生影响，程序不会接受该参数；如果应有的参数设置不正确，参数将采用默认值。

###model.Label model.nr_class
{% highlight matlab linenos %}
>> model.Label
ans =
 1
-1
>> model.nr_class
ans =
 2
{% endhighlight %}

> * model.Label表示数据集中类别的标签都有什么，这里是 1，-1；
> * model.nr_class表示数据集中有多少类别，这里是二分类。

model.totalSV model.nSV
{% highlight matlab linenos %}
>> model.totalSV
ans =
   259
>> model.nSV
ans =
   118
   141
{% endhighlight %}

> * model.totalSV代表总共的支持向量的数目，这里共有259个支持向量；
> * model.nSV表示每类样本的支持向量的数目，这里表示标签为1的样本的支持向量有118个，标签为-1的样本的支持向量为141。
    注意：这里model.nSV所代表的顺序是和model.Label相对应的。

###model.ProbA model.ProbB

关于这两个参数这里不做介绍，使用-b参数时才能用到，用于概率估计。

> -b probability_estimates: whether to train a SVC or SVR model for probability estimates, 0 or 1 (default 0)

###model.sv_coef model.SVs model.rho
{% highlight matlab linenos %}
sv_coef: [259x1 double]
    SVs: [259x13 double]
         model.rho =  0.0514
{% endhighlight %}

> * model.sv_coef是一个259*1的矩阵，承装的是259个支持向量在决策函数中的系数；
> * model.SVs是一个259*13的稀疏矩阵，承装的是259个支持向量。
> * model.rho是决策函数中的常数项的相反数（-b）

在这里首先我们看一下 通过 –s 0 参数（C-SVC模型）得到的最终的分类决策函数的表达式是怎样的？

最终的决策函数为：

![image 1](/assets/images/libsvm_model1.jpg)

在由于我们使用的是RBF核函数（前面参数设置 –t 2），故这里的决策函数即为：

![image 2](/assets/images/libsvm_model2.jpg)

其中|| x-y ||是二范数距离 ;

> b就是-model.rho（一个标量数字）;  
> b = -model.rho;  
> n代表支持向量的个数即 n = model.totalSV（一个标量数字）;  
>	
> 对于每一个i：  
> wi =model.sv_coef(i); 支持向量的系数（一个标量数字）  
> xi = model.SVs(i,:) 支持向量（1*13的行向量）  
>	
> x 是待预测标签的样本 （1*13的行向量）  
> gamma 就是 -g 参数

好的下面我们通过model提供的信息自己建立上面的决策函数如下：

{% highlight matlab linenos %}
%% DecisionFunction
function plabel = DecisionFunction(x,model)

gamma = model.Parameters(4);
RBF = @(u,v)( exp(-gamma.*sum( (u-v).^2) ) );

len = length(model.sv_coef);
y = 0;

for i = 1:len
    u = model.SVs(i,:);
    y = y + model.sv_coef(i)*RBF(u,x);
end
b = -model.rho;
y = y + b;

if y >= 0
    plabel = 1;
else
    plabel = -1;
end
{% endhighlight %}

有了这个决策函数，我们就可以自己预测相应样本的标签了：

{% highlight matlab linenos %}
%%
plable = zeros(270,1);
for i = 1:270
    x = data(i,:);
    plabel(i,1) = DecisionFunction(x,model);
end

%% 验证自己通过决策函数预测的标签和svmpredict给出的标签相同
flag = sum(plabel == PredictLabel)
over = 1;
{% endhighlight %}

最终可以看到 flag = 270 ，即自己建立的决策函数是正确的，可以得到和svmpredict得到的一样的样本的预测标签，事实上svmpredict底层大体也就是这样实现的。

最后我们来看一下，svmpredict得到的返回参数的意义都是什么,在下面这段代码中:

{% highlight matlab linenos %}
%% 
% 首先载入数据
load heart_scale;
data = heart_scale_inst;
label = heart_scale_label;
% 建立分类模型
model = svmtrain(label,data,'-s 0 -t 2 -c 1.2 -g 2.8');
model
% 利用建立的模型看其在训练集合上的分类效果
[PredictLabel,accuracy] = svmpredict(label,data,model);
accuracy
{% endhighlight %}

运行可以看到

{% highlight matlab linenos %}
model = 
    Parameters: [5x1 double]
      nr_class: 2
       totalSV: 259
           rho: 0.0514
         Label: [2x1 double]
         ProbA: []
         ProbB: []
           nSV: [2x1 double]
       sv_coef: [259x1 double]
           SVs: [259x13 double]
Accuracy = 99.6296% (269/270) (classification)
accuracy =
   99.6296
    0.0148
    0.9851
{% endhighlight %}

这里面要说一下返回参数accuracy的三个参数的意义。

返回参数accuracy从上到下依次的意义分别是：
> * 分类准率（分类问题中用到的参数指标）
> * 平均平方误差（MSE (mean squared error)） [回归问题中用到的参数指标]
> * 平方相关系数（r2 (squared correlation coefficient)）[回归问题中用到的参数指标]

其中mse 和r2的计算公式分别为：
![image 3](/assets/images/libsvm_model3.jpg)

[原文链接](http://www.matlabsky.com/thread-12649-1-1.html)

[关于SVM的那点破事](http://www.matlabsky.com/forum-viewthread-tid-10966-fromuid-18677.html)

感谢作者的原创和分享精神
