### （二）Android源码相关分析

* **Android动画框架实现原理**

* **Android各个版本API的区别**
  
  [Android版本介绍](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#uses)
  
* **Requestlayout，onlayout，onDraw，DrawChild区别与联系**

	RequestLayout()方法 ：会导致调用measure()过程 和 layout()过程 。 说明：只是对View树重新布局layout过程包括measure()和layout()过程，不会调用draw()过程，但不会重新绘制 任何视图包括该调用者本身。

	onLayout()方法(如果该View是ViewGroup对象，需要实现该方法，对每个子视图进行布局)

	调用onDraw()方法绘制视图本身

	drawChild()去重新回调每个子视图的draw()方法
	
	invalidate() 层层上传到父级直到传递到ViewRootImpl后触发了scheduleTraversals方法，然后整个View树开始重新按照View绘制流程进行重绘任务。
	
* **invalidate和postInvalidate的区别及使用**

	invalidate:在ui线程刷新view
	postInvalidate：在工作线程刷新view（底层还是handler）
	
* **Activity-Window-View三者的差别**</br>
	1. 在Activity中调用attach，创建了一个Window</br>
	2. 创建的window是其子类PhoneWindow，在attach中创建PhoneWindow</br>
	3. 在Activity中调用setContentView(R.layout.xxx)实际上是调用的getWindow().setContentView()</br>
	5. 调用PhoneWindow中的setContentView方法</br>
	6. 创建ParentView：
 作为ViewGroup的子类，实际是创建的DecorView(作为FramLayout的子类）</br>
	7. 将指定的R.layout.xxx进行填充</br>
通过布局填充器进行填充【其中的parent指的就是DecorView】
	8. 调用到ViewGroup</br>
	9. 调用ViewGroup的removeAllView()，先将所有的view移除掉</br>
	10. 添加新的view：addView()</br>

* **谈谈对Volley的理解**

* **如何优化自定义View**

	降低刷新频率,减少不必要的调用invalidate()方法,尽量调用多参invalidate(),能做到局部刷新而不是整体刷新
	
	使用好硬件加速（targetpi>=11）
	
	初始化时创建对象；不要在onDraw方法内创建绘制对象
	
	做好状态的储存与恢复（onsaveinstance onrestoreinstance）
	
* **低版本SDK如何实现高版本api？**

	在代码中加入版本控制逻辑，添加@targetapi使编译器通过
	
* **描述一次网络请求的流程**


	