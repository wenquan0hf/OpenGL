# OpenGL ES API 命名习惯  
  
OpenGL ES 是个跨平台的 3D 图形开发包规范，最常见的实现是采用C语言实现的，Android OpenGL ES 实现上是使用 Java 语言对底层的 C 接口进行了封装，因此在 `android.opengl javax.microedition.khronos.egl` `javax.microedition.khronos.opengles` 包中定义的 OpenGL 相关的类和方法带有很强的 C 语言色彩。

- 定义的常量都以 GL_ 为前缀。比如 `GL10.GL\_COLOR\_BUFFER\_BIT`
- OpenGL ES 指令以 gl 开头 ，比如 `gl.glClearColor`
- 某些 OpenGL 指令以 3f 或 4f 结尾，3 和 4 代表参数的个数，f 代表参数类型为浮点数，如 `gl.glColor4f` ，i,x 代表 int 如 `gl.glColor4x`
- 对应以v结尾的 OpenGL ES 指令，代表参数类型为一个矢量(Vector) ，如 glTexEnvfv
- 所有 8-bit 整数对应到 byte 类型，16-bit 对应到 short 类型，32-bit 整数(包括 GLFixed)对应到 int类型，而所有 32-bit 浮点数对应到 float 类型。
- GL\_TRUE,GL\_FALSE 对应到 boolean类型
- C 字符串((char*)) 对应到 Java 的 UTF-8 字符串。  

在前面 [Android OpenGL ES 开发中的 Buffer 使用](http://www.imobilebbs.com/wordpress/archives/1706) 说过 OpenGL ES 说过为了提高性能，通常将顶点，颜色等值存放在 java.nio 包中定义的 Buffer 类中。下表列出了 OpenGL ES 指令后缀，Java 类型，Java Buffer(java.nio) 类型的对照表  
  
![](images/43.png)

如下面代码将为顶点指定 color 值，使用 FloatBuffer 来存放顶点的 Color 数组
  
```
// The colors mapped to the vertices.
float[] colors = {
1f, 0f, 0f, 1f, // vertex 0 red
0f, 1f, 0f, 1f, // vertex 1 green
0f, 0f, 1f, 1f, // vertex 2 blue
1f, 0f, 1f, 1f, // vertex 3 magenta
};  
...
// float has 4 bytes, colors (RGBA) * 4 bytes
ByteBuffer cbb
= ByteBuffer.allocateDirect(colors.length * 4);
cbb.order(ByteOrder.nativeOrder());
colorBuffer = cbb.asFloatBuffer();
colorBuffer.put(colors);
colorBuffer.position(0);  
```