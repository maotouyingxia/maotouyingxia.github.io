# Xv6与Unix实用程序

这个实验能让你熟悉Xv6系统和它的系统调用。

在开始实验前请切换到`util`分支：

`git checkout util`

## sleep

为Xv6实现UNIX程序sleep；你的sleep程序需要在用户指定的时长内暂停系统。在`user/sleep.c`中实现这个程序。

提示

- 查看`user`下的其他程序以了解如何获得传递给程序的命令行参数。如果用户忘记指定暂停的时长，sleep应该能够打印错误信息。
- 命令行参数以字符串的形式传递；你可以使用`user/ulib.c`中的`atoi`将其转换为整数
- 使用`user/usys.S`和`kernel/sysproc.c`中的系统调用`sleep`
- 确保main函数调用了`exit()`以退出sleep程序
- 记得把sleep程序添加到Makefile中

在Xv6 Shell里运行程序：

```
$ make qemu
...
init: starting sh
$ sleep 10
(这段时间里什么也没发生)
$ 
```

## pingpong

使用UNIX系统调用让一个字节通过一对方向相反管道在两个进程间像乒乓球一样来回。父进程通过向`parent_fd[1]`写入以发送一个字节；子进程通过从`parent_fd[0]`读取以接收一个字节。在收到父进程发送的一个字节后，子进程通过向`child_fd[1]`写入回复一个字节，紧接着这个字节被父进程接收。在`user/pingpong.c`中实现这个程序。

提示
- 使用pipe创建管道
- 使用fork创建子进程
- 使用read从管道中读取，使用write向管道中写入

在Xv6 Shell里运行程序

```
$ make qemu
...
init: starting sh
$ pingpong
0: received ping
3: received pong
$
```

冒号前的数字是进程号，可以通过系统调用`getpid`获取。

## primes

使用管道实现一个并行的质数筛选器。[这篇文章](https://swtch.com/~rsc/thread/) 中的图片展示了具体的原理。使用`pipe`和`fork`建立多个管道。第一个进程向管道中写入2~35。对于每个质数，创建一个进程通过管道从其左邻居接收数字，然后通过另一个管道向其右邻居发送数字。因为Xv6文件描述符和进程的数量限制，这个质数筛选器支持的最大数字是35。

- 在关闭进程不再需要的文件描述符时要小心，否则程序在输出质数35之前就会耗尽Xv6的资源。
- 在输出质数35后，应当彻底关闭所有的管道，包括所有的子进程。
- 直接向管道中写入32位int的数字比使用ASCII I/O要简单
- 只有有需要的时候才创建子进程

在Xv6 Shell中运行程序

```
$ make qemu
...
init: starting sh
$ primes
prime 2
prime 3
prime 5
prime 7
prime 11
prime 13
prime 17
prime 19
prime 23
prime 29
prime 31
$ 
```

## find

实现一个简化的UNIX find程序：找到目录树中所有名字与某个字符串匹配的文件。在`user/find.c`中实现这个程序。

提示
- 查看`user/ls.c`以了解如何读取目录
- 使用递归确保find能进入子目录
- 对于`.`和`..`无需递归
- qumu会保留上次运行中对文件系统的修改；可以使用`make clean`清除对文件系统的修改

在Xv6 Shell中运行程序

```
$ make qemu
...
init: starting sh
$ mkdir a
$ echo > a/b
$ find . b
./a/b
$ 
```

## xargs

实现一个简化的UNIX xargs程序：从标准输入中读取命令并运行，可用另外一行为命令提供额外的参数。在`user/xargs.c`中实现这个程序。

下面的例子说明了xargs的行为

```
$ xargs echo bye
hello too
bye hello too
CTRL-D
$
```

注意这里的命令`echo bye`和额外的参数是`hello too`组成了命令`echo bye hello too`，所以命令的输出是`bye hello too`。

提示
- 使用`fork`和`exec`系统调用运行每行输入的命令。在父进程中使用`wait`以等待子进程完成命令的执行
- 从标准输入中读入字符直到出现换行符`\n`
- `kernel/param.h`定义了`MAXARG`，可用于定义参数字符串数组`argv`
- qumu会保留上次运行中对文件系统的修改；可以使用`make clean`清除对文件系统的修改

可以通过命令`find . b | xargs grep hello`结合使用`xargs`, `find`,`grep`三条命令，这条命令会对目录`.`下所有名为`b`的文件执行`grep hello`命令。

在Xv6 Shell中运行命令行脚本xargstest.sh测试你的实现

```
$ make qemu
...
init: starting sh
$ sh < xargstest.sh
$ $ $ $ $ $ hello
hello
hello
$ $
```

输出中的多个`$`是因为Xv6 Shell比较原始，不能意识到它在从文件执行命令而不是终端，所以为文件中的每个命令都打印了一个`$`。

- [Lab: Xv6 and Unix utilities](https://pdos.csail.mit.edu/6.828/2019/labs/util.html)