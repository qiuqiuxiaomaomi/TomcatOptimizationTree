# TomcatOptimizationTree
Tomcat性能优化技术研究

#####修改Server.xml配置文件中的Connector节点
![](https://i.imgur.com/VV57bse.png)
#####执行器优化,在Tomcat中每一个用户请求都是一个线程，所以可以使用线程池提高性能。
![](https://i.imgur.com/84PRinX.png)
![](https://i.imgur.com/E1lQzHT.png)
#####连接实践
![](https://i.imgur.com/Q8rph1I.png)
#####使用Nginx+tomcat架构禁用AJP
![](https://i.imgur.com/J9wjL6n.png)

<pre>
Tomcat的默认配置，性能并不是最优的，我们可以通过优化Tomcat以此来提高网站的并发能力。提高
Tomcat的性能可以分为两个方向。
      1）服务器资源（硬件方面）
         服务器所能提供CPU,内存，硬盘的性能对处理能力有决定性影响，所以说服务器性能强，那
         么Tomcat的性能也不会太差。淡然提高服务器的硬件配置，是需要大量金钱支撑的，所以不到
         万不得已不会采用这种方式，一般公司会采取优化配置等软件方面来提升Tomcat性能的方式。

      2）优化配置（软件方面）
         Tomcat的三种运行模式：
               1）bio
                  默认的模式，性能非常低下，没有经过任何优化处理和支持。
               2）nio
                  nio是基于缓冲区，并能提供非阻塞I/O操作的Java Api,它拥有比传统I/O操作
                  更好的性能。
               3）apr
                  安装起来最困难，但是从操作系统级别来解决异步IO问题，大幅度的提高性能。

       Executor重要参数说明：
               1）name：共享线程池的名字。这是Connector为了共享线程池要引用的名字，该名
                        字必须唯一。默认值：None；

               2）namePrefix:在JVM上，每个运行线程都可以有一个name 字符串。这一属性
                        为线程池中每个线程的name字符串设置了一个前缀，Tomcat将把线程
                        号追加到这一前缀的后面。默认值：tomcat-exec-；

               3）maxThreads：该线程池可以容纳的最大线程数。默认值：200；

               5）maxIdleTime：在Tomcat关闭一个空闲线程之前，允许空闲线程持续的时间
                     (以毫秒为单位)。只有当前活跃的线程数大于minSpareThread的值，才会
                     关闭空闲线程。默认值：60000(一分钟)。

               6）minSpareThreads：Tomcat应该始终打开的最小不活跃线程数。默认值：25。

               7）threadPriority：线程的等级。默认是Thread.NORM_PRIORITY   

       Connector重要参数说明：

               1）executor：表示使用该参数值对应的线程池；

               2）minProcessors：服务器启动时创建的处理请求的线程数；

               3）maxProcessors：最大可以创建的处理请求的线程数；

               5）acceptCount：指定当所有可以使用的处理请求的线程数都被使用时，可以放
                               到处理队列中的请求数，超过这个数的请求将不予处理。  

       使用Nginx+tomcat架构禁用AJP 
</pre>

<pre>
Tomcat内存优化主要是对tomcat启动参数优化，可以在Tomcat的启动脚本catalina.sh脚本中设置
java_opts参数。
</pre>

![](https://i.imgur.com/CjX2PXN.png)

<pre>
Tomcat启动命令行中的优化参数，就是JVM的优化。Tomcat首先是跑在JVM之上的，因为它的启动其实
也只是一个java命令行，首先我们需要对这个Java的启动命令进行调优，不管是YGC还是Full GC，GC
过程都会导致应用从程序运行中断，正确的选择不同的GC策略，调整JVM GC的参数，可以极大的减少
由于GC的工作，而导致的程序运行中断方面的问题，进而适当的提高Java程序的工作效率，但是调整
GC是一个极为复杂的过程，由于各个程序具备不同的特点，如:web和GUI程序就有很大的区别（WEB可
以适当的停顿，但是GUI停顿是客户无法接受的），而且由于跑在各个机器上的配置不同，所以GC种类
也不同。

      1：JVM参数配置方法
         Tomcat的启动参数位于安装目录,catalina.sh文件，Java_opts就是用来设置JVM相关
      运行参数的变量，还可以在CATALINA_OPTS变量中设置。关于这两个变量，还设有有些区别：
         1）JAVA_OPTS:用于当Java运行时选项"start","stop","run"命令执行。
         2）CATALINA_OPTS:用于当Java运行时选项“start","run"命令执行。

         首先，在启动Tomcat时，任何指定变量的传递方式都是相同的，可以传递到执行"start",
      "run"命令中，但只有设定在JAVA_OPTS变量里的参数被传递到"stop"命令中，对于Tomcat
      运行过程，可能没有什么区别，影响的是结束程序，而不是启动程序。
         其次，其他应用程序也可以使用JAVA_OPTS变量，但只有在Tomcat中使用CATALINA_OPTS
      变量，如果你设置环境变量为只使用Tomcat，最好建议使用CATALINA_OPTS变量，而如果你
      设置环境变量使用其他的Java应用程序，例如JBOSS,你应该把你的设置放在JAVA_OPTS变量中。
</pre>

<pre>
JVM参数解析：

      通过配置达到：
          1）系统响应时间增快。
          2）JVM回收速度增快同时又不影响系统的响应速度。
          3）JVM内存最大化利用。
          5）线程阻塞情况最小化。
      
      JVM常用参数详解：
          1）-server:
                一定要作为第一个参数，在多个CPU时性能佳，还有一种叫-client模式，特点
            是启动速度快，但运行时性能和内存管理效率不高，通常用于客户端应用程序和调试。
            在32位环境下直接运行Java程序默认启用该模式。Server模式的特点是启动速度比较
            慢，但运行时性能和内存管理效率很高，适用于生产环境，在具有64位能力的JDK环境下
            默认启用该模式，可以不配置该参数。

          2）-Xms
            表示Java初始化堆的大小，-Xms与Xmx设置成一样的值，避免JVM反复申请内存，导致
            性能大起大落，默认值为物理内存的1/64，默认空余堆内存小于40%时，JVM就会增大
            堆知道-Xmx的最大限制。
 
          3）-Xmx
            表示最大Java堆大小，当应用程序需要的内存超出堆的最大值时虚拟机就会提示内存溢出
            ，并且导致服务器崩溃，因此一般建议堆的最大值设置为可用内存的最大值的80%，如何
            知道我的JVM能够使用最大值，使用java -Xmx512M -version命令行来测试，然后逐渐
            的增大512的值，如果执行正常就表示指定的内存大小可用，否则会打印错误信息，默认
            值为物理内存的1/4，默认的空余堆内存大于70%时，JVM会减少堆知道-Xms的最小限制。

          5）-Xss
            表示每个Java线程堆栈大小，JDK5以后每个线程堆栈大小为1M,以前每个线程堆栈大小为
            256K，根据应用的线程所需内存大小进行调整，在相同物理内存下，减小这个值能生成
            更多的线程，但是操作系统对一个进程内的线程数是有限制的，不能无限生成，经验值
            在3000~5000左右，一般小的应用，如果栈不是很深，应该128K就够了，大的应用建议
            在256k~512k,一般不易设置超过1M,要不然容易出现out of memory，这个选项对性能
            影响比较大，需要严格的测试。

          6）-XX:NewSize
            设置新生代内存大小。
 
          7）-XX:MaxNewSize
            设置最大新生代内存大小

          8）-XX:PermSize
            设置持久带内存大小

          9）-XX:MaxPermSize
            设置最大持久带大小，永久代不属于堆内存，堆内存只包含新生代，老年代。

          10）-XX:+AggressiveOpts：作用如其名（aggressive），启用这个参数，则每当 
              JDK 版本升级时，你的 JVM 都会使用最新加入的优化技术（如果有的话）。

          11）-XX:+UseBiasedLocking：
              启用一个优化了的线程锁，我们知道在我们的应用服务，每个http请求就是一个线程，
              有的请求短有的请求长，就会有请求排队的现象，甚至还会出现线程阻塞，这个优化
              了的线程锁使得你的应用服务器内对线程处理自动进行最优调配。

          12）-XX:+DisableExplicitGC：在 程序代码中不允许有显示的调用“System.gc()”。
              每次在到操作结束时手动调用 System.gc() 一下，付出的代价就是系统响应时间
              严重降低，就和关于 Xms，Xmx 里的解释的原理一样，这样去调用 GC 导致系统
              的 JVM 大起大落。

          13）-XX:+UseConcMarkSweepGC
              设置老年代为并发收集器，即CMS GC，这一特性只有jdk1.5后续斑斑才具有的功能，
              它使用的是GC估算触发和heap占用触发，我们知道频繁的GC会造成JVM的大起大落，
              从而影响系统的效率，因此使用了CMS GC后可以再GC次数增多的情况下，每次GC
               的响应时间都很短，不如使用了CMS.

          15）-XX:+UseParNewGC
              对新生代采用多线程回收，这样回收的块，最易最新的JVM版本，但使用
              -XX:+UseConcMarkSweepGC时，-XX:UseParNewGC 会自动开启。因此，如果年轻代的并行 GC 不想开启，可以通过设置 -XX：-UseParNewGC 来关掉。

          16）-XX:MaxTenuringThreshold：
              设置垃圾最大年龄。如果设置为0的话，则新生代对象不经过 Survivor 区，直接
              进入老年代。对于老年代比较多的应用（需要大量常驻内存的应用），可以提高效率。
              如果将此值设置为一 个较大值，则新生代对象会在 Survivor 区进行多次复制，这
              样可以增加对象在新生代的存活时间，增加在新生代即被回收的概率，减少Full GC
              的频率，这样做可以在某种程度上提高服务稳定性。该参数只有在串行 GC 时才有
              效，这个值的设置是根据本地的 jprofiler 监控后得到的一个理想的值，不能一概
              而论原搬照抄

          17）-XX:+CMSParallelRemarkEnabled：
              在使用 UseParNewGC 的情况下，尽量减少 mark 的时间。

          18）-XX:+UseCMSCompactAtFullCollection：
              在使用 concurrent gc 的情况下，防止 memoryfragmention，对 
              live object 进行整理，使 memory 碎片减少。

          19) -XX:LargePageSizeInBytes：
              指定 Java heap 的分页页面大小，内存页的大小不可设置过大， 会影响 Perm 的大小。

          20) XX:+UseFastAccessorMethods：
              使用 get，set 方法转成本地代码，原始类型的快速优化。

          21) -XX:+UseCMSInitiatingOccupancyOnly：
              只有在 oldgeneration 在使用了初始化的比例后 concurrent collector 启动收集。

          22) -Duser.timezone=Asia/Shanghai：
              设置用户所在时区。

          23) -Djava.awt.headless=true：
              这个参数一般我们都是放在最后使用的，这全参数的作用是这样的，有时我们会在我
              们的 J2EE 工程中使用一些图表工具如：jfreechart，用于在 web 网页输出 
              GIF/JPG 等流，在 winodws 环境下，一般我们的 app server 在输出图形时不
              会碰到什么问题，但是在linux/unix 环境下经常会碰到一个 exception 导致你
              在 winodws 开发环境下图片显示的好好可是在 linux/unix 下却显示不出来，
              因此加上这个参数以免避这样的情况出现。

          25) -Xmn：
              新生代的内存空间大小，注意：此处的大小是（eden+ 2 survivor space)。
              与 jmap -heap 中显示的 New gen 是不同的。整个堆大小 = 新生代大小 + 老生代大小 + 永久代大小。
              在保证堆大小不变的情况下，增大新生代后，将会减小老生代大小。此值对系统性能影响较大，Sun官方推
              荐配置为整个堆的 3/8。

          26) -XX:CMSInitiatingOccupancyFraction：
              当堆满之后，并行收集器便开始进行垃圾收集，例如，当没有足够的空间来容纳新分配或提升的对象。
              对于 CMS 收集器，长时间等待是不可取的，因为在并发垃圾收集期间应用持续在运行（并且分配对象）。
              因此，为了在应用程序使用完内存之前完成垃圾收集周期，CMS 收集器要比并行收集器更先启动。因为不同
              的应用会有不同对象分配模式，JVM 会收集实际的对象分配（和释放）的运行时数据，并且分析这些数据，
              来决定什么时候启动一次 CMS 垃圾收集周期。这个参数设置有很大技巧，基本上满足
              (Xmx-Xmn)*(100-CMSInitiatingOccupancyFraction)/100 >= Xmn 就不会出现 
              promotion failed。例如在应用中 Xmx 是6000，Xmn 是 512，那么 Xmx-Xmn 是 5488M，也就是老年
              代有 5488M，CMSInitiatingOccupancyFraction=90 说明老年代到 90% 满的时候开始执行对老年
              代的并发垃圾回收（CMS），这时还 剩 10% 的空间是 5488*10% = 548M，所以即使 Xmn（也就是新生
              代共512M）里所有对象都搬到老年代里，548M 的空间也足够了，所以只要满足上面的公式，就不会出现垃
              圾回收时的 promotion failed，因此这个参数的设置必须与 Xmn 关联在一起。

          27) -XX:+CMSIncrementalMode：
              该标志将开启 CMS 收集器的增量模式。增量模式经常暂停 CMS 过程，以便对应用程序线程作出完全的让
              步。因此，收集器将花更长的时间完成整个收集周期。因此，只有通过测试后发现正常 CMS 周期对应用程序
              线程干扰太大时，才应该使用增量模式。由于现代服务器有足够的处理器来适应并发的垃圾收集，所以这种
              情况发生得很少，用于但 CPU情况。

              -XX:NewRatio：
              年轻代（包括 Eden 和两个 Survivor 区）与年老代的比值（除去持久代），-XX:NewRatio=4 表示年
              轻代与年老代所占比值为 1:4，年轻代占整个堆栈的 1/5，Xms=Xmx 并且设置了 Xmn 的情况下，该参数
              不需要进行设置。

		  28) -XX:SurvivorRatio：
              Eden 区与 Survivor 区的大小比值，设置为 8，表示 2 个 Survivor 区（JVM 堆内存年轻代中默认
              有 2 个大小相等的 Survivor 区）与 1 个 Eden 区的比值为 2:8，即 1 个 Survivor 区占整个年
              轻代大小的 1/10。
			
		  29) -XX:+UseSerialGC：
              设置串行收集器。
			
		  30) -XX:+UseParallelGC：
              设置为并行收集器。此配置仅对年轻代有效。即年轻代使用并行收集，而年老代仍使用串行收集。
			
		  31) -XX:+UseParallelOldGC：
              配置年老代垃圾收集方式为并行收集，JDK6.0 开始支持对年老代并行收集。
			
		  32) -XX:ConcGCThreads：
              早期 JVM 版本也叫-XX:ParallelCMSThreads，定义并发 CMS 过程运行时的线程数。比如 value=4 
              意味着 CMS 周期的所有阶段都以 4 个线程来执行。尽管更多的线程会加快并发 CMS 过程，但其也会带来
              额外的同步开销。因此，对于特定的应用程序，应该通过测试来判断增加 CMS 线程数是否真的能够带来性
              能的提升。如果还标志未设置，JVM 会根据并行收集器中的 -XX:ParallelGCThreads 参数的值来计算出
              默认的并行 CMS 线程数。
			
		  33) -XX:ParallelGCThreads：
              配置并行收集器的线程数，即：同时有多少个线程一起进行垃圾回收，此值建议配置与 CPU 数目相等。
			
		  35) -XX:OldSize：
              设置 JVM 启动分配的老年代内存大小，类似于新生代内存的初始大小 -XX:NewSize。
			
			以上就是一些常用的配置参数，有些参数是可以被替代的，配置思路需要考虑的是 Java 提供的垃圾回收机
            制。虚拟机的堆大小决定了虚拟机花费在收集垃圾上的时间和频度。收集垃圾能够接受的速度和应用有关，应该
            通过分析实际的垃圾收集的时间和频率来调整。假如堆的大小很大，那么完全垃圾收集就会很慢，但是频度会
            降低。假如您把堆的大小和内存的需要一致，完全收集就很快，但是会更加频繁。调整堆大小的的目的是最小化
            垃圾收集的时间，以在特定的时间内最大化处理客户的请求。在基准测试的时候，为确保最好的性能，要把堆
            的大小设大，确保垃圾收集不在整个基准测试的过程中出现。
			
			假如系统花费很多的时间收集垃圾，请减小堆大小。一次完全的垃圾收集应该不超过 3-5 秒。假如垃圾收集成
            为瓶颈，那么需要指定代的大小，检查垃圾收集的周详输出，研究垃圾收集参数对性能的影响。当增加处理
            器时，记得增加内存，因为分配能够并行进行，而垃圾收集不是并行的。
