#  Java反射--->动态代理--->aop--->数据库事务

## 1  反射API

反射API可以查看例子，[Java反射API](http://www.cnblogs.com/rollenholt/archive/2011/09/02/2163758.html) 

- setAccesible（true）方法可以绕过修饰符的限制，访问类的私有变量

- 类的newInstance（）方法需要调用默认的无参数构造器创建实例，因此重写类时，需要提供无参构造器，否则通过类构造器实例化对象，会抛出异常

- 
