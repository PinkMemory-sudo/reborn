

Java中**每个对象都有**一个与之关联的内部锁(也称作监视器)，在有线程获取该内部锁后，其它线程无法访问该内存，从而实现原子性，可见性和有序性。内部锁通过synchronized实现。synchronized关键字可以修饰方法和代码块。synchronized线程**出现异常时会释放锁.**



# Synchronized的使用



## ***修饰代码块***

每个对象都可以作为锁对象，经常使用当前对象this作为锁对象，

```java
synchronized(对象锁){
    // 同步代码块中访问共享变量
}
```

实例：

```java
public class Test {
    public static void main(String[] args) {
        Person person = new Person();
        Runnable task = person::test;
        new Thread(task, "t1").start();
        new Thread(task, "t2").start();
    }

     static class Person {
        public void test() {
            for (int i = 0; i < 100; i++) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}
```

执行代码发现t1和t2交替执行，加入现在需要将for循环作为一个原子性操作，就可以将for循环套在synchronized代码块中

```java
static class Person {
    public void test() {
        synchronized (this) {
            for (int i = 0; i < 100; i++) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}
```

现在t1,t2不再交替执行了，for循环变成了一个原子性操作



## ***修饰实例方法***

同步实例方法的默认锁对象是this(只能是)

想要for循环作为一个原子操作，同样可以将test方法变成同步实例方法

```java
public synchronized static void test() {
    for (int i = 0; i < 100; i++) {
        System.out.println(Thread.currentThread().getName() + ":" + i);
    }
}
```



## ***修饰静态方法***

同步静态方法使用的锁对象默认是当前类的class对象

```java
public synchronized static void test() {
    for (int i = 0; i < 100; i++) {
        System.out.println(Thread.currentThread().getName() + ":" + i);
    }
}
```



**一个类中有多个同步方法怎么办**

相当于多个方法使用了同一把锁,这样就变成了每次只能有一个线程执行该对象的某一个方法,应该在类中声明多把锁,使用同步代码块,最方便的是**使用字符串作为锁对象**

```java
public class Market {

    public final String saleLock="saleLock";
    public final String purchaseLock="purchaseLock";
    
    public  void sale() {
        synchronized (saleLock){
        System.out.println("sale");
        }
    }

    public void purchase() {
        synchronized (purchaseLock){
            System.out.println("Purchase");
        }
    }
}
```



**使用同步代码块还是同步方法**?

同步方法锁粒度大,如果要减少粒度,就是用同步代码块





# 注意事项



注意Synchrozied在判断或循环条件中的使用



## **脏读**

修改共享变量的方法进行了同步,表示同一时刻只能有一个线程对共享变量进行修改,但是在修改过程中,可能会有其他线程去读取共享变量,此时读到的就可能包含之前的数据和一些修改后的数据数据。比如一个线程修改用户名和密码，另一个线程读取用户名和密码，当修改玩用户名还未修改密码时，另一个线程用来读取(因为没有使用同一把锁所以可以在修改的同时去读取)，就读到了新的用户名和旧的密码。

应该是对共享变量的修改和读取都加上同一把锁。所以**共享变量的修改和读取都要同步并使用同一把锁**。



## 死锁



[参考](https://www.cnblogs.com/myworld7/p/12230010.html)

死锁就是鹬蚌相争，彼此依赖对方收里的锁，彼此有都不释放，彼此还不能去抢对方的，只能傻傻的一直等待。



**产生死锁的四个条件**

互斥条件：改资源任一时刻只能有一个线程占用

请求与保持条件：一个进程因请求资源而阻塞时，不释放自己一获得的资源

不剥夺条件：线程已经获得的资源不可剥夺，只有自己使用完毕后才释放资源

循环等待条件：若干进程间形成一种头围相连的循环等待条件



**死锁的代码演示**

```java
```





**如何避免死锁**

破坏行程死锁的四个条件中的任意一条就可以：请求资源时一次请求全部的资源，没有得到时把自己已有的资源释放掉。

互斥条件：无法破坏，因为我们使用锁的目的就是让他互斥

请求与保持条件：一次申请全部资源，这样就不会因某个资源被其他线程占用而阻塞

不可剥夺条件：如果没有申请到其他线程占用的资源时，把自己占用的那部分释放掉

循环等待条件：

