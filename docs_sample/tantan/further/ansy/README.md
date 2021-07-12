#  异步  @Async

## 1. @Async 的使用

Spring 提供了 异步执行的代码的功能,能让我们以多线程的方式执行,但是spring 中自带的@Async 注解执行异步时并没有使用线程池的概念, 导致如果同时执行多个任务可能出现把系统资源耗尽的情况.

对此,spingBoot 一做好了优化,默认使用线程池执行任务



在springboot 项目中需要异步执行的代码上加上注解 @Async,同时在启动类上加上@EnableAsync即可如下

```java
@SpringBootApplication
@EnableAsync
public class AnsyApplication {
    public static void main(String[] args) {
        SpringApplication.run(AnsyApplication.class, args);
    }
}
```



```java
 @Async
 public void test() throws InterruptedException {
     Thread.sleep(1000);
     System.out.println("子线程执行了");
 }
```

测试代码

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = AnsyApplication.class)
public class TestDemo {
    @Autowired
    AnsyService service;
    @Test
    public void testDemo() throws Exception{
        service.test();
        System.out.println("主线程执行了");
         //阻塞当前的主线程，等待异步执行的结束
       Thread.sleep(2000);
    }
```

## 2. CompletableFuture

 此时主线程是不知道子线程什么时候执行结束的, 也不知道执行的结果是什么

如果想要更方便的获取结果 jdk 1.8 提供了 CompletableFuture

### Service 代码

```java
@Service
public class AnsyService {
    @Async
    public CompletableFuture<String> test2() throws InterruptedException {
        Thread.sleep(1000);
        System.out.println("子线程执行了2"+new Date()+Thread.currentThread().getName());
        return  CompletableFuture.completedFuture("ok");
    }
}
```

### 测试代码

```java
 @Test
    public void testDemo2() throws InterruptedException {

            CompletableFuture<String> future = service.test2();
            future.whenComplete((s, throwable) -> {
                System.out.println("执行完成：" + s);
            });
            System.out.println("主线程执行了");



            try {
                future.get(); //阻塞当前的主线程，等待异步执行的结束
                // 测试时使用, tomcat 中运行的diamante无需加这个,因为 tomcat一直在运行
            } catch (Exception e) {
                e.printStackTrace();
            }
    }
```

## 3. 线程池数量

Sprigboot 默认线程池数量是8个,   我们可以修改默认的配置将如下代码放入 启动类中即可

```java
@Bean
    public AsyncTaskExecutor asyncTaskExecutor() {
        ThreadPoolTaskExecutor asyncTaskExecutor = new ThreadPoolTaskExecutor();
        asyncTaskExecutor.setCorePoolSize(15);// 设置线程池初始大小
        asyncTaskExecutor.setMaxPoolSize(50);// 设置线程池最大大小
        asyncTaskExecutor.initialize();// 一定不要忘了这句代码,进行初始化
        return asyncTaskExecutor;
    }
```







