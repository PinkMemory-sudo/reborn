* FileOutoutStream
* FileWriter
* OutPutStreamWriter
* BufferedOutputStream
* BufferedWriter
* 用字节流读字符会出现什么问题
* 用字符流读字节会出现什么问题



File







字节输出流FileOutputStream，只能写字节数组或者单个字节

字节缓冲流的特别之处在于





字节流+字节数组与缓冲流+字节数组的区别

字节流+字节数组

是直接讲数组中的字节写入文件

缓冲流+字节数组

缓冲流默认有一个8k的字节数组，是判断写入的大小，刷新缓存，将数组中的字节复制到8k的字节中。



输出流，文件不存在时会自动创建