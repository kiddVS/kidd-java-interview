# Executor线程池
## 1、使用线程池的好处
* 降低资源消耗，重复使用线程来降低线程创建和销毁造成的消耗。
* 提高响应速度，任务到达时不需要创建线程直接使用线程池里的线程就能立即执行。
* 提高线程的可管理性，使用线程池可以进行线程调度和管理。
## 2、Executor框架是JDK1.5引进的线程管理框架，由三大部分组成
* （1）任务，执行的任务需要实现Runnable或者Callable接口。Runnable执行任务没有返回值，Callable执行的任务有返回值。
* （2）任务的执行，任务执行器（自己理解的）的顶级接口是Executor,只有方法excute（）。核心接口是ExecuteService，主要实现类时ThreadPoolExecutor和ScheduledThreadPoolExecutor，执行器可以看做是持有一个线程池的对象。将任务交给执行器执行就是从执行器中选择线程来执行任务中的核心方法。
* （3）异步计算完成之后的结果。返回的主要是Future<T> 接口的实现类 FutureTask<T>类。
* （4）调用过程
    * ![执行过程](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-5-30/84823330.jpg)
## 3、ThreadPoolExecutor的主要属性
* corePoolSize,核心线程数 线程池中常驻的线程数量
* maximumPoolSize，最大线程数 当核心线程数不够时会额外启动线程执行任务（额外启动的线程只能达到最大线程）。而空闲时最大线程会被慢慢的降到核心线程数。
* keepAliveTime 额外线程当经过此事件还没有执行任务就会被销毁。
* workQueue等待队列，当线程池中的线程都忙碌，没有额外线程来执行任务时，将该任务放入等待队列。
## 4、 利用线程池执行任务实例
* (1)使用ThreadPoolExecutor自己的构造函数生成线程池：
```java
public class Task1 implements Runnable {
    private String name;
    public Task1(String name){
        this.name = name;
    }
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+":执行start"+name);
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+":执行end"+name);
    }
}

		BlockingQueue<Runnable> queue = new ArrayBlockingQueue<Runnable>(10);
		ExecutorService executorService  = new ThreadPoolExecutor(3, 3, 0, TimeUnit.MILLISECONDS,queue);
		for (int i=0;i<10;i++){
			Runnable task = new Task1(String.valueOf(i));
			executorService.execute(task);
		}
		executorService.shutdown();
		while (!executorService.isTerminated()){

		}
		System.out.println("Program all stop!!!");
```
* (2)使用Executors的方法来生成线程池
* Excutors.中有四种生成线程池的策略：

    * FixedThreadPool 可重用固定数量线程的线程池
    * SingleThreadExecutor 用单个worker线程的Executor
    * CachedThreadPool 根据需要创建新线程的线程池
```java
		ExecutorService executorService = Executors.newFixedThreadPool(10);
		for(int i=0;i < 10;i++){
			Runnable task = new Task1(String.valueOf(i));
			executorService.execute(task);
		}
		executorService.shutdown();
		while(!executorService.isTerminated()){

		}
		System.out.println("Porgram Stop");
```
# 5、 ScheduledThreadPoolExecutor详解
## 支持延期、定时的执行任务。Timer也是任务调度的类，但是ScheduledThreadPoolExecutor在各方面都比它强大，JDK1.5之后就没有必要再使用Timer类了。
## ScheduledThreadPoolExecutor常用的调度策略：
*   scheduledThreadPool.schedule(worker, 10, TimeUnit.SECONDS);**此任务只会单次执行**启动后（该方法调用）延迟10s执行任务；
```java
		ScheduledExecutorService executorService = Executors.newScheduledThreadPool(20);		
		Runnable scheduleTask = new ScheduleTaskTest();
		executorService.schedule(scheduleTask,5000,TimeUnit.MILLISECONDS);
```
* scheduledThreadPool.scheduleAtFixedRate(worker, 0, 10,
	TimeUnit.SECONDS); **此任务会循环执行**， 启动后延迟0s，第一个线程启动后10s，第二个线程启动执行任务，再过10s第三个线程执行任务。

* scheduledThreadPool.scheduleWithFixedDelay(worker, 0, 1,
	TimeUnit.SECONDS);**此任务会循环执行**， 启动后延迟0s，第一个线结束后1s，第二个线程启动执行任务，第二个线程结束后1s第三个线程执行任务。
