# 概述  

ApiDemos 的 Graphics 示例中含有OpenGL ES 例子，OpenGL ES 主要用来开发3D图形应用的。OpenGL ES (OpenGL for Embedded Systems) 是 OpenGL 三维图形 API 的子集，针对手机、PDA和游戏主机等嵌入式设备而设计。

> 下面是维基百科中对应OpenGL ES的简介：

OpenGL ES 是从 OpenGL 裁剪定制而来的，去除了 glBegin/glEnd，四边形（GL_QUADS）、多边形（GL_POLYGONS）等复杂图元等许多非绝对必要的特性。经过多年发展，现在主要有两个版本，OpenGL ES 1.x 针对固定管线硬件的，OpenGL ES 2.x 针对可编程管线硬件。OpenGL ES 1.0 是以 OpenGL 1.3 规范为基础的，OpenGL ES 1.1 是以 OpenGL 1.5 规范为基础的，它们分别又支持 common 和 common lite 两种profile。lite profile只支持定点实数，而common profile既支持定点数又支持浮点数。 OpenGL ES 2.0 则是参照 OpenGL 2.0 规范定义的，common profile发布于2005-8，引入了对可编程管线的支持。

在解析Android ApiDemos 中OpenGL ES示例前，有必要对OpenGL ES 开发单独做个简明开发教程，可以帮助从未接触过3D开发的程序员了解OpenGL 的开发的基本概念和方法，很多移动手机平台都提供了对OpenGL ES 开发包的支持，因此尽管这里使用Android平台介绍OpenGL ES ,但基本概念和步骤同样适用于其它平台。

简明开发教程主要参考 [Jayway Team Blog中OpenGL ES 开发教程](http://www.jayway.com/2009/12/03/opengl-es-tutorial-for-android-part-i/) ，这是一个写的比较通俗易懂的开发教程，适合OpenGL ES初学者。

除了这个OpenGL ES 简明开发教程外，以后将专门针对OpenGL ES写个由浅入深的开发教程，尽请关注。  
  
