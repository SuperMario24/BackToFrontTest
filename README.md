# BackToFrontTest
解决App在后台运行，点击App桌面的图标重新启动问题


在项目中，遇到一个问题百思不得其解，那就是：在app使用过程中，点击了home键，然后去看看微信之类的其他应用，这个时候再点击app桌面的图标，
这个时候app是重新启动的，而不是从上次停止的界面开始的。


原因：当点击app桌面图标时，app默认是任务你要新建一个应用，而不会去判断你后台有没有再运行的相同应用。

经过实践我发现：当你点击应用桌面图标，应用会重新创建你的app的启动页，然而，你快速的点击返回按钮，你会发现你会回到上一次退出时的界面。
经过查阅资料发现，系统会记录你启动acitivity的启动顺序的栈。并且把当前的启动页放到了最上方，如下图所示： 



注意：资料上面说以前启动的activity都是不在了，只是系统记录了他们启动的顺序，然而你按返回键，系统就会自动的重新创建新的activity，
加入当app依次启动了1到11的activity，然而，在11这个activity的时候，你点击了home键、或点击了其他软件如微信qq等，这个时候你的app进入后台，
1到11的这些activity其实被系统回收了，但是系统记录了这个activity启动顺序的栈，然后当你回到这个应用时，实际上系统是重新创建了Activity11，
然后点击返回键，右重新创建了Activity10,就是这样倒序 创建activity的原理。

然而，当你把App放入后台时，这个时候点击了app桌面的启动图标，这个时候系统会默认你开启一个新的应用，但是因为一个软件只能在手机上面运行一个，
所以，系统发现你之前的app还在后台，这个时候系统会把新创建的activity放到了之前activity栈的顶部，如上图所示的Activity1


解决：

知道了原因之后，我们就好做处理了。

第一步：查看Activity1的启动模式，如果Activity1的启动模式为singleTask，android:launchMode="singleTask"，把他删除。

第二步：在你的app的AndroidManifest.xml文件的application标签下面设置
android:persistent="true"
持久化为 true；防止你的app挂后台被回收。

第三步：在activity1的onCreate方法setContentView之前中设置如下方法：
   
    if ((getIntent().getFlags() & Intent.FLAG_ACTIVITY_BROUGHT_TO_FRONT) != 0) {    
        finish();    
        return;    
    }
    
用于判断这个Activity的启动标志，看它所在的应用是不是从后台跑到前台的。如果是，则直接把它finish（）掉，
然后系统会去Activity启动历史栈查询上一个activity，然后再新建它，所以还原到了我们按home键出去的那个界面。    
 
     
      
      
      
      
      
      
      
      
      
      
      
