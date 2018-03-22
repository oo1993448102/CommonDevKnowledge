### （二）Android源码相关分析

* **Android动画框架实现原理**

* **Android各个版本API的区别**
  
  [Android版本介绍](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#uses)
  
* **Requestlayout，onlayout，onDraw，DrawChild区别与联系**

	RequestLayout()方法 ：责任链模式</br>
	子View调用requestLayout方法，会标记当前View及父容器，同时逐层向上提交，直到ViewRootImpl处理该事件，ViewRootImpl会调用三大流程，从measure开始，对于每一个含有标记位的view及其子View都会进行测量、布局、绘制。</br>
	requestLayout如果没有改变l,t,r,b，那就不会触发onDraw；但是如果这次刷新是在动画里，mDirty非空，就会导致onDraw。
	
	invalidate() 只执行自身draw方法

	onLayout()方法(如果该View是ViewGroup对象，需要实现该方法，对每个子视图进行布局)

	调用onDraw()方法绘制视图本身

	drawChild()去重新回调每个子视图的draw()方法
	
	![调用](http://upload-images.jianshu.io/upload_images/1734948-b4493f7b0234dd69.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	
	
	
	
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

	AMS和WMS都属于Android中的系统服务，被所有的App公用的

	AMS(ActivityManagerServices)统一调度所有应用程序的Activity，负责系统中所有Activity的生命周期。</br>
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
	
* **自定义View的事件**

	触摸&&绘制

* **AsyncTask+HttpClient 与 AsyncHttpClient有什么区别？**

* **LaunchMode应用场景**	

	sinngleTop:防抖</br>
	singleTask:入口Activity</br>
	singleInstance:单实例，多应用公用，不常见

* **AsyncTask 如何使用?**

	AsyncTask是Android提供的一个助手类，它对Thread和Handler进行了封装，方便我们使用，asyncTask.execute() 只能在UI主线程中调用，多任务时不并发，线程池
	
	AsyncTask有四个重要的回调方法，分别是：onPreExecute（主线程）、doInBackground（工作线程）, onProgressUpdate（主线程，在doInBackground中手动调publishProgress） 和 onPostExecute（主线程 执行完毕）。
	
	局限性：
	
	* 在Android 4.1版本之前，AsyncTask类必须在主线程中加载，这意味着对AsyncTask类的第一次访问必须发生在主线程中；在Android 4.1以及以上版本则不存在这一限制，因为ActivityThread（代表了主线程）的main方法中会自动加载AsyncTask </br>
	* AsyncTask对象必须在主线程中创建 </br>
	* AsyncTask对象的execute方法必须在主线程中调用 </br>
	* 一个AsyncTask对象只能调用一次execute方法

* **SparseArray原理**

	当使用HashMap(K, V),如果K为整数类型时,使用SparseArray的效率更高</br>

```
	mKeys = new int[initialCapacity];
	mValues = new Object[initialCapacity];
```	

key value 分别为一个数组 key是有序插入的</br>
	查找key时使用二分查找法，降低了时间复杂度（O（log2n）），根据找到的key的下标取value

* **请介绍下ContentProvider 是如何实现数据共享的？**

	统一了数据访问方式
	
* **Android Service与Activity之间通信的几种方式**

	* bindService 通过Binder得到Service对象
	* 广播
	* ......

* **IntentService原理及作用是什么？**

	IntentService保留了Service原有的特性，并且将耗时的操作都放在的子线程中，通过Handler的回调方式实现了数据的返回。

	* IntentService继承Service，专门处理异步请求。
	* 客户端通过调用startService(Intent)发起请求，自然数据是通过Intent进行传递的。
	* 一旦Service启动后，对于Intent所传递的数据操作都通过工作线程（worker thread）进行处理。
	* 在完成数据的处理之后，Handler会回调其处理结果。在任务结束后会将自身服务关闭。

	通过Handler、Message、Looper在Service中实现的异步线程消息处理的机制。但是由于是通过衍生Service的方式实现的，因此具有Service的生命周期特性。
	
* **说说Activity、Intent、Service 是什么关系**

	Activity Service 四大组件，通过Intent传递消息

* **ApplicationContext和ActivityContext的区别**
	
	生命周期长短，使用Activity的Context持有某些静态引用会引起内存泄漏
	
	![](https://ws2.sinaimg.cn/large/006tNc79gy1foxdamsdegj30xs0i8grk.jpg)
	
	和UI相关的方法基本都不建议或者不可使用Application

	数字1：启动Activity在这些类中是可以的，但是需要创建一个新的task。一般情况不推荐。

	数字2：在这些类中去layout inflate是合法的，但是会使用系统默认的主题样式，如果你自定义了某些样式可能不会被使用。

	数字3：在receiver为null时允许，在4.2或以上的版本中，用于获取黏性广播的当前值。（可以无视）

	Context数量 = Activity数量 + Service数量 + 1

* **SP是进程同步的吗?有什么方法做到同步？**

	不是</br>
	使用 contentprovider
	
* **谈谈多线程在Android中的使用**

	避免ANR(UI线程5s 广播10s 服务20s)</br>
	防止耗时操作拥堵线程  完成持续性长的耗时操作</br>
	
	thread && runnable:</br>直接继承Thread和实现Runnable接口实现多线程区别
众所周知在Java中类仅支持单继承，当定义一个新的类的时候，它只能扩展一个外部类。假如创建自定义线程类的时候是通过扩展 Thread类的方法来实现的，那么这个自定义类就不能再去扩展其他的类。因此，如果自定义类必须扩展其他的类，那么就可以使用实现Runnable接口的方法来定义该类为线程类，这样就可以避免Java单继承所带来的局限性。但继承Thread和实现Runnable重要区别并不是在于此，更重要的是实现Runnable接口的方式创建的线程可以处理同一资源，从而实现资源的共享。</br>
实现Runnable接口相对于扩展Thread类来说，具有无可比拟的优势。此方式不仅有助于程序的健壮性，使代码能够被多个线程共享，而且代码和数据资源相对独立，从而特别适合多个具有相同代码的线程去处理同一资源的情况。使得线程、代码和数据资源三者有效分离，很好地体现了面向对象程序设计的思想。因此，几乎所有的多线程程序都是通过实现Runnable接口的方式来完成的。
	
	asynctask
	
	intentservice
	
* **进程和 Application 的生命周期**

	进程重要级：前台（foreground）>可视（visible）>服务(service)>背景（background）>空（cache）
	
	application生命周期：

```
	public class App extends Application {

    @Override
    public void onCreate() {
        // 程序创建的时候执行
        Log.d(TAG, "onCreate");
        super.onCreate();
    }
    @Override
    public void onTerminate() {
        // 程序终止的时候执行
        Log.d(TAG, "onTerminate");
        super.onTerminate();
    }
    @Override
    public void onLowMemory() {
        // 低内存的时候执行
        Log.d(TAG, "onLowMemory");
        super.onLowMemory();
    }
    @Override
    public void onTrimMemory(int level) {
        // 程序在内存清理的时候执行
        Log.d(TAG, "onTrimMemory");
        super.onTrimMemory(level);
    }
    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        Log.d(TAG, "onConfigurationChanged");
        super.onConfigurationChanged(newConfig);
    }
    
}
```

* **封装View的时候怎么知道view的大小**
	
	调用view宽高时期view.getMeasuredWidth/Height（确保view已经测量完毕）：
	
	* `Activity/View#onWindowFocusChanged` 它会被调用多次，当 Activity的窗口得到焦点和失去焦点均会被调用
	* `view.post(runnable)` 通过post将一个runnable投递到消息队列的尾部，当Looper调用此 runnable的时候，View也初始化好了。
	* `ViewTreeObserver.addOnGlobalLayoutListener`

* **RecyclerView原理**

	[RecyclerView 剖析](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0630/4400.html)
	
	[RecyclerView 剖析 下](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0701/4401.html)* **AndroidManifest的作用与理解**
























		
	