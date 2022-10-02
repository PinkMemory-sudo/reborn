**File**

用来描述文件或者文件夹



实现了Serializable, Comparable接口



静态成员

静态方法

构造方法

常用方法



文件或文件夹的抽象表述



创建路径

创建一层mkdir

创建多层mkdirs



重新创建目录，目录里的文件会怎么样？

不会被覆盖





File.createTempFile

```
Creates an empty file in the default temporary-file directory, using the given prefix and suffix to generate its name.
```





获得File对象的几种方式：

1. 指定一个pathname
2. 两个参数构成一个pathname

即使指定的路径不存在也不会报错，因为File并不能直接用来进行读写操作

| 构造函数                          | 描述 |
| --------------------------------- | ---- |
| File(String pathname)             |      |
| File(String parent, String child) |      |
| File(File parent, String child)   |      |
| File(URI uri)                     |      |



常用方法

| 方法                | 描述                                                   |
| ------------------- | ------------------------------------------------------ |
| getParent()         | 获得File所在的文件夹(左后一个/前的部分)                |
| getParentFile()     | 获得所在文件夹File对象                                 |
| getPath()           | 就是创建File文件时指定的路径                           |
| getAbsolutePath()   | 绝对路径                                               |
| getCanonicalPath()  | 规范化的绝对路径                                       |
| getXxxFile          | 没有卵用？就是获得对应形式的File                       |
| createNewFile()     | 文件不存在是创建一个空文件                             |
| mkdir()             | 创建一个文件夹                                         |
| mkdirs()            | 创建一个文件夹，包括父目录                             |
| delete()            | 删除文件或者空文件夹                                   |
| deleteOnExit()      | 执行后并不会删除，而是VM退出后删除，与创建临时文件连用 |
| list()              | 返回一个File下的所有文件名，不是文件夹时返回null       |
| ListFiles()         | 返回一个文件夹下的所有File对象，不是文件夹时返回null   |
| renameTo(File dest) | 类似mv，移动或重命名                                   |
|                     |                                                        |

* ?: 规范化的绝对路径，通常涉及从路径名中删除冗余名称，例如:.和..

* 删除文件夹操作
* FilenameFilter，函数式接口，list和ListFiles都可以传进去一个参数。

```
		/**
     * @param   dir    the directory in which the file was found.
     * @param   name   the name of the file.
     * @return  <code>true</code> if and only if the name should be
     */
    boolean accept(File dir, String name);
```

* ListFiles还可以接收另一个参数来过滤File：FileFilter

```
boolean accept(File pathname);
```





获得信息

| 方法             | 描述                      |
| ---------------- | ------------------------- |
| lastModified()   | 上次修改对应的时间戳      |
| length()         | 字节数                    |
| getTotalSpace()  | 字节数,文件所在分区的大小 |
| getFreeSpace()   | 分区可用空间              |
| getUsableSpace() | 分区已用空间              |
|                  |                           |



用来判断的方式

| 方法                     | 描述                         |
| ------------------------ | ---------------------------- |
| isDirectory()            | 是否是文件夹                 |
| isFile()                 | 是否是文件                   |
| exists()                 | 是否真实存在                 |
| canRead()                | 是否可读                     |
| canWrite()               | 是否可写                     |
| canExecute()             |                              |
| isHidden()               | 是否是隐藏文件               |
| isAbsolute()             | 定义File的参数是不是绝对路径 |
| compareTo(File pathname) | 没卵用？                     |
|                          |                              |
|                          |                              |



设置属性

| 方法                                                 | 描述             |
| ---------------------------------------------------- | ---------------- |
| setLastModified(long time)                           | 更改上次修改时间 |
| setReadOnly()                                        | 设置为可读       |
| setWritable(boolean writable, boolean ownerOnly)     |                  |
| setWritable(boolean writable)                        |                  |
| setReadable(boolean readable, boolean ownerOnly)     |                  |
| setReadable(boolean readable)                        |                  |
| setExecutable(boolean executable, boolean ownerOnly) |                  |
| setExecutable(boolean executable)                    |                  |

