[05 | 单件模式(Singleton Pattern)](#05)   




---
---
---
<h2 id="05">05 | 单件模式(Singleton Pattern)</h2>

---

要如何保证一个对象只能被实例化一次？


### 定义单件模式

**单件模式**确保一个类只有一个实例，并提供一个全局访问点。

### 类图   
![](https://github.com/xie-chong/interview-learn/blob/master/note/note-head-first-design-patterns/document-image/uml-05-01.png?raw=true)   

### 剖析经典的单件模式实现

#### 1. 延迟实例化   

该方案不适用于多线程。

```
public class Singleton {
    private static Singleton uniqueInstance;

    private Singleton() {

    }

    public static Singleton getInstance() {
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```

#### 2. “急切”创建实例   

该方案不适用于多线程。依赖JVM在加载这个类时马上创建此类唯一的单件实例。

```
public class Singleton {
    private static Singleton uniqueInstance = new Singleton();

    private Singleton() {

    }

    public static Singleton getInstance() {
        return uniqueInstance;
    }
}
```

#### 3. 多线程处理方案   

同步会降低性能。该方案每次都会同步，其实只有第一次执行此方法时，才需要真正的同步。换句话说，一旦设置好uniqueInstance变量，就不需要同步这个歌方法了。之后的同步都是一种累赘。


```
public class Singleton {
    private static Singleton uniqueInstance;

    private Singleton() {

    }

    public static synchronized Singleton getInstance() {
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```

#### 4. "双重检查枷锁"，在getInstance()中减少使用同步   

 该方案不适用于Java 5以前的版本，早前的版本许多JVM对于volatile关键字的实现会导致双重检查加锁失效。
 
```
 public class Singleton {
    private volatile static Singleton uniqueInstance;

    private Singleton() {

    }

    public static Singleton getInstance() {
        if (uniqueInstance == null) {
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
 
```




















