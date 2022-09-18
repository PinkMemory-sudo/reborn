* 解决冗余？







创建Optional

*of()* 和 ofNullable() 方法

并没有完全摆脱 *NullPointerException*

明确对象不为 *null* 的时候使用 *of()*。

如果对象即可能是 *null* 也可能是非 null，你就应该使用 *ofNullable()* 方法



获得Optional中的对象

get通常与isPresent()连用

另外ifPresent(Consumer )表示如果存在，执行消费知方法。

*orElse(Object)* Optional中有对象返回，没对象返回传入的对象

*orElseGet(*Supplier) Optional中没有对象时执行提供者方法，可以让orElseGet在对象不存在时再去创建对象。

*orElseThrow()* 对象不存在时，可以根据语义抛出指定异常



**orElse() 和 orElseGet() 的不同之处**

orElse不管为不为null都会创建默认对象





























Optional.of(T value)，value为空时就报空指针。

get()  获得value

orElse(T other)，value为空时用other

isPresent()  是否含有value，还可以接受一个*Consumer(消费者*) 参数，表示不为空时执行的函数。

orElseGet()

*orElseThrow()* 



明确对象不为null时使用of，不明确时使用*ofNullable()* 











**链式调用**





















