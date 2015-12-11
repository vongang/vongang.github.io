---
layout: post
title: OpenGL Shader Processing Pipeline
categories: [OpenGL, Shader, C++]
---

[source](http://www.lighthouse3d.com/tutorials/glsl-12-tutorial/pipeline-overview/)
## Pipeline Overview
The following figure is a (very) simplified diagram of the pipeline stages and the data that travels amongst them. Although extremely simplified it is enough to present some important concepts for shader programming. In this subsection the fixed functionality of the pipeline is presented. Note that this pipeline is an abstraction and does not necessarily meet any particular implementation in all its steps.


![](/img/pic/shader_pipeline.png)

### Vertex Transformation

In here a vertex is a set of attributes such as its location in space, as well as its color, normal, texture coordinates, amongst others. The inputs for this stage are the individual vertices attributes. Some of the operations performed by the fixed functionality at this stage are:

Vertex position transformation
Lighting computations per vertex
Generation and transformation of texture coordinates

### Primitive Assembly and Rasterization

The inputs for this stage are the transformed vertices, as well as connectivity information. This latter piece of data tells the pipeline how the vertices connect to form a primitive. It is in here that primitives are assembled.

This stage is also responsible for clipping operations against the view frustum, and back face culling.

Rasterization determines the fragments, and pixel positions of the primitive. A fragment in this context is a piece of data that will be used to update a pixel in the frame buffer at a specific location. A fragment contains not only color, but also normals and texture coordinates, amongst other possible attributes, that are used to compute the new pixel’s color.

The output of this stage is twofold:

The position of the fragments in the frame buffer
The interpolated values for each fragment of the attributes computed in the vertex transformation stage
The values computed at the vertex transformation stage, combined with the vertex connectivity information allow this stage to compute the appropriate attributes for the fragment. For instance, each vertex has a transformed position. When considering the vertices that make up a primitive, it is possible to compute the position of the fragments of the primitive. Another example is the usage of color. If a triangle has its vertices with different colors, then the color of the fragments inside the triangle are obtained by interpolation of the triangle’s vertices color weighted by the relative distances of the vertices to the fragment.

### Fragment Texturing and Coloring

Interpolated fragment information is the input of this stage. A color has already been computed in the previous stage through interpolation, and in here it can be combined with a texel (texture element) for example. Texture coordinates have also been interpolated in the previous stage. Fog is also applied at this stage. The common end result of this stage per fragment is a color value and a depth for the fragment.

### Raster Operations

The inputs of this stage are:

The pixels location
The fragments depth and color values
The last stage of the pipeline performs a series of tests on the fragment, namely:

Scissor test
Alpha test
Stencil test
Depth test
If successful the fragment information is then used to update the pixel’s value according to the current blend mode. Notice that blending occurs only at this stage because the Fragment Texturing and Coloring stage has no access to the frame buffer. The frame buffer is only accessible at this stage.

### Visual Summary of the Fixed Functionality

The following figure presents a visual description of the stages presented above:
![](/img/pic/visualpipeline.png)

### Replacing Fixed Functionality

Recent graphic cards give the programmer the ability to define the functionality of two of the above described stages:

Vertex shaders may be written for the Vertex Transformation stage.
Fragment shaders replace the Fragment Texturing and Coloring stage’s fixed functionality.
In the next subsections these programmable stages, hereafter the vertex processor and the fragment processor, are described.

