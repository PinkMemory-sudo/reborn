- 说说二分搜索的过程
- 说一下快排的过程，写一下伪代码
- 了解哪设计模式，举例说说在jdk源码哪些用到了你说的设计模式
- 每天有那么多人使用支付宝，这些数据如果给你存储，你会怎么设计呢



```java
/*
* 题目多线程间顺序调用   实现A->B->C
* AA打印5次，BB打印10次，CC打印15次
* 接着
* AA打印5次，BB打印10次，CC打印15次
* .........
* 来十轮
* 
* */


class ShareResource{
	
	private int number =1;
	
	private Lock lock =new ReentrantLock();
	private Condition c1 = lock.newCondition();
	private Condition c2 = lock.newCondition();
	private Condition c3 = lock.newCondition();
	
	public void print5() {
		
		 lock.lock();
	        try {
	            //判断
	            while (number != 1){

	                c1.await();
	            }
	            //2 干活
	            for (int i = 1; i < 5; i++) {
					
	            	System.out.println(Thread.currentThread().getName()+"\t"+i);
				}

	            //3 通知
	            number = 2;
	            c2.signal();
	        }catch (Exception e){
	            e.printStackTrace();
	        }finally {
	        	lock.unlock();
	        }
	}
	
	public void print10() {
		
		 lock.lock();
	        try {
	            //1 判断
	            while (number != 2){

	                c2.await();
	            }
	            //2 干活
	            for (int i = 1; i < 10; i++) {
					
	            	System.out.println(Thread.currentThread().getName()+"\t"+i);
				}

	            //3 通知
	            number = 3;
	            c3.signal();
	        }catch (Exception e){
	            e.printStackTrace();
	        }finally {
	        	lock.unlock();
	        }
	}
	
	
	
	public void print15() {
		
		 lock.lock();
	        try {
	            //判断
	            while (number != 3){

	                c3.await();
	            }
	            //2 干活
	            for (int i = 1; i < 15; i++) {
					
	            	System.out.println(Thread.currentThread().getName()+"\t"+i);
				}

	            //3通知
	            number = 1;
	            c1.signal();
	        }catch (Exception e){
	            e.printStackTrace();
	        }finally {
	        	lock.unlock();
	        }
	}
	
}


public class SyncAndReentrantLockDemo {
	
	public static void main(String[] args) {
		
	ShareResource shareResource = new ShareResource();
	
	new Thread( ()->  {
		for (int i = 1; i <= 10; i++) {
			
			try {
				shareResource.print5();
				
			} catch (Exception e) {
				// TODO: handle exception
				e.printStackTrace();
			}
		}
	},"AA").start();

	
	new Thread( ()->  {
		for (int i = 1; i <= 10; i++) {
			
			try {
				shareResource.print10();
				
			} catch (Exception e) {
				// TODO: handle exception
				e.printStackTrace();
			}
		}
	},"BB").start();

	
	new Thread( ()->  {
	
		for (int i = 1; i <= 10; i++) {
			
			try {
				shareResource.print15();
				
			} catch (Exception e) {
				// TODO: handle exception
				e.printStackTrace();
			}
		}
	},"CC").start();

	}
}

```

