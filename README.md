# DailyPractice
This is their own daily practice of small
# 学习记录
这是自己在日常Android工作中遇到的一些问题与解决方法
## GSYVideoPlayer视频播放开源库使用时遇到的问题
[GSYVideoPlayer的github地址](https://github.com/CarGuo/GSYVideoPlayer)
### 跳转URL播放视频
主要代码
"'
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
'"
其中URL来源
'''
url = customInputDialog.getEditMessage().getText().toString();
'''
### 退出播放页面时获取观看进度下次进入自动续播
获取退出播放页面时播放进度
'''
i = video.getCurrentPositionWhenPlaying();
        SharedPreferences sharedPreferences = getSharedPreferences("aaaa", Context.MODE_PRIVATE);
        SharedPreferences.Editor editor = sharedPreferences.edit();//获取编辑器
        editor.putInt("time", i);
        editor.commit();//提交修改
'''
下次进入自动续播
'''
private void getTime() {
        SharedPreferences share=getSharedPreferences("aaaa", Context.MODE_WORLD_READABLE);
        int i=share.getInt("time",0);
        video.setSeekOnStart((long)i);
        //Log.e("222222",i+"");
    }
'''
