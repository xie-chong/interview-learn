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



