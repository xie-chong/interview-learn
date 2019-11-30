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
```
package thinking.in.java.innerclasses;

public class Parcel9 {
    public Destination destination(final String dest) {
        return new Destination() {
            private String label = dest;

            public String readLabel() {
                return label;
            }
        };
    }

    public AbstractDestination destination(String dest, String ab) {
        return new AbstractDestination() {
            private String label = dest;

            public String readLabel() {
                return label;
            }
        };
    }

    public static void main(String[] args) {
        Parcel9 p = new Parcel9();
        Destination d = p.destination("Destination");
        AbstractDestination ad = p.destination("AbstractDestination", "test");

        System.out.println(d.readLabel());
        System.out.println(ad.readLabel());
    }
}


//interface Destination {
//    String readLabel();
//
//}
//
abstract class AbstractDestination {
    abstract String readLabel();
}


```


#### 10.6.1 再访工厂方法



### 10.7 嵌套类   

如果不需要内部类对象与其外围类对象之间有联系，那么可以将内部类申明为**static**。这通常称为**嵌套类**。   

**嵌套类意味着**：
1. 要创建嵌套类的对象，并不需要其外围类的对象。
2. 不能从嵌套类的对象中访问非静态的外围类对象。
3. 嵌套类与普通的内部类还有一个区别。普通内部类的字段与方法，只能放在类的外部层次上，所以普通的内部类不能有**static**数据和**static**字段，也不能包含嵌套类。但是嵌套类可以包含所有这些东西。

```
package thinking.in.java.innerclasses;

//: innerclasses/DotThis.java
// Qualifying access to the outer-class object.
public class Parcel11 {
    private static class ParcelContents implements Contents {
        private int i = 11;

        public int value() {
            return i;
        }
    }

    protected static class ParcelDestination
            implements Destination {
        private String label;

        private ParcelDestination(String whereTo) {
            label = whereTo;
        }

        public String readLabel() {
            return label;
        }

        // Nested classes can contain other static elements:
        public static void f() {
        }

        static int x = 10;

        static class AnotherLevel {
            public static void f() {
            }

            static int x = 10;
        }
    }

    public static Destination destination(String s) {
        return new ParcelDestination(s);
    }

    public static Contents contents() {
        return new ParcelContents();
    }

    public static void main(String[] args) {
//        Contents c = contents();
//        Destination d = destination("Tasmania");

        // 不要静态方法，直接创建内部静态类也是可以的
        Contents c = new ParcelContents();
        Destination d = new ParcelDestination("Tasmania");

        System.out.println("c.value(): " + c.value());
        System.out.println("d.readLabel(): " + d.readLabel());
    }
}

interface Destination {
    String readLabel();

}

interface Contents {
    int value();
}
```

#### 10.7.1 接口内部的类

正常情况下，不能在接口内部放置任何代码，但嵌套类可以作为接口的一部分。你放到接口中的任何类目都自动地是**public**和**static**的。因为类时**static**的，只是将嵌套类置于接口的命名空间内，这并不违反接口的规则。你甚至可以在内部类中实现其外围接口。

如果你想要创建某些公共代码，使得他们可以被某个接口的所有不同实现所共用，那么使用接口内部的嵌套类会显得很方便。

```
package thinking.in.java.innerclasses;

//: innerclasses/ClassInInterface.java
// {main: ClassInInterface$Test}

public interface ClassInInterface {
    void howdy();

    class Test implements ClassInInterface {
        public void howdy() {
            System.out.println("Howdy!");
        }

        public static void main(String[] args) {
            new Test().howdy();
        }
    }
} /* Output:
Howdy!
*///:~

```

#### 10.7.2 从多层嵌套中访问外部类的成员

一个内部类被嵌套多少层并不重要--它能透明地访问所有它所嵌入的外围类的所有成员。   

```
package thinking.in.java.innerclasses;

//: innerclasses/MultiNestingAccess.java
// Nested classes can access all members of all
// levels of the classes they are nested within.

class MNA {
    private void f() {
    }

    class A {
        private void g() {
        }

        public class B {
            void h() {
                g();
                f();
            }
        }
    }
}

public class MultiNestingAccess {
    public static void main(String[] args) {
        MNA mna = new MNA();
        MNA.A mnaa = mna.new A();
        MNA.A.B mnaab = mnaa.new B();
        mnaab.h();
    }
} ///:~

```


### 10.8 为什么需要内部类

**答案**：   

1. 可以认为内部类提供了某种进入其外围类的窗口。
2. 最吸引人的原因：每个内部类都能独立地继承自一个（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响。
3. 内部类使得多重继承的解决方案变得完整。也就是说，内部类允许继承多个非接口类型（类或抽象类）。



使用内部类，还可以获得其他一些特性：   

