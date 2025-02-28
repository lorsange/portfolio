---
layout:     post
title:      "hdStorm Shaders with GLSLFX"
active: notes
date:       2023-12-16
header-img: "gallery/archive/uvl/greenhouse_shot_001.jpg"
tags: []
categories: []
comments: false
---

## Animation

Peye is a vector of screen space coordinates from -1 to 1 where 0,0 is the center of the screen. If you create a vec4 surfaceShader() and return Peye.x, objects will be black when on the left side of the screen and white on the right. Peye.y does the same but vertically. Not sure what Peye stands for but it has something to do with p-something Eye, similar to Neye(nEye) which is related to normal direction to camera. Neye appears to return the a relationship between the view direction and the normal. The more a surface normal is closer to facing away from the view direction, the closer it is to 1 or -1 where the relationship with normals facing left and down is negative.