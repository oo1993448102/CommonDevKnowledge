### （一）Android基础知识点
* **四大组件是什么**
	</br>均需注册使用
	* Activity
	* Service </br>
		startService:service生命周期与启动它的组件无关，需要显示销毁
		
		bindService:生命周期与调用者绑定
	* ContentProvider
		</br>跨应用数据共享 IPC的一种
	* BroadcastReceiver
		</br>静态注册+动态注册(register+unregister)
		
* **四大组件的生命周期和简单用法**
	</br>Activity:
	
	```java
	   protected void onCreate(Bundle savedInstanceState);  
         
       protected void onStart();     
         
       protected void onRestart();  
         
       protected void onResume();  
         
       protected void onPause();  
         
       protected void onStop();  
         
       protected void onDestroy();  
       
    ```
    Service:</br>
    `startService():`
    ![](https://ws1.sinaimg.cn/large/006tNc79gy1fow5dfwlhkj31720bewh7.jpg)
    `stopService():`
    ![](https://ws4.sinaimg.cn/large/006tNc79gy1fow5defsdrj31700figox.jpg)
    `bindService():`
    ![](https://ws4.sinaimg.cn/large/006tNc79gy1fow5c6j5llj317c09wwg8.jpg)
    `unbindService():`
    ![](https://ws1.sinaimg.cn/large/006tNc79gy1fow5c87u90j31660bggne.jpg)
    `startService()`+`bindService():`
    ![](https://ws1.sinaimg.cn/large/006tNc79gy1fow5qlw0owj317w05g755.jpg)
   
   ContentProvider:</br>
  	ContentProvider只有一个onCreate()生命周期方法且只会被调用一次，当其他应用通过ContentResolver第一次访问该ContentProvider时，onCreate()方法将会被回调
  	
  	BroadcastReceiver:</br>
  	动态注册与注册的context同周期</br>
  	静态注册 onReceive()后被注销
 
* **Activity之间的通信方式**
	* Intent
	* SharedPreference\写入本地文件
	* SQLite 各种orm
	* 静态变量
	* ......
	
* **Activity各种情况下的生命周期**
	[https://www.jianshu.com/p/e46d449467d5](https://www.jianshu.com/p/e46d449467d5)
        		
* **横竖屏切换的时候，Activity 各种情况下的生命周期**
	</br>1．不设置 Activity 的 android:configChanges 时，切屏会重新调用各个生命周期默认首先销毁 当前 activity,然后重新加载。

	2．设置 Activity android:configChanges="orientation|keyboardHidden|screenSize"时，切 屏不会重新调用各个生命周期，只会执行 `onConfigurationChanged` 方法。直接设置屏幕方向可以免去这个问题。

* **Activity与Fragment之间生命周期比较**</br>
	![](https://upload-images.jianshu.io/upload_images/2244681-1532340d63d59dc6.png)
	![](https://upload-images.jianshu.io/upload_images/2244681-3685a0866eb07d3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/317)
	
* **Activity上有Dialog的时候按Home键时的生命周期**
 </br> 按Home：onPause() onStop()
 </br>返回App：onRestart() onStart() onResume()
 </br>dialog出现与否不影响生命周期 尝试时使用的是AlertDialog

* **两个Activity 之间跳转时必然会执行的是哪几个方法？**
	</br>A跳转B：A的onPause()  若A不可见 onStop()
</br>	 B的onCreate(),onStart(),onResume()


* **前台切换到后台，然后再回到前台，Activity生命周期回调方法。弹出Dialog，生命值周期回调方法**
	 </br> 按Home：onPause() onStop()
 </br>返回App：onRestart() onStart() onResume()
 </br>dialog出现与否不影响生命周期 尝试时使用的是AlertDialog
 
* **Activity的四种启动模式对比**

	* standard 标准模式，每次都新建一个实例对象
	* singleTop 如果在任务栈顶发现了相同的实例则重用，且调用原来实例的onNewIntent()方法，否则新建并压入栈顶
	* singleTask 如果在任务栈中发现了相同的实例，将其上面的任务终止并移除，重用该实例，且调用原来实例的onNewIntent()方法。否则新建实例并入栈
	* singleInstance 在新任务栈中开启，许不同应用，进程线程等共用一个实例，无论从何应用调用该实例都重用，且调用原来实例的onNewIntent()方法

* **Activity状态保存与恢复**
	</br>`onSaveInstanceState():`
	* 屏幕旋转重建会调用onSaveInstanceState()
	* 启动另一个activity当前activity在离开前会调用onSaveInstanceState()
	* 按Home键的情形和启动另一个activity一样, 当前activity在离开前会onSaveInstanceState()

	`onRestoreInstanceState():`
	
	* 屏幕旋转重建会调用onRestoreInstanceState()
	* 启动另一个activity，返回时如果因为被系统杀死需要重建, 则会从onCreate()重新开始生命周期, 调用onRestoreInstanceState()
	* 按Home键的情形和启动另一个activity一样，用户再次点击应用图标返回时, **如果重建发生**, 则会调用onCreate()和onRestoreInstanceState()
	
	屏幕旋转：</br>
	![](https://ws4.sinaimg.cn/large/006tNc79ly1fowagm8kr0j30h603kaah.jpg)
	![](https://ws4.sinaimg.cn/large/006tNc79ly1fowagmto6oj30fk040t9a.jpg)
	</br>Home：
	![](https://ws2.sinaimg.cn/large/006tNc79ly1fowagne86jj30fm058750.jpg)
	
* **Fragment各种情况下的生命周期**
	</br>hide show: `onHiddenChanged()`
	</br>viewpager: `setUserVisibleHint()`

* **Fragment之间传递数据的方式？**
	* 拿到fragment对象，直接传参
	* 接口回调
	* 事件分发
	* ......
	
* **Activity 怎么和Service 绑定？**
	</br>bindService 方法启动服务, 其它组件可以通过回调获取服务的代理对象和服务交互, 而这两方也进行了绑定, 当启动方销毁时, 服务也会自动进行 unBind 操作, 当发现所有绑定都进行了 unBind 时才会销毁服务. 

* **怎么在Activity中启动自己对应的Service？**
	</br>在 Activity 中可以通过 startService 和 bindService 方法启动 Service。

* **service和activity怎么进行数据交互？**
	* bindService 通过Binder得到Service对象
	* 广播
	* ......
	
* **Service的开启方式**
	</br>startService && bindService

* **请描述一下Service 的生命周期**
	    ![](https://ws1.sinaimg.cn/large/006tNc79gy1fow5qlw0owj317w05g755.jpg)
	    
* **谈谈你对ContentProvider的理解**
	</br>ContentProvider一般为存储和获取数据提供统一的接口，提供了对底层数据存储方式的抽象,可以在不同的应用程序之间共享数据。
	
* **说说ContentProvider、ContentResolver、ContentObserver 之间的关系**
	* ContentProvider:应用提供	 
	* ContentResolver:统一管理与不同ContentProvider间的操作，通过统一ContentResolver访问不同ContentProvider
	* ContentObserver 内容监听器, 可以监听数据的改变状态

* **请描述一下广播BroadcastReceiver的理解**
* **广播的分类**
	</br>广播接受器，通过action进行匹配，任意一个action匹配则成功。一个广播接收器可以接收多个广播发出者发出的广播，一个广播发出者也可以得到多个广播接收器的回应。
	</br>动态注册，多次注册同一个广播只有一个实例，必须显示unregister
	</br>静态注册，onReceive后会自己注销，所以每次接收时自动生成一个新的实例
	</br>广播底层由Binder实现
	
* **广播使用的方式和场景**
	</br>广播的发送者和接收者事先是不需要知道对方的存在的，这样带来的好处便是，系统的各个组件可以松耦合地组织在一起
	</br>监听系统广播 （区别EventBus）
	</br>进程间通信 （区别EventBus）

* **本地广播和全局广播有什么差别？**
	</br>Broadcast是针对应用间、应用与系统间、应用内部进行通信的一种方式 广播底层Binder
	</br>LocalBroadcast比较轻量,由handler实现，仅在自己的应用内发送接收广播，也就是只有自己的应用能收到，数据更加安全广播只在这个程序里，而且效率更高。

* **BroadcastReceiver，LocalBroadcastReceiver 区别**
	</br>LocalBroadcastReceiver不能静态注册，只能采用动态注册的方式。
在发送和注册的时候采用，LocalBroadcastManager的sendBroadcast方法和registerReceiver方法

* **AlertDialog,popupWindow,DialogFragment区别**
	</br>AlertDialog是非阻塞线程的，Popupwindow是阻塞线程的。
	</br> DialogFragment fragmentManager会自动管理DialogFragment的生命周期．

* **Application 和 Activity 的 Context 对象的区别**
	</br>生命周期长短，使用Activity的Context持有某些静态引用会引起内存泄漏
	
	![](https://ws2.sinaimg.cn/large/006tNc79gy1foxdamsdegj30xs0i8grk.jpg)

数字1：启动Activity在这些类中是可以的，但是需要创建一个新的task。一般情况不推荐。

数字2：在这些类中去layout inflate是合法的，但是会使用系统默认的主题样式，如果你自定义了某些样式可能不会被使用。

数字3：在receiver为null时允许，在4.2或以上的版本中，用于获取黏性广播的当前值。（可以无视）

* **Android属性动画特性**
</br>与视图动画相对，3.0 API11后出现，可用于任何对象上，在改变视图的同时改变属性，为了兼容可使用开源库
	
	**ValueAnimator**
	</br>**ObjectAnimator**
	
* **如何导入外部数据库?**
	[http://blog.csdn.net/jing__jie/article/details/51602587](http://blog.csdn.net/jing__jie/article/details/51602587)
	
* **LinearLayout、RelativeLayout、FrameLayout的特性及对比，并介绍使用场景**
	* FrameLayout: 没有过多的测量 所有子view默认排放左上角
	* LinearLayout:线性布局，支持weight，会测量两次
	* RelativeLayout：相对布局，两次测量
	</br>没有嵌套的情况下，LinearLayout，FrameLayout性能优于RelativeLayout，RelativeLayout的功能比较复杂，它的布局过程需要花费更多的CPU时间
	
	**布局调优工具**
		
	* hierarchy viewer
	* lint
	* Android Device Monitor （左上方截图功能的右边，dump）
	
* **谈谈对接口与回调的理解**
	</br>可以用于实现观察者模式、事件监听

* **介绍下SurfaceView**
	
	1 . View适用于主动更新的情况，而SurfaceView则适用于被动更新的情况，比如频繁刷新界面。

	2 . View在主线程中对页面进行刷新，而SurfaceView则开启一个子线程来对页面进行刷新。

	3 . View在绘图时没有实现双缓冲机制，SurfaceView在底层机制中就实现了双缓冲机制。（双缓冲技术是游戏开发中的一个重要的技术。当一个动画争先显示时，程序又在改变它，前面还没有显示完，程序又请求重新绘制，这样屏幕就会不停地闪烁。而双缓冲技术是把要处理的图片在内存中处理好之后，再将其显示在屏幕上。双缓冲主要是为了解决 反复局部刷屏带来的闪烁。把要画的东西先画到一个内存区域里，然后整体的一次性画出来。）
	[SurfaceView 详解](https://www.jianshu.com/p/b037249e6d31)

* **RecyclerView的使用**
	</br>略
	
* **序列化的作用，以及Android两种序列化的区别**
	</br>序列化：对象 --> 字节流	反序列化：字节流-->对象
	* **Serializable** Java 序列化接口 IO读写存储在硬盘上 利于数据持久化
	*  **Parcelable** Android 序列化接口 效率高 在内存中读写 使用麻烦 （AS有相关插件 一键生成所需方法）
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fodg2vz29sj30lw0fkwfp.jpg)

* **差值器（TimeInterpolator） 估值器(TypeEvaluator)** 
	</br>属性动画中使用 用于实现非线性动画
	</br> TimeInterpolator:根据时间流逝的百分比计算出当前属性值改变的百分比。
	</br> TypeEvaluator:根据当前属性改变的百分比来计算改变后的属性值。
	</br>具体可参考[扔物线](http://hencoder.com/ui-1-6/)的教程

* **Android中数据存储方式**
	</br>SOLite SharedPreferences


