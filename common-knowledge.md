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


后端流程概述：

图

后端的不同阶段：

- 指令选择。将内存中的IR表示转换为指定目标的SelectionDAG节点。该阶段的一开始将LLVM IR的三地址结构转换为有向无环图（DAG）形式。使用模式匹配。
- 指令调度（Instruction Scheduling），也称为：前寄存器分配调度（Pre-register Allocation Scheduling）。该阶段确定基本块中的指令顺序，所有指令接下来被转换为 MachineInstr 三地址表示形式。
- 寄存器分配（Register Allocation）。该阶段无限虚拟寄存器被转换为特定目标的有限寄存器集。
- 后寄存器分配调度（Post-register Allocation Scheduling）。该阶段由于目标机器的寄存器信息已经可用，编译器可以根据硬件资源的竞争关系和不同寄存器的访问延迟差异性进一步提升生成的代码质量。
- 代码输出（Code Emission）。该阶段将 MachineInstr 表示的指令转换为 MCInst 实例，可输出为汇编代码、目标代码格式。


TableGen 用于生成记录的定义和类组成。记录仅用于存储信息。

代码生成器`.td`文件：

- <Target>.td 是一个重要的最高层次文件，它通过 TableGen 的include指令引入其他所有文件。
- <Target>InstrFormats.td 定义了指令格式。
- <Target>InstrInfo.td 定义了指令。

子类：

- 一个SelectionDAGBuilder实例（详见SelectionDAGISel.cpp）访问每个函数，并为每个基本块创建一个SelectionDAG对象。每个编译目标都需要实现TargetLowering类中的算法，类<Target>TargetLowering。
- lib/Target/<Target_Name>/<Target>ISelLowering.cpp 特定于目标的合并优化实现。
- 每个编译目标都通过再名为<Target_Name>DAGToDAGISel的SelectionDAGISel子类中实现Select方法来进行指令选择。







