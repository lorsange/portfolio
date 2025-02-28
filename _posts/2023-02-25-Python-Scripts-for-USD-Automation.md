---
layout:     post
title:      "Houdini Python Scripts for USD Automation"
active: notes
date:       2023-02-25
header-img: "img/postcover/renaming-files-thumbnail.jpg"
tags: []
categories: []
comments: false
---

## Turn all "OUT_" prefixed nodes into payload USD components

    target_stage = hou.node('/stage/geo_to_usd')
    base_network = hou.node('/stage/objnet1/import')

    def tree(target_stage, soppath, name):
        #sopimport
        sopimport = target_stage.createNode("sopimport", name)
        sopimport.parm("soppath").set(soppath)
        sopimport.parm("asreference").set(1)
        sopimport.parm("primpath").set(f"/{name}")
        sopimport.parm("savepath").set(f"$HIP/../../usd/assets/{name}/geo.usdc")
        sopimport.parm("enable_savepath").set(1)
        sopimport.parm("authortimesamples").set("never")
        
        #loft
        loft = target_stage.createNode("loftpayloadinfo")
        
        #usdrop
        usdrop = target_stage.createNode("usd_rop")
        usdrop.parm("lopoutput").set(f"$HIP/../../usd/assets/{name}/{name}.usdc")
        
        loft.setInput(0, sopimport)
        usdrop.setInput(0, loft)
        target_stage.layoutChildren()
        
    out_nodes = base_network.glob("OUT_*")
    for node in out_nodes:
        name = node.name()
        tree(target_stage, node.path(), name[4:])

## Import USD files into LOPS

    import os

    def reference(target_stage, filepath, name):
        reference = target_stage.createNode("reference::2.0", name)
        reference.parm("filepath1").set(filepath)
        reference.parm("primpath1").set(name)

    rootdir = r"E:\Architecture\1_School_Work\College\UVL\production\usd\assets"
    target_stage = hou.node('/stage/setup')
    for subdir, dirs, files in os.walk(rootdir):
        for file in files:
            if file != "geo.usdc":
                reference(target_stage, os.path.join(subdir, file), file[:-5])
            

