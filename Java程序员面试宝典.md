1. [ClassLoader 知识](#1)   






----
----
----

---
<h2 id="1">ClassLoader 知识</h2>
---
（1） ClassLoader 基本概念 与 C 或 C++ 编写的程序 不同， Java 程序 并不是 一个 可执行 文件， 而是 由 许多 独立 的 类 文件 组成 的， 每一个 文件 对应 一个 Java 类。 此外， 这些 类 文件 并非 全部 装入 内存， 而是 根据 程序 需要 逐渐 载入。 ClassLoader 是 JVM 实现 的 一部分， ClassLoader 包括 bootstrap classloader（ 启动 类 加载 器）， ClassLoader 在 JVM
