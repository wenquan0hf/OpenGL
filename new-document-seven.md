# 材質渲染  
  
前面討論了如何給3D圖形染色，更一般的情況是使用點陣圖來給 Mesh 上色（渲染材質）。主要步驟如下：

## 創建 Bitmap 對象

使用材質渲染，首先需要構造用來渲染的 Bitmap 對象，Bitmap 對象可以從資源文件中讀取或是從網路下載或是使用代碼構造。為簡單起見，本例從資源中讀取：

```
Bitmap bitmap = BitmapFactory.decodeResource(contect.getResources(),
 R.drawable.icon);  
```  

要注意的是，有些設備對使用的 Bitmap 的大小有要求，要求 Bitmap 的寬度和長度為2的幾次冪（1，2，4，8，16，32，64.。。。)，如果使用不和要求的 Bitmap 來渲染，可能只會顯示白色。

## 創建材質(Generating a texture)

下一步使用 OpenGL 庫創建一個材質(Texture)，首先是獲取一個 Texture Id。

``` 
// Create an int array with the number of textures we want,
// in this case 1.
int[] textures = new int[1];
// Tell OpenGL to generate textures.
gl.glGenTextures(1, textures, 0);  
```  

textures 中存放了創建的 Texture ID，使用同樣的 Texture Id ，也可以來刪除一個Texture：

```
// Delete a texture.
gl.glDeleteTextures(1, textures, 0)  
```  

有了 Texture Id 之後，就可以通知 OpenGL 庫使用這個 Texture：

```
gl.glBindTexture(GL10.GL_TEXTURE_2D, textures[0]);  
```  

## 設置 Texture 參數 glTexParameter

下一步需要給 Texture 填充設置參數，用來渲染的 Texture 可能比要渲染的區域大或者小，這是需要設置 Texture 需要放大或是縮小時 OpenGL 的模式：

```
// Scale up if the texture if smaller.
gl.glTexParameterf(GL10.GL_TEXTURE_2D,
 GL10.GL_TEXTURE_MAG_FILTER,
 GL10.GL_LINEAR);
// scale linearly when image smalled than texture
gl.glTexParameterf(GL10.GL_TEXTURE_2D,
 GL10.GL_TEXTURE_MIN_FILTER,
 GL10.GL_LINEAR);  
```  

常用的兩種模式為 GL10.GL_LINEAR 和 GL10.GL_NEAREST。

需要比較清晰的圖像使用 GL10.GL_NEAREST：  
  
![](images/34.png)

而使用 GL10.GL_LINEAR 則會得到一個較模糊的圖像：  
  
![](images/35.png)

## UV Mapping

下一步要告知 OpenGL 庫如何將 Bitmap 的像素映射到 Mesh 上。這可以分為兩步來完成：

## 定義UV坐標

UV Mapping 指將 Bitmap 的像素映射到 Mesh 上的頂點。UV坐標定義為左上角（0，0），右下角（1，1）（因為使用的2D Texture)，下圖坐標顯示了UV坐標，右邊為我們需要染色的平面的頂點順序：

為了能正確的匹配，需要把 UV 坐標中的（0，1）映射到頂點0，（1，1）映射到頂點2等等。

```  
float textureCoordinates[] = {0.0f, 1.0f,
 1.0f, 1.0f,
 0.0f, 0.0f,
 1.0f, 0.0f };  
```  
  
![](images/36.png)  

如果使用如下坐標定義：

```  
float textureCoordinates[] = {0.0f, 0.5f,
 0.5f, 0.5f,
 0.0f, 0.0f,
 0.5f, 0.0f };  
```  
  
![](images/37.png)  

Texture 匹配到 Plane 的左上角部分。

而

```
float textureCoordinates[] = {0.0f, 2.0f,
 2.0f, 2.0f,
 0.0f, 0.0f,
 2.0f, 0.0f };  
```  

