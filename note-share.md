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

**向上转型**--在继承层次中向上移动，会丢失具体的类型信息，但是该操作是安全的。   
**向下转型**--在继承层次中向下移动，因为基类不会具有大于导出类的接口，所以该操作不安全。

但是在java语言中，所有转型都会得到检查。这种在运行期间对类型进行检查的行为称作“**运行时类型识别**”（RTTI）。以便保证它的确是我们希望的那种类型，否则就会返回一个**ClassCastException**。

示例：
```
package thinking.in.java.polymorphism;

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


4. 构造器内部的多态方法的行为   

如果在一个构造器的内部调用正在构造的对象的某个动态绑定方法，那会发生什么情况？

在一般的方法内部，动态绑定的调用是在运行时才决定的，因为对象无法知道它是属于方法所在的那个类，还是属于那个类的导出类？

**如果要调用构造器内部的一个动态绑定方法，就要用到那个方法的被覆盖后的定义**。然而，这个调用的效果可能相当难于预料，因为被覆盖的方法在对象被完全构造之前就会被调用。这可能会造成一些难于发现的隐藏错误（方法所操纵的成员可能还未初始化,然而，一个动态绑定的方法调用却会向外深入到继承层次结构内部，它可以调用导出类里的方法。）。

```
package thinking.in.java.polymorphism;

//: polymorphism/PolyConstructors.java
// Constructors and polymorphism
// don't produce what you might expect.

class Glyph {
    void draw() {
        System.out.println("Glyph.draw()");
    }

    Glyph() {
        System.out.println("Glyph() before draw()");
        draw();
        System.out.println("Glyph() after draw()");
    }
}

class RoundGlyph extends Glyph {
    private int radius = 1;

    RoundGlyph(int r) {
        radius = r;
        System.out.println("RoundGlyph.RoundGlyph(), radius = " + radius);
    }

    void draw() {
        System.out.println("RoundGlyph.draw(), radius = " + radius);
    }
}

public class PolyConstructors {
    public static void main(String[] args) {
        new RoundGlyph(5);
    }
} /* Output:
Glyph() before draw()
RoundGlyph.draw(), radius = 0
Glyph() after draw()
RoundGlyph.RoundGlyph(), radius = 5
*///:~

```

5. 
11.4.1　冒泡排序面试例题1：请用Java写出一个冒泡排序程序，要求输入10个整数，输出排序结果。解析：本题考查交换排序中冒泡排序的问题。答案：程序如下：

、、、

    public class Test{              public static void bubbleSort（int[] source） {                            for （int i = source.length-1；i ＞ 0；i——） {                     for （int j = 0；j ＜ i；j++） {                         if （source[j] ＞ source[j + 1]） {                             swap（source，j，j + 1）；                         }                     }                 }                  }                      private static void swap（int[] source，int x，int y） {                     int temp = source[x]；                     source[x] = source[y]；                     source[y] = temp；                 }           public   static   void   main（String[]   args）   {          int [] a = {4，2，1，6，3，6，0，-5，1，1 }；          int i；          bubbleSort（a）；               for（i=0；i ＜10；i++）           System.out.printf（"％d  "，a[i]）；      }     }

、、、





、、、

 public class Test{              public static void selectSort（int[] source） {                        for （int i = 0；i ＜ source.length；i++） {                 for （int j = i + 1；j ＜ source.length；j++） {                     if （source[i] ＞ source[j]） {                         swap（source，i，j）；                     }                 }             }                  }

     private static void swap（int[] source，int x，int y） {                     int temp = source[x]；                     source[x] = source[y]；                     source[y] = temp；                 }           public   static   void   main（String[]   args）   {          int [] a = {4，2，1，6，3，6，0，-5，1，1 }；          int i；          selectSort（a）；               for（i=0；i ＜10；i++）           System.out.printf（"％d  "，a[i]）；      }     }
、、、


Java 插入排序程序
、、、
 public class Test{              public static void selectSort（int[] source） {                     for （int i = 1；i ＜ source.length；i++） {                 for （int j = i；（j ＞ 0） ＆＆ （source[j] ＜ source[j-1]）；j——） {                     swap（source，j，j-1）；                 }             }                  }                      private static void swap（int[] source，int x，int y） {                     int temp = source[x]；                     source[x] = source[y]；                     source[y] = temp；                 }           public   static   void   main（String[]   args）   {       int [] a = {4，2，1，6，3，6，0，-5，1，1 }；          int i；          selectSort（a）；               for（i=0；i ＜10；i++）           System.out.printf（"％d  "，a[i]）；      }     }
、、、

Java 希尔排序程序

、、、
 public class Test {        public static int[] a = { 4，2，1，6，3，6，0，-5，1，1 }；//预设数据数组             public static void main（String args[]） {          int i；    //循环计数变量          int Index = a.length；//数据索引变量              System.out.print（"排序前："）；          for （i = 0；i ＜ Index-1；i++）            System.out.printf（"％3s "，a[i]）；          System.out.println（""）；               ShellSort（Index-1）； //选择排序          //排序后的结果         System.out.print（"排序后："）；          for （i = 0；i ＜ Index-1；i++）           System.out.printf（"％3s "，a[i]）；          System.out.println（""）；        }             public static void ShellSort（int Index） {          int i，j，k；   //循环计数变量          int Temp；        //暂存变量          boolean Change；  //数据是否改变          int DataLength；  //分割集合的间隔长度          int Pointer；     //进行处理的位置               DataLength = （int） Index / 2； //初始集合间隔长度               while （DataLength ！= 0）        //数列仍可进行分割          {            //对各个集合进行处理            for （j = DataLength；j ＜ Index；j++） {              Change = false;
 Temp = a[j]；              //暂存Data[j]的值，待交换值时用              Pointer = j-DataLength；  //计算进行处理的位置                   //进行集合内数值的比较与交换值              while （Temp ＜ a[Pointer] ＆＆ Pointer ＞= 0 ＆＆ Pointer ＜= Index） {                a[Pointer + DataLength] = a[Pointer]；                //计算下一个欲进行处理的位置                Pointer = Pointer-DataLength；                Change = true；                if （Pointer ＜ 0 ｜｜ Pointer ＞ Index）                  break；              }              //与最后的数值交换              a[Pointer + DataLength] = Temp；                   if （Change） {                //打印目前排序结果               System.out.print（"排序中："）；                for （k = 0；k ＜ Index；k++）                  System.out.printf（"％3s "，a[k]）；                System.out.println（""）；              }            }            DataLength = DataLength / 2；//计算下次分割的间隔长度          }        }      }

、、、








