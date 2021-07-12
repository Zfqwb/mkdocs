# ThreadLocal

多线程访问同一个共享变量的时候容易出现并发问题,为了保证没有并发问题,我们可以让每个线程使用自己的独有的变量,我们可以使用 一个map 集合,来保证每个线程使用当前线程独有的数据

在tomcat 编程中为了更方便的传递数据,我们希望把数据放到一个工具类全局变量中,让后使用的时候,从工具类的全局变量中获取,这样我们就会少很多参数的传递,进而提升开发效率

但是

多线程访问同一个共享变量的时候容易出现并发问题,为了保证没有并发问题,我们可以让每个线程使用自己的独有的变量, (笨方法)我们可以使用 一个map 集合,来保证每个线程使用当前线程独有的数据:如下

## 1. 自定义Map集合方式

```java

public class ThreadUtil {

    private static final Map map = new HashMap();
    // 获取数据
    public static Object  get(String  name){
        return map.get(name);
    }

    //读取数据
    public static void  set(String key,Object  obj){
         map.put(key,obj);
    }

}sji
```



```java
//数据存放
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello(){
        ThreadUtil.set(Thread.currentThread().getName(),"hahah");
        System.out.println("访问1....");
        return "hello 8080";
    }
}
// 数据获取
@Service
public class HelloService {
    public void test(){
        System.out.println(ThreadUtil.get(Thread.currentThread().getName()));
        System.out.println();
    }

}
```

使用起来相对不是很方便, 其实 jdk 已经自带了一个 比较方便的,和线程绑定的 对象 ThreadLocal

## 2. ThreadLocal

```
public class ThreadUtil {

    private static final ThreadLocal thead = new ThreadLocal();
    // 获取数据
    public static Object  get(){
        return thead.get();
    }

    //读取数据
    public static void  set(Object  obj){
        thead.set(obj);
    }

}
```

```java
//数据存放
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello(){
        ThreadUtil.set("hahah");
        System.out.println("访问1....");
        return "hello 8080";
    }
}

// 数据获取
@Service
public class HelloService {
    public void test(){
        System.out.println(ThreadUtil.get());
        System.out.println();
    }

}
```

