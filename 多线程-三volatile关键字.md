## 多线程-三、volatile关键字
## Volatile关键字主要是为了解决Java内存模型中带来的可见性问题
* 在java内存模型中，所有的变量都存放在主存中，而每个线程都有自己的工作内容，线程对变量的操作都是在本地内存中进行，不能直接操作主存。
* 线程改变了某个变量的值，先改变本地内存该变量的值，然后去改变主存中该变量的值，但是改变主存中该变量值的时间是不确定的。
* 因此在多线程的环境中，线程1改变了变量a的值写入本地内存，但还没来得及写入主存。此时线程2读取的变量a的值还是没改变之前的值，该值就不是最新的值。
* volatile关键字就是为了解决多线程下可见性问题的（可见性是指多个线程访问同一个变量，当一个线程改变了该值时，其它线程能够立即看到最新的值。）
* 使用了volatile关键字的变量，当线程a修改变量的值，会立即更新主存中该变量的值，并且通知其它访问该值的线程本地内存缓存失效，需要去主存中读取该值。这样其它线程读取的就是最新的值了。
## 注意
* java只保证了赋值操作和读取操作的原子性（类似 i++，y=x都是有多步操作，不是原子性），volatile关键字只保证可见性，并不保证原子性，也就是说多线程操作volatile变量还是会有同步问题。
```java
public class Test {
    public volatile int inc = 0;
     
    public void increase() {
        inc++;
    }
     
    public static void main(String[] args) {
        final Test test = new Test();
        for(int i=0;i<10;i++){
            new Thread(){
                public void run() {
                    for(int j=0;j<1000;j++)
                        test.increase();
                };
            }.start();
        }
         
        while(Thread.activeCount()>1)  //保证前面的线程都执行完
            Thread.yield();
        System.out.println(test.inc);
    }
}
```
* volatile关键字除了保证可见性之外，还会保证一定的有序性。
## volatile关键字应用
* 由于在非原子性的操作下volatile还是会员线程安全问题，所以volatile关键字只适合原子性操作（赋值，读取）等

```java
//*状态量标记：
volatile boolean flag = false;
 
while(!flag){
    doSomething();
}
 
public void setFlag() {
    flag = true;
}
```
## synchronized关键字和volatile关键字比较
* volatile修饰成员变量，synchronized用于方法和代码块。
* volatile保证多线程访问数据的可见性，不保证操作的原子性（轻量级锁）。synchronized保证多线程访问资源的同步性，既保证可见性又保证原子性。
* volatile时线程不会阻塞，但是synchronized有可能阻塞。