# GLSurfaceView  
  
Android OpenGL ES 相关的包主要定义在

- javax.microedition.khronos.opengles    GL 绘图指令
- javax.microedition.khronos.egl               EGL 管理Display, surface等
- android.opengl    Android GL辅助类，连接OpenGL 与Android View，Activity
- javax.nio Buffer类  
 
其中 GLSurfaceView 为 android.opengl 包中核心类：

- 起到连接 OpenGL ES 与 Android 的 View 层次结构之间的桥梁作用。
- 使得 Open GL ES 库适应于 Anndroid 系统的 Activity 生命周期。
- 使得选择合适的 Frame buffer 像素格式变得容易。
- 创建和管理单独绘图线程以达到平滑动画效果。
- 提供了方便使用的调试工具来跟踪 OpenGL ES 函数调用以帮助检查错误。  

使用过 Java ME ,JSR 239 开发过 OpenGL ES 可以看到 Android 包javax.microedition.khronos.egl ,javax.microedition.khronos.opengles 和 JSR239 基本一致，因此理论上不使用 android.opengl 包中的类也可以开发 Android 上 OpenGL ES 应用，但此时就需要自己使用 EGL 来管理 Display，Context，Surfaces 的创建，释放，捆绑，可以参见 [Android OpenGL ES 开发教程(5)：关于EGL](http://www.imobilebbs.com/wordpress/archives/1873) 。

使用 EGL 实现 GLSurfaceView 一个可能的实现如下：

```
class GLSurfaceView extends SurfaceView
 implements SurfaceHolder.Callback, Runnable {
 public GLSurfaceView(Context context) {
 super(context);
 mHolder = getHolder();
 mHolder.addCallback(this);
 mHolder.setType(SurfaceHolder.SURFACE_TYPE_GPU);
 }
 public void setRenderer(Renderer renderer) {
 mRenderer = renderer;
 }
 public void surfaceCreated(SurfaceHolder holder) {
 }
 public void surfaceDestroyed(SurfaceHolder holder) {
 running = false;
 try {
 thread.join();
 } catch (InterruptedException e) {
 }
 thread = null;
 }
 public void surfaceChanged(SurfaceHolder holder,
 int format, int w, int h) {
 synchronized(this){
 mWidth = w;
 mHeight = h;
 thread = new Thread(this);
 thread.start();
 }
 }
 public interface Renderer {
 void EGLCreate(SurfaceHolder holder);
 void EGLDestroy();
 int Initialize(int width, int height);
 void DrawScene(int width, int height);
 }
 public void run() {
 synchronized(this) {
 mRenderer.EGLCreate(mHolder);
 mRenderer.Initialize(mWidth, mHeight);
 running=true;
 while (running) {
 mRenderer.DrawScene(mWidth, mHeight);
 }
 mRenderer.EGLDestroy();
 }
 }
 private SurfaceHolder mHolder;
 private Thread thread;
 private boolean running;
 private Renderer mRenderer;
 private int mWidth;
 private int mHeight;
}
class GLRenderer implements GLSurfaceView.Renderer {
 public GLRenderer() 
 }
 public int Initialize(int width, int height){
 gl.glClearColor(1.0f, 0.0f, 0.0f, 0.0f);
 return 1;
 }
 public void DrawScene(int width, int height){
 gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
 egl.eglSwapBuffers(eglDisplay, eglSurface);
 }
 public void EGLCreate(SurfaceHolder holder){
 int[] num_config = new int[1];
 EGLConfig[] configs = new EGLConfig[1];
 int[] configSpec = {
 EGL10.EGL_RED_SIZE,            8,
 EGL10.EGL_GREEN_SIZE,        8,
 EGL10.EGL_BLUE_SIZE,        8,
 EGL10.EGL_SURFACE_TYPE,     EGL10.EGL_WINDOW_BIT,
 EGL10.EGL_NONE
 };
 this.egl = (EGL10) EGLContext.getEGL();
 eglDisplay = this.egl.eglGetDisplay(EGL10.EGL_DEFAULT_DISPLAY);
 this.egl.eglInitialize(eglDisplay, null);
 this.egl.eglChooseConfig(eglDisplay, configSpec,
 configs, 1, num_config);
 eglConfig = configs[0];
 eglContext = this.egl.eglCreateContext(eglDisplay, eglConfig,
 EGL10.EGL_NO_CONTEXT, null);
 eglSurface = this.egl.eglCreateWindowSurface(eglDisplay,
 eglConfig, holder, null);
 this.egl.eglMakeCurrent(eglDisplay, eglSurface,
 eglSurface, eglContext);
 gl = (GL10)eglContext.getGL();
 }
 public void EGLDestroy(){
 if (eglSurface != null) {
 egl.eglMakeCurrent(eglDisplay, EGL10.EGL_NO_SURFACE,
 EGL10.EGL_NO_SURFACE, EGL10.EGL_NO_CONTEXT);
 egl.eglDestroySurface(eglDisplay, eglSurface);
 eglSurface = null;
 }
 if (eglContext != null) {
 egl.eglDestroyContext(eglDisplay, eglContext);
 eglContext = null;
 }
 if (eglDisplay != null) {
 egl.eglTerminate(eglDisplay);
 eglDisplay = null;
 }
 }
 private EGL10 egl;
 private GL10 gl;
 private EGLDisplay eglDisplay;
 private EGLConfig  eglConfig;
 private EGLContext eglContext;
 private EGLSurface eglSurface;
}  
```  

可以看到需要派生 SurfaceView ，并手工创建，销毁 Display，Context，工作繁琐。

使用 GLSurfaceView 内部提供了上面类似的实现，对于大部分应用只需调用一个方法来设置 OpenGLView 用到的 GLSurfaceView.Renderer。

```
public void  setRenderer(GLSurfaceView.Renderer renderer)   
```  

GLSurfaceView.Renderer 定义了一个统一图形绘制的接口，它定义了如下三个接口函数：

```
// Called when the surface is created or recreated.
public void onSurfaceCreated(GL10 gl, EGLConfig config)
// Called to draw the current frame.
public void onDrawFrame(GL10 gl)
// Called when the surface changed size.
public void onSurfaceChanged(GL10 gl, int width, int height)  
```  

- onSurfaceCreated：在这个方法中主要用来设置一些绘制时不常变化的参数，比如：背景色，是否打开 z-buffer等。
- onDrawFrame：定义实际的绘图操作。
- onSurfaceChanged：如果设备支持屏幕横向和纵向切换，这个方法将发生在横向`<->`纵向互换时。此时可以重新设置绘制的纵横比率。  
 
如果有需要，也可以通过函数来修改 GLSurfaceView 一些缺省设置：

- setDebugFlags(int) 设置 Debug 标志。
- setEGLConfigChooser (boolean) 选择一个 Config 接近 16bitRGB 颜色模式，可以打开或关闭深度(Depth)Buffer ,缺省为RGB_565 并打开至少有 16bit 的 depth Buffer。
- setEGLConfigChooser(EGLConfigChooser)  选择自定义 EGLConfigChooser。
- setEGLConfigChooser(int, int, int, int, int, int) 指定 red ,green, blue, alpha, depth ,stencil 支持的位数，缺省为 RGB_565 ,16 bit depth buffer。  

GLSurfaceView 缺省创建为 RGB_565 颜色格式的 Surface，如果需要支持透明度，可以调用 getHolder().setFormat(PixelFormat.TRANSLUCENT)。

GLSurfaceView 的渲染模式有两种，一种是连续不断的更新屏幕，另一种为 on-demand ，只有在调用 requestRender() 在更新屏幕。 缺省为 RENDERMODE_CONTINUOUSLY 持续刷新屏幕。