# <p align="center">Java 的快速失败和安全失败</p>


## 一、快速失败（fail—fast）
在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出 Concurrent Modification Exception。

**原理：** 迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变 modCount 的值。每当迭代器使用 hashNext()/next() 遍历下一个元素之前，都会检测 modCount 变量是否为 expectedmodCount 值，是的话就返回遍历；否则抛出异常，终止遍历。

**注意：** 这里异常的抛出条件是检测到 modCount != expectedmodCount 这个条件。如果集合发生变化时修改 modCount 值刚好又设置为了 expectedmodCount 值，则异常不会抛出。因此，不能依赖于这个异常是否抛出而进行并发操作的编程，这个异常只建议用于检测并发修改的 bug。

**场景：** java.util 包下的集合类都是快速失败的，不能在多线程下发生并发修改（迭代过程中被修改）。

## 二、安全失败（fail—safe）
采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。

**原理：** 由于迭代时是对原集合的拷贝进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发 Concurrent Modification Exception。

**缺点：** 基于拷贝内容的优点是避免了 Concurrent Modification Exception，但同样地，迭代器并不能访问到修改后的内容，即：迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。
**场景：** java.util.concurrent 包下的容器都是安全失败，可以在多线程下并发使用，并发修改。


**解决方法我在此整理一下吧：**

1. 在单线程的遍历过程中，如果要进行 remove 操作，可以调用迭代器的 remove 方法而不是集合类的 remove 方法。迭代器会把当前current=null,next维持不变，原来的集合尺寸也相应的减小1.修改后的集合必须要上一个遍历操作完成后才能继续。

2. 使用 java 并发包(java.util.concurrent)中的类来代替 ArrayList 和 HashMap。比如使用 CopyOnWriterArrayList 代替 ArrayList，CopyOnWriterArrayList 在是使用上跟 ArrayList 几乎一样，CopyOnWriter 是写时复制的容器(COW)，在读写时是线程安全的。该容器在对 add 和 remove 等操作时，并不是在原数组上进行修改，而是将原数组拷贝一份，在新数组上进行修改，待完成后，才将指向旧数组的引用指向新数组，所以对于 CopyOnWriterArrayList 在迭代过程并不会发生 fail-fast 现象。但 CopyOnWrite 容器只能保证数据的最终一致性，不能保证数据的实时一致性。对于 HashMap，可以使用 ConcurrentHashMap，ConcurrentHashMap 采用了锁机制，是线程安全的。在迭代方面，ConcurrentHashMap 使用了一种不同的迭代方式。在这种迭代方式中，当 iterator 被创建后集合再发生改变就不再是抛出 ConcurrentModificationException，取而代之的是在改变时new新的数据从而不影响原有的数据 ，iterator 完成后再将头指针替换为新的数据 ，这样 iterator 线程可以使用原来老的数据，而写线程也可以并发的完成改变。即迭代不会发生 fail-fast，但不保证获取的是最新的数据。


```
   public static void main(String args[]) {
        // Create a hash map
        HashMap<String,Object> balance = new HashMap();
        balance.put("Zara", new Double(3434.34));
        balance.put("Mahnaz", new Double(123.22));
        balance.put("Ayan", new Double(1378.00));
        balance.put("Daisy", new Double(99.22));
        balance.put("Qadir", new Double(-19.08));

//    for (Map.Entry<String, Object> entry : balance.entrySet()) {
//            System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
//            if(entry.getKey().equals("Ayan")){
//                balance.put("Ayan","oop");
//            }
//        }

        Iterator it = balance.entrySet().iterator();
        while(it.hasNext()){
            String nextStr = ObjectUtils.toString(it.next());
            String[] strArr = StringUtils.split(nextStr, "=");
            if(StringUtils.equals("Daisy",strArr[0])||StringUtils.equals("Ayan",strArr[0])){
                it.remove();
            }
        }

        Iterator it2 = balance.entrySet().iterator();

        while(it2.hasNext()){
            System.out.println(it2.next());
        }
    }
```





