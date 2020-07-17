# 类型安全

Rust是一门类型安全的语言。但此处我们所说的“类型安全”是什么意思呢？安全听起来是好事，但我们在哪些地方是安全的呢？


这里是C语言1999标准即C99中关于未定义行为(undefined behavior)的定义：

> __未定义行为__
>
> 不可移植的或使用错误程序构造或使用错误数据的行为，本国际标准不对此施加任何要求。


考虑如下的一段C程序：

```c
int main(int argc, char **argv) {
    unsigned long a[1];
    a[3] = 0x7ffff7b36cebUL;
    return 0;
}
```

根据C99，因为这段程序访问了数组`a`末尾后的一个元素，故它的行为是未定义的，这意味着它可以做任何事情。当我们在Jim的笔记本上运行这段程序，得到了如下输出：

```shell
undef: Error: .netrc file is readable by others.
undef: Remove password or make file unreadable by others.
```

然后奇怪的事情发生了，Jim的笔记本甚至都没有.netrc文件。如果你自己尝试，它可能会做一些完全不同的事情。


C编译器为这个`main`函数生成的机器码恰好将数组`a`放在栈中返回地址前三个字的位置，所以在`a[3]`中存储`0x7ffff7b36cebUL`改变了`main`的返回地址使其只想C标准库的中间位置即像一个_.netrc_文件查询密码，当`main`返回时，程序并不是在`main`的调用中执行，而是在库中的机器码中继续执行：

```shell
warnx(_("Error: .netrc file is readable by others."));
warnx(_(Remove password or make file unreadable by others.));
```

为了允许数组引用影响后续返回语句的行为，C编译器是完全符合标准的。一个未定义的操作不仅会产生未定义的结果：它还允许程序执行任何操作


