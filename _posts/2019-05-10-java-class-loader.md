
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

java 采用双亲委派模型.

AppClassloader 的parent clsssloader 是 Extention Class Loader。

Extentsion 的 parent classloader 是BootstrapClassLoader。

BootstrapClassLoader 是根class loader, 是用c/c++ 实现的， 没有parent class loader.

# class是如何被class loader加载的

看一下ClassLoader.loadClass 是如何实现的。
```java
    protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            // First, check if the class has already been loaded
            Class c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }

                if (c == null) {
                    // If still not found, then invoke findClass in order
                    // to find the class.
                    long t1 = System.nanoTime();
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }

```


一个class是有它的classloader和它本身来唯一标识的。
