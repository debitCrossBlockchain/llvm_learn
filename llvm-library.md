

LLVM和Clang的基本库

所有LLVM库都以libLLVM作为前缀

| 库             | 功能                                                         | 记忆                                    |
| -------------- | ------------------------------------------------------------ | --------------------------------------- |
| libLLVMCore    | 该库包含LLVM IR相关的所有逻辑：IR构造（数据布局、指令、基本块和函数）以及IR校验器。它还负责管理编译器中各种编译流程。 |                                         |
| libLLVMCodeGen | 该库实现与目标无关的代码生成和机器级别（比LLVM IR更低级版本）的分析与转换。 | libLLVMCodeGen包含通用算法              |
| libLLVMTarget  | 该库通过通用模板抽象来提供对目标机器信息的访问接口。         | libLLVMTarget包含用于抽象单个机器的接口 |
| libLLVMSupport | 该库包含一个通用工具集合。错误、整数和浮点处理、命令行解析、调试、文件支持和字符串处理都是在这个库中实现的算法示例。 |                                         |

注意传递给编译器时，如-lLLVMSupport和-lLLVMCore有依赖关系，所以传递给链接器的正确的顺序是-lLLVMCore -lLLVMSupport。幸运的是，llvm-config --libs可以为你排好传递给链接器参数的顺序。



一篇过时的[文档](https://releases.llvm.org/3.0/docs/UsingLibraries.html)，将这些库有个分类：

| 类别                          | 库   |      |
| ----------------------------- | ---- | ---- |
| **Core Libraries**            | 略   |      |
| **Analysis Libraries**        |      |      |
| **Transformation Libraries**  |      |      |
| **Code Generation Libraries** |      |      |
| **Target Libraries**          |      |      |
| **Runtime Libraries**         |      |      |


