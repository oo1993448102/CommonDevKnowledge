###（一）Android基础知识点
* **四大组件是什么**
	</br>均需注册使用
	* Activity
	* Service </br>
		startService:service生命周期与启动它的组件无关，需要显示销毁
		
		bindService:生命周期与调用者绑定
	* ContentProvider
		</br>跨应用数据共享 IPC的一种
	* BroadcastReceiver
		</br>静态注册+动态注册
		
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
        		
* **两个Activity 之间跳转时必然会执行的是哪几个方法？**
	</br>A跳转B：A的onPause()  若A不可见 onStop()
</br>	 B的onCreate(),onStart(),onResume()
	