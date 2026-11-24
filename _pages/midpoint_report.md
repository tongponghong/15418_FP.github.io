---
layout: page
title: "Midpoint Report"
permalink: /midpoint/
pdf: false
---
### Tunger Hong and Jenny Quan 

## Revised (detailed) plan for the coming weeks

**Nov 24 \- Nov 25:** Implement parallel BVH Traversal (Tunger). Expand renderer to include specular materials and (optionally, time permitting) environmental lighting (Jenny). Try to see if there are low-hanging optimizations to current CUDA implementation (Jenny).  
**Nov 27 \- Nov 29:** Thanksgiving weekend. Start wavefront implementation (Tunger)  
**Nov 30 \- Dec 1:** Implement parallel BVH build (Tunger). Finish wavefront implementation and CUDA optimization (Jenny).  
**Dec 2 \- Dec 4:** Finalize above work (Tunger) and begin running experiments (Jenny).  
**Dec 7 \- Dec 8:** Finish running experiments (Jenny and Tunger) and begin outlining the final report and poster (Jenny and Tunger).   
**Dec 9 \- Dec 10:** Final Report write-up and poster creation. (Jenny and Tunger)

## Summary of work completed so far (1-2 paragraphs)

- Successfully set up boilerplate stuff, including a GPU namespace, data structure for scene data, and data structure for render parameters  
  - GPU namespace to keep all the device-side code   
  - Device-side data structure for the scene data and geometry   
  - Moved all render parameters to the GPU and kept them to determine camera details and samples per pixel.   
- Successfully figured out copying the scene into the GPU  
  - Currently have moved all triangle based meshes, and spheres/other shapes will be added next  
  - BVH nodes and the constructed tree on the CPU is currently transported over, full construction of the tree on the GPU will happen later.   
  - Lambertian constant materials (solid color/albedo) are currently added, and full support for image textures will come next  
  - Area lights (emissive meshes) and camera details are added as well, and delta lights and other sources will be added with enough time.   
- Successfully wrote CUDA kernels for rendering a scene of lights, triangles, and solid Lambertian surfaces. We had to refactor the light transport, which was originally recursive, to be an iterative, loop-based algorithm. Currently, all kernels use a naive division of blocks of threads, and we will fine-tune this later.   
  - New kernels for sampling different area lights  
  - New kernel for path tracing and the trace function itself  
  - New kernels for doing basic ray hits against a triangle primitive

## What we plan to show at poster session

- Rendered images showcasing the capabilities of our renderer, including light transport on lambertian surfaces, mirrors, and glass, as well as environmental lighting, time permitting. For each render, we will display the number of scene objects, total number of ray samples, and render time.  
- Live rendering of still images  
- Graphs showing what happens when we perturb parameters such as ray depth, samples per pixel, image resolution/dimension, different BVH layouts (more leaves, deeper trees)  
- Graphs showing differences in initialization time for still scene BVH construction.   
- If possible, real-time rendering

## Preliminary results

We successfully rendered a scene of lights and Lambertian surfaces. The GPU render is shown on the left, and a reference CPU render is shown on the right. The spheres look a bit off, as our current scene data only supports triangles and not spheres; this can be fixed by adding support for sphere geometry (ray-sphere hit is already implemented on the CPU). In addition, while the GPU render successfully captures the overall scene and light transport, including base color, shadows, and color bleed, the overall brightness seems to be a bit off compared to the reference. We theorize that this could be caused either by the GPU’s lack of processing delta lights, incorrect aggregation of direct vs indirect ray bounces, or CPU gamma encoding, but further investigation would be needed to pinpoint this issue. 

![gpu_res]({{"assets/images/gpu_res.png" | relative_url}})\\
*GPU result*

![cpu_res]({{"assets/images/cpu_res.png" | relative_url}})\\
*CPU result*

## Outstanding issues, uncertainty, and remaining work

- We want to write a parallel implementation of SAH BVH construction, which will speed up rendering. However, we are unsure how effective this will be in terms of render time, as well as how to traverse a tree-based data structure in parallel  
  - We are thinking that this will likely help for very large animated scenes, but not as much for still scenes  
- We also want to support rendering specular surfaces and, time permitting, environmental lights and real-time rendering  
- The above two points would require tweaking the data that is copied into GPU memory, writing additional kernel functions, and a thorough understanding of the relevant algorithms.


