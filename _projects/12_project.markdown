---
title: 3D Reconstruction Toolbox
description:
img: /assets/img/project/thumbnail/ps_train.png
---

## Introduction
The is the toolbox I developed for my Master's thesis to run various 3D reconstruction algorithms and evaluate their performance under varied material and geometric conditions. The [source code](https://github.com/imkaywu/3DRecon_Algo_Eval) is publicly available.

## Algorithms
* PMVS: patch-based Multi-view Stereo
* EPS: example-based Photometric Stereo
* GSL: gray-encoded Structured Light
* VH: volumetric based Visual Hull
* SC: space carving

## Evaluation

There are three separate directories: `pairwise`, `train`, and `test`. Each directory, there are two files:
* `run` (`train`, `test`); run the algorithms to compute the accuracy and completeness
* `analysis`: plot the graphs
* `eval_acc_cmplt`: evaluate the accuracy and completeness of MVS, SL and VH
* `eval_angle`: evaluate the angle difference of PS

## Results

### Dependency checking

#### MVS

<!-- ![mvs dependency checking](/assets/img/project/3drecon_toolkit/mvs_depend_check.png) -->
<div class="img_row">
    <img class="col three" src="/assets/img/project/3drecon_toolkit/mvs_depend_check.png" alt="" title="example image"/>
</div>

#### PS
<!-- ![ps dependency checking](/assets/img/project/3drecon_toolkit/ps_depend_check.png) -->
<div class="img_row">
    <img class="col three" src="/assets/img/project/3drecon_toolkit/ps_depend_check.png" alt="" title="example image"/>
</div>

#### SL
<!-- ![sl dependency checking](/assets/img/project/3drecon_toolkit/sl_depend_check.png) -->
<div class="img_row">
    <img class="col three" src="/assets/img/project/3drecon_toolkit/sl_depend_check.png" alt="" title="example image"/>
</div>

### Training

#### MVS
<!-- ![mvs training](/assets/img/project/3drecon_toolkit/mvs_train.png) -->
<div class="img_row">
    <img class="col three" src="/assets/img/project/3drecon_toolkit/mvs_train.png" alt="" title="example image"/>
</div>

#### PS
<!-- ![ps training](/assets/img/project/3drecon_toolkit/ps_train.png) -->
<div class="img_row">
    <img class="col three" src="/assets/img/project/3drecon_toolkit/ps_train.png" alt="" title="example image"/>
</div>

#### SL
<!-- ![sl training](/assets/img/project/3drecon_toolkit/sl_train.png) -->
<div class="img_row">
    <img class="col three" src="/assets/img/project/3drecon_toolkit/sl_train.png" alt="" title="example image"/>
</div>

## files that not used
`evaluate.mat`: compute the accuracy and completeness of the reconstruction

`evaluate_mvs.mat` and `evaluate_sl.mat` are currently not used

`eval_ps.mat`: evaluate the PS as a height map