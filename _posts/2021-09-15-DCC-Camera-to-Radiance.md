---
layout:     post
title:      "Import Cameras in Radiance from Other Software"
active: notes
date:       2021-09-15
image:
  feature: 
header-img: "img/postcover/splitrender-thumbnail.jpg"
tags: []
categories: []
comments: false
---

You can use the following python script to convert camera coordinates from a DCC like 3DSmax into the Radiance coordinates to get the same result. A CLI can be downloaded [here](https://github.com/Latimerias/lati_tools/tree/main/RadianceCAMConverter).

            import numpy as np
            
            print("Expects angles coming from a Z-up DCC")
            
            # user inputs euler angles
            # float() converts the input into a float, without this the values would be read as strings even if they were numbers, all values read by input() are read as strings.
            e1 = float(input("x angle = "))
            e2 = float(input("y angle = "))
            e3 = float(input("z angle = "))

            # convert euler to quaternion
            w = np.sqrt(np.cos(e2*np.pi/180)*np.cos(e1*np.pi/180)+np.cos(e2*np.pi/180)*np.cos(e3*np.pi/180)-np.sin(e2*np.pi/180)*np.sin(e1*np.pi/180)*np.sin(e3*np.pi/180)+np.cos(e1*np.pi/180)* np.cos(e3*np.pi/180)+1)/2
            x = ((np.sin(e2*np.pi/180)*np.sin(e3*np.pi/180)-np.cos(e2*np.pi/180)*np.sin(e1*np.pi/180)*np.cos(e3*np.pi/180)-np.sin(e1*np.pi/180))/np.sqrt(np.cos(e2*np.pi/180)*np.cos(e1*np.pi/180)+ np.cos(e2*np.pi/180)*np.cos(e3*np.pi/180)-np.sin(e2*np.pi/180)*np.sin(e1*np.pi/180)*np.sin(e3*np.pi/180)+np.cos(e1*np.pi/180)*np.cos(e3*np.pi/180)+1)/2)*-1
            y = (np.sin(e2*np.pi/180)*np.cos(e1*np.pi/180)+np.sin(e2*np.pi/180)*np.cos(e3*np.pi/180)+np.cos(e2*np.pi/180)*np.sin(e1*np.pi/180)*np.sin(e3*np.pi/180))/np.sqrt(np.cos(e2*np.pi/180)* np.cos(e1*np.pi/180)+np.cos(e2*np.pi/180)*np.cos(e3*np.pi/180)-np.sin(e2*np.pi/180)*np.sin(e1*np.pi/180)*np.sin(e3*np.pi/180)+np.cos(e1*np.pi/180)*np.cos(e3*np.pi/180)+1)/2
            z = (np.cos(e1*np.pi/180)*np.sin(e3*np.pi/180)+np.cos(e2*np.pi/180)*np.sin(e3*np.pi/180)+np.sin(e2*np.pi/180)*np.sin(e1*np.pi/180)*np.cos(e3*np.pi/180))/np.sqrt(np.cos(e2*np.pi/180)* np.cos(e1*np.pi/180)+np.cos(e2*np.pi/180)*np.cos(e3*np.pi/180)-np.sin(e2*np.pi/180)*np.sin(e1*np.pi/180)*np.sin(e3*np.pi/180)+np.cos(e1*np.pi/180)*np.cos(e3*np.pi/180)+1)/2

            # multiply quaternion by vector, this is a simplification of quaternion matrix multiplication 
            vd = (2*x*z+2*w*y)*-1, (2*y*z-2*w*x)*-1, (w**2-x**2-y**2+z**2)*-1
            vu = (2*x*y-2*w*z), (w**2-x**2+y**2-z**2), (2*y*z+2*w*x)

            print("-vd = ",vd)
            print("-vu = ",vu)
            u)

            dummy = input("press enter to exit")