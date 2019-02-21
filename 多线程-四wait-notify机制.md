# wait/notify 机制
## 使用注意
* 必须用在线程的同步代码块中，即synchronized（lock）关键字包裹的临界区中，并且只能使用lock.wait()进行锁定，不能使用其他对象wait()。
* A线程调用lock.wait()进行锁定之后，线程阻塞，将CPU时间片段交出并且**释放锁**。此时B线程可以拿到lock锁，然后调用lock.notify()方法唤醒A线程，但是此时A线程并不是立即执行，它需要重新拿到lock锁。等到B线程执行完成，释放了lock锁之后，A线程拿到lock锁就可以从wait()处继续执行了。
```java
		Object lock = new Object();
		new Thread(){
			@Override
			public void run(){
				try {
					synchronized (lock){
					System.out.println("thread1 start");
						for(int i=0;i<50;i++){
							System.out.println("A"+i);
						}
					System.out.println("thread1 wait");
					
					lock.wait();
					System.out.println("thread1 continue");
					}
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		}.start();
		new Thread(){
			@Override
			public void run(){
				synchronized (lock){
					System.out.println("thread2 start");
					for(int i=0;i<50;i++){
						System.out.println(i);
					}
					System.out.println("thread2 notify");
					lock.notify();
					for(int i=0;i<50;i++){
						System.out.println(i<<1);
					}
				System.out.println("thread2 continue");
			}
			}
		}.start();
```