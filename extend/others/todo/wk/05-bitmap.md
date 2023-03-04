[(32条消息) Bitmap.createBitmap 导致内存溢出的考虑_隔壁小王66的博客-CSDN博客_createbitmap 崩溃](https://blog.csdn.net/qq_16131393/article/details/103458663)

```java
int bitmapSize = bitmap.getByteCount()/1024/1024
if (8 < bitmapSize) {
    Matrix matrix = new Matrix();
    //目标大小/原大小 再开方 获取缩放比例
    double scale1 = Math.sqrt(8.0 / bitmapSize);
    float scale = (float) scale1;
    matrix.postScale(scale, scale);
    bitmap = Bitmap.createBitmap(bitmap, 0 ,0,bitmap.getWidth(),bitmap.getHeight(),matrix,true);
}
```