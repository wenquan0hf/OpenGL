# 构造  

在 Andorid 平台上构造一个 OpenGL View 非常简单，主要有两方面的工作：

## GLSurfaceView

Android 平台提供的 OpenGL ES API 主要定义在包android.opengl,javax.microedition.khronos.egl,javax.microedition.khronos.opengles,java.nio 等几个包中，其中类 GLSurfaceView 为这些包中的核心类：

- 起到连接 OpenGL ES与Android 的 View 层次结构之间的桥梁作用。
- 使得 Open GL ES 库适应于 Anndroid 系统的 Activity 生命周期。
- 使得选择合适的 Frame buffer 像素格式变得容易。
- 创建和管理单独绘图线程以达到平滑动画效果。
- 提供了方便使用的调试工具来跟踪 OpenGL ES 函数调用以帮助检查错误。  

因此编写 OpenGL ES 应用的起始点是从类 GLSurfaceView 开始，设置 GLSurfaceView  只需调用一个方法来设置 OpenGLView 用到的 GLSurfaceView.Renderer.   

```
public void  setRenderer(GLSurfaceView.Renderer renderer)  
```   

## GLSurfaceView.Renderer

GLSurfaceView.Renderer 定义了一个统一图形绘制的接口，它定义了如下三个接口函数：  

```
Called when the surface is created or recreated.
public void onSurfaceCreated(GL10 gl, EGLConfig config)
// Called to draw the current frame.
public void onDrawFrame(GL10 gl)
// Called when the surface changed size.
public void onSurfaceChanged(GL10 gl, int width, int height)  
```  

- onSurfaceCreated：在这个方法中主要用来设置一些绘制时不常变化的参数，比如：背景色，是否打开 z-buffer 等。
- onDrawFrame：定义实际的绘图操作。
- onSurfaceChanged：如果设备支持屏幕横向和纵向切换，这个方法将发生在横向 `<->` 纵向互换时。此时可以重新设置绘制的纵横比率。  

有了上面的基本定义，可以写出一个 OpenGL ES 应用的通用框架。

创建一个新的 Android 项目：OpenGLESTutorial, 在项目在添加两个类 TutorialPartI.java 和 OpenGLRenderer.java

具体代码如下：

`TutorialPartI.java`

```
public class TutorialPartI extends Activity {
// Called when the activity is first created.
 @Override
public void onCreate(Bundle savedInstanceState) {
 super.onCreate(savedInstanceState);
 this.requestWindowFeature(Window.FEATURE_NO_TITLE); // (NEW)
 getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
 WindowManager.LayoutParams.FLAG_FULLSCREEN); // (NEW)
 GLSurfaceView view = new GLSurfaceView(this);
 view.setRenderer(new OpenGLRenderer());
 setContentView(view);
 }
}  
```  

OpenGLRenderer.java

```
public class OpenGLRenderer implements Renderer {
 public void onSurfaceCreated(GL10 gl, EGLConfig config) {
 // Set the background color to black ( rgba ).
 gl.glClearColor(0.0f, 0.0f, 0.0f, 0.5f);  // OpenGL docs
 // Enable Smooth Shading, default not really needed.
 gl.glShadeModel(GL10.GL_SMOOTH);// OpenGL docs.
 // Depth buffer setup.
 gl.glClearDepthf(1.0f);// OpenGL docs.
 // Enables depth testing.
 gl.glEnable(GL10.GL_DEPTH_TEST);// OpenGL docs.
 // The type of depth testing to do.
 gl.glDepthFunc(GL10.GL_LEQUAL);// OpenGL docs.
 // Really nice perspective calculations.
 gl.glHint(GL10.GL_PERSPECTIVE_CORRECTION_HINT, // OpenGL docs.
 GL10.GL_NICEST);
 }
 public void onDrawFrame(GL10 gl) {
 // Clears the screen and depth buffer.
 gl.glClear(GL10.GL_COLOR_BUFFER_BIT | // OpenGL docs.
 GL10.GL_DEPTH_BUFFER_BIT);
 }
 public void onSurfaceChanged(GL10 gl, int width, int height) {
 // Sets the current view port to the new size.
 gl.glViewport(0, 0, width, height);// OpenGL docs.
 // Select the projection matrix
 gl.glMatrixMode(GL10.GL_PROJECTION);// OpenGL docs.
 // Reset the projection matrix
 gl.glLoadIdentity();// OpenGL docs.
 // Calculate the aspect ratio of the window
 GLU.gluPerspective(gl, 45.0f,
 (float) width / (float) height,
 0.1f, 100.0f);
 // Select the modelview matrix
 gl.glMatrixMode(GL10.GL_MODELVIEW);// OpenGL docs.
 // Reset the modelview matrix
 gl.glLoadIdentity();// OpenGL docs.
 }
}  
```  

编译后运行，屏幕显示一个黑色的全屏。这两个类定义了 Android OpenGL ES 应用的最基本的类和方法，可以看作是 OpenGL ES 的”Hello ,world”应用，后面将逐渐丰富这个例子来画出 3D 图型。

框架代码[下载](http://www.imobilebbs.com/download/android/opengles/OpenGLESTutorialTemplate.zip)：可以作为你自己的 OpenGL 3D 的初始代码。