# 元组
元组是各种类型的若干元素的集合。你可以将一个元组写为一个用逗号分隔，圆括号括起的元素序列。例如，`('Brazil', 1985)`是一个元组，其第一个元素是静态分配的字符串，第二个元素是一个整数；它的类型是`(&str, i32)`（或者是任何Rust为1985推断出的整数类型）。给定一个元组`t`，你可以用`t.0`，`t.1`等等获取它的元素。

元组不太像数组：一方面，元组的每个元素的类型可以互不相同，但数组的元素的类型必须一致。另外，元组只允许常数作为索引，像`t.4`。你不能写成`t.i`或者`t[i]`去索引第i个元素。

Rust代码中元组类型常被用作从一个函数返回多个值。例如，`split_at`方法作用与字符串切片上，将一个字符串分为两半并返回它们，它的声明类似于这样：
```rust
    fn split_at(&self, mid: usize) -> (&str, &str);

```

它的返回类型`(&str, &str)`是包含两个字符串切片的元组。你可以使用元组解构（pattern-matching syntax）将返回值的每个元素分配给不同的变量：
```rust
    let text = "I see the eigenvalue in thine eye";
    let (head, tail) = text.split_at(21);
    assert_eq!(head, "I see the eigenvalue ");
    assert_eq!(tail, "in thine eye");

```
下面这个比解构更易读：
```rust
    let text = "I see the eigenvalue in thine eye";
    let temp = text.split_at(21);
    let head = temp.0;
    let tail = temp.1;
    assert_eq!(head, "I see the eigenvalue ");
    assert_eq!(tail, "in thine eye");

```

你还可以看到元组被用作一种最小-drama结构类型。在第二章提到的Mandelbrot程序中，我们需要给函数传递图像的宽度和高度，让它绘出该图像并写入磁盘。我们可以声明一个包含宽度和高度成员的结构，但是对这么显而易见的事情来说这样的声明过于繁杂了，所以我们就用一个元组。
```rust
/// 将缓冲区“ pixels”（其大小由“ bounds”指定）写入名为“ filename”的文件。
fn write_image(filename: &str, pixels: &[u8], bounds: (usize, usize)) -> Result<(), std::io::Error> { ... }

```
`bound`参数的类型是`(usize, usize)`，即包含两个`usize`值的元组。我们固然可以把宽度和高度的参数分开写，并且这两种方式得到的机器码大致相同。这事关声明是否清楚，让人理解。我们将大小视为一个整体的值，而不是两个。使用元组可以让代码符合我们的意思。


另一个或许让人觉得惊讶的常用的元组类型，是空元组`()`。传统意义上我们叫它单位类型，因为它只有一个值，也写作`()`。当没有一个有意义的值去表示但是上下文需要某种类型的时候，Rust使用这个单位类型。


例如，一个不返回值的函数的返回类型是`()`，标准库中的`std::mem::swap`函数没有有意义的返回值；它只交换两个参数的值。`std::mem::swap`的声明如下：

```rust
fn swap<T>(x: &mut T, y: &mut T);
```

这个`<T>`说明了`swap`是泛型的：你可以使用任意T类型的值的引用。但是`swap`的函数签名完全省略了返回类型，也即返回单位类型的简写：

```rust
fn swap<T>(x: &mut T, y: &mut T) -> ();
```

类似的，之前提到过的`write_bitmap`的例子的返回类型为`Result<(), std::io::Error>`，意思为如果出错了，函数返回一个`std::io::Error`类型的值，否则不返回任何值。


如果愿意，可以在元组最后一个元素后加一个逗号：`(&str, i32)`和`(&str, i32,)`是等价的，正如表达式`("Brazil", 1985)`和`("Brazil", 1985,)`一样，Rust在使用逗号的地方都允许使用额外的尾随逗号：函数参数，数组，结构体和枚举定义等等。对我们来说可能看起来很奇怪，但是当在列表末尾添加和删除元素时，能更容易地看出差异。


出于一致性的考虑，特别是包含单个元素的元组。字面量`("lonely hearts",)`是包含一个字符串的元组，它的类型是`(&str,)`,这里的逗号是必须要有的，用来分辨它是单元素元组还是括号括起来的表达式。
