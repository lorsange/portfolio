---
layout:     post
title:      "Mass Renaming Files"
active: notes
date:       2021-03-25
header-img: "img/postcover/renaming-files-thumbnail.jpg"
tags: []
categories: []
comments: false
---

Sometimes images or any type of file sequence gets rendered out with the wrong naming convention and if you have hundreds of files manual renaming is out of the question. Heres how you can automatically rename file sequences on windows through the powershell.

Step 1: set the proper drive that files you want to rename are on by typing 'drive:' which would look something like 'd:', it is not case sensitive.

Step 2: set the location of the files to be renamed by typing: `set-location -Path "location"` which would look something like this set-location -Path "D:\stuff\stuff\stuff\stuff".

Step 3: is to rename all files using the command: `Dir | %{Rename-item $_ -NewName ("name_{0}.ext" -f $nr++)}`
The only words you change are in quotes, with "name" being the name you want for each file and .ext being the file extension. So it could look something like this: `Dir |%{Rename-item $_ -NewName ("frame_{0}.jpg" -f $nr++)}`. Note that you need to remove the single quotes around the commands.
