# 字符
Rust的字符类型`char`代表了单个的32位的Unicode字符。
Rust位单个字符单独使用`char`类型，但是为字符串和文字流使用UTF-8编码。所以，一个`String`表示它的文本是一个UTF-8的字节序列，而不是一个字符数组。

字符字面量由单引号括起来的字符，像`'8'`或者`'!'`。你可以使用任何你喜欢的Unicode字符：`'錆'`是一个`char`字面量，表示日本汉字*sabi*(rust)。

与字节文字一样，一些字符需要反斜杠转义：
![](../img/img-book/ch3-11.png)

如果你愿意，你可以使用十六进制写出字符的UniCode的码位点：
* 如果字符的码位点在U+0000到U+007F范围内（也就是说，如果它的范围是ASCII的字符集合），那么你可以将字符写为'\xHH'，其中HH是一个两位的十六进制数字。例如，字符字面量`'*'`和`'\x2A'`是等价的，因为字符 `*`的码位点是42，或者十六进制下的2A。
* 你可以将任何Unicode字符写为`'\u{HHHHHH}'`，HHHHHH代表1至6位长的十六进制数。例如，字符字面量`'\u{CA0}'`代表字符“ಠ”，是不赞成的表情的UniCode中的一个卡纳达语字符，“ಠ_ಠ”。同样的字面量也可以简单的写成`'ಠ'`.

一个`char`始终是范围为0x0000到0xD7FF或者0xE000到0x10FFFF的Unicode码位点。一个`char`永远不会是代理对的一半[A char is never a surrogate pair half]（即，码位点从0xD800到0xDFFF），或者Unicode码位空间之外的一个值（即，大于0x10FFFF）。Rust使用类型系统和动态检查确保`char`值始终在允许范围内。

Rust永远不会在`char`和其他类型间进行隐式转换。你可以使用算子`as`将一个`char`转换为一个整数类型；对小于32位的类型，字符值的高位被截断：
```rust
    assert_eq!('*' as i32, 42);
    assert_eq!('ಠ' as u16, 0xca0);
    assert_eq!('ಠ' as i8, -0x60); //U+0CA0截断为8为，带符号

```
从另一个方向看，`u8`是算子`as`转换`char`所使用的唯一类型：Rust想让as算子执行小代价，可靠的转换，但是`u8`以外的每个整数类型都包含不允许的Unicode代码点的值，因此这些转换需要在运行时检查。相反地，标准库函数`std::char::from_u32`接受任何`u32`值并返回一个`Option<char>`：如果这个`u32`是一个非法的Unicode码位点，那么`from_u32`返回`None`；否则，它返回`Some(c)`，c是一个`char`。

标准库为字符提供了一些有用的方法，你可以从在线文档中搜索“`char` (primitive type)”，对于模块“`std::char`”来说。举例如下：
```rust
    assert_eq!('*'.is_alphabetic(), false);
    assert_eq!('β'.is_alphabetic(), true);
    assert_eq!('8'.to_digit(10), Some(8));
    assert_eq!('ಠ'.len_utf8(), 3);
    assert_eq!(std::char::from_digit(2, 10), Some('2'));
```
自然，孤立的单个字符不如字符串和文本流那么有趣。我们将在本章的字符串类型这一节广泛地讨论Rust标准库中的`String`类型和文本处理。
