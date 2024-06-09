---
title: Can Web Technologies Achieve 3D Modeling?
date: 2020-09-20 22:58:39
tags: Work
---

Can web technologies (HTML5, CSS3, JavaScript) achieve stunning 3D modeling or even 3D animation effects? For now, I think not. For example, consider expecting such a page effect:

<img src="preview.png" class="no-shadow" width="100%">

d3.js is out of consideration; it is merely a data visualization tool and is not in the domain of 3D modeling.

Three.js seems to be the more popular 3D modeling library at present. If three.js can do it, how should it be done? The initial idea is to draw a cube like this:

<img src="cube_target.png" class="no-shadow" width="20%">

A cube in 3D modeling is equivalent to "hello world" in programming; it's very easy to create:

<img src="cube_1.png" class="no-shadow" width="20%">

Add lighting to the scene, and the cube will no longer be pitch black. Then, add color to the cube, change the scene's background color, and move the light above the cube to make it look decent:

<img src="cube_2.png" class="no-shadow" width="20%">

The edges of the target cube are glowing and gradient-colored. How to add an edge to the cube? The cube itself does not have this attribute; you can only draw another cube using linear materials (LineBasicMaterial in three.js, while the cube uses MeshPhongMaterial) and overlay it on the solid cube:

<img src="cube_3.png" class="no-shadow" width="20%">

How to make the linear cube glow? Linear materials (LineBasicMaterial) cannot use gradient colors; only shader materials (ShaderMaterial) can use gradient colors. Shader materials can achieve colorful effects, like this (from [StackOverflow](https://stackoverflow.com/questions/52614371/apply-color-gradient-to-material-on-mesh-three-js)):

<img src="line_1.png" class="no-shadow" width="20%">

But here lies the problem. In three.js, rendering an object requires two parameters: geometry and material. Linear materials and shader materials are both types of materials. (TorusKnotGeometry is the geometric shape used in the above image)


```
Line Cube = EdgesGeometry + LineBasicMaterial
Gradient Line = TorusKnotGeometry + ShaderMaterial
```


Combining linear materials and shader materials (LineBasicMaterial and ShaderMaterial) is illogical; I have not found a way to achieve glowing cube edges. Applying the shader to the cube results in this effect (color from 0x215ec9 to 0x000000):

<img src="cube_4.png" class="no-shadow" width="20%">

So what next? I realized that even if a nice-looking cube is achieved, it’s still far from rendering the entire image. For instance, how to achieve such text effects?

<img src="part_1.png" class="no-shadow" width="30%">

Three.js' Texture itself works well, but how to stably place text on the cube with a transparent black background box? Moreover, what about these colorful lines and precise arrow directions:

<img src="part_2.png" class="no-shadow" width="15%">

And the positioning layout and animation effects of more than a dozen elements on the whole picture.

I believe three.js (WebGL) can technically achieve such effects. Even the official examples include web games. However, if developing a web game, image assets would definitely be used. Where do these assets come from? Back to tools like PS and AI. If such productivity tools are used, there is no need to write layouts and animations in JS. Pure web technologies seem difficult to fully solve the problem of 3D modeling. H5 animations face a similar situation.

Another issue with coding for 3D modeling is its non-intuitiveness. Coding defies intuition and visuals, which might work for writing web or APP interfaces (2D). If it were possible to directly place cubes and lines on a canvas, drag to change positions with a mouse, adjust colors, and add various other elements, like playing a game (such as Minecraft), wouldn't it be much better than coding... isn't that Adobe Animate?

Unfortunately, Adobe Animate has no Linux version, and the Linux alternative, Blender, has some performance issues.

<img src="blender.png" class="no-shadow" width="100%">

Returning to the expected effect, the image comes from a big-screen UI [design demonstration](https://www.zcool.com.cn/work/ZMjg2NTA1Njg=.html). The original effect is not actually 3D; the tools used are PS and AI. So, can web technologies achieve this under the premise of needing only 2D effects? If code is used to create various graphics, it still boils down to 3D modeling issues. The simplest way is to take a background image and overlay text on it. Where does the background image come from?
