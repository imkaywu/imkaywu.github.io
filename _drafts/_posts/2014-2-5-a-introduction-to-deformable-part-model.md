---
title: An Introduction to Deformable Part Model
tags: [Object Recognition, DMP]
categories: blog
---

Deformable Part Model是最近两年最为流行的图像中物体检测模型，利用这个模型的方法在近几届PASCAL VOC Challenge中都取得了较好的效果。其作者，Brown University的[Pedro Felzenszwalb](http://cs.brown.edu/~pff/)教授，也因为这项成就获得了VOC组委会授予的终身成就奖。有人认为这个模型是目前最好的物体检测算法。


不同于bag of features和hog模板匹配，这类“object conceptually weaker”的模型，在Deformable Part Model中，通过描述每一部分和部分间的位置关系来表示物体（part+deformable configuration）。其实早在1973年，Part Model就已经在 "The representation and matching of pictorial structures" 这篇文章中被提出了。

**part model**

![part model](/assets/images/deformable part model.jpg)

Part Model中，我们通过描述a collection of parts以及connection between parts来表示物体。图1表示经典的弹簧模型，物体的每一部分通过弹簧连接。我们定义一个energy function，该函数度量了两部分之和：每一部分的匹配程度，部分间连接的变化程度（可以想象为弹簧的形变量）。与模型匹配最好的图像就是能使这个energy function最小的图片。形式化表示中，我们可以用一无向图 G=(V,E) 来表示物体的模型，V={v1,...,vn} 代表n个部分，边 (vi,vj)∈E 代表两部分间的连接。物体的某个实例的configuration可以表示为 L=(l1,...,ln)，li 表示为 vi 的位置（可以简单的将图片的configuration理解为各部分的位置布局，实际configuration可以包含part的其他属性）。给定一幅图像，用 mi(li) 来度量 vi 被放置图片中的 li 位置时，与模板的不匹配程度；用 dij(li,lj) 度量 vi,vj 被分别放置在图片中的 li,lj 位置时，模型的变化程度。因此，一副图像相对于模型的最优configuration，就是既能使每一部分匹配的好，又能使部分间的相对关系与模型尽可能的相符的那一个。同样的，模型也自然要描述这两部分。可以通过下面的公式描述最优configuration：

![formular DPM](/assets/images/formular_DPM.png)

Pedro Felzenszwalb教授提出的Deformable Part Model用到了三方面的知识：1.Hog Features 2.Part Model 3. Latent SVM。

作者通过Hog特征模板来刻画每一部分，然后进行匹配。并且采用了金字塔，即在不同的分辨率上提取Hog特征
利用上段提出的Part Model。在进行object detection时，detect window的得分等于part的匹配得分减去模型变化的花费。
在训练模型时，需要训练得到每一个part的Hog模板，以及衡量part位置分布cost的参数。文章中提出了Latent SVM方法，将deformable part model的学习问题转换为一个分类问题。利用SVM学习，将part的位置分布作为latent values，模型的参数转化为SVM的分割超平面。具体实现中，作者采用了迭代计算的方法，不断地更新模型。
作者的页面上有模型的matlab实现源码，必须运行在linux或mac平台中。另外，源码中已经包含PASCAL VOC中各个类别训练好的模型，可以直接用，如果需要自己训练模型，这个过程是很耗时的。为了提高效率，作者又在2010年的“Cascade Object Detection with Deformable Part Models”这篇文章中对part model做了改进，将效率提高了20倍左右。

相关资料：

Fischler, M.A. and Elschlager, R.A. The representation and matching of pictorial structures, 1973

Felzenszwalb, P.F. and Huttenlocher, D.P. Pictorial structures for object recognition,2005

http://people.csail.mit.edu/torralba/courses/6.870/slide/6870_2008-09-10_histograms_pinto_opt.pdf
