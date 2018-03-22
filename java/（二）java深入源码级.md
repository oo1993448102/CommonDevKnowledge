### （二）java深入源码级面试题

* **哪些情况下的对象会被垃圾回收机制处理掉？**

	Java GC机制主要完成3件事：确定哪些内存需要回收，确定什么时候需要执行GC，如何执行GC。
	
	分代分配，分代回收。对象将根据存活的时间被分为：年轻代（Young Generation）、年老代（Old Generation）、永久代（Permanent Generation，也就是方法区）
	
	**年轻代Minor GC或叫Young GC:**采用停止-复制（Stop-and-copy）”清理法。分为Eden区和两个Survivor区。内存首先在Eden区内连续分配，当Eden满时执行一次GC，仍存活的移入同一个survivor区，重复上述过程直至当前survivor区满，GC后仍存活的直接移入另一个survivor区（总有一个survivor为空）
	
	**年老代Major GC也叫Full GC:**对象如果在年轻代存活了足够长的时间而没有被清理掉（即在几次 Young GC后存活了下来），则会被复制到年老代，年老代的空间一般比年轻代大，能存放更多的对象，在年老代上发生的GC次数也比年轻代少。</br>
	如果对象比较大（比如长字符串或大数组），Young空间不足，则大对象会直接分配到老年代上（大对象可能触发提前GC，应少用，更应避免使用短命的大对象）。</br>
	年老代中维护一个512 byte的块——”card table“，所有老年代对象引用新生代对象的记录都记录在这里。Young GC时，只要查这里即可，不用再去查全部老年代，因此性能大大提高。</br>
	标记-清除，标记-整理算法
	
	
	**方法区（永久代）：**永久代的回收有两种：常量池中的常量，无用的类信息，常量的回收很简单，没有引用了就可以被回收。对于无用的类进行回收，必须保证3点：
	
	1. 类的所有实例都已经被回收
	2. 加载类的ClassLoader已经被回收
	3. 类对象的Class对象没有被引用（即没有通过反射引用该类的地方）

	引用计数法(循环引用时失效)  标记-清除算法 标记-整理算法 copying算法 generation算法
	
* **讲一下常见编码方式？**

 ASCII 码  128位
 
 GBK 《汉字内码扩展规范》
 
 UTF-16 Unicode码 2字节定长编码
 
 UTF-8 1~6字节变长编码
 
* **utf-8编码中的中文占几个字节；int型几个字节？**

	常用中文字符占用3个字节（大约2万多字），但超大字符集中的更大多数汉字要占4个字节
	</br>英文一个字节
	
* **静态代理和动态代理的区别，什么场景使用？（Proxy）**

	静态：由程序员创建或工具生成代理类的源码，再编译代理类。所谓静态也就是在程序运行前就已经存在代理类的字节码文件，代理类和委托类的关系在运行前就确定了。装饰器模式。
	</br>优点：业务类只需要关注业务逻辑本身，保证了业务类的重用性。这是代理的共有优点。 
