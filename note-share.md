1. [Java 浅拷贝和深拷贝](https://www.jianshu.com/p/94dbef2de298)   

2. 匿名内部类与正规的继承

匿名内部类与正规的继承相比有些受限，因为匿名内部类即可以扩展类，也可以实现接口，但是不能两者兼备。而且如果是实现接口，也只能实现一个接口。
```
package thinking.in.java;

public class Parce19 {
    public Destination destination(String dest) {
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
        Parce19 p = new Parce19();
        Destination d = p.destination("Destination");
        AbstractDestination ad = p.destination("AbstractDestination", "test");

        System.out.println(d.readLabel());
        System.out.println(ad.readLabel());
    }
}


interface Destination {
    String readLabel();

}

abstract class AbstractDestination {
    abstract String readLabel();
}



```


3. 多态类型转换示例   
```
package test.interview;

interface Base {
//    String getFields();
}

public class Agg implements Base {
    public String getFields() {
        String name = "Agg";
        return name;
    }

    public static void main(String[] args) {
        Base a = new Agg();
        // 此方法在Base中是不存在的，必须把a转换为Agg类的一个实例，这样才可以调用它的方法。
        System.out.println(((Agg) a).getFields());
//        System.out.println(a.getFields());
    }
}
```

```
package test.interview;

class Base {
}

class Agg extends Base {
    public String getFields() {
        String name = "Agg";
        return name;
    }
}

public class Avf {
    public static void main(String[] args) {
        Base a = new Agg();
        //此方法在Base中是不存在的，必须把a转换为Agg类的一个实例，这样才可以调用它的方法。
        System.out.println(((Agg) a).getFields());
    }
}

```


3. 向下转型与运行时类型识别

向上转型--在继承层次中向上移动，会丢失具体的类型信息，但是该操作是安全的。   
向下转型--在继承层次中向下移动，因为基类不会具有大于导出类的接口，所以该操作不安全。

但是在java语言中，所有转型都会得到检查。这种在运行期间对类型进行检查的行为称作“运行时类型识别”（RTTI）。以便保证它的确是我们希望的那种类型，否则就会返回一个**ClassCastException**。

示例：
```
package thinking.in.java.innerclasses.polymorphism;

//: polymorphism/RTTI.java
// Downcasting & Runtime type information (RTTI).
// {ThrowsException}

class Useful {
    public void f() {
        System.out.println("Useful.f()");
    }

    public void g() {
        System.out.println("Useful.g()");
    }
}

class MoreUseful extends Useful {
    public void f() {
        System.out.println("MoreUseful.f()");
    }

    public void g() {
        System.out.println("MoreUseful.f()");
    }

    public void u() {
        System.out.println("MoreUseful.u()");
    }

    public void v() {
    }

    public void w() {
    }
}

public class RTTI {
    public static void main(String[] args) {
        Useful[] x = {
                new Useful(),
                new MoreUseful()
        };
        x[0].f();
        x[1].g();
        // Compile time: method not found in Useful:
        //! x[1].u();
        ((MoreUseful) x[1]).u(); // Downcast/RTTI
        ((MoreUseful) x[0]).u(); // Exception thrown
    }
}
/*
Useful.f()
MoreUseful.f()
MoreUseful.u()
Exception in thread "main" java.lang.ClassCastException: ...*/

```




