# 导言  
  
Android ApiDemos 到目前为止，介绍完了出 View 以外的所有例子，在介绍 Graphics 示例时跳过了和 OpenGL ES 相关的例子，OpenGL ES 3D 图形开发需要专门的开发教程，因此从今天开始一边继续 Android ApiDemos Views 例子的解析，同时开始 Android OpenGL ES 开发教程。

在学习 Android OpenGL ES 开发之前，你必须具备 Java 语言开发经验和一些 Android 开发的基本知识，但并不需要有图形开发的经验，本教程也会涉及到一些基本的线性几何知识，如矢量，矩阵运算等。

对于 Android 开发的基本知识，可以参见 [Android 简明开发教程](http://www.imobilebbs.com/wordpress/archives/1006) ，特别注意的是 [Android简明开发教程二：安装开发环境](http://www.imobilebbs.com/wordpress/archives/831)。本教程采用 Windows + Eclipse + Android SDK 作为开发的环境。

此外之前介绍的关于 Android OpenGL ES 开发的文章

[Android OpenGL ES 开发中的 Buffer 使用](http://www.imobilebbs.com/wordpress/archives/1706)

[Android OpenGL ES 简明开发教程](http://www.imobilebbs.com/wordpress/archives/1583)

也可以先看看，有助于学习 Android OpenGL ES 开发。

此外 Android SDK 中有关 OpenGL ES API 的开发文档

- android.opengl
- javax.microedition.khronos.egl
- javax.microedition.khronos.opengles
- java.nio  

注：上述 Android 文档基本为空，可以参见 [JSR239](http://www.imobilebbs.com/download/android/opengles/jsr239.zip) 的文档，比较详细。

和 [OpenGL ES Specification](https://www.khronos.org/opengles/1_X/) 都是学习时常用到的参考资料。