# <p align="center">jstack-DeadLockSample （JAVA 死锁诊断）</p>


## 在windows下使用jstack诊断

### 1. 使用命令找出死锁进程pid

```
$ jps
14080
18836 KotlinCompileDaemon
5160 DeadLockSample
14940 Jps
18956 Launcher

```

### 2. 查看Java堆栈信息

```
$ jstack 5160
2020-01-28 10:56:10
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.231-b11 mixed mode):

"Thread2" #13 prio=5 os_prio=0 tid=0x0000000027534000 nid=0x4740 waiting for monitor entry [0x00000000291cf000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at geek.bsae.DeadLockSample.run(DeadLockSample.java:19)
        - waiting to lock <0x0000000716657740> (a java.lang.String)
        - locked <0x0000000716657778> (a java.lang.String)

"Thread1" #12 prio=5 os_prio=0 tid=0x000000002752a800 nid=0x1898 waiting for monitor entry [0x00000000290cf000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at geek.bsae.DeadLockSample.run(DeadLockSample.java:19)
        - waiting to lock <0x0000000716657778> (a java.lang.String)
        - locked <0x0000000716657740> (a java.lang.String)

"Service Thread" #11 daemon prio=9 os_prio=0 tid=0x00000000274d1800 nid=0x19c4 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C1 CompilerThread3" #10 daemon prio=9 os_prio=2 tid=0x0000000027434000 nid=0x4a58 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread2" #9 daemon prio=9 os_prio=2 tid=0x0000000027431000 nid=0x10c waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread1" #8 daemon prio=9 os_prio=2 tid=0x000000002742f000 nid=0x3a14 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread0" #7 daemon prio=9 os_prio=2 tid=0x0000000027425800 nid=0x564 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Monitor Ctrl-Break" #6 daemon prio=5 os_prio=0 tid=0x000000002740e800 nid=0x2f68 runnable [0x00000000289ce000]
   java.lang.Thread.State: RUNNABLE
        at java.net.SocketInputStream.socketRead0(Native Method)
        at java.net.SocketInputStream.socketRead(SocketInputStream.java:116)
        at java.net.SocketInputStream.read(SocketInputStream.java:171)
        at java.net.SocketInputStream.read(SocketInputStream.java:141)
        at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)
        at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)
        at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)
        - locked <0x00000007167cf040> (a java.io.InputStreamReader)
        at java.io.InputStreamReader.read(InputStreamReader.java:184)
        at java.io.BufferedReader.fill(BufferedReader.java:161)
        at java.io.BufferedReader.readLine(BufferedReader.java:324)
        - locked <0x00000007167cf040> (a java.io.InputStreamReader)
        at java.io.BufferedReader.readLine(BufferedReader.java:389)
        at com.intellij.rt.execution.application.AppMainV2$1.run(AppMainV2.java:64)

"Attach Listener" #5 daemon prio=5 os_prio=2 tid=0x0000000027394000 nid=0x1560 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Signal Dispatcher" #4 daemon prio=9 os_prio=2 tid=0x00000000273ea000 nid=0x3210 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Finalizer" #3 daemon prio=8 os_prio=1 tid=0x0000000002b4e800 nid=0x55c in Object.wait() [0x00000000286ce000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x0000000716508ed8> (a java.lang.ref.ReferenceQueue$Lock)
        at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:144)
        - locked <0x0000000716508ed8> (a java.lang.ref.ReferenceQueue$Lock)
        at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:165)
        at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:216)

"Reference Handler" #2 daemon prio=10 os_prio=2 tid=0x0000000027373000 nid=0x3424 in Object.wait() [0x00000000285cf000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x0000000716506c00> (a java.lang.ref.Reference$Lock)
        at java.lang.Object.wait(Object.java:502)
        at java.lang.ref.Reference.tryHandlePending(Reference.java:191)
        - locked <0x0000000716506c00> (a java.lang.ref.Reference$Lock)
        at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:153)

"main" #1 prio=5 os_prio=0 tid=0x0000000002a52800 nid=0x47ec in Object.wait() [0x000000000289f000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x00000007166577b0> (a geek.bsae.DeadLockSample)
        at java.lang.Thread.join(Thread.java:1252)
        - locked <0x00000007166577b0> (a geek.bsae.DeadLockSample)
        at java.lang.Thread.join(Thread.java:1326)
        at geek.bsae.DeadLockSample.main(DeadLockSample.java:34)

"VM Thread" os_prio=2 tid=0x0000000025ca6800 nid=0x2964 runnable

"GC task thread#0 (ParallelGC)" os_prio=0 tid=0x0000000002a68000 nid=0x2a64 runnable

"GC task thread#1 (ParallelGC)" os_prio=0 tid=0x0000000002a6a000 nid=0x8cc runnable

"GC task thread#2 (ParallelGC)" os_prio=0 tid=0x0000000002a6b800 nid=0x49f0 runnable

"GC task thread#3 (ParallelGC)" os_prio=0 tid=0x0000000002a6d000 nid=0x38b8 runnable

"GC task thread#4 (ParallelGC)" os_prio=0 tid=0x0000000002a70000 nid=0x36ec runnable

"GC task thread#5 (ParallelGC)" os_prio=0 tid=0x0000000002a71800 nid=0x42c runnable

"GC task thread#6 (ParallelGC)" os_prio=0 tid=0x0000000002a74800 nid=0x4d38 runnable

"GC task thread#7 (ParallelGC)" os_prio=0 tid=0x0000000002a75800 nid=0x45b8 runnable

"GC task thread#8 (ParallelGC)" os_prio=0 tid=0x0000000002a77000 nid=0xf30 runnable

"GC task thread#9 (ParallelGC)" os_prio=0 tid=0x0000000002a78000 nid=0x2c98 runnable

"VM Periodic Task Thread" os_prio=2 tid=0x00000000274dc000 nid=0x39c4 waiting on condition

JNI global references: 12


Found one Java-level deadlock:
=============================
"Thread2":
  waiting to lock monitor 0x0000000025cb1f18 (object 0x0000000716657740, a java.lang.String),
  which is held by "Thread1"
"Thread1":
  waiting to lock monitor 0x0000000025cb2128 (object 0x0000000716657778, a java.lang.String),
  which is held by "Thread2"

Java stack information for the threads listed above:
===================================================
"Thread2":
        at geek.bsae.DeadLockSample.run(DeadLockSample.java:19)
        - waiting to lock <0x0000000716657740> (a java.lang.String)
        - locked <0x0000000716657778> (a java.lang.String)
"Thread1":
        at geek.bsae.DeadLockSample.run(DeadLockSample.java:19)
        - waiting to lock <0x0000000716657778> (a java.lang.String)
        - locked <0x0000000716657740> (a java.lang.String)

Found 1 deadlock.

```

### 3. 死锁程序

```
public class DeadLockSample extends Thread {
    private String first;
    private String second;

    public DeadLockSample(String name, String first, String second) {
        super(name);
        this.first = first;
        this.second = second;
    }

    public void run() {
        synchronized (first) {
            System.out.println(this.getName() + " obtained: " + first);
            try {
                Thread.sleep(1000L);
                synchronized (second) {
                    System.out.println(this.getName() + " obtained: " + second);
                }
            } catch (InterruptedException e) {
                // Do nothing
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        String lockA = "lockA";
        String lockB = "lockB";
        DeadLockSample t1 = new DeadLockSample("Thread1", lockA, lockB);
        DeadLockSample t2 = new DeadLockSample("Thread2", lockB, lockA);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
    }
}
```
