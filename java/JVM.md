记录极客时间的JVM专栏学习总结，主要分要四个大的部分：基础原理、高效实现、代码优化和黑科技。
# 一 基础原理

## 01 Java代码是如何运行的？
Java代码的运行需要JRE的支持，Java程序的运行主要有以下特点。

### Java程序执行需要JVM
1. 为了可移植性，对硬件兼容
2. JVM代为管理内存、垃圾回收以及一些异常处理，提高开发效率
3. 还提供了诸如数组越界、动态类型、安全权限等等的动态检测

### JVM执行Java的具体过程
1. JVM加载字节码到JVM中，保存在方法区中
![](https://static001.geekbang.org/resource/image/ab/77/ab5c3523af08e0bf2f689c1d6033ef77.png)
2. JVM执行程序借助程序计数器和栈帧（存放局部变量以及字节码的操作数）控制程序执行的流程
3. Java执行时解释执行和即时编译（Just-In-Time compilation，JIT）结合，翻译成机器码。对于热点代码采用即时编译，提高执行效率。
即时编译器有C1和C1编译器，在编译时间和执行效率做了取舍，C1是客户端编译器，编译时间短效率较低；C2编译时间长，效率高。
![](https://static001.geekbang.org/resource/image/5e/3b/5ee351091464de78eed75438b6f9183b.png)

## 02 Java的基本类型

1. boolean类型，jvm映射成int类型，true对应1，false对应0
