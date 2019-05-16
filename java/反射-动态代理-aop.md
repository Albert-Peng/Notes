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

- 对调用对象的包装，类似装饰器模式，RPC、序列化和反序列化等等

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
JDK的动态代理实现的思路和[Java动态代理和静态代理剖析](https://blog.csdn.net/qq_43171869/article/details/85268950)阐述的思路是一样的，在运行时生成代理类的字节码，并通过被代理类实例的classloader加载进虚拟机。在代理中，通过反射调用被代理类的方法，在InvocationHandler中实现调用方法前后的逻辑。下面编写一个使用JDK动态代理API的实例。  

```java
//代理接口
public interface Subject {
    void doSomething();
}

//需要被代理类
public class RealSubject implements Subject {
    @Override
    public void doSomething() {
        System.out.println("I am real subject and I am doing something!");
    }
}

//实现InvocationHandler，定义代理方法的逻辑
public class MyInvocationHandler implements InvocationHandler {

    private Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("before do something!");
        Object object = method.invoke(target,args);
        System.out.println("after do something!");
        return object;
    }
}

//调用Proxy类的静态方法生成代理对象，创建代理类时需要传入实现InvocationHandler接口的实例
public class Main {
    public static void main(String[] args) {
        Subject realObject = new RealSubject();
        MyInvocationHandler myInvocationHandler = new MyInvocationHandler(realObject);
        //classloader、interfaces应该传入被代理类的
        Subject proxyObject = (Subject) Proxy.newProxyInstance(realObject.getClass().getClassLoader(),realObject.getClass().getInterfaces(),myInvocationHandler);
        proxyObject.doSomething();
    }
}

```
### 2.3 CGLIB动态代理

#### CGLIB简介
CGLIB是一个代码生成库，它为没有实现接口的类提供代理，为JDK的动态代理提供了很好的补充。通常可以使用Java的动态代理创建代理，但当要代理的类没有实现接口或者为了更好的性能，CGLIB是一个好的选择。目前，CGLIB实现动态代理广泛应用在AOP框架之中，比如Spring AOP就是采用JDK动态代理和CGLIB实现；另外，还有Hibernate也是采用CGLIB生成代理。

#### CGLIB原理
- CGLIB原理：动态生成一个要代理类的子类，子类重写要代理的类的所有不是final的方法。在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑。它比使用java反射的JDK动态代理要快。

- CGLIB底层：使用字节码处理框架ASM，来转换字节码并生成新的类。不鼓励直接使用ASM，因为它要求你必须对JVM内部结构包括class文件的格式和指令集都很熟悉。

- CGLIB缺点：对于final方法，无法进行代理。  


https://www.jianshu.com/p/9a61af393e41?from=timeline&isappinstalled=0