1. 内部类可以有多个实例，每个实例都有自己的状态信息，并且与外围类对象的信息相互独立。
2. 在单个外围类中，可以让多个内部类以不同的方式实现同一个接口，或继承同一个类。
3. 创建内部类对象的时刻并不依赖于外围类对象的创建。
4. 内部类并没有令人迷惑的“is-a”关系，它就是一个独立的实体。


考虑这样一种情形：即必须在一个类中一某种方式实现两个接口。由于接口的灵活性，你有两种选择：**使用单一类，或者使用内部类**   

```
package thinking.in.java.innerclasses;

//: innerclasses/MultiInterfaces.java
// Two ways that a class can implement multiple interfaces.

interface A {
}

interface B {
}

class X implements A, B {
}

class Y implements A {
    B makeB() {
        // Anonymous inner class:
        return new B() {
        };
    }
}

public class MultiInterfaces {
    static void takesA(A a) {
    }

    static void takesB(B b) {
    }

    public static void main(String[] args) {
        X x = new X();
        Y y = new Y();
        takesA(x);
        takesA(y);
        takesB(x);
        takesB(y.makeB());
    }
} ///:~


```

**如果拥有的是抽象的类或具体的类**，而不是接口，那就只能使用内部类才能实现多重继承。

```
package thinking.in.java.innerclasses;

//: innerclasses/MultiImplementation.java
// With concrete or abstract classes, inner
// classes are the only way to produce the effect
// of "multiple implementation inheritance."

class D {
}

abstract class E {
}

class Z extends D {
    E makeE() {
        return new E() {
        };
    }
}

public class MultiImplementation {
    static void takesD(D d) {
    }

    static void takesE(E e) {
    }

    public static void main(String[] args) {
        Z z = new Z();
        takesD(z);
        takesE(z.makeE());
    }
} ///:~


```


#### 10.8.1 闭包与调用   

**闭包**（closure）是一个可调用的对象，它记录一些信息，这些信息来自于创建它的作用域。通过这个定义，可以看出内部类是面向对象的闭包，因为它不仅包含外围类对象（创建内部类的作用域）的信息，还自动拥有一个指向此外围类对象的引用，在此作用域内，内部类有权操作所有的成员，包括private成员。   

通过内部类提闭包的功能是优良解决java语言指针的方案，它比指针更灵活、更安全。

```
package thinking.in.java.innerclasses;

//: innerclasses/Callbacks.java
// Using inner classes for callbacks

interface Incrementable {
    void increment();
}

// Very simple to just implement the interface:
class Callee1 implements Incrementable {
    private int i = 0;

    public void increment() {
        i++;
        System.out.println(i);
        ;
    }
}

class MyIncrement {
    public void increment() {
        System.out.println("Other operation");
    }

    static void f(MyIncrement mi) {
        mi.increment();
    }
}

// If your class must implement increment() in
// some other way, you must use an inner class:
class Callee2 extends MyIncrement {
    private int i = 0;

    public void increment() {
        super.increment();
        i++;
        System.out.println(i);
    }

    private class Closure implements Incrementable {
        public void increment() {
            // Specify outer-class method, otherwise
            // you'd get an infinite recursion:
            Callee2.this.increment();
        }
    }

    Incrementable getCallbackReference() {
        return new Closure();
    }
}

class Caller {
    private Incrementable callbackReference;

    Caller(Incrementable cbh) {
        callbackReference = cbh;
    }

    void go() {
        callbackReference.increment();
    }
}

public class Callbacks {
    public static void main(String[] args) {
        Callee1 c1 = new Callee1();
        Callee2 c2 = new Callee2();
        MyIncrement.f(c2);
        Caller caller1 = new Caller(c1);
        Caller caller2 = new Caller(c2.getCallbackReference());
        caller1.go();
        caller1.go();
        caller2.go();
        caller2.go();
    }
} /* Output:
Other operation
1
1
2
Other operation
2
Other operation
3
*///:~

```


#### 10.8.2 内部类与控制框架   


### 10.9 内部类的继承   

因为内部类的构造器必须链接到指向其外围类对象的引用，所以在继承内部类的时候，事情会变得有点复杂。问题在于，那个指向外围类对象的“秘密的”引用必须被初始化，而在导出类中不再存在可连接的默认对象。要解决这个问题，必须使用特殊的语法来明确说清他们之间的关联。

```
package thinking.in.java.innerclasses;

//: innerclasses/InheritInner.java
// Inheriting an inner class.

class WithInner {
    class Inner {
    }
}

public class InheritInner extends WithInner.Inner {
    //! InheritInner() {} // Won't compile
    InheritInner(WithInner wi) {
        wi.super();
    }

    public static void main(String[] args) {
        WithInner wi = new WithInner();
        InheritInner ii = new InheritInner(wi);
    }
} ///:~

```

