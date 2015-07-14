# 阶段小结  
  
之前介绍了什么是 OpenGL ES ，OpenGL ES 管道的概念，什么是 EGL，Android中OpenGL ES 的开发包以及 GLSurfaceView，OpenGL ES 所支持的基本几何图形：点，线，面，已及如何使用这些基本几何通过构成较复杂的图像（20面体）。

* [Android OpenGL ES 开发教程(1)：导言](http://www.imobilebbs.com/wordpress/archives/1722)
* [Android OpenGL ES 开发教程(2)：关于 OpenGL ES](http://www.imobilebbs.com/wordpress/archives/1848)
* [Android OpenGL ES 开发教程(3)：OpenGL ES 管道(Pipeline)](http://www.imobilebbs.com/wordpress/archives/1854)
* [Android OpenGL ES 开发教程(4)：OpenGL ES API 命名习惯](http://www.imobilebbs.com/wordpress/archives/1859)
* [Android OpenGL ES 开发教程(5)：关于 EGL](http://www.imobilebbs.com/wordpress/archives/1873)
* [Android OpenGL ES 开发教程(6)：GLSurfaceView](http://www.imobilebbs.com/wordpress/archives/1889)
* [Android OpenGL ES 开发教程(7)：创建实例应用 OpenGLDemos 程序框架](http://www.imobilebbs.com/wordpress/archives/1892)
* [Android OpenGL ES 开发教程(8)：基本几何图形定义](http://www.imobilebbs.com/wordpress/archives/1938)
* [Android OpenGL ES 开发教程(9)：绘制点 Point](http://www.imobilebbs.com/wordpress/archives/1951)
* [Android OpenGL ES 开发教程(10)：绘制线段 Line Segment](http://www.imobilebbs.com/wordpress/archives/1961)
* [Android OpenGL ES 开发教程(11)：绘制三角形 Triangle](http://www.imobilebbs.com/wordpress/archives/1966)
* [Android OpenGL ES 开发教程(12)：绘制一个20面体](http://www.imobilebbs.com/wordpress/archives/1970)  

但到目前为止还只是绘制2D图形，而忽略了一些重要的概念，3D坐标系，和3D坐标变换，在介绍OpenGL ES Demo 程序框架时，创建一个 OpenGLRenderer 实现 GLSurfaceView.Renderer接口

```
public void onSurfaceChanged(GL10 gl, int width, int height) {
 // Sets the current view port to the new size.
 gl.glViewport(0, 0, width, height);
 // Select the projection matrix
 gl.glMatrixMode(GL10.GL_PROJECTION);
 // Reset the projection matrix
 gl.glLoadIdentity();
 // Calculate the aspect ratio of the window
 GLU.gluPerspective(gl, 45.0f,
 (float) width / (float) height,
 0.1f, 100.0f);
 // Select the modelview matrix
 gl.glMatrixMode(GL10.GL_MODELVIEW);
 // Reset the modelview matrix
 gl.glLoadIdentity();
 }
}  
```  

我们忽略了对 glViewport，glMatrixMode，gluPerspective 以及20面体时glLoadIdentity，glTranslatef,glRotatef等方法的介绍，这些都涉及到如何来为3D图形构建模型，在下面的几篇将详细介绍 OpenGL ES的坐标系和坐标变换已经如何进行3D建模。