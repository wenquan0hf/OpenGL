# 阶段小结  
  
之前介绍了什么是 OpenGL ES ，OpenGL ES 管道的概念，什么是 EGL，Android中OpenGL ES 的开发包以及 GLSurfaceView，OpenGL ES 所支持的基本几何图形：点，线，面，已及如何使用这些基本几何通过构成较复杂的图像（20面体）。

但到目前为止还只是绘制 2D 图形，而忽略了一些重要的概念，3D 坐标系，和 3D 坐标变换，在介绍 OpenGL ES Demo 程序框架时，创建一个 OpenGLRenderer 实现 GLSurfaceView.Renderer 接口

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

我们忽略了对 glViewport，glMatrixMode，gluPerspective 以及 20 面体时 glLoadIdentity，glTranslatef，glRotatef 等方法的介绍，这些都涉及到如何来为 3D 图形构建模型，在下面的几篇将详细介绍 OpenGL ES 的坐标系和坐标变换已经如何进行3D建模。