</br>缺点： 
1）代理对象的一个接口只服务于一种类型的对象，如果要代理的方法很多，势必要为每一种方法都进行代理，静态代理在程序规模稍大时就无法胜任了。</br> 
2）如果接口增加一个方法，除了所有实现类需要实现这个方法外，所有代理类也需要实现此方法。增加了代码维护的复杂度。

	动态（通过反射实现）：

	在静态代理模式下，Proxy所做的事情，无非是调用在不同的request时，调用触发realSubject对应的方法；更抽象点看，Proxy所作的事情；在Java中 方法（Method）也是作为一个对象来看待了，动态代理工作的基本模式就是将自己的方法功能的实现交给 InvocationHandler角色，外界对Proxy角色中的每一个方法的调用，Proxy角色都会交给InvocationHandler来处理，而InvocationHandler则调用具体对象角色的方法。
	
	![](https://ws3.sinaimg.cn/large/006tKfTcgy1fp22kmq4pcj30ku06u74w.jpg) 
	
	具体步骤是： 	
	a. 实现InvocationHandler接口创建自己的调用处理器 </br>
	b. 给Proxy类提供ClassLoader和代理接口类型数组创建动态代理类 </br>
	c. 以调用处理器类型为参数，利用反射机制得到动态代理类的构造函数 </br>
	d. 以调用处理器对象为参数，利用动态代理类的构造函数创建动态代理类对象</br> 
	
	动态代理优点 ：
1.动态代理类的字节码在程序运行时由Java反射机制动态生成，无需程序员手工编写它的源代码。 
2.动态代理类不仅简化了编程工作，而且提高了软件系统的可扩展性，因为Java 反射机制可以生成任意类型的动态代理类。

	```
public class MyInvocationHandler implements InvocationHandler {
    private String TAG = "MyInvocationHandler";
    private Object obj;

    public Object bind(Object obj){
        this.obj = obj;
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(),obj.getClass().getInterfaces(),this);
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Log.d(TAG,"start"+method.getName());
        Object result = method.invoke(obj,args);
        Log.d(TAG,"end"+method.getName());
        return result;
    }
}
	```
	
	```
	 Human human = new HumanImp();
        ((TextView)findViewById(R.id.tv_content)).setText(((Human)new MyInvocationHandler().bind(human)).eat());
    ```
	
	缺点： 
	JDK的动态代理机制只能代理实现了接口的类，而不能实现接口的类就不能实现JDK的动态代理，cglib是针对类来实现代理的，他的原理是对指定的目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，所以不能对final修饰的类进行代理。	

	作用：1，方法增强，让你可以在不修改源码的情况下，增强一些方法，比如添加日志等。
2.以用作远程调用，好多rpc框架就是用代理方式实现的。

	[参考文章](http://blog.csdn.net/fengyuzhengfan/article/details/49586277)
	
* **Java的异常体系**

	![](https://ws3.sinaimg.cn/large/006tNc79gy1fp31ljunraj30na0dudlm.jpg)

   Error是程序无法处理的错误，比如OutOfMemoryError、ThreadDeath等。这些异常发生时，Java虚拟机（JVM）一般会选择线程终止。   
	Exception是程序本身可以处理的异常，这种异常分两大类运行时异常（不检查异常）和非运行时异常（检查异常，不处理，程序就不能编译通过）。程序中应当尽可能去处理这些异常。
	
	try、catch、finally:   
	 * try、catch、finally三个语句块均不能单独使用，三者可以组成 try...catch...finally、try...catch、 
    try...finally三种结构，catch语句可以有一个或多个，finally语句最多一个。 
    * try、catch、finally三个代码块中变量的作用域为代码块内部，分别独立而不能相互访问。 
    如果要在三个块中都可以访问，则需要将变量定义到这些块的外面。 
    * 多个catch块时候，只会匹配其中一个异常类并执行catch块代码，而不会再执行别的catch块， 
    并且匹配catch语句的顺序是由上到下。

	throw:方法体内部
	</br>throws:方法体外部
	
* **谈谈你对解析与分派的认识**

	调用目标在编译器进行编译时就必须确定下来，这类方法的调用称为解析
	
	解析调用一定是个静态过程，在编译期间就完全确定，在类加载的解析阶段就会把涉及的符号引用转化为可确定的直接引用，不会延迟到运行期再去完成。而分派调用则可能是静态的也可能是动态的，根据分派依据的宗量数（方法的调用者和方法的参数统称为方法的宗量）又可分为单分派和多分派。两类分派方式两两组合便构成了**静态单分派、静态多分派、动态单分派、动态多分派**四种分派情况。

	* 静态分派</br>
	所有依赖静态类型来定位方法执行版本的分派动作，都称为静态分派，静态分派的最典型应用就是多态性中的方法重载（方法相同，参数不同）。静态分派发生在编译阶段，因此确定静态分配的动作实际上不是由虚拟机来执行的。

	* 动态分派</br>
	动态分派与多态性的另一个重要体现——方法覆写（完全相同方法）有着很紧密的关系。向上转型后调用子类覆写的方法便是一个很好地说明动态分派的例子。这种情况很常见，因此这里不再用示例程序进行分析。很显然，在判断执行父类中的方法还是子类中覆盖的方法时，如果用静态类型来判断，那么无论怎么进行向上转型，都只会调用父类中的方法，但实际情况是，根据对父类实例化的子类的不同，调用的是不同子类中覆写的方法。
很明显，这里是要根据变量的实际类型来分派方法的执行版本的。而实际类型的确定需要在程序运行时才能确定下来，这种在运行期根据实际类型确定方法执行版本的分派过程称为动态分派。

	[参考文章](https://www.jianshu.com/p/1976b01c07d2)
	
* **修改对象A的equals方法的签名，那么使用HashMap存放这个对象实例的时候，会调用哪个equals方法？**

	```
	//HashMap
	if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
	```
	
	调用原方法的
	
* **Java中实现多态的机制是什么？**

	继承、重写、向上转型
	</br>实现多态形式：继承和接口
	1. 使用父类类型的引用指向子类的对象；
	2. 该引用只能调用父类中定义的方法和变量；
	3. 如果子类中重写了父类中的一个方法，那么在调用这个方法的时候，将会调用子类中的这个方法；（动态连接、动态调用）;

* **如何将一个Java对象序列化到文件里？**
 
 对象实现Serializable，不希望被序列化的属性加transient属性</br>
 ObjectOutputStream & ObjectInputStream
 
 ```java
 	File aFile=new File("e:\\c.txt");
    Stu a=new Stu(1, "aa", "1");
    FileOutputStream fileOutputStream=null;
    try {
      fileOutputStream = new FileOutputStream(aFile);
      ObjectOutputStream objectOutputStream=new ObjectOutputStream(fileOutputStream);
      objectOutputStream.writeObject(a);
      objectOutputStream.flush();
      objectOutputStream.close();
    } catch (FileNotFoundException e) {
      e.printStackTrace();
    } catch (IOException e) {
      e.printStackTrace();
    }finally {
      if(fileOutputStream!=null)
      {
        try {
          fileOutputStream.close();
        } catch (IOException e) {
          // TODO Auto-generated catch block
          e.printStackTrace();
        }      
      }
    }
    ```
    
    ```java
    FileInputStream fileInputStream=new FileInputStream(aFile);
	ObjectInputStream objectInputStream=new ObjectInputStream(fileInputStream);
	Stu s=(Stu)objectInputStream.readObject();
	System.out.println(s);
	```
	
* **说说你对Java反射的理解**
	
	允许当程序运行时改变程序结构或变量类型
	
	在 Java 和 Android 开发中，一般情况下下面几种场景会用到反射机制：
	
	* 需要访问隐藏属性或者调用方法改变程序原来的逻辑，这个在开发中是很常见的，由于一些原因，系统并没有开放一些接口出来，这个时候利用反射是一个有效的解决办法。
	* 自定义注解，注解就是在运行时利用反射机制来获取的。
	* 在开发中动态加载类，比如在 Android 中的动态加载解决65k问题等等，模块化和插件化都离不开反射。
	
	[参考文章](https://www.jianshu.com/p/6277c1f9f48d)
	
* **说说你对Java注解的理解**

	注解本身不能对代码运行产生影响，但是注解可以作为一个标记，用反射之类的手段获取到这个标记后，就能对标记的内容进行处理。例如在方法参数上加注解，用反射获取到注解后对该注解标记的形式参数注入实际参数。

	自定义注解：

	```
	@Retention(RetentionPolicy.RUNTIME)
	@Target({ElementType.FIELD})
	@Documented
	@Inherited
	public @interface Bind {
    	int value() default 1;
    	boolean canBeNull() default false;
	}
	```

	元注解（给自定义注解使用的注解）
	
	**@Rentention Rentention</br>**
	@Rentention Rentention用来标记自定义注解的有效范围，他的取值有以下三种：

	RetentionPolicy.SOURCE: 只在源代码中保留 一般都是用来增加代码的理解性或者帮助代码检查之类的，比如我们的Override;</br>
	RetentionPolicy.CLASS: 默认的选择，能把注解保留到编译后的字节码class文件中，仅仅到字节码文件中，运行时是无法得到的;</br>
	RetentionPolicy.RUNTIME:注解不仅能保留到class字节码文件中，还能在运行通过反射获取到，这也是我们最常用的。
	
	**@Target</br>**
	@Target指定Annotation用于修饰哪些程序元素。
	@Target也包含一个名为”value“的成员变量，该value成员变量类型为ElementType[ ]，ElementType为枚举类型，值有如下几个：

	ElementType.TYPE：能修饰类、接口或枚举类型 </br>	ElementType.FIELD：能修饰成员变量</br> ElementType.METHOD：能修饰方法 </br>ElementType.PARAMETER：能修饰参数</br> 	ElementType.CONSTRUCTOR：能修饰构造器</br> 	ElementType.LOCAL_VARIABLE：能修饰局部变量 </br>	ElementType.ANNOTATION_TYPE：能修饰注解 </br>	ElementType.PACKAGE：能修饰包</br>
	
	**@Documented**</br>
	使用了@Documented的可以在javadoc中找到
	
	**@Interited**</br>
	注解里的内容可以被子类继承，比如父类中某个成员使用了上述@Bind(value)，Bind中的value能给子类使用到。
	
	Android库中使用注解：
	编译时动态处理，动态**生成代码**，如Butter Knife、Dagger 2</br>
	运行时动态处理，获得注解信息，如Retrofit
	
* **说说你对依赖注入的理解**

	像这种非自己主动初始化依赖，而通过外部来传入依赖的方式，我们就称为依赖注入。

	依赖注入的实现有多种途径，而在 Java 中，使用注解是最常用的。比如通过ButterKnife、Dagger依赖注入库实现，都是使用注解来实现依赖注入，但它利用 APT(Annotation Process Tool) 在编译时生成辅助类，这些类继承特定父类或实现特定接口，程序在运行时加载这些辅助类，调用相应接口完成依赖生成和注入。
	
	[反射、注解与依赖注入总结
](https://www.jianshu.com/p/24820bf3df5c)

* **说一下泛型原理，并举例说明**

	实现原理：类型擦除 先检查，再编译</br>
	出现原因：解决类型转换问题</br>
	java 伪泛型，编译期间会擦除泛型，只留原始类型，生成的Java字节码中是不包含泛型中的类型信息，所以可以通过反射添加原始泛型不允许的参数</br>
	若无限定用Object替换，若有限定用第一个边界的类型变量来替换。
	
	[参考文章](http://blog.csdn.net/wisgood/article/details/11762427)
	
* **Java中String的了解**

	final类，不可继承。

	String对象一旦被创建就是固定不变的了，对String对象的任何改变都不影响到原对象，相关的任何change操作都会生成新的对象。
	
	优化：字符串常量池，已存在直接返回，故常量池中一定不存在两个相同的字符串。
	
* **String为什么要设计成不可变的？**

	设计考虑,效率优化问题,以及安全性这三大方面
	
* **Object类的equal和hashCode方法重写，为什么？**

	两个Object equals时，其hashcode必相等

