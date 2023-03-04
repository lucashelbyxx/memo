```java
package com.example.helloworld;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.graphics.Bitmap;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.google.android.gms.vision.clearcut.LogUtils;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class FirstActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);
        setContentView(R.layout.first_layout);

        Button button1 = findViewById(R.id.button1);
        button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                View view1 = getWindow().getDecorView();
                Bitmap bitmap = testViewSnapshot(view1);
                int width = bitmap.getWidth();
                int height = bitmap.getHeight();
                System.out.println("Bitmap weight = "+width+" Bitmap height "+height);
                Bitmap bitmap1 = Bitmap.createBitmap(bitmap, 0, 100, bitmap.getWidth() , bitmap.getHeight()/2);
                //将bitMap保存到相册中
                saveBitmap(bitmap1);
                Toast.makeText(FirstActivity.this, "button1 Clicked.", Toast.LENGTH_SHORT).show();
            }
        });
    }

    //对View控件里面的内容进行截图
    private Bitmap testViewSnapshot(View view) {
        //使控件可以进行缓存
        view.setDrawingCacheEnabled(true);
        //获取缓存的 Bitmap
        Bitmap drawingCache = view.getDrawingCache();
        //复制获取的 Bitmap
        drawingCache = Bitmap.createBitmap(drawingCache);
        //关闭视图的缓存
        view.setDrawingCacheEnabled(false);

        return  drawingCache;
    }


    //保存BitMap图片到本地文件
    private void saveBitmap(Bitmap bitmap) {
        //获取需要存储到本地的路径
        File file = new File( Environment.getExternalStorageDirectory(),"/Download/");
        //如果文件夹不存在 就创建文件夹
        if(!file.exists()){
            file.mkdirs();
        }
        File path =  new File(file,System.currentTimeMillis()+".png");
        try {
            FileOutputStream fileOutputStream = new FileOutputStream(path);
            // compress - 压缩的意思  将bitmap保存到本地文件中
            bitmap.compress(Bitmap.CompressFormat.PNG, 80, fileOutputStream);
            //存储完成后需要清除相关的进程
            fileOutputStream.flush();
            fileOutputStream.close();
            //保存图片后发送广播通知更新数据库
            Uri uri = Uri.fromFile(path);
            Intent intent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
            intent.setData(uri);
            sendBroadcast(intent);
            Toast.makeText(this, "保存成功", Toast.LENGTH_SHORT).show();
        } catch (FileNotFoundException e) {
            Toast.makeText(this, "error", Toast.LENGTH_SHORT).show();
        } catch (IOException e) {
            Toast.makeText(this, "error1", Toast.LENGTH_SHORT).show();
        }

    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main,menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
//        if (R.id.add_item == item.getItemId()) {
//            Toast.makeText(this,"add",Toast.LENGTH_SHORT).show();
//        }
//        if (R.id.remove_item == item.getItemId()) {
//            Toast.makeText(this,"remove",Toast.LENGTH_SHORT).show();
//        }
        switch (item.getItemId()){
            case R.id.add_item:
                Toast.makeText(this,"add_item",Toast.LENGTH_SHORT).show();
                break;
            case R.id.remove_item:
                Toast.makeText(this,"remove_item",Toast.LENGTH_SHORT).show();
                break;
        }
        return true;
    }
}
```

