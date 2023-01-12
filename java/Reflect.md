# java 反射

- [java 反射](#java-反射)
  - [例子](#例子)
    - [反射获得内部类构造函数](#反射获得内部类构造函数)

---

## 例子

### 反射获得内部类构造函数

ReflectDemo在 `package springpractice.reflect.service` 路径下，并且希望在其他路径中new这个对象。 由于ReflectDemo是非public class，对于包外是不可见的

```java
package springpractice.reflect.service;

/**
 * @date 5/6/2022 10:46 PM
 */

class ReflectDemo  implements ReflectInterface{

    String name ;

    ReflectDemo(String name){
        this.name = name;
    }

    @Override
    public void printSomthing(){
        System.out.println("success to print!");
    }

}
```

因为ReflectDemo是不可见的，为了证明反射的功能，我们让ReflectDemo继承了一个接口，并且实现了接口中的printSomthing()方法

``` java
package springpractice.reflect.service;

/**
 * @date 5/6/2022 10:58 PM
 */
public interface ReflectInterface {

   void printSomthing();
}

```

在其他路径使用反射new ReflectDemo对象，（实际上市ReflectInterface
）

```java
package springpractice.reflect;

import springpractice.reflect.service.ReflectInterface;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

/**
 * @date 5/6/2022 10:48 PM
 */
public class ReflectTest {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class clazz = Class.forName("springpractice.reflect.service.ReflectDemo");
        Constructor reflectDemoConstructor = clazz.getDeclaredConstructor(String.class);
        reflectDemoConstructor.setAccessible(true);
        String name = "Tom";
        ReflectInterface obj = (ReflectInterface)reflectDemoConstructor.newInstance(name);
        obj.printSomthing();
    }
}
```

输出：

```txt
success to print!
```
