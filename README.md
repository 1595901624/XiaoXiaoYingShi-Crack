# XiaoXiaoYingShi-Crack

### 小小影视优化版

##### 简介
麻X影视凉凉后，众多新的视频软件犹如雨后春笋般冒出。试用许多软件之后，发现这款小小影视还是不错的，但是软件仍有一些限制，针对其作出部分优化。
具体去除广告的细节在文末给出，**为保证大家能够长期使用此软件，对于无线观看视频次数的修改细节暂不公布，敬请见谅**！！

大家如有问题请提交issue。
 
##### 2.1.2
1. 去启动页广告，广告不会被加载(并不是简单将广告隐藏)
2. 去除启动页进入主页5s倒计时
3. 去除视频播放前的5s广告，广告不会被加载(并不是简单将广告隐藏)
4. 去除视频播放页面中间的条形广告，广告不会被加载(并不是简单将广告隐藏)
5. 去除观看视频次数的限制，不登录账号也有效


##### 捐赠二维码

**推荐通过扫码领红包的方式捐赠哟**

<figure class="half">
    <img src="https://github.com/1595901624/mhzs/blob/master/lucky.jpg?raw=true">
    &nbsp;
    <img src="https://github.com/1595901624/mhzs/blob/master/alipayc.jpg?raw=true">
    <!--&nbsp;-->
    <!--<img src="https://github.com/1595901624/mhzs/blob/master/wechatc.jpg?raw=true">-->
</figure>


### “小小影视”绿化/破解流程

#### 0x01 去首页广告

- 修改点：2

- 修改方式：启动`SplashActivity`后直接跳转到`MainActivity`。将调用倒计时方法的函数替换为跳转页面的方法。修改编译器自动生成的方法。

- 具体修改：

  * `Smali`修改

  ```Smali
  #第一处
  .method public static synthetic a(Lcom/gulu/all/ui/activity/SplashActivity;)V
      .registers 1
  
      .line 3
      #invoke-virtual {p0}, Lcom/gulu/all/ui/activity/SplashActivity;->ea()V
      invoke-virtual {p0}, Lcom/gulu/all/ui/activity/SplashActivity;->ca()V
  
      return-void
  .end method
  
  #第二处
  .method public static synthetic d(Lcom/gulu/all/ui/activity/SplashActivity;)V
      .registers 1
  
      .line 1
      #invoke-virtual {p0}, Lcom/gulu/all/ui/activity/SplashActivity;->Y()V
  
      return-void
  .end method
  
  #第三处
  .method public onRequestPermissionsResult(I[Ljava/lang/String;[I)V
      .registers 4
  
      .line 1
      invoke-super {p0, p1, p2, p3}, Landroidx/fragment/app/FragmentActivity;->onRequestPermissionsResult(I[Ljava/lang/String;[I)V
  
      .line 2
      invoke-static {p0, p1, p3}, Lcom/fanle/adlibrary/utils/ADPermissionsCheckUtils;->a(Landroid/content/Context;I[I)Z
  
      move-result p1
  
      if-eqz p1, :cond_c
  
      .line 3
      #invoke-virtual {p0}, Lcom/gulu/all/ui/activity/SplashActivity;->ea()V
  	invoke-virtual {p0}, Lcom/gulu/all/ui/activity/SplashActivity;->ca()V
  	
      :cond_c
      return-void
  .end method
  ```

  - 相关`Java`方法的含义

  ```java
  /**
  * ca方法
  * 跳转MainActivity
  */
  /* renamed from: ca */
  public /* synthetic */ void mo5115ca() {
          if (TextUtils.isEmpty(PreferencesUtil.getString("lock_screen_pwd"))) {
              if (ActivityStack.getInstance().getStackSize() <= 1) {
                  ActivityUtils.a(MainActivity.class, C0416R.C0411anim.screen_zoom_in, C0416R.C0411anim.screen_zoom_out);
              }
              finish();
              return;
          }
          ARouter.c().a(AroutePath.LOCK_SCREEN_PWD).s();
          this.f2421d.postDelayed(new C2734wT(this), 200);
      }
  
  /**
  * ea方法
  * 展示广告
  */
  /* renamed from: ea */
  public final void m2944a() {
          String a = ADUtil.a(ADPPlat.SPLASH);
          if (TextUtils.isEmpty(a)) {
              m2948d();
              return;
          }
          if (!a.equals("1")) {
              ADPlugManager.g().a(this, ADPPlat.SPLASH, a, this.mFlAd, new C2693sW(this, a));
          } else if (this.f2420c != null) {
              Glide.with(this).load(this.f2420c.getPic()).apply(new RequestOptions().placeholder(C0416R.mipmap.startup).error(C0416R.mipmap.startup)).listener(new C2669rW(this)).into(this.mImgSplash);
          } else {
              m2948d();
          }
      }
  
  /**
  * Y方法
  * 初始化倒计时方法
  */
  /* renamed from: Y */
  public final void m2948d() {
          this.mStartSkipCountDown.setVisibility(0);
          AdGroup adGroup = this.f2420c;
          int parseInt = (adGroup == null || TextUtils.isEmpty(adGroup.getCountdown())) ? 5 : Integer.parseInt(this.f2420c.getCountdown());
          this.f2422e = new CountDownUtil((long) (parseInt * 1000), 1000, this);
          this.f2422e.start();
      }
  
  /**
  * onRequestPermissionsResult方法
  */
  public void onRequestPermissionsResult(int i, String[] strArr, int[] iArr) {
          super.onRequestPermissionsResult(i, strArr, iArr);
          if (ADPermissionsCheckUtils.a(this, i, iArr)) {
              m2944a();
          }
      }
  ```

