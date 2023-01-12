
# 单元测试

- [单元测试](#单元测试)
  - [准备被测试类](#准备被测试类)
  - [测试类](#测试类)
- [待整理](#待整理)

---

## 准备被测试类

UnitTestController.java: 准备了add 和minus接口

```java
package springpractice.unittest.api;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import springpractice.unittest.service.UTService;

/**
 * @date 4/23/2022 3:42 PM
 */
@Api(tags = "unit test controller")
@Controller
public class UnitTestController {

    @Autowired
    UTService utService;

    @ResponseBody
    @ApiOperation("add 2 int")
    @RequestMapping(value = "/add", method = RequestMethod.GET)
    public int add(@RequestParam(value = "param1") Integer a,
                   @RequestParam(value = "param2") Integer b) {
        return utService.add(a, b);
    }

    @ResponseBody
    @ApiOperation("minus 2 int")
    @RequestMapping(value = "/minus", method = RequestMethod.GET)
    public int minus(@RequestParam(value = "param1") Integer a,
                     @RequestParam(value = "param2") Integer b) {
        return utService.minus(a, b);
    }

}

```

UTService.java 承接controller方法 调用 UTAutowired.java 和 UTNonAutoWired.java，并且在UTService中使用到了private方法

```java
package springpractice.unittest.service;

import org.springframework.stereotype.Service;

/**
 * @date 4/23/2022 3:58 PM
 */
@Service
public class UTService {


    private UTAutowired utAutowired;

    public UTService(UTAutowired utAutowired) {
        this.utAutowired = utAutowired;
    }

    public int add(int a, int b) {
        return utAutowired.add(a, b);
    }

    public int minus(int a, int b) {


        boolean compare = compare(a, b);
        if (compare) {
            UTNonAutoWired utNonAutoWired = new UTNonAutoWired(a, b);
            return utNonAutoWired.minus();
        } else {
            UTNonAutoWired utNonAutoWired = new UTNonAutoWired(b, a);
            return utNonAutoWired.minus();
        }

    }

    private boolean compare(int a, int b) {
        boolean result;
        if (a >= b) {
            result = true;
        } else {
            result = false;
        }
        return result;
    }

}

```

处理加法和减法的方法放在UtAutoWired 和 UTNonAutoWired ，区别是一个可以注入，一个需要new

```java
package springpractice.unittest.service;

/**
 * @date 4/23/2022 4:00 PM
 */
public class UTNonAutoWired {

    private Integer a;

    private Integer b;

    public UTNonAutoWired(Integer a, Integer b) {
        this.a = a;
        this.b = b;
    }

    public int minus() {
        return a - b;
    }
}

```

```java
package springpractice.unittest.service;

import org.springframework.stereotype.Component;

/**
 * @date 4/23/2022 3:59 PM
 */
@Component
public class UTAutowired {

    public int add(int a, int b) {
        return a + b;
    }

}
```

## 测试类

```java


import org.junit.Test;
import org.junit.jupiter.api.Assertions;
import org.mockito.ArgumentMatchers;
import org.mockito.Mockito;

/**
 * @date 4/23/2022 4:54 PM
 */
public class UTServiceTest {

    @Test
    public void addTest() {
        UTAutowired utAutowired = Mockito.mock(UTAutowired.class);

        UTService utService1 = new UTService(utAutowired);

        int result = 100;
        Mockito.when(utAutowired.add(ArgumentMatchers.anyInt(), ArgumentMatchers.anyInt())).thenReturn(result);

        int actual = utService1.add(1, 2);

        Assertions.assertEquals(100, actual);
    }

}
```

第一种写法的测试，并没有使用 `@SpringBootTest` 注解，而是直接mock 构造函数中需要的UTAutowired, new  UTService对象。
Mock对象的返回值设为100。

```java
package springpractice.unittest.service;

import org.junit.Test;
import org.junit.jupiter.api.Assertions;
import org.junit.runner.RunWith;
import org.mockito.Mockito;
import org.powermock.api.mockito.PowerMockito;
import org.powermock.core.classloader.annotations.PrepareForTest;
import org.powermock.modules.junit4.PowerMockRunner;

/**
 * @date 4/25/2022 11:43 PM
 */
@RunWith(PowerMockRunner.class)
@PrepareForTest(UTService.class)
public class UTServiceTestForNew {

    @Test
    public void minusTest() throws Exception {
        UTNonAutoWired utNonAutoWired = Mockito.mock(UTNonAutoWired.class);
        UTAutowired utAutowired = Mockito.mock(UTAutowired.class);
        UTService utService1 = new UTService(utAutowired);

        PowerMockito.whenNew(UTNonAutoWired.class).withAnyArguments().thenReturn(utNonAutoWired);

        int result = 100;
        Mockito.when(utNonAutoWired.minus()).thenReturn(result);

        int actual = utService1.minus(1,2);
        Assertions.assertEquals(100,actual);


    }
}
```

如果被测试类中的对象是在方法中new的，则需要使用Powermock。new的对象所在的被测类需要用`@PrepareForTest`注解注明


# 待整理



如果需要使用到某个类中的static 或者final方法时，需要使用到powermock，并且需要在测试类中加上这个注解

**例如**B的测试类BTest中需要使用到A的静态方法，则

```java
@SpringBootTest(classes = {RestFulWrapper.class})
@RunWith(PowerMockRunner.class)
@PrepareForTest({A.class})
@PowerMockRunnerDelegate(SpringRunner.class)
public class Btest{
}
```

被测试类的构造函数，Autowired成员变量等都需要在测试类中用@MockBean注入

使用import org.junit.jupiter.api.Test; 不需要测试方法和类是public的
而使用import org.junit.Test;需要测试方法和类是public的

case：
当被测试类中有：

```java
    protected static final ObjectMapper OBJECT_MAPPER = new ObjectMapper().configure(
        DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
```

这样的成员变量需要，需要在测试中mock时：

```java
       ObjectMapper objectMapper = mock(ObjectMapper.class);
        Field field =  PowerMockito.field(RestfulLoader.class,"OBJECT_MAPPER");
        field.set(RestfulLoader.class,objectMapper);
```

case：
当要使用被测试类中同类的方法时：
例如被测试类是 WebServiceWrapper：

``` java
        WebserviceWrapper webserviceWrapper = new WebserviceWrapper(wsClientCache,wsdlParser,webServiceTransformer,connectionContextCache);
        WebserviceWrapper webserviceWrapperSpy = Mockito.spy(webserviceWrapper);

        Mockito.doReturn(response).when(webserviceWrapperSpy).doSoapRequest(ArgumentMatchers.any(SoapRequest.class),
            ArgumentMatchers.any(HttpProxyConfig.class),ArgumentMatchers.any(Interceptor.class));
        webserviceWrapper.getRawTableData(dbName,rawTableName,authenticator,httpAdapterConfig,authInfo);
```
