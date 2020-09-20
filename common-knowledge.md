这部分注定是比较零散的知识。

1. LLVM中使用的C++最佳惯例，形成的项目的[编码标准](https://llvm.org/docs/CodingStandards.html)；

   LLVM为程序员提供了通用的数据结构，详见[程序员手册](https://llvm.org/docs/ProgrammersManual.html)。

2. LLVM使用模板特化来实现快速和经常使用的任务。

3. LLVM中使用轻量级的字符串引用`StringRef`，它可以像`const char*`那样进行值传递，但它也存储字符串的大小；它与`string`对象不同，并不拥有自己的缓冲区，因此不会分配堆空间，只是引用其外部的字符串。

4. 一个流程(pass)是指一次转换或分析过程。流程管理器(PassManager)用于注册、调度和声明流程之间的依赖关系。

5. LLVM中所有`.td`文件都是TableGen语言编写的，TableGen的输出始终是一个`.inc`文件。

6. LLVM IR这个术语专指Instruction类所代表的不同后端共享的中间表示。

7. LLVM安装文件夹下的`clang-c`子文件夹是libclang C头文件所在位置，通过`extern C`包含该`Index.h`头文件（是Clang C接口的主入口点）。

   所以当看到一个文件头使用了如下判断，就知道是使用了libclang库了。libclang库的C接口很稳定。

   ```c++
   extern "C" {
   #include "clang-c/Index.h"
   }
   ```


8. 从AST进行代码分析比使用控制流图（CFG）进行分析更难。所以通常不使用libclang或游标，而是使用[Clang插件](https://clang.llvm.org/docs/ClangPlugins.html)实现代码分析，插件直接使用Clang C++ API从AST创建[CFG](https://clang.llvm.org/doxygen/classclang_1_1CFG.html#a2e832d72829f1735bdc0aa7f1e80686c)。
9. 可以将Clang AST序列化并保存在以PCH为扩展名的文件中。
10. Clang在生成AST节点的同时执行类型检查。为了辅助执行语义分析，DeclContext基类包含每个作用域的第一个到最后一个Decl节点的引用。
11. 驱动程序生成LLVM IR的具体前端动作是CodeGenAction，其提供的代码生成ASTConsumer实例，被称为BackendConsumer。
12. 调用ParseAST执行词法分析和语法分析，这将通过调用HandleTranslationUnit函数调用具体的ASTConsumer。