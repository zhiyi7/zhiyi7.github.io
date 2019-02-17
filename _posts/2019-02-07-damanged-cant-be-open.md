---
layout: post
title: 解决 XXX.app 已损坏，打不开。 您应该将它移到废纸篓
---

打开一些你不可告人来路的应用会提示“*.app 已损坏，打不开。 您应该将它移到废纸篓”，如果搜索解决方案，很多都是让你关掉 gatekeeper。其实对于大多数应用来说不用那么大动干戈，直接把这个文件属性改一下即可：

`sudo xattr -rd com.apple.quarantine /Applications/[LockedApp].app`

实在不行，再使用 `sudo spctl --master-disable`