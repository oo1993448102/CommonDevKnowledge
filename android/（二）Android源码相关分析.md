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

	![](https://upload-images.jianshu.io/upload_images/2412005-b52e5a25eaf57fbc.png?imageMogr2/auto-orient/)

* **谈谈对Volley的理解**

* **如何优化自定义View**

	降低刷新频率,减少不必要的调用invalidate()方法,尽量调用多参invalidate(),能做到局部刷新而不是整体刷新
	
	使用好硬件加速（targetpi>=11）
	
	初始化时创建对象；不要在onDraw方法内创建绘制对象
	
	做好状态的储存与恢复（onsaveinstance onrestoreinstance）
	
* **低版本SDK如何实现高版本api？**

	在代码中加入版本控制逻辑，添加@targetapi使编译器通过
	
* **描述一次网络请求的流程**
	
	域名解析、TCP的三次握手、建立TCP连接后发起HTTP请求、服务器响应HTTP请求、浏览器解析html代码，同时请求html代码中的资源（如js、css、图片等）、最后浏览器对页面进行渲染并呈现给用户
	
	HTTP请求格式:</br>
	* 请求行: 请求方法（GET/POST/DELETE/PUT/HEAD）、URI路径、HTTP版本号</br>
	* 请求头: 缓存相关信息（Cache-Control，If-Modified-Since）
	客户端身份信息（User-Agent）等键值对信息</br>
	* 消息体: 客户端发给服务端的请求数据，这部分数据并不是每个请求必须的(post put)

	HTTP响应格式：</br>
	
	* 状态行：有HTTP协议版本号，状态码和状态说明三部分构成。</br>
	* 响应头：用于说明数据的一些信息，比如数据类型、内容长度等键值对。</br>
	* 消息体：服务端返回给客户端的HTML文本内容。或者其他格式的数据，比如：视频流、图片或者音频数据。

* **HttpUrlConnection 和 okhttp关系**

	从Android4.4开始HttpURLConnection的底层实现采用的是okHttp
	
* **Bitmap对象的理解**

	Bitmap对象的内存分为两部分:

	* Bitmap对象

	* Bitmap像素数据
	
	占用内存大，多对象易引发OOM</br>
	
	Bitmap占用内存计算 = 图片最终显示出来的宽 * 图片最终显示出来的高 * 图片品质（Bitmap.Config的值）</br>
	
	减少内存消耗：根据View的大小利用BitmapFactory.Options计算合适的inSimpleSize(>1)来对Bitmap进行相对应的裁剪
	
	Bitmap的优化主要是加快图片的加载速度和降低图片占用内存的大小
	
* **looper架构**

	封装消息循环和消息队列的一个类
	
	(1) Looper类用来为一个线程开启一个消息循环。 默认情况下android中新诞生的线程是没有开启消息循环的。(主线程除外,主线程系统会自动为其创建Looper对象,开启消息循环。) Looper对象通过MessageQueue来存放消息和事件。一个线程只能有一个Looper,对应一个MessageQueue。
	
	(2) 通常是通过Handler对象来与Looper进行交互的。Handler可看做是Looper的一个接口,用来向指定的Looper发送消息及定义处理方法。
	
	[Looper类浅析](https://www.aliyun.com/jiaocheng/56571.html)
	
* **ActivityThread，AMS，WMS的工作原理**

	AMS和WMS都属于Android中的系统服务

	AMS(ActivityManagerServices)统一调度所有应用程序的Activity</br>
	WMS(WindowManagerService)控制所有Window的显示与隐藏以及要显示的位置
	
	[Android系统服务 —— WMS与AMS](https://www.jianshu.com/p/47eca41428d6)
	
* **自定义View如何考虑机型适配**

	合理使用warp_content，match_parent.</br>
	尽可能的是使用RelativeLayout</br>
	针对不同的机型，使用不同的布局文件放在对应的目录下，android会自动匹配。</br>
	尽量使用点9图片。</br>
	使用与密度无关的像素单位dp，sp</br>
	引入android的百分比布局。</br>
	切图的时候切大分辨率的图，应用到布局当中。在小分辨率的手机上也会有很好的显示效果。</br>
	
* **AsyncTask+HttpClient 与 AsyncHttpClient有什么区别？**

* **AsyncTask 如何使用?**

	AsyncTask是Android提供的一个助手类，它对Thread和Handler进行了封装，方便我们使用，asyncTask.execute() 只能在UI主线程中调用，多任务时不并发，线程池
	
	AsyncTask有四个重要的回调方法，分别是：onPreExecute（主线程）、doInBackground（工作线程）, onProgressUpdate（主线程，在doInBackground中手动调publishProgress） 和 onPostExecute（主线程 执行完毕）。
	
	局限性：
	
	* 在Android 4.1版本之前，AsyncTask类必须在主线程中加载，这意味着对AsyncTask类的第一次访问必须发生在主线程中；在Android 4.1以及以上版本则不存在这一限制，因为ActivityThread（代表了主线程）的main方法中会自动加载AsyncTask </br>
	* AsyncTask对象必须在主线程中创建 </br>
	* AsyncTask对象的execute方法必须在主线程中调用 </br>
	* 一个AsyncTask对象只能调用一次execute方法

* **如何取消AsyncTask？**

	cancel方法 --> isCancelled()为true --> 在doInBackground中手动检查决定是否继续运行
	
	我们可以随时调用 cancel（boolean）去取消这个加载任务，调用这个方法会间接调用 iscancelled 并且返回true 。</br>
调用cancel()后，在doInBackground（）return后 我们将会调用onCancelled(Object) 不在调用onPostExecute(Object)</br>
为了保证任务更快取消掉，你应该在doInBackground（）周期性的检查iscancelled 去进行判断。</br>

* **为什么不能在子线程更新UI？**

	Exception:Only the original thread that created a view hierarchy can touch its views
	