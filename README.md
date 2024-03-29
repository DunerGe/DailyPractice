# DailyPractice
This is their own daily practice of small
# 学习记录
这是自己在日常Android工作中遇到的一些问题与解决方法
## GSYVideoPlayer视频播放开源库使用时遇到的问题
[点击进入GSYVideoPlayer的github地址](https://github.com/CarGuo/GSYVideoPlayer)    
### 显示视频标题与返回按钮
只需要将resolveNormalVideoUI方法中的两个属性由默认的隐藏改为显示
### 设置视频封面
只需要将备注掉的setThumbImageView属性备注取消，并将自己的封面图片替换
### 跳转URL播放视频
主要代码
```
private void playVideo() {
        detailPlayer.release();
        gsyVideoOptionBuilder.setUrl(url)
                .setCacheWithPlay(cache)
                .setVideoTitle("测试视频")
                .build(detailPlayer);
        gsyVideoOptionBuilder.build(detailPlayer);
        detailPlayer.postDelayed(new Runnable() {
            @Override
            public void run() {
                detailPlayer.startPlayLogic();
            }
        }, 1000);
    }
```
其中URL来源
`url = customInputDialog.getEditMessage().getText().toString();`
### 退出播放页面时获取观看进度下次进入自动续播
获取退出播放页面时播放进度
```
i = video.getCurrentPositionWhenPlaying();
        SharedPreferences sharedPreferences = getSharedPreferences("aaaa", Context.MODE_PRIVATE);
        SharedPreferences.Editor editor = sharedPreferences.edit();//获取编辑器
        editor.putInt("time", i);
        editor.commit();//提交修改
```
下次进入自动续播
```
private void getTime() {
        SharedPreferences share=getSharedPreferences("aaaa", Context.MODE_WORLD_READABLE);
        int i=share.getInt("time",0);
        video.setSeekOnStart((long)i);
        //Log.e("222222",i+"");
    }
```
## banner广告图片轮播控件学习使用
[点击进入banner广告图片轮播控件github地址](https://github.com/youth5201314/banner)

此为项目中需要实现广告图片轮播功能采用banner控件进行实现，此处记录使用方法方便自己日后查看
### 使用步骤
以下仅为自己所采用的步骤，其他方法可参照[作者原贴](https://github.com/youth5201314/banner)
#### 依赖banner
Gradle
```
compile 'com.youth.banner:banner:1.4.10'  //最新版本
```
#### 在AndroidManifest.xml添加权限
```
<!-- if you want to load images from the internet -->
<uses-permission android:name="android.permission.INTERNET" /> 

<!-- if you want to load images from a file OR from the internet -->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```
#### 在布局文件中添加banner，可以设置自定义属性
此步骤依照个人喜好可省略，直接在Activity或者Fragment中new Banner();
```
<com.youth.banner.Banner
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/banner"
    android:layout_width="match_parent"
    android:layout_height="高度自己设置" />
```
#### 重写图片加载器
```
public class GlideImageLoader extends ImageLoader {
    @Override
    public void displayImage(Context context, Object path, ImageView imageView) {
        /**
          注意：
          1.图片加载器由自己选择，这里不限制，个人喜欢使用Glide框架，所以只提供Glide方法。
          2.返回的图片路径为Object类型，由于不能确定你到底使用的那种图片加载器，传输的到的是什么格式，
          那么这种就使用Object接收和返回，你只需要强转成你传输的类型就行，切记不要胡乱强转！
         */
        eg：
        //Glide 加载图片简单用法
        Glide.with(context).load(path).into(imageView);
    }
    
    //提供createImageView 方法，如果不用可以不重写这个方法，主要是方便自定义ImageView的创建
    @Override
    public ImageView createImageView(Context context) {
        //使用fresco，需要创建它提供的ImageView，当然你也可以用自己自定义的具有图片加载功能的ImageView
        SimpleDraweeView simpleDraweeView=new SimpleDraweeView(context);
        return simpleDraweeView;
    }
}
```
#### 在Activity或者Fragment中配置Banner
此处注意：start()方法必须放到最后执行，点击事件请放到start()前，具体请点击原作者github地址
此处提供三种使用，以个人需求自己决定
```
--------------------------简单使用-------------------------------
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Banner banner = (Banner) findViewById(R.id.banner);
    //设置图片加载器
    banner.setImageLoader(new GlideImageLoader());
    //设置图片集合
    banner.setImages(images);
    //banner设置方法全部调用完毕时最后调用
    banner.start();
}
--------------------------详细使用-------------------------------
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Banner banner = (Banner) findViewById(R.id.banner);
    //设置banner样式
    banner.setBannerStyle(BannerConfig.CIRCLE_INDICATOR_TITLE);
    //设置图片加载器
    banner.setImageLoader(new GlideImageLoader());
    //设置图片集合
    banner.setImages(images);
    //设置banner动画效果
    banner.setBannerAnimation(Transformer.DepthPage);
    //设置标题集合（当banner样式有显示title时）
    banner.setBannerTitles(titles);
    //设置自动轮播，默认为true
    banner.isAutoPlay(true);
    //设置轮播时间
    banner.setDelayTime(1500);
    //设置指示器位置（当banner模式中有指示器时）
    banner.setIndicatorGravity(BannerConfig.CENTER);
    //banner设置方法全部调用完毕时最后调用
    banner.start();
}
-----------------当然如果你想偷下懒也可以这么用--------------------
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Banner banner = (Banner) findViewById(R.id.banner);
    banner.setImages(images).setImageLoader(new GlideImageLoader()).start();
}
```
#### (依个人可选可不选)增加体验
```
//如果你需要考虑更好的体验，可以这么操作
@Override
protected void onStart() {
    super.onStart();
    //开始轮播
    banner.startAutoPlay();
}

@Override
protected void onStop() {
    super.onStop();
    //结束轮播
    banner.stopAutoPlay();
}
```
## TextView显示指定的行数并且多出的部分显示省略号
大家都知道，如果要让TextView只显示一行，多出部分用省略号代替的话非常容易，只需要为xml文件中的TextView控件添加android:singleLine="true"即可，那么如果我想让它显示多行的时候应该怎么做呢？其实也很简单，只要添加以下两个属性就可以了：    
android:maxLines="2"    
android:ellipsize="end"    
## NavigationView各项Item图标只显示灰色
解决NavigationView各项Item图标只显示灰色的方法很简单，只需要在java代码中添加
```
navView.setItemIconTintList(null);
```
## Android下的跨进程通信方式
- 四大组件间可以通过Bundle传递实现   

- 共享文件   

- Messenger, 轻量级的跨进程通信方案，和Handler使用有点类似，底层是通过AIDL实现   

- AIDL，Android接口定义语言，用于定义跨进程通讯的接口   

- ContentProvider, 常用于跨进程共享数据，比如系统的相册，音乐等   

- 使用Socket传输数据
## Java与JS通信实现
### js调用Java的方式基本有三种
- 通过schema方式，使用shouldOverrideUrlLoading方法对url协议进行解析，js使用iframe来调用native代码

- 在webview页面里直接注入原生js代码方式，使用addJavascriptInterface方法来实现，Demo中有这种实现方式

- 使用prompt,console,log,alert方式，这三个方法对js里是属性原生的，在android webview这一层是可以重写这三个方法的，一般使用pormpt，因为这个再js里使用的不多，用来和native通讯副作用比较少。
### Java调用JS基本只有一种方式就是loadUrl
- 4.4之前Native通过loadUrl来调用JS方法,只能让某个JS方法执行,但是无法获取该方法的返回值

- 4.4及之后,通过evaluateJavascript异步调用JS方法,并且能在onReceiveValue中拿到返回值   
## 图片选择框架
[PictureSelector](https://github.com/LuckSiege/PictureSelector)
## 自定义三角角标
首先是自定义标签的流程    
[自定义view详解](https://www.jianshu.com/p/d8dcfaa0f652)    
首先通过这篇文章了解自定义的详细步骤和过程，接下来是自定义三角角标    
[自定义三角角标](https://www.jianshu.com/p/d5027463208e)
需要定义旋转角度、长度、背景颜色、字体大小与颜色等问题
## android shape/layer-list实现渐变阴影效果
[](https://www.jianshu.com/p/d2aa65a91a42)
## github中文乱码解决方法
[](https://www.cnblogs.com/yanzige/p/9810333.html)
## 判断APP是否在后台运行
```
public static boolean isBackground(Context context) {
        ActivityManager activityManager = (ActivityManager) context
                .getSystemService(Context.ACTIVITY_SERVICE);
        List<ActivityManager.RunningAppProcessInfo> appProcesses = activityManager
                .getRunningAppProcesses();
        for (ActivityManager.RunningAppProcessInfo appProcess : appProcesses) {
            if (appProcess.processName.equals(context.getPackageName())) {
                /*
                BACKGROUND=400 EMPTY=500 FOREGROUND=100
                GONE=1000 PERCEPTIBLE=130 SERVICE=300 ISIBLE=200
                 */
                Log.i(context.getPackageName(), "此appimportace ="
                        + appProcess.importance
                        + ",context.getClass().getName()="
                        + context.getClass().getName());
                if (appProcess.importance != ActivityManager.RunningAppProcessInfo.IMPORTANCE_FOREGROUND) {
                    Log.i(context.getPackageName(), "处于后台"
                            + appProcess.processName);
                    return true;
                } else {
                    Log.i(context.getPackageName(), "处于前台"
                            + appProcess.processName);
                    return false;
                }
            }
        }
        return false;
    }
```
## 判断当前activity是否在运行
```
private boolean isTopActivity() {
        boolean isTop = false;
        ActivityManager am = (ActivityManager) getSystemService(ACTIVITY_SERVICE);
        ComponentName cn = am.getRunningTasks(1).get(0).topActivity;
        if (cn.getClassName().contains(TAG)) {
            isTop = true;
        }
        return isTop;
    }
```
## 上下滚动textView
### ScrrollTextView.java
```
import android.animation.ObjectAnimator;
import android.content.Context;
import android.os.Handler;
import android.support.annotation.Nullable;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.LinearLayout;
import android.widget.TextView;
 
import com.lanjinger.choiassociatedpress.R;
 
import java.util.List;
 
/**
 * 上下滚动的 textView
 */
public class ScrollTextView extends LinearLayout {
    private TextView mBannerTV1;
    private TextView mBannerTV2;
    private Handler handler;
    private boolean isShow = false;
    private int startY1, endY1, startY2, endY2;
    private Runnable runnable;
    private List<String> list;
    private int position = 0;
    private int offsetY = 100;
    private boolean hasPostRunnable = false;
 
    public ScrollTextView(Context context) {
        this(context, null);
    }
 
    public ScrollTextView(Context context, @Nullable AttributeSet attrs) {
        this(context, attrs, 0);
    }
 
    public ScrollTextView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
 
        View view = LayoutInflater.from(context).inflate(R.layout.widget_scroll_text_layout, this);
        mBannerTV1 = view.findViewById(R.id.tv_banner1);
        mBannerTV2 = view.findViewById(R.id.tv_banner2);
        handler = new Handler();
        runnable = new Runnable() {
            @Override
            public void run() {
                isShow = !isShow;
                if (position == list.size() - 1) {
                    position = 0;
                }
 
                if (isShow) {
                    mBannerTV1.setText(list.get(position++));
                    mBannerTV2.setText(list.get(position));
                } else {
                    mBannerTV2.setText(list.get(position++));
                    mBannerTV1.setText(list.get(position));
                }
 
                startY1 = isShow ? 0 : offsetY;
                endY1 = isShow ? -offsetY : 0;
                ObjectAnimator.ofFloat(mBannerTV1, "translationY", startY1, endY1).setDuration(300).start();
 
                startY2 = isShow ? offsetY : 0;
                endY2 = isShow ? 0 : -offsetY;
                ObjectAnimator.ofFloat(mBannerTV2, "translationY", startY2, endY2).setDuration(300).start();
 
                handler.postDelayed(runnable, 3000);
            }
        };
    }
 
    public List<String> getList() {
        return list;
    }
 
    public void setList(List<String> list) {
        this.list = list;
 
        //处理最后一条数据切换到第一条数据 太快的问题
        if (list.size() > 1) {
            list.add(list.get(0));
        }
    }
 
    public void startScroll() {
        mBannerTV1.setText(list.get(0));
        if (list.size() > 1) {
            if(!hasPostRunnable) {
                hasPostRunnable = true;
                //处理第一次进入 第一条数据切换第二条 太快的问题
                handler.postDelayed(runnable,3000);
            }
        } else {
            //只有一条数据不进行滚动
            hasPostRunnable = false;
//            mBannerTV1.setText(list.get(0));
        }
    }
 
    public void stopScroll() {
        handler.removeCallbacks(runnable);
        hasPostRunnable = false;
    }
 
 
}
```
### widget_scroll_text_layout.xml
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal">
 
    <TextView
        android:id="@+id/tv_banner1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_centerVertical="true"
        android:ellipsize="end"
        android:singleLine="true"
        android:textColor="@color/skin_common_title"
        android:textSize="12sp" />
 
    <TextView
        android:id="@+id/tv_banner2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_centerVertical="true"
        android:ellipsize="end"
        android:singleLine="true"
        android:textColor="@color/skin_common_title"
        android:textSize="12sp" />
 
</RelativeLayout>
```
### 具体调用
```
ScrollTextView marqueeText = headView.findViewById(R.id.xxxx);
 
        List<String> demographicsList = new ArrayList<>();
 
        demographicsList.add("今日测试股票 上市");
        demographicsList.add("今日科伦药业 中国人保 可申购");
        demographicsList.add("今日中国平安 上市");
 
        marqueeText.setList(demographicsList);
        marqueeText.startScroll();
```

### textview输入数字
```
android:inputType="number|numberDecimal"
```
### EditView输入监听，关闭软键盘
 ```
 TextWatcher textWatcher = new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int       start, int count, int after) {
            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
            }

            @Override
            public void afterTextChanged(Editable s) {
                InputMethodManager imm = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);
                imm.hideSoftInputFromWindow(getWindow().getDecorView().getWindowToken(), 0);
            }
        };
        dsName.addTextChangedListener(textWatcher);
 ```
### 密码输入切换显示与隐藏
```
   //密碼是否可見
    private boolean isPwdVisible = false;
    /**
     * 设置密码是否可见
     */
    private void setPasswordVisible() {
        //ImageView点击事件
        imageView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //修改密码是否可见的状态
                isPwdVisible = !isPwdVisible;
                //設置密碼是否可見
                if (isPwdVisible) {
                    //设置密码为明文，并更改眼睛图标
                    editText1.setTransformationMethod(HideReturnsTransformationMethod.getInstance());
                    imageView.setImageResource(R.mipmap.show_pwd_image);
                } else {
                    //设置密码为暗文，并更改眼睛图标
                    editText1.setTransformationMethod(PasswordTransformationMethod.getInstance());
                    imageView.setImageResource(R.mipmap.hide_pwd_image);
                }
                //设置光标位置的代码需放在设置明暗文的代码后面
                editText1.setSelection(editText1.getText().toString().length());
            }
        });
```
