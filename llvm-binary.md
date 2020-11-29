



llvm 二进制介绍

| 二进制文件名 | 功能                                                         | 所属 | 记忆                                           |
| ------------ | ------------------------------------------------------------ | ---- | ---------------------------------------------- |
| llvm-config  | 获取编译使用LLVM的程序所需的各种配置信息。                   | 配置 |                                                |
| **opt**      | 使用libLLVMipa库实现与目标无关的过程间优化。这是一个旨在IR级对程序进行优化的工具。 | 优化 | .bc modular **opt**imizer and analysis printer |
| **llc**      | 使用libLLVMCodeGen库实现部分功能。这是一个通过特定后端将LLVM位码转换成目标机器汇编语言文件或部门文件的工具。 | 后端 | **ll**vm system **c**ompiler                   |
| llvm-link    | 将几个位码链接在一起，产生一个包含所有输入的LLVM**位码**文件。 | 链接 |                                                |
| llvm-as      | 汇编器。把LLVM IR从人类能看懂的文本格式汇编成二进制格式，即.bc的位码文件，此处得到的不是目标平台的机器码。 |      | llvm .ll -> .bc **as**sembler                  |
| llvm-dis     | 反汇编器。llvm-as的逆过程，反汇编。对象是LLVM IR的二进制格式，不是机器码。 |      | llvm .bc -> .ll **dis**assembler               |
| lli          | 解释执行LLVM IR。                                            |      | **ll**vm **i**nterpreter & dynamic compiler    |

