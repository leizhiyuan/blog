title: andorid 实现拖动滑动效果
tags:
  - java
  - 编程
id: 146
categories:
  - 学习笔记
date: 2012-04-12 11:05:43
---

这个大家都有过体会，就是当你左右拖动的时候，能够做到向左向右翻页。代码我写了很详细的注释。包括xml的注释，所以就不怎么解释了。先测试下代码高亮能用不.我当前用的是**WP-Syntax**插件。将就着还行吧。如果你知道更好的话。不妨给我推荐一下。
<pre lang="java">package com.android.flip;

import android.app.Activity;
import android.os.Bundle;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.view.View;
import android.view.GestureDetector.OnGestureListener;
import android.view.animation.AnimationUtils;
import android.widget.ImageView;
import android.widget.ViewFlipper;

/**
 * Android实现左右滑动效果
 * @author byStander
 *
 */</pre>
<!--more-->
<pre lang="java">public class MainActivity extends Activity implements OnGestureListener {  //实现了手势监听类
    private ViewFlipper flipper;  //切换的类。可以实现多个view之间的切换，也可以说设置间隔时间自动切换
    private GestureDetector detector;  //相应手势请求的类。包括触摸

    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        detector = new GestureDetector(this);  //以手势监听类来初始化它
        flipper = (ViewFlipper) this.findViewById(R.id.ViewFlipper1);
//以下是设置内部的view。调用自写函数
        flipper.addView(addTextView(R.drawable.one));
        flipper.addView(addTextView(R.drawable.two));
        flipper.addView(addTextView(R.drawable.three));
        flipper.addView(addTextView(R.drawable.four));
        flipper.addView(addTextView(R.drawable.five));
    }
    //以id为参数设置图片view
    private View addTextView(int id) {
        ImageView iv = new ImageView(this); //参数是上下文
        iv.setImageResource(id);
        return iv;
    }
    //当触摸事件没有被处理的时候调用此。
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        // TODO Auto-generated method stub
        return this.detector.onTouchEvent(event);
    }
    //当手指按下的时候触发。所有的其他时间都必须先调用此方法。
    @Override
    public boolean onDown(MotionEvent e) {
        // TODO Auto-generated method stub
        return false;
    }

    @Override
    public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
            float velocityY) {
        if (e1.getX() - e2.getX() &gt; 120) {//设置当翻页产生的距离大于120的时候才触发翻页效果
            this.flipper.setInAnimation(AnimationUtils.loadAnimation(this, R.anim.push_left_in));
            this.flipper.setOutAnimation(AnimationUtils.loadAnimation(this, R.anim.push_left_out));
            this.flipper.showNext();//设置显示下一个。
            return true;
        } else if (e1.getX() - e2.getX() &lt; -120) {
            this.flipper.setInAnimation(AnimationUtils.loadAnimation(this, R.anim.push_right_in));
            this.flipper.setOutAnimation(AnimationUtils.loadAnimation(this, R.anim.push_right_out));
            this.flipper.showPrevious();//否则显示前一个
            return true;
        }

        return false;
    }

    @Override
    public void onLongPress(MotionEvent e) {
        // TODO Auto-generated method stub

    }

    @Override
    public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX,
            float distanceY) {
        // TODO Auto-generated method stub
        return false;
    }

    @Override
    public void onShowPress(MotionEvent e) {
        // TODO Auto-generated method stub

    }

    @Override
    public boolean onSingleTapUp(MotionEvent e) {
        // TODO Auto-generated method stub
        return false;
    }
}</pre>
本文参考:

1.[http://www.cnblogs.com/hanyonglu/archive/2012/02/13/2349827.html](http://www.cnblogs.com/hanyonglu/archive/2012/02/13/2349827.html)

2.[https://developer.android.com/resources/samples/ApiDemos/index.html](https://developer.android.com/resources/samples/ApiDemos/index.html)
具体工程源码稍后发布。因为115网盘暂时不能分享