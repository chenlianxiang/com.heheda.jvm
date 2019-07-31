# Java内存区域与内存溢出异常

## 运行时数据区域

### 程序计数器（<font color="red">线程私有的</font>）

> 程序计数器：是一块比较小的内存空间，它可以看作是<font color="red">当前线程所执行的字节码的行号指示器</font>
>
> 由于Java虚拟机的多线程是通过线程轮转并分配处理器执行时间的方式来实现的。为了线程切换后能够恢复到正确的执行位置，每条线程都需要一个独立的程序计数器,每个线程的计数器互不影响，独立存储
>
> <font color="red">程序计数器是唯一一块不会发生OutOfMenoryError(**<font color="blue">内存溢出</font>**)的区域</font>

### Java虚拟机栈（<font color="red">线程私有的</font>）

> * 生命周期与线程相同
>
> * 虚拟机栈描述的是<font color="red">Java方法执行的内存模型</font>：每个方法在执行的同时都会创建一个栈帧
>
>   > 栈帧存储 <font color='blue'>局部变量表、操作数栈、动态链接、方法出口</font>

<font color="red">**tip:局部变量表所需的空间是完全确定的，在进入一个方法时，这个方法需要在帧中分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表的大小**</font>

> 两种异常：
>
> > 1. 如果线程请求的栈深度大于虚拟机所允许的深度，将会抛出<font color="blue">StackOverflowError</font>异常
> > 2. 如果虚拟机可以动态扩展，如果扩展时无法申请到足够的内存，就会抛出<font color="blue">OutOfMemoryError</font>异常

### 本地方法栈（<font color="red">线程私有的</font>）

> 本地方法栈（Native Method Stack）与虚拟机栈所发挥作用是非常相似的，他们的区别不过是
>
> <font color="red">虚拟机栈为虚拟机执行Java方法（字节码）服务</font>
>
> <font color="red">本地方法栈则为虚拟机使用到的Native方法服务</font>
>
> <font  color="blue">同样也会抛出 StackOverflowError、OutOfMemoryError异常</font>

### Java堆(<font color='red'>线程共享</font>)

> Java堆（Java Head）是Java虚拟机所管理的<font color="red">内存最大的一块</font>
>
> Java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建
>
> Java堆<font color="red">唯一的目的就是存放对象实例</font>
>
> Java堆是垃圾收集器管理的主要区域（<font color="red">也称"GC堆"</font>）
>
> 现在收集器基本采用<font color="red">分代收集算法</font>
>
> Java堆：新生代和老年代   Eden空间、From Survivor空间、To Survivor空间
>
> 从内存分配角度：线程共享的Java堆可能划分出多个线程私有的分配缓冲区，
>
> 划分目的：<font color="red">更好的回收内存，或者更快的分配内存</font>
>
> <font color="red">**tip：Java堆可以处于物理上不连续的内存空间，只要逻辑是连续的即可，在实现时，既可以显示成固定大小的，也可以是扩展的（通过 -Xms和-Xms控制），如果在堆中没有内存完成实例分配，并且堆也无法扩展时，将会抛出<font color="blue">cc异常</font>**</font>  

### 方法区(<font color='red'>线程共享</font>)

> 方法区（Method Area）与Java堆一样，是各个线程共享的内存区域
>
> 用于存储已被虚拟机加载的类信息、常量、静态变量、即使编译器编译后的代码
>
> JDK1.7以前称之为“永久代” JDK1.8 元空间
>
> <font color="blue">tip:永久代有 -XX:MaxPermSize的上限，容易遇到内存溢出的问题，当方法区语法满足内存分配的需求是，将会抛出OutOfMemoryError异常</font>
>
> 

### 运行时常量池

> 运行时常量池是方法区的一部分。Class文件除了有类的版本、字段、方法、接口、等描述信息外，还有一项信息是常量池，用于存放编译器生成的各种<font color="red">字面量和符号引用</font>,这部分内容将在类加载后进入方法区的运行时常量池中存放
>
> <font color="blue">tip:运行时常量池是方法区的一部分，自然收到方法区的限制，当常量池无法再申请到内存时会抛出OutOfMemoryError异常</font>

### 直接内存

> 直接内存并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范定义的内存区域，但是这部分内存也被频繁的使用，也会导致OutOfMemoryError异常
>
> NIO基于通道（channel）与缓冲区（Buffer）的I/O方法，直接使用Native函数库直接分配堆外内存，然后通过存储在Java堆中的DirectByteBuffer对象作为这块内存的引用进行操作，这样能在一些场景中显著提高性能，避免了Java堆和Native堆中来回复制数据
>
> <font color="blue">本机直接内存不会收到Java堆大小的限制，但是收到本机总内存的限制，从而导致动态扩展时出现OutOfMemoryError异常</font>











































