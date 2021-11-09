[TOC]

## Spring源码剖析

### 构建工具:Gradle

Spring从大版本5之后就是使用Gradle构建的了,如果想在本地查看spring源代码,需要先安装gradle.安装过程可以看视频或课件笔记

为什么使用Gradle

> 想一想使用mven构建的大型项目,一次编译要花多久?
> 在高性能设备上,可能需要几分钟,在性能稍微差劲的设备上,这个时间可能就是好几倍.
>
> Gradle解决的痛点就是maven构建大型项目速度缓慢的问题,使用了并行编译(paralle build)和缓存,相对maven大大提升了项目构建速度.
>
> 当然maven也可以设置并行编译
>
> ```shell
> mvn -T 4 install -- will use 4 threads
> mvn -T 1C install -- will use 1 thread per available CPU core
> ```
>
> 不过通过这种方式得到的编译加速远比不过gradle使用的缓存机制的加速效果.不过maven在将来也有可能实现缓存加速机制.
>
> 顺便看了一下maven能用来提升编译速度的命令
>
> ```shell
> # -pl:指定需要编译的模块
> # -am:让编译器找出指定模块的依赖来进行编译
> mvn -T 1C install -pl $moduleName –am
> ```
>
> 如果遇到缓存相关的问题,最好在使用上述命令之前执行`maven clean`命令

参考文档:

[如何将maven项目迁移到gradle](https://zhuanlan.zhihu.com/p/185013144)

### BeanDefinition初始化步骤

BeanDefinition代表的是实例化Bean之前的操作,而不是实例化Bean本身

spring通过`BeanDefinitionReader`读取项目配置xml,并用`BeanDefinitionHolder`持有这些配置(此时这些bean还未实例化)

### Ordered,PriorityOrdered

因为spring作为一个框架,相同接口可能有很多实现类,这些实现类应该通过什么顺序调用呢?因此引入Ordered接口,用来指定实现类的调用顺序

### spring中的事件机制

采用**发布-订阅**模式实现(和观察者模式很相似).