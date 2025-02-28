---
layout:     post
title:      "Karma Guide"
active: notes
date:       2023-08-07
header-img: "gallery/archive/uvl/greenhouse_shot_001.jpg"
tags: []
categories: []
comments: false
---

## TL:DR

Karma XPU and CPU are different and everything is confusing so the best practice is to not let the program do any guessing for you. First this means using .rat or .exr files that are mipmapped for better performance and ACES support which you can create using imaketx or hoiiotool utilities. Second, specify the color space in the texture name so there is no guessing done by karma.  

## Intro 

Before talking about karma it is essential that you understand karma CPU and karma XPU are in no way the same. This is a big issue because much of the SideFX documentation does not make this obvious and often makes statements like karma has "Some Feature" but neglects to say it only works on the CPU side. Because of this it is best to stick to one, though that being said if you are working with XPU you should be able to switch to CPU fairly seamlessly but will get slightly different results. It is also important to note that anticipating the release of H20 there are likely to be many changes to XPU that make much of this post irrelevant.

## Texture Formats

From the [Docs](https://www.sidefx.com/docs/houdini/solaris/kug/materials.html#maps) .exr or .rat files are most efficient and predictable as they support color spaces like ACES and mipmapping for better memory management. I have not done tests on efficiency between .rat or .exr. You will notice in the docs that as of H19.5 you can use the environment variable HOUDINI_TEXTURE_DISK_CACHE to auto-generate .rat files at render time for a 1 time cost. This currently only work on karma CPU and does not seem to be configurable for color space conversion and therefore I have not found very usable, and resort to manual conversion below. 

## Color Spaces

Color spaces are very confusing in karma right now because XPU and CPU do not load textures the same, leading to inconsistent linearization of textures between CPU and XPU. The safest way to approach this seems to be by specifying everything manually, not letting anything be determined by karma. For me this has meant naming conventions like "pine_roots_001_diff_2k_acescg.exr". If you look at the [Docs](https://www.sidefx.com/docs/houdini/solaris/kug/materials.html#maps) on MaterialX having the color space in the file name is the first check by karma (assuming both XPU and CPU) to determine the color space when set to "auto" or and of the "color" signatures. What this implies is that if you have a texture already in ACEScg you can set the signature to "vector3" since it does require any color transforms. A brief explaination on ACES below. 

## Convert to ACES and Create MipMapped textures

Using the utilities that ship with houdini you have a couple options. [hoiiotool](https://www.sidefx.com/docs/houdini/ref/utils/hoiiotool.html) is feature rich tool, [imaketx](https://www.sidefx.com/docs/houdini/ref/utils/imaketx.html) is a more specific tool for making mipmapped textures that has color conversion capabilities. 

        examples of imaketx

        imaketx "diffuse_srgb_texture.png" --colormanagement=ocio --colorconvert srgb_texture acescg  "diffuse_acescg.rat" 

This is straightforward except for the --colorconvert arguments because you cannot pass in something like Utility-sRGB-Texture which you are used to seeing. The argument you actually want to pass in for Utility-sRGB-Texture is srgb_texture. You can find this through the .OCIO config file and will see that color spaces have a property called **aliases** that contains a list of alternative names. You will want the one that is no special characters, spaces or caps. You can also drop down an **OCIO transform vop** to see the list of all ocio color space names and aliases.

If you're confused about ACES there are a couple things to keep in mind when performing color transforms to assure accuracy.

  1. Data maps like roughness, normal, displacement should be transformed from Utility - Raw into ACEScg. This preserves the values of each pixel so there are consistent reads.

  2. Color data like your diffuse map or subsurface color are a bit more complex because they are in a certain color space which needs to be transformed into the desired color space, usually ACEScg. In order to choose the proper transform you need to know the source color space of the texture. 99% of the time it will be sRGB with sRGB gamma that can be checked with a tool like exiftool. If you texture is coming from sRGB and has not already been linearized you can use Utility-sRGB-Texture into ACEScg. This will result in a much darker texture, which is from the linearization. This is fine however because you will never view ACEScg images, they will have a ODT that transforms them into their appropriate output device like sRGB, rec.709, P3-DCI etc.. on a monitor. This lets the computer and the user work with predictable, linear values while viewing non-linear, realistic outputs.

  3. HDRIs don't tend fall into either of these categories. Like color data, the texture already exist in some color space that is most likely sRGB. However unlike color data, the texture should already be linearized (assuming its from a reliable source) because it is holding data we want to use to light our scenes. So what you have is an sRGB texture that is linear. This can be transformed with Utility-Linear-sRGB into ACEScg. This will result in very small changes in color as the sRGB colors are transformed into the ACEScg gamut. 

## Working with Aces

I have found in early versions of 20.5 that Houdini does not render out images with the ACEScg space by default. To set it up you need to change your "Render Working Space" in Edit > OCIO Editor to ACEScg. You also need to set "Output Colorspace" to be ACEScg on the Karma Render Settings LOP under Image Output > AOVs > Output Colorspace. If you fail to do this and log your render you will see at the end all image planes are converted to linear srgb.  