可以看到，**InheritInner**只能继承自内部类，而不是外围类。但是当要生成一个构造器时，默认的构造器并不算好，而且不能只是传递一个指向外围类对象的引用。还必须在构造器内使用如下语法：**encloseingClassReference.super();** 这样才提供了必要的引用，然后程序才能编译通过。


### 10.10 内部类可以被覆盖吗   

如果创建一个内部类，然后继承其外围类并重新定义此内部类时，会发生什么呢？内部类可以被覆盖吗？（其实并不起什么作用）
```
package thinking.in.java.innerclasses;

//: innerclasses/BigEgg.java
// An inner class cannot be overriden like a method.

class Egg {
    private Yolk y;

    protected class Yolk {
        public Yolk() {
            System.out.println("Egg.Yolk()");
        }
    }

    public Egg() {
        System.out.println("New Egg()");
        y = new Yolk();
    }
}

public class BigEgg extends Egg {
    public class Yolk {
        public Yolk() {
            System.out.println("BigEgg.Yolk()");
        }
    }

    public static void main(String[] args) {
        new BigEgg();
    }
} /* Output:
New Egg()
Egg.Yolk()
*///:~


```
这个例子说明，当继承了某个外围类的时候，内部类并没有发生什么特别神奇的变化。这两个内部类时完全独立的两个实体，各自在自己的命名空间内。

当然，明确的继承某个内部类也是可以的。
```
package thinking.in.java.innerclasses;

//: innerclasses/BigEgg2.java
// Proper inheritance of an inner class.

class Egg2 {
    protected class Yolk {
        public Yolk() {
            System.out.println("Egg2.Yolk()");
        }

        public void f() {
            System.out.println("Egg2.Yolk.f()");
        }
    }

    private Yolk y = new Yolk();

    public Egg2() {
        System.out.println("New Egg2()");
    }

    public void insertYolk(Yolk yy) {
        y = yy;
    }

    public void g() {
        y.f();
    }
}

public class BigEgg2 extends Egg2 {
    public class Yolk extends Egg2.Yolk {
        public Yolk() {
            System.out.println("BigEgg2.Yolk()");
        }

        public void f() {
            System.out.println("BigEgg2.Yolk.f()");
        }
    }

    public BigEgg2() {
        insertYolk(new Yolk());
    }

    public static void main(String[] args) {
        Egg2 e2 = new BigEgg2();
        e2.g();
    }
} /* Output:
Egg2.Yolk()
New Egg2()
Egg2.Yolk()
BigEgg2.Yolk()
BigEgg2.Yolk.f()
*///:~

```

### 10.11 局部内部类   

典型的方式是在一个方法体的里面创建。
局部内部类不能有访问说明符，因为它不是外围类的一部分，但是它可以访问但钱代码块内的常量以及此外围类的所有成员。
```
//: innerclasses/LocalInnerClass.java
// Holds a sequence of Objects.
import static net.mindview.util.Print.*;

interface Counter {
  int next();
}	

public class LocalInnerClass {
  private int count = 0;
  Counter getCounter(final String name) {
    // A local inner class:
    class LocalCounter implements Counter {
      public LocalCounter() {
        // Local inner class can have a constructor
        print("LocalCounter()");
      }
      public int next() {
        printnb(name); // Access local final
        return count++;
      }
    }
    return new LocalCounter();
  }	
  // The same thing with an anonymous inner class:
  Counter getCounter2(final String name) {
    return new Counter() {
      // Anonymous inner class cannot have a named
      // constructor, only an instance initializer:
      {
        print("Counter()");
      }
      public int next() {
        printnb(name); // Access local final
        return count++;
      }
    };
  }	
  public static void main(String[] args) {
    LocalInnerClass lic = new LocalInnerClass();
    Counter
      c1 = lic.getCounter("Local inner "),
      c2 = lic.getCounter2("Anonymous inner ");
    for(int i = 0; i < 5; i++)
      print(c1.next());
    for(int i = 0; i < 5; i++)
      print(c2.next());
  }
} /* Output:
LocalCounter()
Counter()
Local inner 0
Local inner 1
Local inner 2
Local inner 3
Local inner 4
Anonymous inner 5
Anonymous inner 6
Anonymous inner 7
Anonymous inner 8
Anonymous inner 9
*///:~

```


居然局部内部类的名字在方法外是不可见的，那为什么我们仍然使用局部内部类而不是匿名内部类？
1. 我们需要一个已命名的构造器或者需要重载构造器，而匿名内部类只能用于实例初始化。  
2. 需要不止一个该内部类的对象。


### 10.12 内部类标识   

内部类必须生成一个.class文件以包含他们的Class对象信息。
命名规则：外围类的名字，加上"$"，再加上内部类的名字。
如果内部类是匿名的，编译器会简单地产生一个数字作为其标识符。
如果内部类是嵌套在别的内部类之中，只需要直接将它们的名字加在其外围类标识符与"$"的后面。









