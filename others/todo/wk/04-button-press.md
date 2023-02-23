[(32条消息) Android 实现控件按住缩小，松开放大的效果_Amarao的博客-CSDN博客_android 按住](https://amarao.blog.csdn.net/article/details/86706031?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-86706031-blog-80075345.pc_relevant_paycolumn_v3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-1-86706031-blog-80075345.pc_relevant_paycolumn_v3&utm_relevant_index=2)

[(32条消息) Android实现按住缩小 松开放大的效果_l_215851356的博客-CSDN博客](https://blog.csdn.net/l_215851356/article/details/80075345)

[Android Studio 之 Button（圆角，描边，按压效果） - MElephant - 博客园 (cnblogs.com)](https://www.cnblogs.com/hyacinthLJP/p/14307673.html)

```java
package com.example.test;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;


public class MainActivity extends AppCompatActivity implements View.OnClickListener , View.OnTouchListener{
    private View mTestBtn;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mTestBtn = (View) findViewById(R.id.testView);
        mTestBtn.setOnClickListener(this);
        mTestBtn.setOnTouchListener(this);
    }

    @Override
    public void onClick(View view) {

    }

    @Override
    public boolean onTouch(View view, MotionEvent motionEvent) {
        switch (motionEvent.getAction()) {
            case MotionEvent.ACTION_DOWN:
                if (view.getId() == R.id.testView) {
                    mTestBtn.setScaleX((float) 0.95);
                    mTestBtn.setScaleY((float) 0.95);
                }
                break;

            case MotionEvent.ACTION_UP:
                if (view.getId() == R.id.testView) {
                    mTestBtn.setScaleX(1);
                    mTestBtn.setScaleY(1);
                }
                break;
            default:
        }
        return false;
    }
}
```