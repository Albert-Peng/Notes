# Java异常机制

## 1 Java异常体系结构

![Java体系结构图](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1557074066848&di=a220962754d3ce9e8d5633fd33deb4ac&imgtype=0&src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20170913%2F277eb90e137d4574b016d63aaedde3aa.png)  

### 1.1 checked异常和unchecked异常

checked异常（编译时异常）可以在编译期中修复，期望程序可以从checked异常中恢复过来，因此需要在程序中显式处理，否则不能通过编译；unchecked异常无需显式处理，程序不能恢复，由Error和RuntimeException组成，编译器不检查方法是否处理或者抛出这两种类型的异常，因此编译期间出现这种类型的异常也不会报错，默认由虚拟机提供处理方式。

### 1.2 常见的RuntimeException

- NullPointerException：空指针异常，试图访问null

- ClassCastException：类型转换异常，子类可以直接转换为父类或者接口类，否则可能会抛出类型转换异常，可以在强制转换前，使用instance of关键字进行判断。

- ArrayIndexOutOfBoundsException:数组越界异常

- ArithmeticException：算术错误异常，比如除以0

- ArrayStoreException：数组存储异常，数组存储非声明类型对象

### 1.3 常见的checked Exception

- IOException：IO异常

- ClassNotFoundException:不能找到类异常

- SQLException：SQL异常

- FileNotFoundException：文件未找到

### 1.4 常见的Error

- InstantiationError：实例化错误，当一个应用试图通过Java的new操作符构造一个抽象类或者接口时抛出该异常

- InternalError：虚拟机内部错误

- NoClassDefFoundError：未找到类定义错误。当Java虚拟机或者类装载器试图实例化某个类，而找不到该类的定义时抛出该错误。

- NoSuchFieldError：域不存在错误。当应用试图访问或者修改某类的某个域，而该类的定义中没有该域的定义时抛出该错误。

- NoSuchMethodError：方法不存在错误。当应用试图调用某类的某个方法，而该类的定义中没有该方法的定义时抛出该错误。

- OutOfMemoryError：内存不足错误。当可用内存不足以让Java虚拟机分配给一个对象时抛出该错误。

- StackOverflowError：堆栈溢出错误。当一个应用递归调用的层次太深而导致堆栈溢出时抛出该错误。

- ThreadDeath：线程结束。当调用Thread类的stop方法时抛出该错误，用于指示线程结束。

### 1.5 面试题目：ClassNotFoundException和NoClassDefFoundError

- ClassNotFoundException是编译异常，NoClassDefFoundError是运行时Error，程序可以尝试从异常中恢复，却不会尝试从错误中恢复，程序会直接崩溃

- ClassNotFoundException是JVM加载类时，在类路径下找不到类，就会抛出ClassNotFoundException异常，比如通过反射Class.forName()加载类以及ClassLoader.loadClass()加载时，可能会抛出异常

- NoClassDefFoundError是Java虚拟机在编译时能找到合适的类，而在运行时不能找到合适的类导致的错误。

- ClassNotFoundException产生的原因往往就是类路径下，没有需要的类，只需要将相关的类放到路径下或者添加依赖就能解决。

#### NoClassDefError的产生原因

- 类没有打包进jar包中，或者manifest声明不正确；
- classpath在运行时被覆盖，导致和编译时的classpath不一致；
- 因为NoClassDefFoundError是java.lang.LinkageError的一个子类，所以可能由于程序依赖的原生的类库不可用而导致；
- 检查日志文件中是否有java.lang.ExceptionInInitializerError这样的错误，NoClassDefFoundError有可能是由于静态初始化失败导致的；
- 如果你工作在J2EE的环境，有多个不同的类加载器，也可能导致NoClassDefFoundError

#### NoClassDefFound的解决办法

- 当发生由于缺少jar文件，或者jar文件没有添加到classpath，或者jar的文件名发生变更会导致java.lang.NoClassDefFoundError的错误。

