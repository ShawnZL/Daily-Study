# SpringBoot异步编程

## 异步框架解决什么问题

在SpringBoot的日常开发中，一般都是同步调用的。但实际中有很多场景非常适合使用异步来处理，如：注册新用户，送100个积分；或下单成功，发送push消息等等。

就拿注册新用户这个用例来说，为什么要异步处理？

- 第一个原因：容错性、健壮性，如果送积分出现异常，不能因为送积分而导致用户注册失败；因为用户注册是主要功能，送积分是次要功能，即使送积分异常也要提示用户注册成功，然后后面在针对积分异常做补偿处理。
- 第二个原因：提升性能，例如注册用户花了20毫秒，送积分花费50毫秒，如果用同步的话，总耗时70毫秒，用异步的话，无需等待积分，故耗时20毫秒。

故，异步能解决2个问题，性能和容错性。

## SpringBoot如何实现异步调用

对于异步方法调用，从Spring3开始提供了`@Async`注解，我们只需要在方法上标注此注解，此方法即可实现异步调用。

当然，我们还需要一个配置类，通过Enable模块驱动注解`@EnableAsync` 来开启异步功能。

执行的时候，将会在独立的线程中被执行，调用者无需等待它的完成，即可继续其他的操作。

分为不带参数的异步调用；带参数的异步调用；调用返回Future的异步线程

## 实现异步调用

### 第一步：新建配置类，开启@Async功能支持

使用`@EnableAsync`来开启异步任务支持，`@EnableAsync`注解可以直接放在SpringBoot启动类上，也可以单独放在其他配置类上。我们这里选择使用单独的配置类`SyncConfiguration`。

```
@Configuration
@EnableAsync
public class AsyncConfiguration {

}
```

### 第二步：在方法上标记异步调用

增加一个Component类，用来进行业务处理，同时添加`@Async`注解，代表该方法为异步处理。

```
@Component //将普通pojo实例化到spring容器中，相当于配置文件中<bean id="" class=""/>
@Slf4j
public class AsyncTask {

    @SneakyThrows
    @Async
    public void doTask1() {
        long t1 = System.currentTimeMillis();
        Thread.sleep(2000);
        long t2 = System.currentTimeMillis();
        log.info("task1 cost {} ms" , t2-t1);
    }

    @SneakyThrows
    @Async
    public void doTask2() {
        long t1 = System.currentTimeMillis();
        Thread.sleep(3000);
        long t2 = System.currentTimeMillis();
        log.info("task2 cost {} ms" , t2-t1);
    }
}
```

**@Component**泛指各种组件，就是说当我们的类不属于各种归类的时候（不属于@Controller、@Services等的时候），我们就可以使用@Component来标注这个类

如果不想每次都写private final Logger logger = LoggerFactory.getLogger(XXX.class); 可以用注解 **@Slf4j**

#### 第三步：在Controller中进行异步方法调用

```
@RestController
@RequestMapping("/async")
@Slf4j
public class AsyncController {
    @Autowired
    private AsyncTask asyncTask;

    @RequestMapping("/task")
    public void task() throws InterruptedException {
        long t1 = System.currentTimeMillis();
        asyncTask.doTask1();
        asyncTask.doTask2();
        Thread.sleep(1000);
        long t2 = System.currentTimeMillis();
        log.info("main cost {} ms", t2-t1);
    }
}
```

通过访问`http://localhost:8080/async/task`查看控制台日志：

## 异步框架自定义线程池

使用`@Async`注解，在默认情况下用的是**SimpleAsyncTaskExecutor线程池，该线程池不是真正意义上的线程池**。

使用此线程池无法实现线程重用，每次调用都会新建一条线程。若系统中不断的创建线程，最终会导致系统占用内存过高，引发`OutOfMemoryError`错误，正因如此，所以我们在使用Spring中的**@Async**异步框架时一定要自定义线程池，替代默认的`SimpleAsyncTaskExecutor`。

```
pring提供了多种线程池：

SimpleAsyncTaskExecutor：不是真的线程池，这个类不重用线程，每次调用都会创建一个新的线程。

SyncTaskExecutor：这个类没有实现异步调用，只是一个同步操作。只适用于不需要多线程的地

ConcurrentTaskExecutor：Executor的适配类，不推荐使用。如果ThreadPoolTaskExecutor不满足要求时，才用考虑使用这个类

ThreadPoolTaskScheduler：可以使用cron表达式

ThreadPoolTaskExecutor ：最常使用，推荐。其实质是对java.util.concurrent.ThreadPoolExecutor的包装
```

