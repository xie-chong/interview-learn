<p align="center">instanceof 和 isInstance 的区别</p>

在Think in Java第十四章遇到这个问题，一些博客模糊提到了isInstance是instanceof的动态实现，查阅文档参考SOF上的一些回答如下：

## 1. obj.instanceof(class)

**表示对象obj是否是class类或其子类的对象**

1. 一个对象是自身类的一个对象
2. 一个对象是自身类父类和接口的一个对象
3. 所有对象都是Object类的对象
4. 凡是null有关的都是false


## 2. class.isInstance(obj)

文档中这样描述   
>Determines if the specified Object is assignment-compatible with the object represented by this Class. This method is the dynamic equivalent of the Java language instanceof operator.

**即对象obj能否转化为类class的对象，动态等价于instanceof**

1. 一个对象能转化为自身类的对象
2. 一个对象能被转化为自身类的父类和实现的接口的对象
3. 所有对象都能转化为Object类的对象
4. 凡是null有关的都是false

可见与instanceof用法相同，关键在于动态等价

## 3. 动态等价性

```
class Father{}
class Son extends Father{}

public class Test{
    public static boolean DynamicEqual(Object fatherObj,Object sonObj){
        return fatherObj.getClass().isInstance(sonObj); // pass
        // return sonObj instanceof Father; // pass
        // return sonObj instanceof (fatherObj.getClass()); //error
    }
    public static void main(String[] args){
        //same using
        Father father = new Father();
        Son son = new Son();
        System.out.println(son instanceof Son); // true
        System.out.println(son instanceof Father); // true
        System.out.println(son instanceof Object); // true
        System.out.println(null instanceof Object); // false
        System.out.println();

        System.out.println(Son.class.isInstance(son)); // true
        System.out.println(Father.class.isInstance(son)); // true
        System.out.println(Object.class.isInstance(son)); // true
        System.out.println(Object.class.isInstance(null)); // false
        System.out.println();
        //different using
        System.out.println(DynamicEqual(father, son));
    }
}
```

* 对obj.instanceof(class)，在编译时编译器需要知道类的具体类型

* 对class.isInstance(obj)，编译器在运行时才进行类型检查，故可用于反射，泛型中

参考：

https://stackoverflow.com/questions/8692214/when-to-use-class-isinstance-when-to-use-instanceof-operator

https://stackoverflow.com/questions/15757014/isinstance-instanceof-why-theres-no-generic-way

http://blog.csdn.net/u010002184/article/details/79306195
