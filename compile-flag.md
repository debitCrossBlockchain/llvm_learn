

方便使用的编译选项集合

| clang编译选项集合         |                                                 |      |
| ------------------------- | ----------------------------------------------- | ---- |
| llvm-config --system-libs | llvm编译时需要链接的系统库                      |      |
| llvm-config --cxxflags    | TODO ，链接的库不一定不全，需要自行根据需要补充 |      |
|                           |                                                 |      |

单个的选项

| clang编译选项 | 含义                                                         | 示例跳转               |                                                              |
| ------------- | ------------------------------------------------------------ | ---------------------- | ------------------------------------------------------------ |
| -fno-rtti     | 如果使用的llvm库编译时没有链接RTTI，添加该选项可以避免链接错误 |                        |                                                              |
| -###          | 查看编译工具为了完成命令而调用的所有其他工具                 |                        |                                                              |
| -emit-llvm    | 和-c标志一起使用，告诉clang以LLVM位码的格式生成一个目标文件。<br>和-S -c标志一起使用，生成人工可读的LLVM汇编码。 |                        | .bc是LLVM位码的文件扩展名；<br>.ll则是LLVM汇编文件的扩展名。 |
| -Xclang       | Clang驱动程序通过-cc1选项生成另一个自身实例，以调用其中内部编译器。通过在编译器驱动程序中使用-Xclang<option>可以将特定参数传递给该命令行工具。 | [link](#Option_Xclang) |                                                              |



| llc标志       |                                          |      |
| ------------- | ---------------------------------------- | ---- |
| -filetype=obj | 指定输出目标文件格式，而不是目标汇编文件 |      |
|               |                                          |      |
|               |                                          |      |

## 一个小例子

hello.c

<details>
    <summary>hello.c文件内容</summary>
```C
#include <stdio.h>
int main() {
  printf("Hello, World!\n");
  return 0;
}
```
</details>

<a id="Option_Xclang">clang -Xclang -ast-dump examples/Ch3/hello.c</a>

<details>
    <summary>clang -Xclang -ast-dump examples/Ch3/hello.c 部分输出</summary>
<code>
……
`-FunctionDecl 0x2b3e4d0 <examples/Ch3/hello.c:2:1, line:5:1> line:2:5 main 'int ()'
  `-CompoundStmt 0x2b3e6b0 <col:12, line:5:1>
    |-CallExpr 0x2b3e610 <line:3:3, col:27> 'int'
    | |-ImplicitCastExpr 0x2b3e5f8 <col:3> 'int (*)(const char *, ...)' <FunctionToPointerDecay>
    | | `-DeclRefExpr 0x2b3e570 <col:3> 'int (const char *, ...)' Function 0x2b30310 'printf' 'int (const char *, ...)'
    | `-ImplicitCastExpr 0x2b3e658 <col:10> 'const char *' <BitCast>
    |   `-ImplicitCastExpr 0x2b3e640 <col:10> 'char *' <ArrayToPointerDecay>
    |     `-StringLiteral 0x2b3e598 <col:10> 'char [15]' lvalue "Hello, World!\n"
    `-ReturnStmt 0x2b3e690 <line:4:3, col:10>
      `-IntegerLiteral 0x2b3e670 <col:10> 'int' 0
</code>
</details>