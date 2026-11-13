---
layout: page
title: "Proposal"
permalink: /proposal/
---
# Parallel Path Tracing 
### Tunger Hong, Jenny Quan

URL: 

## Summary 
We are going to parallelize the path tracer and creation of bounding volume hierarchies (BVHs) built into the graphics application [Scotty3D](https://github.com/CMU-Graphics/Scotty3D). 

## Background 
### What is Ray Tracing?
Path tracing is another way of rendering images as opposed to rasterized approaches. Rasterization relies on converting existing vertex data into primitives, then into fragments, and finally into pixel data. Path tracing employs a different approach, where rays are emitted from a light source and traced back to the camera. The results ultimately become more physically accurate to the real world, but are also much more expensive, as calculating results from light ray bounces can be very difficult depending on the materials in the scene. Path tracing also takes into account illumination and light intensity, through the rendering equation. 

![Rendering Equation]({{"assets/images/rendering_eqn.png" | relative_url}})

After figuring out whether or not a ray hit the triangle, we can then figure out properties such as depth/occlusion, color, etc., from how the rays and triangles are defined. 

When ray tracing and ray-intersection tests are implemented with software, it comes at a high cost, as millions of rays need to be created all at once, and all must be checked for any possible intersections. A naive approach involves tracing every single ray and checking every single triangle, but this is impossibly slow for very large scenes with thousands or even millions of triangles. 

### What are Bounding Volume Hierarchies (BVHs)?
BVHs are a way of grouping scene geometry to reduce ray intersection calculation. They are often stored as trees, where each parent node has two children that correspond to two new buckets for scene geometry. An example is shown below:

![Rabbit BVH]({{"assets/images/rabbit_BVH.png" | relative_url}}) \\
*Rabbit BVH, sourced from [Scotty3D A3 assignment](https://github.com/CMU-Graphics/Scotty3D/blob/main/assignments/A3/T3-bounding-volume-hierarchy.md)*

With BVHs, we can group triangles within a bounding box, simplifying ray calculations as it is much easier to calculate intersection points within a box, but also that if you miss the box, you would also miss all the triangles inside.


However, creating these BVHs can also be expensive. Animated scenes constantly move different objects, which means that we potentially recalculate hundreds of different nodes within the BVH tree just to move one specific area. 

## The Challenge

Since every ray can come up with its own calculation, parallelization can be difficult since correctness and randomness can become issues for image fidelity. In addition, the rendering equation above is recursive, but we must figure out an iterative approach if we want to parallelize this implementation. 

For BVHs, since these are stored as trees, this means that cache locality can be very poor, since a lot of the traversal will be pointer chasing. As a result, memory access can pose a large challenge in overhead for these approaches. 

## Resources
We will be using our own [Scotty3D](https://github.com/CMU-Graphics/Scotty3D) implementation as a starting point for our code and implementation the CUDA approach from there. Some papers that we have been reading are:

[On fast Construction of SAH-based Bounding Volume Hierarchies](https://www.sci.utah.edu/~wald/Publications/2007/ParallelBVHBuild/fastbuild.pdf)

[NVIDIA BVH traversal](https://developer.nvidia.com/blog/thinking-parallel-part-ii-tree-traversal-gpu/)

[NVIDIA guide to CUDA accelerated ray tracing](https://developer.nvidia.com/blog/accelerated-ray-tracing-cuda/)

[Active Thread Compaction for GPU Path Tracing](https://www.sci.utah.edu/~wald/Publications/2011///PathCompaction/download//compact.pdf)


## Goals and Deliverables

### Plan to achieve:
- Improvements in speedup during path tracing scenes with large amounts of triangles due to creation and traversal being sped up
- Benchmarking both sequential and parallel implementations on GHC machines to compare speedup 
- A wavefront-style implementation for thread parallelism 
- 30 fps on lower resolutions 

### Hope to achieve:
- Real time framerates of 60+ fps at 4k resolution 
- Real time framerates on dynamic/animated scenes 

## Platform Choice
CUDA makes sense for us since a each batch of rays can be bucketed into different thread blocks, and a lot of the math itself can be parallelized as well. 

## Schedule 
Week(s) of \\
11/9: Proposal and testing Scotty3D implementation. Also some parallelization planning \\
11/16: Figuring out the CUDA implementation, changing Scotty3D boilerplate as needed, basic materials  \\
11/23: Starting parallel BVH construction, more complicated materials (e.g. glass) \\
11/30: Benchmarking and testing  \\
12/7: Finalizing results, writing final report and poster presentation 


