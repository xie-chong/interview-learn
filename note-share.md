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

、、、
asii
 public class Test{      public static void main(String args[]) {           String aSource[] = { "dad", "bood", "bada", "Admin",  "  ", "Good",      "aete", "cc", "Ko", "Beta", "Could" };      boolean isChanged;      int nMaxIndex = aSource.length-1;      String sTemp = null;      try {           do {                   isChanged = false;                   for (int i = 0; i < nMaxIndex; i++) {                       if (StringCmp(aSource[i], aSource[i + 1]) > 0) {                         sTemp = aSource[i];                         aSource[i] = aSource[i + 1];                         aSource[i + 1] = sTemp;                         isChanged = true;                      }                    }                nMaxIndex--;         } while (isChanged);      } catch (Exception e) {         System.out.print(e.getMessage());      }      for (int i = 0; i < aSource.length; i++) {         System.out.println(aSource[i]);      }     }     /**     *字符串比较函数 */     public static int StringCmp(String s1, String s2) throws Exception {          int nResult = s1.length()-s2.length();          int nLen = Math.min(s1.length(), s2.length());          int nCharOrder1,nCharOrder2;          char aChar1[] = s1.toCharArray();          char aChar2[] = s2.toCharArray();          try {              for (int i = 0; i < nLen; i++) {                   if (aChar1[i] != aChar2[i]) {                       System.out.println(aChar1[i]);                       System.out.println(aChar1[i]-96);                       System.out.println(aChar1[i]-64);                      //如果大于a，则减96，否则减64                       nCharOrder1 = aChar1[i] > 96 ? aChar1[i]-96                                 : aChar1[i]-64;                       nCharOrder2 = aChar2[i] > 96 ? aChar2[i]-96                                 : aChar2[i]-64;                       if (nCharOrder1 != nCharOrder2) {                          nResult = nCharOrder1-nCharOrder2;                          break;                       } else {                          nResult = aChar1[i]-aChar2[i];                          break;                       }                    }              }         } catch (Exception e) {              throw e;         }         return nResult;       }     }
、、、


面试例题4：编写一个字符串截取程序，要求按字节长度截取一个字节数组形势的字符串，字符包含中英文，要求如果最后截取的是半个中文字符，则舍弃它。[美国著名软件I公司2015年面试题]

解析：Web应用程序在浏览器中显示字符串时，由于显示长度的限制，常常需要将字符串截取后再进行显示。但目前很多流行的语言，如C#、Java内部采用的都是 Unicode 16（UCS2）编码，在这种编码中所有的字符都是两个字符，因此，如果要截取的字符串是中、英文、数字混合的，就会产生问题，字符串既有汉字，又有英文字符和数字。要解决这个问题的方法是首先得到该字符串的UCS2编码的字节数组，由于上面生成的字节数组中前两个字节是标志位，bytes[0] =-2，bytes[1] =-1，因此，要从第三个字节开始扫描，对于一个英文或数字字符，UCS2编码的第二个字节是相应的ASCII，第一个字节是0，如a的UCS2编码是097，而汉字两个字节都不为0，因此，可以利于UCS2编码的这个规则来计算实际的字节数。答案：代码如下。     public class a1 {            /**          * @param args          */           public static void main(String[] args) {             // TODO Auto-generated method stub             Stringstr="中国A我";             byte[] b="s".getBytes();             new a1().A(str,4);         }           public  void A(String str,int i) {             byte b[] = new byte[1024];             int num = 0;             b = str.getBytes();             if(b[i-1]>0) {                 System.out.println(new String(b,0,i));             }else {                 for(int j=0;j<i;j++) {                     if(b[j]<0) {                         num++;                         num = num%2;                     }else {                         num = 0;                     }                 }                 if(num==0) {                     System.out.println(new String(b,0,i)); }else {                     System.out.println(new String(b,0,i-1));                 }             }         }     } 



面试例题3：编写一个截取字符串的函数，输入为一个字符串和字节数，输出为按字节截取的字符串。但是要保证汉字不被截半个，如“"我ABC",4”，应该截为“我AB”，输入“"我ABC汉DEF",6”，应该输出为“我ABC”，而不是“我ABC+汉的半个”。[美国著名软件公司GS2009年11月面试题]解析：本题不能直接使用String类的substring(int beginIndex, intendIndex)方法，因为它是按char计算的。“爱”和“a”都被作为一个字符来看待，length都是1。string.getbytes()是按byte计算的，搞清楚这个应该就能懂了，一个汉字的getbytes().length==2。如果原始字符不为null，也不是空字符串，将原始字符串转换为GBK编码格式，要截取的字节数大于0，且小于原始字符串的字节数。按照字符来分解字符串的，如果遇到中文汉字，截取字节总数减1。

、、、
答案：代码如下。     import java.io.UnsupportedEncodingException;     public class Test {          public static boolean isChineseChar(char c)                  throws UnsupportedEncodingException {              return String.valueOf(c).getBytes("GBK").length > 1;          }          public static String cutstring(String orignal, int count)                  throws UnsupportedEncodingException {              if (orignal != null && !"".equals(orignal)) {                   orignal = new String(orignal.getBytes(), "GBK");                   if (count > 0 && count < orignal.getBytes("GBK").length) {                      StringBuffer buff = new StringBuffer();                      char c;                      for (int i = 0; i < count-1; i++) {                          c = orignal.charAt(i);                          buff.append(c);                          if (Test.isChineseChar(c)) {                             --count;                          }                      }                      return buff.toString();                  }              }              return orignal;          }               public static void main(String[] args) {              String s ="我gfr是Chinese";    
try {                  System.out.println("cut1bit" + Test.cutstring(s, 1));                  System.out.println("cut2bit" + Test.cutstring(s, 2));                  System.out.println("cut4bit" + Test.cutstring(s, 6));                  System.out.println("cut6bit" + Test.cutstring(s, 7));              } catch (UnsupportedEncodingException e) {                  e.printStackTrace();              }          }      }
、、、



```
package test.interview;

import java.util.Comparator;
import java.util.PriorityQueue;
import java.util.Queue;

public class PriorityQueueTest {

    public static void main(String[] args) {
//        System.out.println((6 - 1) >>> 1);
        Queue<Integer> queue = new PriorityQueue<>(10, new Comparator<Integer>() {
            @Override
            public int compare(Integer i, Integer j) {
                int result = i % 2 - j % 2;
                if (result == 0) {
                    result = i - j;
                }
                return result;
            }
        });

        for (int i = 0; i < 10; i++) {
            queue.offer(i);
        }

        for (int i = 0; i < 10; i++) {
            System.out.println(queue.poll());
        }
    }
}

```



