# TomcatOptimizationTree
Tomcat性能优化技术研究


<pre>
Tomcat内存优化主要是对tomcat启动参数优化，可以在Tomcat的启动脚本catalina.sh脚本中设置
java_opts参数。
</pre>

http://blog.51cto.com/8248183/2062343

<pre>
minSpareThreads ：Tomcat初始化时创建的 socket 线程数
maxSpareThreads： Tomcat连接器的最大空闲 socket 线程数
acceptAccount： 监听端口队列最大数，满了之后客户请求会被拒绝（不能小于maxSpareThreads ）
connectionTimeout： 连接超时
compressionMinSize="2048"启用压缩的输出内容大小，默认为2KB
01、nio(new I/O)，是Java SE 1.4及后续版本提供的一种新的I/O操作方式(即java.nio包及
       其子包)。Java nio是一个基于缓冲区、并能提供非阻塞I/O操作的Java API，因此nio也
       被看成是non-blocking I/O的缩写。它拥有比传统I/O操作(bio)更好的并发运行性能。
</pre>