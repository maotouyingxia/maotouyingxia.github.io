# 实现一个简单的Shell

这个实验将阐明UNIX shells如何进行基本的系统调用

你要做的事是为xv6实现一个简单的shell。它应该能够运行带参数的命令，处理输入输出重定向以及建立两元管道。对于下面的例子以及类似的命令，你的shell应当与xv6 shell `sh`有同样的表现。

```
echo hello there
echo something > file.txt
ls | grep READ
grep lion < data.txt | wc > count
echo echo hello | nsh
find . b | xargs grep hello
```

你需要把源代码放在`user/nsh.c`中，并修改`Makefile`以编译它。我们会用其他xv6文件的拷贝来测试你的`user/nsh.c`,所以尽管你可以修改其他的文件，但是你的shell不能依赖于那些修改。你的shell应该使用`@`作为提示符而不是`$`，以避免与真正的hell混淆。一个xv6会话