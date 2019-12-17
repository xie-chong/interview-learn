public class Test {      public static void main(String[] args) {           SellThread sell = new SellThread();           Thread sell1 = new Thread(sell, "sellman1");           Thread sell2 = new Thread(sell, "sellman2");           Thread sell3 = new Thread(sell, "sellman3");           sell1.start();           sell2.start();           sell3.start();      }     }

 class SellThread implements Runnable {      private int i = 20;      public void run() {          while (true) {              if (i > 0) {                   try {                       Thread.sleep(100);                   } catch (Exception e) {                   }                   System.out.println(Thread.currentThread().getName() + " sell "                            + i--);             }           }        }     }

	结果一共卖掉了22张票，估计电影院会为无缘无故多收门票钱而很高兴，不过一会儿也许就要面对愤怒的顾客了……
	如果这时使用synchronized关键字，就可以避免这种冲突，修改程序如下：     

public class Test {      public static void main(String[] args) {           SellThread sell = new SellThread();           Thread sell1 = new Thread(sell, "sellman1");           Thread sell2 = new Thread(sell, "sellman2");           Thread sell3 = new Thread(sell, "sellman3");           sell1.start();           sell2.start();           sell3.start();      }     }     class SellThread implements Runnable {      private int i = 20;      String a = "now ok!";      public void run() {           while (true) {               synchronized (a) {                    if (i > 0) {                         try {                             Thread.sleep(100);                         } catch (Exception e) {                         }                         System.out.println(Thread.currentThread().getName()                                  + " sell " + i--);                    }                 }            }      }     }

	这样只卖了20张票，synchronized()的括号中需要的是一个class的对象，所以不能直接在括号中写上i，于是这里定义了一个String的对象a。a可以认为是一个标志位，a标志位默认任何人都能使用，这样售票员sellman1的卖票线程拿到了a以后就开始卖票了，同时他把a这个对象标志位置为不可用，然后其他售票员卖票的线程发现他们拿不到a这个对象，就只能先搁置了，一直到sellmanl的卖票线程释放了a，a的标志位就又变成可用，这个时候其他售票员（线程）就可以竞争了，看谁先拿到a这个对象，不过Stringa和卖票没什么关系，所以我们可以用this来代替synchronized()中的a，它和a的效果一样表示谁拿到了this对象才能执行。

	这里有两个容易误解的地方。
