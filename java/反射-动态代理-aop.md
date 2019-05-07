#  Java反射--->动态代理--->aop--->数据库事务

## 1  反射API

反射API可以查看例子，[Java反射API](http://www.cnblogs.com/rollenholt/archive/2011/09/02/2163758.html) 

- setAccesible（true）方法可以绕过修饰符的限制，访问类的私有变量

- 类的newInstance（）方法需要调用默认的无参数构造器创建实例，因此重写类时，需要提供无参构造器，否则通过类构造器实例化对象，会抛出异常

## 2 动态代理

### 2.1 代理设计模式

代理设计模式的类图如下：  

![代理设计模式](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1965692009,2527935801&fm=26&gp=0.jpg)

#### 代理模式的应用场景

- 权限控制

- AOP，在代码执行前后做一些额外的操作，实现代码的增强

- 日志输出，输出日志

#### 静态代理的局限

[Java动态代理和静态代理剖析](https://blog.csdn.net/qq_43171869/article/details/85268950)
- 静态代理的代理类需要编写代码实现代理方法，如果代理方法增加等情况，需要手动扩展重写代理方法，工作繁琐、重复
- 静态代理的代理类和被代理类深度耦合
- 动态代理，在编码时，代理逻辑与业务逻辑互相独立，各不影响，没有侵入，没有耦合。
#### 静态代理和动态代理的思路
![静态代理](https://img-blog.csdnimg.cn/2018122621173213)  
![动态代理](https://img-blog.csdnimg.cn/2018122621173238)  
动态代理在运行时生成字节码，并通过自定义classloader加载到JVM，实现动态代理，动态生成字节码的工具有ASM和Javassist等。

### 2.2 JDK动态代理

https://www.cnblogs.com/zuidongfeng/p/8735241.html  jdk动态代理实现原理
