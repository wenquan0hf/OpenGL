# 绘制线段 Line Segment  
  
创建一个 DrawLine Activity，定义四个顶点：
  
```
float vertexArray[] = {
 -0.8f, -0.4f * 1.732f, 0.0f,
 -0.4f, 0.4f * 1.732f, 0.0f,
 0.0f, -0.4f * 1.732f, 0.0f,
 0.4f, 0.4f * 1.732f, 0.0f,
};  
``` 

分别以三种模式 GL\_LINES，GL\_LINE\_STRIP，GL\_LINE\_LOOP 来绘制直线：
  
```
public void DrawScene(GL10 gl) {
 super.DrawScene(gl);
 ByteBuffer vbb
 = ByteBuffer.allocateDirect(vertexArray.length*4);
 vbb.order(ByteOrder.nativeOrder());
 FloatBuffer vertex = vbb.asFloatBuffer();
 vertex.put(vertexArray);
 vertex.position(0);
 gl.glLoadIdentity();
 gl.glTranslatef(0, 0, -4);
 gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
 gl.glVertexPointer(3, GL10.GL_FLOAT, 0, vertex);
 index++;
 index%=10;
 switch(index){
 case 0:
 case 1:
 case 2:
 gl.glColor4f(1.0f, 0.0f, 0.0f, 1.0f);
 gl.glDrawArrays(GL10.GL_LINES, 0, 4);
 break;
 case 3:
 case 4:
 case 5:
 gl.glColor4f(0.0f, 1.0f, 0.0f, 1.0f);
 gl.glDrawArrays(GL10.GL_LINE_STRIP, 0, 4);
 break;
 case 6:
 case 7:
 case 8:
 case 9:
 gl.glColor4f(0.0f, 0.0f, 1.0f, 1.0f);
 gl.glDrawArrays(GL10.GL_LINE_LOOP, 0, 4);
 break;
 }
 gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
}  
```  

这里 index 的目的是为了延迟一下显示（更好的做法是使用固定时间间隔）。前面说过 GLSurfaceView 的渲染模式有两种，一种是连续不断的更新屏幕，另一种为 on-demand，只有在调用 requestRender() 在更新屏幕。 缺省为 RENDERMODE_CONTINUOUSLY 持续刷新屏幕。

OpenGLDemos 使用的是缺省的 RENDERMODE\_CONTINUOUSLY 持续刷新屏幕 ，因此 Activity 的 drawScene 会不断的执行。本例中屏幕上顺序以红，绿，蓝色显示 LINES, LINE\_STRIP,LINE\_LOOP。  
  
![](images/57.png) 



 