#### 0x02 去播放页广告

- 修改点：1

- 修改方式：从源头下手，修改广告工具类。将其返回内容设置为空。在`MovieDetailActivity`中不管是加载播放视频前的`5s`广告还是视频详情下方的广告都会先判断`ADUtil#a`是否为空。`ADUtil`所在的包位置为`com.fanle.adlibrary.utils`。

- 具体修改：

  - `Smali`修改

  ```Smali
  #注释掉整段，最后返回空字符串，这里可以直接返回null
  .method public static a(Lcom/fanle/adlibrary/domain/ADPPlat;)Ljava/lang/String;
    .registers 21
  
    # const/4 v0, 0x4
  
      # .line 1
      # new-array v1, v0, [Ljava/lang/String;
  
      # const-string v2, "1"
  
      # const/4 v3, 0x0
  
      # aput-object v2, v1, v3
  
      # const-string v4, "2"
  
      # const/4 v5, 0x1
  
      # aput-object v4, v1, v5
  
      # const-string v6, "3"
  
      # const/4 v7, 0x2
  
      # aput-object v6, v1, v7
  
      # const-string v8, "4"
  
      # const/4 v9, 0x3
  
      # aput-object v8, v1, v9
  
      # .line 2
      # sget-object v10, Lmk;->a:[I
  
      # invoke-virtual/range {p0 .. p0}, Ljava/lang/Enum;->ordinal()I
  
      # move-result v11
  
      # aget v10, v10, v11
  
      # const-string v11, ""
  
      # if-eq v10, v5, :cond_37
  
      # if-eq v10, v7, :cond_30
  
      # if-eq v10, v9, :cond_29
  
      # move-object v10, v11
  
      # goto :goto_3d
  
      # :cond_29
      # const-string v10, "ad3"
  
      # .line 3
      # invoke-static {v10}, Lcom/fanle/adlibrary/utils/ADSPConfig;->a(Ljava/lang/String;)Ljava/lang/String;
  
      # move-result-object v10
  
      # goto :goto_3d
  
      # :cond_30
      # const-string v10, "ad2"
  
      # .line 4
      # invoke-static {v10}, Lcom/fanle/adlibrary/utils/ADSPConfig;->a(Ljava/lang/String;)Ljava/lang/String;
  
      # move-result-object v10
  
      # goto :goto_3d
  
      # :cond_37
      # const-string v10, "ad1"
  
      # .line 5
      # invoke-static {v10}, Lcom/fanle/adlibrary/utils/ADSPConfig;->a(Ljava/lang/String;)Ljava/lang/String;
  
      # move-result-object v10
  
      # :goto_3d
      # const-string v12, "[|]"
  
      # .line 6
      # invoke-virtual {v10, v12}, Ljava/lang/String;->split(Ljava/lang/String;)[Ljava/lang/String;
  
      # move-result-object v10
  
      # if-eqz v10, :cond_95
  
      # .line 7
      # array-length v12, v10
  
      # if-lez v12, :cond_95
  
      # .line 8
      # array-length v12, v10
  
      # const/4 v13, 0x0
  
      # const/4 v14, 0x0
  
      # const/4 v15, 0x0
  
      # const/16 v16, 0x0
  
      # const/16 v17, 0x0
  
      # :goto_50
      # if-ge v13, v12, :cond_9b
  
      # aget-object v3, v10, v13
  
      # const/16 v18, -0x1
  
      # .line 9
      # invoke-virtual {v3}, Ljava/lang/String;->hashCode()I
  
      # move-result v19
  
      # packed-switch v19, :pswitch_data_e8
  
      # goto :goto_7e
  
      # :pswitch_5e
      # invoke-virtual {v3, v8}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z
  
      # move-result v3
  
      # if-eqz v3, :cond_7e
  
      # const/4 v3, 0x3
  
      # goto :goto_7f
  
      # :pswitch_66
      # invoke-virtual {v3, v6}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z
  
      # move-result v3
  
      # if-eqz v3, :cond_7e
  
      # const/4 v3, 0x2
  
      # goto :goto_7f
  
      # :pswitch_6e
      # invoke-virtual {v3, v4}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z
  
      # move-result v3
  
      # if-eqz v3, :cond_7e
  
      # const/4 v3, 0x1
  
      # goto :goto_7f
  
      # :pswitch_76
      # invoke-virtual {v3, v2}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z
  
      # move-result v3
  
      # if-eqz v3, :cond_7e
  
      # const/4 v3, 0x0
  
      # goto :goto_7f
  
      # :cond_7e
      # :goto_7e
      # const/4 v3, -0x1
  
      # :goto_7f
      # if-eqz v3, :cond_90
  
      # if-eq v3, v5, :cond_8e
  
      # if-eq v3, v7, :cond_8b
  
      # if-eq v3, v9, :cond_88
  
      # goto :goto_91
  
      # :cond_88
      # const/16 v17, 0x1
  
      # goto :goto_91
  
      # :cond_8b
      # const/16 v16, 0x1
  
      # goto :goto_91
  
      # :cond_8e
      # const/4 v15, 0x1
  
      # goto :goto_91
  
      # :cond_90
      # const/4 v14, 0x1
  
      # :goto_91
      # add-int/lit8 v13, v13, 0x1
  
      # const/4 v3, 0x0
  
      # goto :goto_50
  
      # :cond_95
      # const/4 v14, 0x0
  
      # const/4 v15, 0x0
  
      # const/16 v16, 0x0
  
      # const/16 v17, 0x0
  
      # .line 10
      # :cond_9b
      # new-array v0, v0, [I
  
      # const/4 v2, 0x0
  
      # aput v14, v0, v2
  
      # aput v15, v0, v5
  
      # aput v16, v0, v7
  
      # aput v17, v0, v9
  
      # add-int v17, v17, v16
  
      # add-int v17, v17, v15
  
      # add-int v3, v17, v14
  
      # if-lez v3, :cond_b9
  
      # .line 11
      # new-instance v4, Ljava/util/Random;
  
      # invoke-direct {v4}, Ljava/util/Random;-><init>()V
  
      # invoke-virtual {v4, v3}, Ljava/util/Random;->nextInt(I)I
  
      # move-result v3
  
      # add-int/2addr v3, v5
  
      # goto :goto_ba
  
      # :cond_b9
      # const/4 v3, 0x0
  
      # :goto_ba
      # const/4 v4, 0x0
  
      # .line 12
      # :goto_bb
      # array-length v6, v0
  
      # if-ge v2, v6, :cond_e6
  
      # .line 13
      # aget v6, v0, v2
  
      # add-int/2addr v4, v6
  
      # if-gt v5, v3, :cond_e0
  
      # if-gt v3, v4, :cond_e0
  
      # .line 14
      # new-instance v0, Ljava/lang/StringBuilder;
  
      # invoke-direct {v0}, Ljava/lang/StringBuilder;-><init>()V
  
      # const-string v3, "\'\u52a0\u8f7d\u5e7f\u544a\uff1a"
  
      # invoke-virtual {v0, v3}, Ljava/lang/StringBuilder;->append(Ljava/lang/String;)Ljava/lang/StringBuilder;
  
      # aget-object v3, v1, v2
  
      # invoke-virtual {v0, v3}, Ljava/lang/StringBuilder;->append(Ljava/lang/String;)Ljava/lang/StringBuilder;
  
      # invoke-virtual {v0}, Ljava/lang/StringBuilder;->toString()Ljava/lang/String;
  
      # move-result-object v0
  
      # const-string v3, "ADUtil"
  
      # invoke-static {v3, v0}, Landroid/util/Log;->i(Ljava/lang/String;Ljava/lang/String;)I
  
      # .line 15
      # aget-object v0, v1, v2
  
      # return-object v0
  
      # .line 16
      # :cond_e0
      # aget v6, v0, v2
  
      # add-int/2addr v5, v6
  
      # add-int/lit8 v2, v2, 0x1
  
      # goto :goto_bb
  
      # :cond_e6
  
      const-string v11, ""
  
      return-object v11
  
      nop
  
      :pswitch_data_e8
      .packed-switch 0x31
          :pswitch_76
          :pswitch_6e
          :pswitch_66
          :pswitch_5e
      .end packed-switch
  .end method
  ```
  
  - 相关`Java`方法的含义
  
  ```java
  /**
  * 修改后的ADUtil类
  */
  package com.fanle.adlibrary.utils;
  
  public class ADUtil {
      /* renamed from: a */
      public static String m8923a(ADPPlat adpPlat) {
          return "";
      }
  ```

#### 0x03 无限次数播放

暂不公布，敬请谅解！！