將使用一些不存在的 Texture 去渲染平面(UV坐標為0，0-1，1 而 (0,0)-(2,2)定義超過 UV定義的大小），這時需要告訴 OpenGL 庫如何去渲染這些不存在的 Texture 部分。

有兩種設置

* GL_REPEAT 重複Texture。
* GL_CLAMP_TO_EDGE 只靠邊線繪製一次。  
* 
下面有四種不同組合：   
  
![](images/38.png)

本例使用如下配置：

```  
gl.glTexParameterf(GL10.GL_TEXTURE_2D,
 GL10.GL_TEXTURE_WRAP_S,
 GL10.GL_REPEAT);
gl.glTexParameterf(GL10.GL_TEXTURE_2D,
 GL10.GL_TEXTURE_WRAP_T,
 GL10.GL_REPEAT);  
```  

然後是將 Bitmap 資源和 Texture 綁定起來：

```
GLUtils.texImage2D(GL10.GL_TEXTURE_2D, 0, bitmap, 0);  
```  

## 使用Texture

為了能夠使用上面定義的 Texture，需要創建一 Buffer 來存儲UV坐標：

```
FloatBuffer byteBuf = ByteBuffer.allocateDirect(texture.length * 4);
byteBuf.order(ByteOrder.nativeOrder());
textureBuffer = byteBuf.asFloatBuffer();
textureBuffer.put(textureCoordinates);
textureBuffer.position(0);  
```  

## 渲染

```
// Telling OpenGL to enable textures.
gl.glEnable(GL10.GL_TEXTURE_2D);
// Tell OpenGL where our texture is located.
gl.glBindTexture(GL10.GL_TEXTURE_2D, textures[0]);
// Tell OpenGL to enable the use of UV coordinates.
gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
// Telling OpenGL where our UV coordinates are.
gl.glTexCoordPointer(2, GL10.GL_FLOAT, 0, textureBuffer);
// ... here goes the rendering of the mesh ...
// Disable the use of UV coordinates.
gl.glDisableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
// Disable the use of textures.
gl.glDisable(GL10.GL_TEXTURE_2D);   
```  

本例代碼是在一個平面上（SimplePlane）下使用 Texture 來渲染，首先是修改 Mesh 基類，使它能夠支持定義 UV 坐標：

```
// Our UV texture buffer.
private FloatBuffer mTextureBuffer;
/**
 * Set the texture coordinates.
 *
 * @param textureCoords
 */
protected void setTextureCoordinates(float[] textureCoords) {
 // float is 4 bytes, therefore we multiply the number if
 // vertices with 4.
 ByteBuffer byteBuf = ByteBuffer.allocateDirect(
 textureCoords.length * 4);
 byteBuf.order(ByteOrder.nativeOrder());
 mTextureBuffer = byteBuf.asFloatBuffer();
 mTextureBuffer.put(textureCoords);
 mTextureBuffer.position(0);
}  
```  

並添加設置 Bitmap 和創建 Texture 的方法：

```  
// Our texture id.
private int mTextureId = -1;
// The bitmap we want to load as a texture.
private Bitmap mBitmap;
/**
 * Set the bitmap to load into a texture.
 *
 * @param bitmap
 */
public void loadBitmap(Bitmap bitmap) {
 this.mBitmap = bitmap;
 mShouldLoadTexture = true;
}
/**
 * Loads the texture.
 *
 * @param gl
 */
private void loadGLTexture(GL10 gl) {
 // Generate one texture pointer...
 int[] textures = new int[1];
 gl.glGenTextures(1, textures, 0);
 mTextureId = textures[0];
 // ...and bind it to our array
 gl.glBindTexture(GL10.GL_TEXTURE_2D, mTextureId);
 // Create Nearest Filtered Texture
 gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_MIN_FILTER,
 GL10.GL_LINEAR);
 gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_MAG_FILTER,
 GL10.GL_LINEAR);
 // Different possible texture parameters, e.g. GL10.GL_CLAMP_TO_EDGE
 gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_WRAP_S,
 GL10.GL_CLAMP_TO_EDGE);
 gl.glTexParameterf(GL10.GL_TEXTURE_2D, GL10.GL_TEXTURE_WRAP_T,
 GL10.GL_REPEAT);
 // Use the Android GLUtils to specify a two-dimensional texture image
 // from our bitmap
 GLUtils.texImage2D(GL10.GL_TEXTURE_2D, 0, mBitmap, 0);
}  
```  

最後修改 draw 方法來渲染材質：  

```
// Indicates if we need to load the texture.
private boolean mShouldLoadTexture = false;
/**
 * Render the mesh.
 *
 * @param gl
 *            the OpenGL context to render to.
 */
public void draw(GL10 gl) {
 ...
 // Smooth color
 if (mColorBuffer != null) {
 // Enable the color array buffer to be used during rendering.
 gl.glEnableClientState(GL10.GL_COLOR_ARRAY);
 gl.glColorPointer(4, GL10.GL_FLOAT, 0, mColorBuffer);
 }
 if (mShouldLoadTexture) {
 loadGLTexture(gl);
 mShouldLoadTexture = false;
 }
 if (mTextureId != -1 && mTextureBuffer != null) {
 gl.glEnable(GL10.GL_TEXTURE_2D);
 // Enable the texture state
 gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
 // Point to our buffers
 gl.glTexCoordPointer(2, GL10.GL_FLOAT, 0, mTextureBuffer);
 gl.glBindTexture(GL10.GL_TEXTURE_2D, mTextureId);
 }
 gl.glTranslatef(x, y, z);
 ...
 // Point out the where the color buffer is.
 gl.glDrawElements(GL10.GL_TRIANGLES, mNumOfIndices,
 GL10.GL_UNSIGNED_SHORT, mIndicesBuffer);
 ...
 if (mTextureId != -1 && mTextureBuffer != null) {
 gl.glDisableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
 }
 ...
}  
```  

本例使用的 SimplePlane 定義如下：

```
package se.jayway.opengl.tutorial.mesh;
/**
 * SimplePlane is a setup class for Mesh that creates a plane mesh.
 *
 * @author Per-Erik Bergman (per-erik.bergman@jayway.com)
 *
 */
public class SimplePlane extends Mesh {
 /**
 * Create a plane with a default with and height of 1 unit.
 */
 public SimplePlane() {
 this(1, 1);
 }
 /**
 * Create a plane.
 *
 * @param width
 *            the width of the plane.
 * @param height
 *            the height of the plane.
 */
 public SimplePlane(float width, float height) {
 // Mapping coordinates for the vertices
 float textureCoordinates[] = { 0.0f, 2.0f, //
 2.0f, 2.0f, //
 0.0f, 0.0f, //
 2.0f, 0.0f, //
 };
 short[] indices = new short[] { 0, 1, 2, 1, 3, 2 };
 float[] vertices = new float[] { -0.5f, -0.5f, 0.0f,
 0.5f, -0.5f, 0.0f,
 -0.5f,  0.5f, 0.0f,
 0.5f, 0.5f, 0.0f };
 setIndices(indices);
 setVertices(vertices);
 setTextureCoordinates(textureCoordinates);
 }
}  
```  
![](images/40.png)

本例示例代碼[下載](http://www.imobilebbs.com/download/android/opengles/OpenGLESTutorial4.zip) ，到本篇為止介紹了 OpenGL ES 開發的基本方法，更詳細的教程將在以後發布，後面先回到 Android ApiDemos 中 OpenGL ES 的示例。