将一个大的任务拆分成多个子任务进行并行处理，最后将子任务结果合并成最后的计算结果。

所以主要包含两个部分：

* 将任务切分成指定大小
* 将每个小任务合并



**优点**

每个线程都有一个线程队列(与ThreadPollExecutor不同)，当自己队列里的任务都完成后会去其他线程的队列中偷任务

为了避免窃取任务的线程和被窃取任务的线程竞争，使用双端队列

充分利用多处理器



**重要类**

有三类：线程池，任务，执行任务的线程



**ForkJoinPoll**

用来运行ForkJoinTask。与其他线程池不同就是它会窃取任务。当在构造方法中设置asyncMode为true的时候这种处理更加高效。



**ForkJoinTask**

用来代表运行在FoekJoinPoll里的任务

主要方法：

| 方法   | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| fork   | 在当前线程运行的线程池中安排一个异步执行。简单的理解就是再创建一个子任务 |
| join   | 当任务完成时返回结果                                         |
| invoke | 开始执行任务，如果必要，等待计算完成                         |

**子类**

RecursiveAction

一个递归无结果的ForkJoinTask，很好理解，就是一个Action，没有结果

RecursiveTask

一个递归有结果的ForkJoinTask



**ForkJoinWirkerThread**

A thread managed by a **ForkJoinPool**, which executes **ForkJoinTasks**.

ForkJoinWorkThread持有ForkJoinPool和ForkJoinPool.WorkQueue的引用，以表明该线程属于哪个线程池，它的工作队列是哪个





同样是提交任务，submit会返回ForkJoinTask，而execute不会

任务提交给线程池以后，会将这个任务加入到当前提交者的任务队列中





**使用**

流程就是创建任务，将任务提交到ForkJoinPoll，然后执行



1. 创建任务

有两种任务：带返回值(RecursiveTask)的和不带返回值的(RecursiveAction)

根据情况定义一个ForkJoinTask子类的实现类，将计算所需要的资源等封装到这个类中，可以在创建任务的时候通过构造函数传进来。

eg:用来求n-m之和的任务

```java
public class ActionTest extends RecursiveTask<Integer> {

    private int start;
    private int end;
    private int taskSize;

    public ActionTest(int start, int end) {
        this.start = start;
        this.end = end;
    }

     /**
     * compute实现两个功能：任务的切分和计算
     */
    @Override
    protected Integer compute() {
        if ((end - start) > 1) {
            int mid = (end + start) / 2;
            System.out.println(start + "-" + end + "," + mid);
            ActionTest left = new ActionTest(start, mid);
            ActionTest right = new ActionTest(mid + 1, end);
            left.fork();
            right.fork();
            return left.join() + right.join();
        } else {
            // 计算时注意start==end的情况
            return getSum();
        }
    }

    private int getSum() {
        if (start == end) {
            return start;
        } else {
            return start + end;
        }
    }
}
```



2. 创建ForkJoinPool

使用默认的并行数量限制(JVM处理器个数)，默认的线程工厂，没有UncaughtExceptionHandler，non-async LIFO processing mode



使用指定的并行数



更加具体的创建











要点：

如何设置任务参数，如何切分成若干小任务，如何每次平均分配任务。











使用

1. 集成抽象类RecursiveAction     Recursive-递归
2. 重写抽象方法compute



实际就是一个帝国算法，将任务分割成指定大小的任务后才执行compute方法，否则接着分隔。





有返回值的

RecursiveTask



fork调起子任务

join等待子任务完成，获得子任务执行结果





ForkJoinTask需要通过ForkJoinPool来执行



**工作窃取算法**





**如何分隔任务**

设置阈值













RecursiveAction：用于没有返回结果的任务

RecursiveTask:用于有返回结果的任务

































切割成子任务放入双端队列，

线程从双端队列中双端队列中获取要执行的任务

任务的执行结果放入另一个队列



