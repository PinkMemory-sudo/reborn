好处：

* 



集合获得Stream直接.stream

数组获得stream：Arrays.stream(数组)



# 获取流



| 操作                | 方法                                                      |
| ------------------- | --------------------------------------------------------- |
| 通过数组获得流      | Stream.of(T...values)/Arrays.stream(T...values[起止位置]) |
| 通过集合获得流      | xxx.Stream()                                              |
| 合并两个流          | Stream.concat()                                           |
| 创建整数/浮点数等流 | IntStream/...                                             |

IntStream.range(1, 100).boxed()，创建出来的元素时int类型，可以用boxed进行装箱。range的范围时左闭右开,全闭合是***rangeClosed***





# 操作流



* 中间操作是是lazy操作，不会立马执行，终止时才执行

* 对stream的操作也不会影响到创建它的数组和集合

| 方法                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| filter                    | 过滤，返回值为false时过滤掉                                  |
| map                       | 映射，将元素映射成其他值                                     |
| mapToInt                  | 将Interger转为int在进行运算，减少内存占有，还有Long和Double的 |
| sorted                    | 排序，传入比较器                                             |
| distinct                  | 去重，要想对象去重，要重写对象的hashCode和equals             |
| skip                      | 表示从哪开始                                                 |
| limit                     | 传入int，表示只要前几个                                      |
| reduce(T identity,计算器) | T为初始值，计算器来计算相邻两个，返回结果，例如求流中元素的和 |
| min                       | 传入比较器，得最小值                                         |
| max                       | 传入比较器，得最大值                                         |



# 终止流

| 方法       | 描述                          |
| ---------- | ----------------------------- |
| anyMatch   | 传入一个Predicate             |
| allMatch   | 传入一个Predicate             |
| noneMatch  | 传入一个Predicate             |
| findFirst  | 与findAny相同，返回第一个元素 |
| count()    | 元素个数                      |
| Collectors | 对流中的元素进行总结归纳      |



Collectors的静态方法

* Collection

| 方法                                                         | 描述                       |
| ------------------------------------------------------------ | -------------------------- |
| toCollection                                                 |                            |
| toList                                                       |                            |
| toSet                                                        |                            |
| toMap(Function keyMapper,  Function valueMapper)             |                            |
| joining                                                      |                            |
| joining(CharSequence delimiter)                              |                            |
| joining(CharSequence delimiter, CharSequence prefix, CharSequence suffix) |                            |
| <T, K>groupingBy(Function)                                   | 运行结果一样的为一组       |
| groupingBy(Function)                                         | 分组后有多个流，又可以聚合 |
| groupingBy(Function,Collector)                               | 分组后再对每一组进行操作   |



groupingBy可用于将List转为Map

groupingBy(Function,Collector)

例如分组后

对每个分组中的元素进行分组，

将每个元素转换成String,List等

可以理解成groupingBy(Function,Collector)，function为key，Collectors为value，没有Collectors时value默认为该组中所有元素组成的value。





TODO：

flapMap

可以对stream流中单个元素再进行拆分，相当于双重for循环

peek

reduce

min

max

count

anyMatch

allMatch

noneMatch

findFirst

findAny

empty

of

builder

generate

concat



并行流的线程安全问题：

在parallelStream里面使用外部变量



groupingBy



Collectors



flatMap

reduce



```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Employee {
    private String name;
    private String position;
    private double salary;
}
```



* Collectors.toCollection(HashSet::new)
* Collectors.toList()
* Collectors.toSet()
* Collectors.toMap(key,value)



* 



匹配

```
groupingBy(Function<? super T, ? extends K> classifier)
groupingBy(Function<? super T, ? extends K> classifier,
                                          Collector<? super T, A, D> downstream)
groupingBy(Function<? super T, ? extends K> classifier,
                                  Supplier<M> mapFactory,
                                  Collector<? super T, A, D> downstream)                                                                                  
```

```
toMap(Function<? super T, ? extends K> keyMapper,
                                Function<? super T, ? extends U> valueMapper,
                                BinaryOperator<U> mergeFunction)
```

```
toMap(Function<? super T, ? extends K> keyMapper,
                            Function<? super T, ? extends U> valueMapper,
                            BinaryOperator<U> mergeFunction,
                            Supplier<M> mapSupplier)
```