[(28条消息) 保存bitmap到手机相册_房水手的博客-CSDN博客_bitmap保存到相册](https://blog.csdn.net/weixin_44068690/article/details/122824343)



[(28条消息) Android 分区存储之图片处理_duke_knight的博客-CSDN博客_content://media/external](https://blog.csdn.net/duke_knight/article/details/114027621)

[Android Q & Android 11存储适配(一) 基础知识点梳理 - 掘金 (juejin.cn)](https://juejin.cn/post/6854573214447140871)

[Android 存储路径Environment - 简书 (jianshu.com)](https://www.jianshu.com/p/dfaa23088f46?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)

[Android | View & Fragment & Window 的 getContext() 一定返回 Activity 吗？ - 简书 (jianshu.com)](https://www.jianshu.com/p/7d4bc7083a2f)



## 其他保存图片文章

[保存系统相册并兼容Android10及更高版本 - 掘金 (juejin.cn)](https://juejin.cn/post/6953484994496954398)

[Android保存照片到相册 - 掘金 (juejin.cn)](https://juejin.cn/post/6850418121724051470)

[(28条消息) android10 保存图片到系统相册，刷新媒体库_红地毯前吃泡面的博客-CSDN博客_android10保存图片到相册](https://blog.csdn.net/jingzz1/article/details/106272289/)



```
button1.setOnClickListener(new View.OnClickListener() {
            @RequiresApi(api = Build.VERSION_CODES.R)
            @Override
            public void onClick(View view) {
                View view1 = getWindow().getDecorView();
                Bitmap bitmap = testViewSnapshot(view1);
                int width = bitmap.getWidth();
                int height = bitmap.getHeight();
                System.out.println("Bitmap weight = "+width+" Bitmap height "+height);
                Bitmap bitmap1 = Bitmap.createBitmap(bitmap, 0, 100, bitmap.getWidth() , bitmap.getHeight()/2);
                saveImageToGallery2(view.getContext(), bitmap1);
                //将bitMap保存到相册中
                //saveBitmap(bitmap);
                Toast.makeText(FirstActivity.this, "button1 Clicked.", Toast.LENGTH_SHORT).show();
            }
        });
        

@RequiresApi(api = Build.VERSION_CODES.R)
    public static void saveImageToGallery2(Context context, Bitmap image){
        Long mImageTime = System.currentTimeMillis();
        String imageDate = new SimpleDateFormat("yyyyMMddHHmmss").format(new Date(mImageTime));
        String SCREENSHOT_FILE_NAME_TEMPLATE = "IMG%s.png";//图片名称，以"winetalk"+时间戳命名
        String mImageFileName = String.format(SCREENSHOT_FILE_NAME_TEMPLATE, imageDate);

        final ContentValues values = new ContentValues();
        values.put(MediaStore.MediaColumns.RELATIVE_PATH, Environment.DIRECTORY_DCIM
                + File.separator + "Camera"); //Environment.DIRECTORY_SCREENSHOTS:截图,图库中显示的文件夹名。"dh"
        values.put(MediaStore.MediaColumns.DISPLAY_NAME, mImageFileName);
        values.put(MediaStore.MediaColumns.MIME_TYPE, "image/jpg");
        values.put(MediaStore.MediaColumns.DATE_ADDED, mImageTime / 1000);
        values.put(MediaStore.MediaColumns.DATE_MODIFIED, mImageTime / 1000);
        values.put(MediaStore.MediaColumns.DATE_EXPIRES, DateUtils.SECOND_IN_MILLIS*5);
        System.out.println("xxxx"+(mImageTime + DateUtils.DAY_IN_MILLIS) / 1000);
        values.put(MediaStore.MediaColumns.IS_PENDING, 1);

        ContentResolver resolver = context.getContentResolver();
        final Uri uri = resolver.insert(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, values);
        try {
            // First, write the actual data for our screenshot
            try (OutputStream out = resolver.openOutputStream(uri)) {
                if (!image.compress(Bitmap.CompressFormat.JPEG, 100, out)) {
                    throw new IOException("Failed to compress");
                }
            }
            // Everything went well above, publish it!
            values.clear();
            values.put(MediaStore.MediaColumns.IS_PENDING, 0);
            // values.putNull(MediaStore.MediaColumns.DATE_EXPIRES);
            resolver.update(uri, values, null, null);
            Toast.makeText(context,"保存图片成功",
                    Toast.LENGTH_SHORT).show();
        }catch (IOException e){
            resolver.delete(uri, null);
            Toast.makeText(context,"失败",
                    Toast.LENGTH_SHORT).show();

        }
    }

        
```

