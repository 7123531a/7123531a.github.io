
# JVM 提供的 class loader

1. BootstrapClassLoader
加载jre/lib下的jar

2. Launcher.ExtClassLoader
加载\jre\lib\ext下的jar

3. Launcher.AppClassLoader (ClassLoader.getSystemClassLoader())
加载classpath指定的jar

Java还提供了一个叫做Thread context class loader的概念, 当虚拟机启动的时候，AppClassLoader将被设置成当前线程的classloader. 
稍后将会介绍为什么我们需要它。

# class loader hierarchy
java 采用双亲委派模型，BootstrapClassLoader 是根class loader
