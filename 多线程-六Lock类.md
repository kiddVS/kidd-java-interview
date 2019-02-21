# Lock接口
## Lock接口的基本使用
* Lock lock = new ReentrantLock();lock.lock()获取锁，finally块中lock.unlock();释放锁
* 代码示例
```java
public class SellTickets implements Runnable {

    private Integer ticketCount = 100;
    private Lock lock = new ReentrantLock();

    @Override
    public void run() {
        while (ticketCount > 0){
            try {
                lock.lock();
                if(ticketCount>0) {
                    Integer count = ticketCount;
                    System.out.println(Thread.currentThread().getName() + "正在出售第" + ticketCount + "张票");
                    Thread.sleep(100);
                    ticketCount = --count;
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
            finally {
                lock.unlock();
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

}

main:
		SellTickets sellTickets = new SellTickets();
		Thread th1 = new Thread(sellTickets);
		Thread th2 = new Thread(sellTickets);
		Thread th3 = new Thread(sellTickets);
		th1.start();
		th2.start();
		th3.start();

```
## Lock接口可以看做synchronized关键字的加强，有许多它不具有的特性
* Lock可以try获取锁，无阻塞的尝试获取锁（获取不到可以执行别的逻辑），synchronized必须阻塞
* Lock在wait（）过程中可以响应中断，interrupt。而synchronized在wait（）过程中如果中断interrupt则会抛出异常。
* Lock可以超时获取锁，一定时间获取不到锁可以执行其他逻辑。
* Lock等待唤醒机制加的灵活。
## Lock的wait，notify机制,利用lock对象的newCondition方法产生Condition对象，await()方法等待中断，signal方法唤醒。
```java
		Lock lock = new ReentrantLock();
		Condition condition = lock.newCondition();

		Thread thread1 = new Thread(){
			@Override
			public void run(){
				lock.lock();
				System.out.println("thread1 acquire lock;");
				try {
					System.out.println("thread1 await;");
					condition.await();
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println("thread1 continue;");
				lock.unlock();
			}
		};
		Thread thread2 = new Thread(){
			@Override
			public void run(){
				lock.lock();
				System.out.println("thread2 acquire lock;");
					System.out.println("thread2 notify thread1;");
					condition.signal();
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println("thread2 continue;");
				lock.unlock();
			}
		};
		thread1.start();
		thread2.start();
```

## 公平锁和非公平锁
* 公平锁是先等待获取锁的线程先获取，非公平锁不一定按照尝试获取锁的顺序分配锁。