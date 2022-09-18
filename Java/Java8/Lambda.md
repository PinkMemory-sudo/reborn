Lambda是什么，



这个表达式代表什么？一个方法？实现类？对象？



第一种看法：实现了接口中的一个方法，当成是一个对象，可以把这个对象当成变量来给其他变量获参数赋值

java.util.function

|           |                                    |
| --------- | ---------------------------------- |
| consumer  | 指输入，不输出                     |
| Supplier  | 只输出，不输入                     |
| Predicate | 判断，输入参数，返回布尔值         |
| function  | 映射，输入参数，经过函数运算返回值 |



ToIntFunction传入一个参数，返回值是int的function

ToIntBiFunction传入两个参数，返回值是int

还有ToLong，ToDouble



































