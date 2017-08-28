---
title: Point cloud animation in Blender
categories: 
  - Dev
tags:
  - Blender
---

This is a tutorial on how to render the point cloud animation in Blender. We use the Blender Render engine instead of Cycles.

## Data preparation

The problem of rendering a point cloud in Blender is that Blender is built to work with meshes. Therefore, pure point cloud will appear as black dots in Blender. Here are two feasible approaches to render a textured point cloud: first, convert a point cloud into a surface mesh in MeshLab; second, generate a surface patch for each vertex.

### Convert to surface mesh in MeshLab

1. **Estimate surface normals**: if the point cloud doesn't have surface orientation, we need to first compute surface normals from neighbouring vertices: `Filters`>`Point Set`>`Compute normals for point sets`>`Neighbour num: 16`;
2. **Downsample point cloud** (if necessary): `Filters`>`Poisson-disk Sampling`>`Number of samples: [nsamps]` + `Base Mesh Subsampling: check`;
3. **Meshing**: `Filters`>`Remeshing, Simplification and Reconstruction`>`Screened Poisson Surface Reconstruction`>`Reconstruction Depth: 12`.


### Create a patch for each vertex

The second approach is originally developed in this [post](https://visheshvision.wordpress.com/2014/04/28/rendering-a-colored-point-cloud-in-blender/), which generates a surface patch, or splat for each vertex. This is because the smalles primitive that Blender can handle is a plane. Thus, the idea is to generate a small plane for each vertex and orient them perpendicular to the vertex normal. The pseudocode is

```cpp
// normal: (nx, ny, nz)
// vertex: (x, y, z)
float mag_normal = sqrt(nx * nx + ny * ny + nz * nz);

// compute the azimuth, and elevation angle
float theta = asin(ny / mag_normal);
float phi = atan(nx / nz);

// the original plane p: coords (0, 0, 0), normal (0, 0, 1)
rotateX(p, theta);
rotateY(p, phi);
translate(p, vertex)
//
```


## Origin to Geometry
The pivot point (cursor) may very well not be the center of mass. To change the pivot point to the center of object, press `SHIFT`+`CTRL`+`AlT`+`C`>`Origin to Geometry` or `Origin to Center of Mass`.


## Make color (texture) visible in Render Mode
The texture can be seen in the `Texture` Mode, but still not visible in `Material` and `Rendered` Mode. This can be changed by: `Material`>`new`>`Options`>`Vertex Color Paint`.


## Add keyframes
Here is a quick tutorial on how to create a turntable animation in Blender, the key points are summarized as follows:
* In the first frame, `Object`>`Transform`>`Z: [azimuth angle]`>`right click`>`Insert (Replace) Single Keyframe`.
* In the final frame, `Object`>`Transform`>`Z: [azimuth angle]`>`right click`>`Insert (Replace) Single Keyframe`.
* Open graph editor: `T`>`Interpolation: Linear`.

<iframe width="560" height="315" src="https://www.youtube.com/embed/2r0KsLYr3wA" frameborder="0" allowfullscreen></iframe>


## Animation
There are two approaches:

1. set the output format as a video format then render;
2. set the output format as image format, generate a image sequence, then convert image sequence into a video.

The difference is that it's possible to stop the rendering process for the second approach whereas you will probably end up with a corrupted file if stop the rendering in the first case. Here we only discuss the second one. See this [post](https://blender.stackexchange.com/questions/15142/how-to-render-an-animation-as-video-in-blender) for some details.

* **Output directory**: `Render`>`Output`>`[odir]`+`File Format` (select an image format). This is the folder where your animation files will be saved, whether it is rendered as image files or a video file.
* **Generate image sequence**: `Render`>`Animation`.
* **Video Editing**: `Choose Screen layout`>`Video Editing`>`SHIFT`+`A`>`Image`>`A`(select all images)>`Add Image Strip`>`Choose Screen layout`>`Default`.
* **Video output**: `Render`>`Output`>`File Format` (select a video format)
* **Generate video**: `Render`>`Animation`.


## Lighting (Optional)
Here is a simple way to add a world lighting: `World`>`World`>`Real Sky: check`>`Environment Lighting: check`>`White` or `Sky Texture`.