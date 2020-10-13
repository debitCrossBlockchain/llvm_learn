这部分注定是比较零散的知识。

《Getting Started with LLVM Core Library》知识点：

1. LLVM中使用的C++最佳惯例，形成的项目的[编码标准](https://llvm.org/docs/CodingStandards.html)；

   LLVM为程序员提供了通用的数据结构，详见[程序员手册](https://llvm.org/docs/ProgrammersManual.html)。

2. LLVM使用**模板特化**来实现快速和经常使用的任务。

3. LLVM中使用轻量级的字符串引用`StringRef`，它可以像`const char*`那样进行值传递，但它也存储字符串的大小；它与`string`对象不同，并不拥有自己的缓冲区，因此不会分配堆空间，只是引用其外部的字符串。

4. 一个流程(pass)是指一次转换或分析过程。流程管理器(PassManager)用于注册、调度和声明流程之间的依赖关系。

5. LLVM中所有`.td`文件都是TableGen语言编写的，TableGen的输出始终是一个`.inc`文件。

6. LLVM安装文件夹下的`clang-c`子文件夹是libclang C头文件所在位置，通过`extern C`包含该`Index.h`头文件（是Clang C接口的主入口点）。

   所以当看到一个文件头使用了如下判断，就知道是使用了libclang库了，libclang库的C接口很稳定。

   ```c++
   extern "C" {
   #include "clang-c/Index.h"
   }
   ```


7. 从AST进行代码分析比使用控制流图（CFG）进行分析更难。所以通常不使用libclang或游标，而是使用[Clang插件](https://clang.llvm.org/docs/ClangPlugins.html)实现代码分析，插件直接使用Clang C++ API从AST创建[CFG](https://clang.llvm.org/doxygen/classclang_1_1CFG.html#a2e832d72829f1735bdc0aa7f1e80686c)。

8. 可以将Clang AST序列化并保存在以PCH为扩展名的文件中，即进行预处理，之后可以从磁盘文件加载。

9. Clang在生成AST节点的同时执行类型检查。为了辅助执行语义分析，DeclContext基类包含每个作用域的第一个到最后一个Decl节点的引用。

10. 驱动程序生成LLVM IR的具体前端动作是CodeGenAction，其提供的代码生成ASTConsumer实例，被称为BackendConsumer。

    调用ParseAST执行词法分析和语法分析，这将通过调用HandleTranslationUnit函数调用具体的ASTConsumer。

11. 通用的中间表示IR，使不同的后端能共享对源程序的相同理解。LLVM IR比Java字节码抽象级别更低。

12. LLVM IR这个术语专指Instruction类所代表的不同后端共享的中间表示。by the way, 从这个角度理解IR的三种形式。

13. LLVM IR的三种等价表达形式：

    1. 内存表示（Instruction类等）；
    2. 磁盘表示，压缩的`.bc`；
    3. 磁盘表示，人工可读的`.ll`；
    
14. LLVM IR对编译目标的依赖根植于C/C++语言固有的目标依赖性质。需要与内核的**系统调用**期望的类型相匹配。

15. 整个LLVM文件的内容被视为定义一个LLVM模块。模块是LLVM IR顶层数据结构。每个模块包含一系列函数，每个函数由包含一系列指令的一系列基本块组成。

16. LLVM局部变量以`%`符号开头的名称命名。

17. **静态单赋值**（SSA）形式。该形式下每个变量都不会被重新赋值，每个变量只有唯一一条定义它的赋值语句。

18. 代码组织成**三地址指令**。数据处理指令有两个源操作数，结果放在不同的目的操作数中。

19. 函数主体被明确划分为多个**基本块**（basic block，BB）。

20. [`nsw`](http://lists.cs.uiuc.edu/pipermail/llvmdev/2011-November/045730.html)标识指定此加法操作具有“no signed wrap”，已知指令是无溢出的。

21. LLVM IR最重要的类：

    1. [Module](https://llvm.org/doxygen/classllvm_1_1Module.html)，该类聚合了编译过程中使用的所有数据。
    2. Function，该类包含与函数定义或声明有关的所有对象。比如，参数列表。
    3. BasciBlock，一系列指令。
    4. Instruction，一条指令，最小单元了。
    5. Value。use-def链。从Value继承的类意味着该类定义了可以被其他指令使用的结果。记忆方法，“**使用**（Use）一个或多个**值**（Value）”。
    6. User。访问def-use链。User的子类意味着该类使用一个或多个Value接口。

    ```mermaid
    classDiagram
        %% Function、Instruction都是Value、User的子类
        Value <|-- Function
        Value <|-- Instruction
        User <|-- Function
        User <|-- Instruction
        %% BasicBlock仅是Value的子类
        Value <|-- BasicBlock
        
        class Value {
            +use_begin()
            +use_end()
        }
        class User {
            +op_begin()
            +op_end()
        }
        
        class Function {
            +isDeclaration()
            +getArgumentList()
        }
        class BasicBlock {
            +getTerminator()
            +getSinglePredecessor()
        }
        class Instruction {
            +getOpcode()
            +op_begin()
            +op_end()
        }
    ```

    

22. 上下文是LLVMContext类的一个实例，为了保证线程安全，必须使用该实例，因为多线程IR的生成必须按每个线程对应一个上下文来完成。

23. 在IR层执行的优化是目标代码无关的优化。

24. opt优化标志：

    | 优化标志 | 含义                                                         | 备注                      |
    | -------- | ------------------------------------------------------------ | ------------------------- |
    | -O0      | 无优化。该级别编译速度最快，并生成与原始代码行为最接近、最易调试的代码。 | 有些版本3.5.0没有这个选项 |
    | -O1      |                                                              |                           |
    | -O2      | 适中的优化级别，实现大部分优化。                             |                           |
    | -O3      | 编译需要较长的时间完成，或可能生成更大的代码（以便程序运行得较快）。 |                           |
    | -O4      | 链接时优化。                                                 |                           |
    | -Os      | 与-O2类似，用额外的代码减少代码的大小。                      |                           |
    | -Oz      | 与-Os类似，进一步优化了代码大小。                            |                           |

25. opt支持单独的代码优化流程（pass）。详见https://llvm.org/docs/Passes.html。

26. 流程（pass）间的依赖关系有两种。

    1. 显式依赖。循环信息和支配树。
    2. 隐式依赖。

27. Pass类是实现代码优化的主要资源，不能直接使用该类。实现Pass时，应该选择让流程性能最佳的**合适粒度**的最佳子类。

    1. ModulePass：作用于整个模块。
    2. FunctionPass：一次处理一个函数。
    3. BasicBlockPass：作用于每个基本块。

28. `-shared`来创建共享库。

29. 后端流程概述：

    ```mermaid
    graph LR
A[LLVM IR] --> B[流程]
    B[流程] --> C[指令选择]
    C[指令选择] --> D[指令调度]
    %% 记忆组块Part1: 指令选择、指令调度
    D[指令调度] --> E[流程]
    E[流程] --> F[寄存器分配]
    %% 记忆组块Part2: 寄存器分配
    F[寄存器分配] --> G[流程]
    G[流程] --> H[指令调度]
    %% 记忆组块Part3: 指令调度
    H[指令调度] --> I[流程]
    %% 记忆组块Part4: Part1-3前后都有流程Pass可以优化
    I[流程] --> J{代码导出}
    J{代码导出} --> K[汇编代码]
    J{代码导出} --> L[目标代码]
    ```
    
    后端的不同阶段： 
    
    - 指令选择。将内存中的IR表示转换为指定目标的SelectionDAG节点。该阶段的一开始将LLVM IR的三地址结构转换为有向无环图（DAG）形式。使用**模式匹配**。
    - 指令调度（Instruction Scheduling），也称为：**前**寄存器分配调度（Pre-register Allocation Scheduling）。该阶段确定基本块中的指令顺序，所有指令接下来被转换为 MachineInstr 三地址表示形式。
    - 寄存器分配（Register Allocation）。该阶段无限虚拟寄存器被转换为特定目标的有限寄存器集。
    - **后**寄存器分配调度（Post-register Allocation Scheduling）。该阶段由于目标机器的寄存器信息已经可用，编译器可以根据硬件资源的竞争关系和不同寄存器的访问延迟差异性进一步提升生成的代码质量。
    - 代码输出（Code Emission）。该阶段将 MachineInstr 表示的指令转换为 MCInst 实例，可输出为汇编代码、目标代码格式。


30. TableGen 用于生成记录（record）的组成，分为定义（definitions）和类（classes）。记录仅用于存储信息。

    代码生成器`.td`文件：

    - <Target>.td 是一个重要的最高层次文件，它通过 TableGen 的include指令引入其他所有文件。定义后端锁支持的ISA功能和处理器系列。
    - <Target>RegisterInfo.td 定义了寄存器和寄存器类。
    - <Target>InstrInfo.td 定义了指令。
    - <Target>InstrFormats.td 定义了指令格式。
    - <Target>Schedule.td 中包含目标机器指令延迟和硬件流程信息的指令执行进程表（Instructioin Itinerary）。
    - <Target>CallingConv.td ABI定义的调用约定。

31. 了解LLVM类和目标子类：

    - SelectionDAG类，使用DAG来表示每个基本块的计算，每个SDNode节点对应一个指令或操作数。
    - 头文件llvm/include/llvm/CodeGen/ISDOpcodes.h包含**目标无关节点**的定义，lib/Target/<Target>/<Target>ISelLowering.h定义**目标相关的节点**。lib/Target/<Target>/<Target>ISelLowering.cpp 实现特定于目标的合并优化。
    - <Target>TargetLowering。一个SelectionDAGBuilder实例（详见SelectionDAGISel.cpp）访问每个函数，并为每个基本块创建一个SelectionDAG对象。每个编译目标都需要实现目标降级TargetLowering类中的算法，子类为<Target>TargetLowering。
    - <Target>DAGToDAGISel.cpp。每个编译目标都通过在名为<Target>DAGToDAGISel.cpp的SelectionDAGISel子类中实现Select方法来进行指令选择。Select()方法调用SelectCod()，最终调用SelectCodCommo()，后者是一个与目标相关的函数，以便通过使用之前生成的匹配表来匹配节点。
    - <Target>TargetMachine.cpp。关于特定目标的属性（如数据分布和ABI）的信息。
    - <Target>AsmPrinter.cpp。汇编代码输出。

32. 指令选择是将LLVM IR转换为代表目标指令的SelectionDAGger节点（SDNode）的过程。……

    关于指令选择，LLVM还支持一种成为快速指令选择的算法（对应于FastIsel类）。

33. LLVM提供了几个不同的调度程序，它们都是ScheduleDAGSDNodes（lib/CodeGen/SelectionDAG/ScheduleDAGSDNodes.cpp）的子类。

34. 寄存器分配的处理对象是MachineInstr 类。寄存器分配的基本任务是将数量不限的虚拟寄存器转换为物理（有限的）寄存器。由于编译目标的物理寄存器数量有限，需要为一些物理寄存器分配对应的内存地址，即溢出地址（spill slots）。

35. LLVM有4个寄存器分配实现，可使用llc的`regalloc=<regalloc_name>`选择它们：

    1. pbqp
    2. greedy：提供了一个全局寄存器分配算法，支持变量生存周期分割和最小化溢出次数。
    3. basic：提供扩展接口。
    4. fast：局部（每个基本块运行）的分配算法。

    default会根据-O级别选择其中之一。

36. 寄存器分配器的基本框架图：

    发现多了：寄存器合并器、寄存器分配器、寄存器重写器

    ```mermaid
    graph LR
    A[虚拟寄存器] --> B[流程]
    B[流程] --> C[寄存器合并器]
    C[寄存器合并器] --> D[流程]
    D[流程] --> E[寄存器分配器]
    E[寄存器分配器] --> F[流程]
    F[流程] --> G[寄存器重写器]
    G[寄存器重写器] --> H[物理寄存器]
    ```

37. 机器代码（简称MC）类包含一个用于对函数和指令进行低层操作的完整框架。在MC框架中机器代码指令（MCInst）代替了机器指令（MachineInstr），它是更为轻量的指令表示格式。

