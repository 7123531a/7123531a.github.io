## purpose

ThreadLocal is used to associate the state with a thread, like user id, transacion id etc.

## relationship between thread and thread local

ThreadLocalMap is package private so that Thread can refer to that. The Thread Object has one instance field of type ThreadLocalMap. when set/get/remove method is called on ThreadLocal, they will operate on the ThreadLocalMap of current thread.

TheadLocalMap's key is a weak reference to ThreadLocal object.

Because we could associate multiple variables to one thread, that's why the ThreadLocalMap is designed.

## common usage pattern

```java
public class ThreadLocalBestPractice {

    private static ThreadLocal<Object> threadLocal = new ThreadLocal<>();
    
    public void businessMethod() {
        try {
            // set value
            threadLocal.set(new Object());
            
            // business logic
            // ...
            
        } finally {
            // clean up
            threadLocal.remove();
        }
    }
}

```

## InheritableThreadLocal

Inheritable thread-local variables are used in preference to ordinary thread-local variables when the per-thread-attribute being maintained in the variable(eg UserID) must be automatically transmitted to any child threads that are created.