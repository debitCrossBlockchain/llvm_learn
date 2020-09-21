这部分注定是比较零散的知识。

《Getting Started with LLVM Core Library》知识点：

1. LLVM中使用的C++最佳惯例，形成的项目的[编码标准](https://llvm.org/docs/CodingStandards.html)；

   LLVM为程序员提供了通用的数据结构，详见[程序员手册](https://llvm.org/docs/ProgrammersManual.html)。

2. LLVM使用**模板特化**来实现快速和经常使用的任务。

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
13. 通用的中间表示IR，使不同的后端能共享对源程序的相同理解。LLVM IR比Java字节码抽象级别更低。
14. LLVM IR这个术语专指Instruction类所代表的不同后端共享的中间表示。
15. LLVM IR的三种等价表达形式：

    1. 内存表示（Instruction类等）；
    2. 磁盘表示，压缩的`.bc`；
    3. 磁盘表示，人工可读的`.ll`；
16. LLVM IR对编译目标的依赖根植于C/C++语言固有的目标依赖性质。需要与内核的**系统调用**期望的类型相匹配。
17. 整个LLVM文件的内容被视为定义一个LLVM模块。模块是LLVM IR顶层数据结构。每个模块包含一系列函数，每个函数由包含一系列指令的一系列基本块组成。
18. LLVM局部变量以`%`符号开头的名称命名。
19. **静态单赋值**（SSA）形式。该形式下每个变量都不会被重新赋值，每个变量只有唯一一条定义它的赋值语句。
20. 代码组织成**三地址指令**。数据处理指令有两个源操作数，结果放在不同的目的操作数中。
21. 函数主体被明确划分为多个**基本块**（basic block，BB）。
22. [`nsw`](http://lists.cs.uiuc.edu/pipermail/llvmdev/2011-November/045730.html)标识指定此加法操作具有“no signed wrap”，已知指令是无溢出的。
23. LLVM IR最重要的类：

    1. [Module](https://llvm.org/doxygen/classllvm_1_1Module.html)，该类聚合了编译过程中使用的所有数据。
    2. Function，该类包含与函数定义或声明有关的所有对象。比如，参数列表。
    3. BasciBlock，一系列指令。
    4. Instruction，一条指令，最小单元了。
    5. Value。use-def链。从Value继承的类意味着该类定义了可以被其他指令使用的结果。
    6. User。访问def-use链。User的子类意味着该类使用一个或多个Value接口。

24. 上下文是LLVMContext类的一个实例，为了保证线程安全，必须使用该实例，因为多线程IR的生成必须按每个线程对应一个上下文来完成。

25. 在IR层执行的优化是目标代码无关的优化。

26. opt优化标志：

    | 优化标志 | 含义                                                         | 备注                      |
    | -------- | ------------------------------------------------------------ | ------------------------- |
    | -O0      | 无优化。该级别编译速度最快，并生成与原始代码行为最接近、最易调试的代码。 | 有些版本3.5.0没有这个选项 |
    | -O1      |                                                              |                           |
    | -O2      | 适中的优化级别，实现大部分优化。                             |                           |
    | -O3      | 编译需要较长的时间完成，或可能生成更大的代码（以便程序运行得较快）。 |                           |
    | -O4      | 链接时优化。                                                 |                           |
    | -Os      | 与-O2类似，用额外的代码减少代码的大小。                      |                           |
    | -Oz      | 与-Os类似，进一步优化了代码大小。                            |                           |

27. opt支持单独的代码优化流程（pass）。详见https://llvm.org/docs/Passes.html。

28. 流程（pass）间的依赖关系有两种。

    1. 显式依赖。循环信息和支配树。
    2. 隐式依赖。

29. Pass类是实现代码优化的主要资源，不能直接使用该类。实现Pass时，应该选择让流程性能最佳的**合适粒度**的最佳子类。

    1. ModulePass：作用于整个模块。
    2. FunctionPass：一次处理一个函数。
    3. BasicBlockPass：作用于每个基本块。

30. `-shared`来创建共享库。

31. 