# 实现一个简单的shell

这个实验将阐明UNIX shells如何进行基本的系统调用。

在开始实验前请切换到`sh`分支：

`git checkout sh`

你要做的事是为Xv6实现一个简单的shell。它应该能够运行带参数的命令，处理输入输出重定向以及建立两元管道。对于下面的例子以及类似的命令，它应当与Xv6 shell有同样的表现。

```
echo hello there
echo something > file.txt
ls | grep READ
grep lion < data.txt | wc > count
echo echo hello | nsh
find . b | xargs grep hello
```

你需要把源代码放在`user/nsh.c`中，并把它加入`Makefile`。我们会用其他Xv6文件的拷贝来测试你的`user/nsh.c`，所以尽管你可以修改其他的文件，但是你的shell不能依赖于那些修改。你的shell应该使用`@`作为提示符而不是`$`，以避免与真正的shell混淆。你的shell应该能像下面这样工作：

```
Xv6 kernel is booting
$ nsh
@ grep Ken < README
Xv6 is a re-implementation of Dennis Ritchie's and Ken Thompson's Unix
@ 
```

请不要使用类似于`malloc()`的内存分配函数。作为代替，你应该只使用（栈分配的）本地变量和全局变量。你可以对命令的长度，参数的个数以及单个命令的长度给出合理的限制。

我们在`user/testsh.c`中为你提供了一个名为`testsh`测试程序。要完成这个实验，你的shell需要通过下面所有的测试：

```
$ testsh nsh
simple echo: PASS
simple grep: PASS
two commands: PASS
output redirection: PASS
input redirection: PASS
both redirections: PASS
simple pipe: PASS
pipe and redirects: PASS
lots of commands: PASS
passed all tests
```

## 提示

你不必实现测试中不需要的功能。例如，你无需实现圆括号或者引号。

*The Kernighan and Ritchie C* 包括许多有用的代码。你可以在你的实现中使用它们（需附上说明代码出处的注释）。例如，你也许会发现Section 5.12中parser的`gettoken()`函数很有用。

在功能上nsh比xv6的sh要简单得多，所以你最好从头开始编写nsh。虽然如此，你可以阅读xv6 shell的源码（位于`user/sh.c`）以获取灵感或者借用代码，只要你附上说明代码出处的注释。

Xv6在`user/ulibc`中为你提供了一个C的小型函数库，你可以使用它们。但正如在上面提到的，你不可以使用`malloc()`。

记得关闭不再需要的文件描述符，这样做可以避免文件描述符耗尽，还因为一个从管道中读取的进程不会见到EOF直到这个管道的所有写描述符关闭。

你的代码中所有的系统调用都应该检查是否返回了错误。

`testsh`会重定向你的shell的标准输出，这意味着你不会看到它。你的shell应该使用`fprintf(2,...)`向文件描述符2，标准错误，输出错误和调试信息。

如果`testsh`给出警告，修改`testsh`使其打印它想要你的shell执行的指令和修改你的shell使其打印它接收并执行的命令可能会有用。

另外一个有用的技巧是修改`testsh.c`中的`one()`使其打印它从你的shell读取的输出和它希望的输出。


- [Lab: Simple Xv6 shell](https://pdos.csail.mit.edu/6.828/2019/labs/sh.html)