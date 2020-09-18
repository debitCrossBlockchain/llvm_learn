这部分注定是比较零散的知识。

1. LLVM中使用的C++最佳惯例，形成的项目的[编码标准](https://llvm.org/docs/CodingStandards.html)；

   LLVM为程序员提供了通用的数据结构，详见[程序员手册](https://llvm.org/docs/ProgrammersManual.html)。

2. LLVM使用模板特化来实现快速和经常使用的任务。

3. LLVM中使用轻量级的字符串引用`StringRef`，它可以像`const char*`那样进行值传递，但它也存储字符串的大小；它与`string`对象不同，并不拥有自己的缓冲区，因此不会分配堆空间，只是引用其外部的字符串。

4. 一个流程(pass)是指一次转换或分析过程。流程管理器(PassManager)用于注册、调度和声明流程之间的依赖关系。

5. LLVM中所有`.td`文件都是TableGen语言编写的，TableGen的输出始终是一个`.inc`文件。

6. 

