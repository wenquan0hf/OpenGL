# 三維坐標系及坐標變換初步 

  
OpenGL ES 圖形庫最終的結果是在二維平面上顯示3D物體（常稱作模型Model)這是因為目前的打部分顯示器還只能顯示二維圖形。但我們在構造3D模型時必須要有空間現象能力，所有對模型的描述還是使用三維坐標。也就是使用3D建模，而有 OpenGL ES 庫來完成從3D模型到二維屏幕上的顯示。

這個過程可以分成三個部分：

* 坐標變換，坐標變換通過使用變換矩陣來描述，因此學習3D繪圖需要了解一些空間幾何，矩陣運算的知識。三維坐標通常使用齊次坐標來定義。變換矩陣操作可以分為視角（Viewing），模型（Modeling）和投影（Projection）操作，這些操作可以有選擇，平移，縮放，正側投影，透視投影等。
* 由於最終的3D模型需要在一個矩形窗口中顯示，因此在這個窗口之外的部分需要裁剪掉以提高繪圖效率，對應3D圖形，裁剪是將處在剪切面之外的部分扔掉。
* 在最終繪製到顯示器（2D屏幕），需要建立起變換後的坐標和屏幕像素之間的對應關係，這通常稱為「視窗」坐標變換(Viewport) transformation.  
* 
如果我們使用照相機拍照的過程做類比，可以更好的理解3D 坐標變換的過程。

![](images/61.png) 

1. 拍照時第一步是架起三角架並把相機的鏡頭指向需要拍攝的場景，對應到3D變換為 viewing transformation （平移或是選擇Camera ）
2. 然後攝影師可能需要調整被拍場景中某個物體的角度，位置，比如攝影師給架好三角架後給你拍照時，可以要讓你調整站立姿勢或是位置。對應到3D繪製就是 Modeling transformation （調整所繪模型的位置，角度或是縮放比例）。
3. 之後攝影師可以需要調整鏡頭取景（拉近或是拍攝遠景），相機取景框所能拍攝的場景會隨鏡頭的伸縮而變換，對應到3D繪圖則為 Projection transformation(裁剪投影場景）。
4. 按下快門後，對於數碼相機可以直接在屏幕上顯示當前拍攝的照片，一般可以充滿整個屏幕（相當於將坐標做規範化處理NDC），此時你可以使用縮放放大功能顯示照片的部分。對應到3D繪圖相當於viewport transformation （可以對最終的圖像縮放顯示等）  

下圖為 Android OpenGL ES坐標變換的過程：

![](images/62.png) 

* Object Coordinate System: 也稱作Local coordinate System，用來定義一個模型本身的坐標系。
* World Coordinate System: 3d 虛擬世界中的絕對坐標系，定義好這個坐標系的原點就可以用來描述模型的實現的位置，Camera 的位置，光源的位置。
* View Coordinate System: 一般使用用來計算光照效果。
* Clip Coordinate System:  對3D場景使用投影變換裁剪視錐。
* Normalized device coordinate System (NDC): 規範後坐標系。
* Windows Coordinate System: 最後屏幕顯示的2D坐標系統，一般原點定義在屏幕左上角。
  
![](images/63.png) 

對於 Viewing transformation (平移，選擇相機）和 Modeling transformation（平移，選擇模型）可以合併起來看，只是應為向左移動相機，和相機不同將模型右移的效果是等效的。

所以在 OpenGL ES 中，  

* 使用GL10.GL_MODELVIEW 來同時指定viewing matrix 和modeling matrix.
* 使用GL10.GL_PROJECTION 指定投影變換，OpenGL 支持透視投影和正側投影（一般用於工程製圖）。
* 使用glViewport 指定 Viewport 變換。  

此時再看看下面的代碼，就不是很難理解了，後面就逐步介紹各種坐標變換。
  
```
public void onSurfaceChanged(GL10 gl, int width, int height) {
// Sets the current view port to the new size.
gl.glViewport(0, 0, width, height);
// Select the projection matrix
gl.glMatrixMode(GL10.GL_PROJECTION);
// Reset the projection matrix
gl.glLoadIdentity();
// Calculate the aspect ratio of the window
GLU.gluPerspective(gl, 45.0f, (float) width / (float) height, 0.1f, 100.0f);
// Select the modelview matrix
 gl.glMatrixMode(GL10.GL_MODELVIEW);
// Reset the modelview matrix
 gl.glLoadIdentity();
}  
```