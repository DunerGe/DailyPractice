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
## 低功耗蓝牙BLE开发学习