### 为@Async实现一个自定义线程池

```
@Configuration
@EnableAsync
public class SyncConfiguration {
    @Bean(name = "asyncPoolTaskExecutor")
    public ThreadPoolTaskExecutor executor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        //核心线程数
        taskExecutor.setCorePoolSize(10);
        //线程池维护线程的最大数量,只有在缓冲队列满了之后才会申请超过核心线程数的线程
        taskExecutor.setMaxPoolSize(100);
        //缓存队列
        taskExecutor.setQueueCapacity(50);
        //许的空闲时间,当超过了核心线程出之外的线程在空闲时间到达之后会被销毁
        taskExecutor.setKeepAliveSeconds(200);
        //异步方法内部线程名称
        taskExecutor.setThreadNamePrefix("async-");
        /**
         * 当线程池的任务缓存队列已满并且线程池中的线程数目达到maximumPoolSize，如果还有任务到来就会采取任务拒绝策略
         * 通常有以下四种策略：
         * ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
         * ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
         * ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
         * ThreadPoolExecutor.CallerRunsPolicy：重试添加当前的任务，自动重复调用 execute() 方法，直到成功
         */
        taskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        taskExecutor.initialize();
        return taskExecutor;
    }
}
```

### 多个线程池处理

假设现在2个高并发接口：一个是修改用户信息接口，刷新用户redis缓存；一个是下订单接口，发送app push信息。往往会根据接口特征定义两个线程池，这时候我们在使用`@Async`时就需要通过指定线程池名称进行区分。

#### 为@Async指定线程池名字

```
@SneakyThrows
@Async("asyncPoolTaskExecutor")
public void doTask1() {
  long t1 = System.currentTimeMillis();
  Thread.sleep(2000);
  long t2 = System.currentTimeMillis();
  log.info("task1 cost {} ms" , t2-t1);
}
```

当系统存在多个线程池时，我们也可以配置一个默认线程池，对于非默认的异步任务再通过`@Async("otherTaskExecutor")`来指定线程池名称。

#### 配置默认线程池

可以修改配置类让其实现`AsyncConfigurer`，并重写`getAsyncExecutor()`方法，指定默认线程池：

```
@Configuration
@EnableAsync
@Slf4j
public class AsyncConfiguration implements AsyncConfigurer {

    @Bean(name = "asyncPoolTaskExecutor")
    public ThreadPoolTaskExecutor executor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        //核心线程数
        taskExecutor.setCorePoolSize(2);
        //线程池维护线程的最大数量,只有在缓冲队列满了之后才会申请超过核心线程数的线程
        taskExecutor.setMaxPoolSize(10);
        //缓存队列
        taskExecutor.setQueueCapacity(50);
        //许的空闲时间,当超过了核心线程出之外的线程在空闲时间到达之后会被销毁
        taskExecutor.setKeepAliveSeconds(200);
        //异步方法内部线程名称
        taskExecutor.setThreadNamePrefix("async-");
        /**
         * 当线程池的任务缓存队列已满并且线程池中的线程数目达到maximumPoolSize，如果还有任务到来就会采取任务拒绝策略
         * 通常有以下四种策略：
         * ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
         * ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
         * ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
         * ThreadPoolExecutor.CallerRunsPolicy：重试添加当前的任务，自动重复调用 execute() 方法，直到成功
         */
        taskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        taskExecutor.initialize();
        return taskExecutor;
    }

    /**
     * 指定默认线程池
     */
    @Override
    public Executor getAsyncExecutor() {
        return executor();
    }

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return (ex, method, params) ->
            log.error("线程池执行任务发送未知错误,执行方法：{}",method.getName(),ex);
    }
}
```

如下，`doTask1()`方法使用默认使用线程池`asyncPoolTaskExecutor`，`doTask2()`使用线程池`otherTaskExecutor`，非常灵活。**注意只能存在一个AsyncConfiguration，其他线程池需要我们自己编辑！！！见上为@Async实现一个自定义线程池**

```
@Async
public void doTask1() {
  long t1 = System.currentTimeMillis();
  Thread.sleep(2000);
  long t2 = System.currentTimeMillis();
  log.info("task1 cost {} ms" , t2-t1);
}

@SneakyThrows
@Async("otherTaskExecutor")
public void doTask2() {
  long t1 = System.currentTimeMillis();
  Thread.sleep(3000);
  long t2 = System.currentTimeMillis();
  log.info("task2 cost {} ms" , t2-t1);
}
```

