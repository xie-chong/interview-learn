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


### 10.3 使用 .this 与 .new

如果需要生成对外部类对象的引用，可以使用外部类的名字后面紧跟原点和**this**。
```
package thinking.in.java.innerclasses;

public class DotThis {
    private int num = 99;

    void f() {
        System.out.println("DotThis.f()");
    }

    public class Inner {
        public DotThis outer() {
            return DotThis.this;
            // A plain "this" would be Inner's "this"
        }

        void test() {
            System.out.println("num = " + num);
            f();
        }
    }

    public Inner inner() {
        return new Inner();
    }

    public static void main(String[] args) {
        DotThis dt = new DotThis();
        DotThis.Inner dti = dt.inner();
        dti.outer().f();

        dti.test();
    }
}
/*
DotThis.f()
num = 99
DotThis.f()*/

```

如果想要告知某些其他对象，去创建其某个内部类的对象，必须在new表达式中提供对其他外部类对象的引用，这需要使用 **.new**语法。   

```
package thinking.in.java.innerclasses;

//: innerclasses/DotNew.java
// Creating an inner class directly using the .new syntax.

public class DotNew {
    public class Inner {
    }

    public static void main(String[] args) {
        DotNew dn = new DotNew();
        DotNew.Inner dni = dn.new Inner();
    }
} ///:~

```

  在拥有外部类对象之前是不可能创建内部类对象的。这是因为内部类对象会暗暗地连接到创建它的外部类对象上。但是，如果你创建的是**嵌套类**（静态内部类），那么它就不需要对外部类对象的引用。


### 10.4 内部类与向上转型

### 10.5 在方法和作用域内的内部类

在一个方法里面或者在任意的作用域内定义内部类。

**这么做的理由**：
1. 你实现了某类型的接口，于是可以创建并返回对其的引用。
2. 你需要解决一个复杂的问题，想创建一个类来辅助你的解决方案。

### 10.6 匿名内部类

```
//: innerclasses/Parcel7.java
// Returning an instance of an anonymous inner class.

public class Parcel7 {
  public Contents contents() {
    return new Contents() { // Insert a class definition
      private int i = 11;
      public int value() { return i; }
    }; // Semicolon required in this case
  }
  public static void main(String[] args) {
    Parcel7 p = new Parcel7();
    Contents c = p.contents();
  }
} ///:~

```

上述匿名内部类的语法是下述形式的简化
```
//: innerclasses/Parcel7b.java
// Expanded version of Parcel7.java

public class Parcel7b {
  class MyContents implements Contents {
    private int i = 11;
    public int value() { return i; }
  }
  public Contents contents() { return new MyContents(); }
  public static void main(String[] args) {
    Parcel7b p = new Parcel7b();
    Contents c = p.contents();
  }
} ///:~

```

如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，那么编译器会要求其参数引用时**final**的。

**与正规的继承相比有些受限，匿名内部类既可以扩展类，也可以实现接口，但是不能两者兼备。而且，如果实现接口，也只能实现一个接口**。

#### 10.6.1 再访工厂方法

### 10.7 嵌套类







































































































