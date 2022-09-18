Lambda表达式

将函数作为参数传给函数

()->{}

如果Lambda表达式中的语句块只有一行，则可以不用使用return语句,返回值的类型由编译器推理得

函数式接口可以转为lambda，

**@FunctionalInterface** Java8添加的一个新注解，显式表示该接口是函数式接口

默认方法和静态方法不会破坏函数式接口的定义



## 方法引用



方法引用使得开发者可以***直接引用现存的方法***，Java类的构造方法或者实例对象。

方法引用和Lambda表达式配合使用，使得java类的构造方法看起来紧凑而简洁，没有很多复杂的模板代码。

| 类型         | 语法               | 对应的Lambda表达式                   |
| ------------ | ------------------ | ------------------------------------ |
| 静态方法引用 | 类名::staticMethod | (args) -> 类名.staticMethod(args)    |
| 实例方法引用 | inst::instMethod   | (args) -> inst.instMethod(args)      |
| 对象方法引用 | 类名::instMethod   | (inst,args) -> 类名.instMethod(args) |
| 构建方法引用 | 类名::new          | (args) -> new 类名(args)             |

