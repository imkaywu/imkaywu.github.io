---
title: 3D Reconstruction Real-world Dataset
description: 
img: /assets/img/project/thumbnail/real_world_dataset.png
---

## News
The new project page can be found [here](https://imkaywu.github.io/3drecon_dataset).

## Introduction
This dataset is created for my Master's research on development and evaluation of algorithm-free framework for 3D reconstruction, which is part of the OpenVL project. The goal is to find a set of descriptions and a mapping from a well-defined problem domain to a well reconstructed object model. Each object is captured using three different setups

* Multi-View Stereo setup: cameras are distributed over 3 rings, with a between angle of $$30^\circ$$
* Phototmetric Stereo setup: fixed camera, and hand-held light source (lamp), a diffuse and a specular reference object
* Structured Light setup: fixed camera and projector pair, with between angle of around $$10^\circ$$.

## Overview of Objects
<!-- ![real world dataset](/assets/img/project/real_world_dataset/real_world_dataset.png) -->
<div class="img_row">
    <img class="col three" src="/assets/img/project/real_world_dataset/real_world_dataset.png" alt="" title="example image"/>
</div>

## Sample code
The code to run and evaluation the algorithms are [here](https://github.com/imkaywu/3DRecon_Algo_Eval). The algorithms are in the directory `algo/`, and the code to run and evaluate the algorthms is in the directory `eval/real_world/`.

### Algorithms
* PMVS: patch-based Multi-view Stereo
* EPS: example-based Photometric Stereo
* GSL: gray-encoded Structured Light
* VH: volumetric based Visual Hull
* SC: space carving

## Reconstruction Results

### Patch-based MVS
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/box/box_mvs.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat0/cat0_mvs.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat1/cat1_mvs.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/cup/cup_mvs.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/dino/dino_mvs.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/house/house_mvs.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/pot/pot_mvs.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/statue/statue_mvs.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/vase/vase_mvs.png" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of Patch-based Multi-View Stereo
</div>


### Example-based PS
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/box/box_ps.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat0/cat0_ps.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat1/cat1_ps.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/cup/cup_ps.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/dino/dino_ps.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/house/house_ps.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/pot/pot_ps.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/statue/statue_ps.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/vase/vase_ps.png" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of Example-based Photometric Stereo
</div>


### Gray code SL
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/box/box_sl.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat0/cat0_sl.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat1/cat1_sl.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/cup/cup_sl.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/dino/dino_sl.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/house/house_sl.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/pot/pot_sl.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/statue/statue_sl.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/vase/vase_sl.png" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of Gray code Structured Light
</div>


### Visual Hull
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/box/box_sc.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat0/cat0_sc.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/cat1/cat1_sc.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/cup/cup_sc.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/dino/dino_sc.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/house/house_sc.png" alt="" title="example image"/>
</div>
<div class="img_row">
    <img class="col one" src="/assets/img/project/real_world_dataset/pot/pot_sc.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/statue/statue_sc.png" alt="" title="example image"/>
    <img class="col one" src="/assets/img/project/real_world_dataset/vase/vase_sc.png" alt="" title="example image"/>
</div>
<div class="col three caption">
    Results of Visual Hull
</div>