</pre>

<pre>
常见的 Java 内存溢出有以下三种

      （1） java.lang.OutOfMemoryError: Java heap space —-JVM Heap（堆）溢出

            JVM 在启动的时候会自动设置 JVM Heap 的值，其初始空间（即-Xms）是物理内存的1/64，
            最大空间（-Xmx）不可超过物理内存。可以利用 JVM提供的 -Xmn -Xms -Xmx 等选项可进行设置。Heap 的
            大小是 Young Generation 和 Tenured Generaion 之和。在 JVM 中如果 98％ 的时间是用于 GC，且
            可用的 Heap size 不足 2％ 的时候将抛出此异常信息。

            解决方法：手动设置 JVM Heap（堆）的大小。  
      （2） java.lang.OutOfMemoryError: PermGen space  —- PermGen space溢出。

            PermGen space 的全称是 Permanent Generation space，是指内存的永久保存区域。为什么会内存溢
            出，这是由于这块内存主要是被 JVM 存放Class 和 Meta 信息的，Class 在被 Load 的时候被放
            入 PermGen space 区域，它和存放 Instance 的 Heap 区域不同，sun 的 GC 不会在主程序运行期
            对 PermGen space 进行清理，所以如果你的 APP 会载入很多 CLASS 的话，就很可能出现 
            PermGen space 溢出。

            解决方法： 手动设置 MaxPermSize 大小

      （3） java.lang.StackOverflowError   —- 栈溢出

           栈溢出了，JVM 依然是采用栈式的虚拟机，这个和 C 与 Pascal 都是一样的。函数的调用过程都体现在堆栈
           和退栈上了。调用构造函数的 “层”太多了，以致于把栈区溢出了。通常来讲，一般栈区远远小于堆区的，因为函
           数调用过程往往不会多于上千层，而即便每个函数调用需要 1K 的空间（这个大约相当于在一个 C 函数内声明
           了 256 个 int 类型的变量），那么栈区也不过是需要 1MB 的空间。通常栈的大小是 1－2MB 的。
           通常递归也不要递归的层次过多，很容易溢出。

           解决方法：修改程序。
</pre>