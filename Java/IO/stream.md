**InputStream**



| 方法                             | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| read()                           | 读取一个字节返回                                             |
| reda(byte[])                     | 读取一些字节到指定数组，返回读取到的字节个数                 |
| read(byte b[], int off, int len) | 读取指定len的字节到数组，从off位置开始存储，返回读取到的个数 |



## 字节流

**FileInputStream**

就是InputStream的实现类，实现了进行读的方法



**BufferedInputStream**

BufferedInputStream是提前将数据读取到缓冲区(默认8k)，然后可以通过read()三个方法进行读取，数组取完了后在去把数组加满。与FileInputStream相比，减少了IO操作次数。





## 字符流





**InputStreamReader**

| 方法                                      | 描述                   |
| ----------------------------------------- | ---------------------- |
| read()                                    | 读取一个字符(0-0xffff) |
| read(char cbuf[], int offset, int length) | 一次读一个字符数组     |
|                                           |                        |



























的read()比FileInputStream的read()效率要高，但是比byte[]低

 IO操作的消耗



字符缓冲流适合一次读一行进行处理

读取字节文件时，还是字节流+byte数组效率要高