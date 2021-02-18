内容框架，包含：

- 分阶段解读。代码生成器有多个阶段组成，包括指令选择、指令调度、寄存器分配、寄存器分配后指令调度以及各阶段可能有的优化和调整过程。[1]
  - 目标机器平台上的合法化；
  - 指令选择，TableGen查表；
  - 指令调度；
  - 发射MI；
  - 寄存器分配；
  - 代码发射；
  - SelectionDAG介绍前两个部分。
- DAG介绍，有向无环图。打开dot文件的工具，[参考](https://stackoverflow.com/questions/3433655/free-visual-editor-for-graph-dot-files)。
- 【工具】llc，静态编译，将DAG内容**可视化**。-view；llvm-mc汇编器和反汇编器

思考一个问题: 如果使用IR来表示上文中的DAG有什么困难(换言之为什么不使用IR来直接表示DAG)?



[1]: https://blog.csdn.net/SiberiaBear/article/details/103836318

摘录描述：

> LLVM在O0编译时使用名为FastISel的指令选择PASS, 在O2编译时使用名为SelectionDAG的指令选择PASS, 同时当前社区还在推进名为GlobalISel的指令选择PASS(当前仅在AArch64上支持), 希望能替代SelectionDAG.[^2]

> 上文提到SelectionDAG是基于DAG covering的指令选择实现, 因此需要首先将输入程序流的表示方式从IR转换为DAG, 该过程又被称做lowering. lowering会将IR节点一一的翻译为DAG节点的同时也会处理调用约定, 使其遵守特定目标的ABI规范(这也是lowering名字的含义). 在lowering后程序流是以名为SDNode的节点组成的DAG.[^2]

> 在早期的SelectionDAG实现里还考虑的了调度的优化, 然而现在LLVM已经支持了PreRA与PostRA的调度, 在此处调度优化并不重要, 本文暂不涉及[^2]

> SelectionDAG是LLVM指令选择的基础框架, 不论是O0编译时使用的FastISel还是O2编译时使用的SelectionDAGISel都会使用SelectionDAG来描述输入的程序流. 将输入的IR转换成SelectionDAG的过程被称作lowering, 在lowering之前我们通过IR表示一段程序, 在lowering之后我们使用SelectionDAG来描述同一段程序.[^3]

详细解释图中每个符号的作用, 

简单介绍一下如何阅读这样的图.

> 

> 注意这里promote与legalize中promote的区别: legalize中的promote行为是必须的, 即由于架构不支持某个操作数类型, legalize才做对应的promote操作. 而combine中的操作都是合法的, 只是combine后的操作比combine前的更优. 在自定义架构上修改代码时需要注意这个区别.

> 将不支持的中端IR语义转换为架构支持的行为被称作legalize operation.[^4]

## 入门

先介绍工具，从整体直观学习。

1. 可视化工具

   GVEditor 1.02

2. 可视化指令

   ```
   llc -view-dag-combine1-dag main.ll
   ```

   

3. DAG图示介绍

   图中每个大的**方块**是一个名为SDNode的节点, 代表一个操作数或操作符. 根据不同的类型每个方块又划分为三行或四行子方块。
   其中表示操作数的节点包含三行, 从上到下依次代表节点类型枚举, 节点编号与节点的数据类型. 以左上角t3节点为例它代表一个数据类型为i32的寄存器操作数, 其节点编号为t3. 又比如最右侧的t15节点它代表一个数据类型为i8的常量, 其值为31, 节点编号t15。
   表示操作符的节点包含四行, 其中最上层一行表示这个操作符所接受的输入操作数的序号, 后三行与表示操作数的节点类似, 注意操作符本身没有数据类型, 它的数据类型是指这个操作符输出的操作数的数据类型. 以t11节点为例, 它代表一个截断操作, 其输出一个i8类型的数据, truncate操作接受一个输入操作数。
   节点间的**箭头**代表了节点的**依赖关系**, 图中一共有三类箭头, 黑色实线, 蓝色虚线以及红色实线, 它们分别代表三类依赖关系.
   **黑色实线**代表**数据依赖**, 箭头指向的数据来源. 以t19节点为例, 节点左移操作接受两个输入操作数, 被移位数据是比较少见的依赖的操作数(序号为0)的来源是t18, 移位位数的操作数(序号为1)的来源是t16. 其结果被store操作(节点t23)与拷贝操作(节点t26)引用.
   **蓝色虚线**代表一种**特殊的依赖关系**(chain dependence). 它指的是节点在调度时不能被移动到所依赖的节点之前. 以t23节点为例, store操作与load操作可能访问同一内存地址, 那么编译器需要保守处理将store指令放在load操作后. 然而store与load之间并没有直接的数据依赖关系(尽管在这个case中两者可以通过t18间接保证依赖, 但也可能出现完全没有依赖的情况), 因此编译器会为其添加一个额外的chain依赖. 为生成chain依赖, 编译器会为被依赖节点额外添加一个名为ch类型的输出操作数, 为依赖的来源节点额外添加一个输入操作数.
   **红色实线**类似蓝色虚线, 但它代表一种**更强的关系**(glue), 它指的是被glue的节点在调度时不能被分开. chain依赖的节点之间相互顺序不能改变, 但是两者可以分开调度, 也可以合并在一起, 而glue依赖要求节点必须绑定在一起. glue通常比较少见, 在上文的case中t26与t28之间存在一条glue依赖, 原因是t28是tail call操作, t26是函数调用前设置传参. 根据RISCV calling convention函数传参寄存器为x10-x17, 为保证正确性需要将两个节点绑定到一起, 否则可能存在第三个节点被调度到两者之间并在后面寄存器分配时分配到x10导致传参被覆写.[^3]

4. 

## 源码详细阅读

SelectionDAG的OPCode类型是`ISD::`XX，位于源码`/usr/local/include/llvm/CodeGen/ISDOpcodes.h`中，如`INSERT_SUBVECTOR`等。

### LLVM IR 转 SelectionDAG

1.IR OPCode -> SelectionDAG ISD OpCode

2.IR依赖关系 -> DAG



SelectionDAGBuilder通过visitXXX，访问对应的函数，处理每个OpCode。以visitCall为例，...。

### SelectionDAGISel 全局选择

有一个大的类SelectionDAGISel基类，负责处理SelectionDAG的构建、Combine、Legalize，以及MachineIntrDAG的Pre-RA Scheduling的固定管线。这个SelectionDAGISel基类可以根据Target，定义子类。在SelectionDAGISel中的处理，也叫做Global Selection全局选择。

对于

### SelectionDAG后面部分的指令选择

ISD OpCode -> Machine OpCode

基于Pattern，汇聚起来就是Match Table。

[^1]: https://blog.csdn.net/SiberiaBear/article/details/103836318
[^2]: [LLVM笔记(9) - 指令选择(一) 概述](https://www.cnblogs.com/Five100Miles/p/12822190.html)
[^3]: [LLVM笔记(10) - 指令选择(二) lowering](https://www.cnblogs.com/Five100Miles/p/12824942.html)

[^4]: [LLVM笔记(12) - 指令选择(四) legalize](https://www.cnblogs.com/Five100Miles/p/12865995.html)