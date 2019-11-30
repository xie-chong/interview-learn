[第 10 章 内部类](#10)   



---
<h2 id="10">第 10 章 内部类</h2>

---

可以将一个类的定义放在另一个类的定义内部，这就是**内部类**。

### 10.1 创建内部类
**一般**：
```
package thinking.in.java.innerclasses;

//: innerclasses/Parcel1.java
// Creating inner classes.

public class Parcel1 {
    class Contents {
        private int i = 11;

        public int value() {
            return i;
        }
    }

    class Destination {
        private String label;

        Destination(String whereTo) {
            label = whereTo;
        }

        String readLabel() {
            return label;
        }
    }

    // Using inner classes looks just like
    // using any other class, within Parcel1:
    public void ship(String dest) {
        Contents c = new Contents();
        Destination d = new Destination(dest);
        System.out.println(d.readLabel());
    }

    public static void main(String[] args) {
        Parcel1 p = new Parcel1();
        p.ship("Tasmania");
    }
} /* Output:
Tasmania
*///:~

```


**典型：** 外部类将有一个方法，该方法返回一个指向内部类的引用。
```
package thinking.in.java.innerclasses;

//: innerclasses/Parcel2.java
// Returning a reference to an inner class.

public class Parcel2 {
    class Contents {
        private int i = 11;

        public int value() {
            return i;
        }
    }

    class Destination {
        private String label;

        Destination(String whereTo) {
            label = whereTo;
        }

        String readLabel() {
            return label;
        }
    }

    public Destination to(String s) {
        return new Destination(s);
    }

    public Contents contents() {
        return new Contents();
    }

    public void ship(String dest) {
        Contents c = contents();
        Destination d = to(dest);
        System.out.println(d.readLabel());
    }

    public static void main(String[] args) {
        Parcel2 p = new Parcel2();
        p.ship("Tasmania");
        Parcel2 q = new Parcel2();
        // Defining references to inner classes:
        Parcel2.Contents c = q.contents();
        Parcel2.Destination d = q.to("Borneo");
    }
} /* Output:
Tasmania
*///:~

```


如果想从外部类的非静态方法之外的任意位置创建某个内部类对象，那么必须具体地指明这个对象的类型：**OuterClassName.InnerclassName**。


### 10.2 链接到外部类

**内部类自动拥有对其外围类所有成员的访问权**。   
    这是如何做到的呢？   
    当某个外围类的对象创建了一个内部类对象时，此内部类对象必定会秘密的捕获一个指向那个外围类对象的引用。然后，在你访问此外围类的成员时，就是用那个引用来选择外围的成员。幸运的是，编译器会帮你处理所有的细节，但你现在可以看到：**内部类的对象只能在与其外围类的对象相关联的情况下才能被创建（就像你应该看到的，在内部类是非static类时）**。构建内部类对象时，需要一个指向其外围类对象的引用，如果编译器访问不到这个引用就会报错。


### 10.3 使用.this 与 .new

如果需要生成对外部类对象的引用，可以使用外部类的名字后面紧跟原点和**this**。
```

```

如果想要告知某些其他对象，去创建其某个内部类的对象，必须在new表达式中提供对其他外部类对象的引用，这需要使用**.new**语法。   
```

```
    在拥有外部类对象之前是不可能创建内部类对象的。这是因为内部类对象会暗暗地链接打死创建它的外部类对象上。但是，如果你创建的是**嵌套类**（静态内部类），那么它就不需要对外部类对象的引用。