子任务会添加到当前工作线程所维护的双端队列中，进入队列的头部，当一个工作线程的队列里暂时没有任务时，它会随机从其他工作线程的队列的尾部获取一个任务(工作窃取算法)。



Join方法的主要作用是阻塞当前线程并等待获取结果



切割成先任务后进行计算，合并任务结果





由线程池调度



**创建ForkJoin任务**



ForkJoin任务有两种：有返回值的和没有返回值的。

```

```



**如何切分任务**

设置阈值，分隔任务就是分隔任务的参数，参数小了任务就小了。常用的做法就是讲参数添加的集合中，切分集合。

由此可见forkJoin的使用场景的执行相同的计算但是参数不同，计算比较耗时时使用ForkJoin



**compute方法**

首先进行阈值判断

达到阈值时执行计算，返回结果

为达到阈值时，分割任务，子任务通过调用fork进入当前任务的线程池中。





























**异常处理**

我们没办法在主线程里直接捕获异常，所以ForkJoinTask提供了isCompletedAbnormally()方法来检查任务是否已经抛出异常或已经被取消了，并且可以通过ForkJoinTask的getException方法获取异常。









任务是如何切割的

设置一个阈值，阈值用来判断是执行据算还是分隔任务

高于阈值的进行切割，低于阈值的进行计算。



结果队列



任务的结果怎么汇总





## 有返回值的



### 创建任务类

```java
public class ForkJoinSumTask extends RecursiveTask<List<String>> {
		// 阈值
    public static final int subTaskScale = 1;
		// 大任务的参数
    private List<Integer> userIdSet;

    public ForkJoinSumTask(List<Integer> userIdSet) {
        this.userIdSet = userIdSet;
    }

    @Override
    protected List<String> compute() {
        List<String> nameSet = new ArrayList<>();
        if (!(userIdSet.size() > subTaskScale)) {
            int id = userIdSet.get(0);
            // 模拟通过id查询名字，耗时100ms
            try {
                Thread.sleep(100);
                nameSet.add("" + System.currentTimeMillis());
                return nameSet;
            } catch (InterruptedException e) {
                e.printStackTrace();
                return new ArrayList<>();
            }
        } else {
            ForkJoinSumTask subTask1 = new ForkJoinSumTask(userIdSet.subList(0, userIdSet.size() / 2));
            ForkJoinSumTask subTask2 = new ForkJoinSumTask(userIdSet.subList(userIdSet.size() / 2, userIdSet.size()));
            subTask1.fork();
            subTask2.fork();

            List<String> sub1 = subTask1.join();
            List<String> sub2 = subTask2.join();
            nameSet.addAll(sub1);
            nameSet.addAll(sub2);
            return nameSet;
        }
    }
}
```

类似递归，达到阈值时执行计算，没有达到时继续分隔。



**fork**

异步执行任务在当前任务所在的线程池中。



**join**

返回任务执行的结果(阻塞等待子任务返回)















### 创建任务和线程池

```java
@Test
public void test() {
  	long begin = System.currentTimeMillis();
  	ForkJoinPool forkJoinPool = new ForkJoinPool();
  	List<Integer> userIdSet = IntStream.rangeClosed(0, 			100).boxed().collect(Collectors.toList());
  	ForkJoinSumTask forkJoinSumTask = new ForkJoinSumTask(userIdSet);
  		List<String> nameList = forkJoinPool.invoke(forkJoinSumTask);
  	nameList.forEach(System.out::println);
  	System.out.println((System.currentTimeMillis() - begin) / 1000);
  	Map<String, Long> map = 	nameList.stream().collect(Collectors.groupingBy(String::toString, Collectors.counting()));
  System.out.println(map);
}
```



**ForkJoinPoll**



`invoke`

执行提交的任务，执行完成后返回计算结果



`execute`

安排异步执行



`submit`

提交任务到execution，返回提交的任务





**ForkJoinTask**



`fork`

安排当前任务运行的线程池去异步执行这个任务



`join`

获得任务的计算结果



`invoke`























