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

* **Activity与Fragment之间生命周期比较**
	<center>![](https://upload-images.jianshu.io/upload_images/2244681-1532340d63d59dc6.png)</center>
	<center>![](https://upload-images.jianshu.io/upload_images/2244681-3685a0866eb07d3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/317)</center>
	
* **Activity上有Dialog的时候按Home键时的生命周期**
 </br> 按Home：onPause() onStop()
 </br>返回App：onRestart() onStart() onResume()
 </br>dialog出现与否不影响生命周期 尝试时使用的是AlertDialog

* **两个Activity 之间跳转时必然会执行的是哪几个方法？**
	</br>A跳转B：A的onPause()  若A不可见 onStop()
</br>	 B的onCreate(),onStart(),onResume()
<<<<<<< HEAD

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
	
	屏幕旋转：
	![](https://ws4.sinaimg.cn/large/006tNc79ly1fowagm8kr0j30h603kaah.jpg)
	![](https://ws4.sinaimg.cn/large/006tNc79ly1fowagmto6oj30fk040t9a.jpg)
	Home：
	![](https://ws2.sinaimg.cn/large/006tNc79ly1fowagne86jj30fm058750.jpg)
	
* **Fragment各种情况下的生命周期**
	</br>hide show: `onHiddenChanged()`
	</br>viewpager: `setUserVisibleHint()`

* **Fragment之间传递数据的方式？**