- 当类不在classpath中时，这种情况很难确切的知道，但如果在程序中打印出System.getproperty(“java.classpath”)，可以得到程序实际运行的classpath运行时明确指定你认为程序能正常运行的 -classpath 参数，如果增加之后程序能正常运行，说明原来程序的classpath被其他人覆盖了。

- NoClassDefFoundError也可能由于类的静态初始化模块错误导致，当你的类执行一些静态初始化模块操作，如果初始化模块抛出异常，哪些依赖这个类的其他类会抛出NoClassDefFoundError的错误。如果你查看程序日志，会发现一些java.lang.ExceptionInInitializerError的错误日志，ExceptionInInitializerError的错误会导致java.lang.NoClassDefFoundError: Could not initialize class

- 权限问题、xml配置、类加载等等，具体请参看[详解NoClassDefError](https://blog.csdn.net/jamesjxin/article/details/46606307)

## 2 Java异常处理机制

### 2.1 Java异常关键字

- try：包含可能触发异常的代码块

- catch：捕捉异常关键字，对应一种异常类型和异常处理代理

- throws：方法声明关键字，声明方法可能抛出的异常。在继承关系中，重写父类的带有抛出异常的方法，子类方法抛出的异常必须和父类方法一致或者是父类方法异常的子类，否则无法通过编译。

- throw：抛出具体的异常

- finally：主要用于回收在try块里打开的物理资源（如数据库连接、网络连接和磁盘文件），异常机制总是保证finally块总是被执行。只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，如果finally中使用了return或者throw等终止方法的语句，则就不会跳回执行，直接停止。

- 最为极端的情况是，在try或者catch块中执行退出虚拟机方法System.exit(1),finally代码块将不会被执行。

### 2.2 Java异常处理流程

1. 不管是在try、catch、finally代码块中，出现异常，JVM会生成一个异常对象，并提交给JVM处理，这个过程称为异常的抛出。

2. JVM收到异常对象后，会寻找能够处理该异常对象的catch代码块，如果找到合适的catch块，将交由其处理；否则程序将会推出，终止运行。

3. 对于checked异常需要显式处理：对于知道如何处理的异常，需要使用try-catch代码块捕捉处理；对于不知道如何处理的异常，向外抛出。

### 2.3 异常对象的常用方法

- getMessage():返回该异常的详细描述字符

- printStackTrace():将该异常的跟踪栈信息输出到标准错误输出。

- printStackTrace(PrintStream s):将该异常的跟踪栈信息输出到指定的输出流

- getStackTrace():返回该异常的跟踪栈信息。

### 2.4 自定义异常类

自定义异常类可以继承Exception或RuntimeExceprion，继承Exceprtion的自定义异常为Checked异常（必须抛出或者try cacth），继承自RuntimeExceprion的自定义异常类为运行时异常。另外，还需要提供无参构造器和代参构造器。

```java
public class TestException4 extends Exception{
      public TestException4()
      {

      }
  
      public TestException4(String msg)
     {
         super(msg);
     }
  }
```

## 3 Java注意点

- 尽量不要捕捉通用异常，而是捕捉特定异常，有利于获取更多的调试信息或者向外抛出

- 不要生吞异常，即捕捉而不做处理，这样导致程序意外结束，调试时很难发现

- 尽量不要使用e.printStackTrace(),因为这个方法是输出到标准输出中，在分布式系统中，不知道会输出到那里，因此最好输出到特定的日志中，方便查看

- throw early，catch late

- try-catch代码段会产生额外的性能开销，或者换个角度说，它往往会影响JVM对代码进行优化，所以建议仅捕获有必要的代码段，尽量不要一个大的try包住整段的代码；与此同时，利用异常控制代码流程，也不是一个好主意，远比我们通常意义上的条件语句（if/else、switch）要低效。

- Java实例化一个异常会将当前栈进行快照，耗费性能，必要时才用异常
