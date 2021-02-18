# 教程

1. LLVM_Cookbook这本书是实操，入手LLVM的最佳教程。其中的实验设计的很好，真正做完对LLVM有了更深的了解。

2. 为自己的语言实现前端的Kaleidoscope的教程：https://llvm.org/docs/tutorial/

3. 官方介绍AST：https://clang.llvm.org/docs/IntroductionToTheClangAST.html

4. 关于Clang设计的详细信息：https://clang.llvm.org/docs/InternalsManual.html

5. LLVM分析、变换和辅助流程（Pass）的完整列表：https://llvm.org/docs/Passes.html

6. UFMG的DCC888课程，课程地址：https://homepages.dcc.ufmg.br/~fernando/classes/dcc888/ementa/

7. CSE 401, WI '08: Introduction to Compiler Construction，课程地址：https://courses.cs.washington.edu/courses/cse401/08wi/

8. 关于复杂目标平台的更多知识，例如流水线、调度，请参见Chen Chung-Shu和Anoushe Jamshidi写的Tutorial: Creating an LLVM Backend for the Cpu0 Architecture中的章节。

   台湾的陳鍾樞（[https://github.com/Jonathan2251](https://link.zhihu.com/?target=https%3A//github.com/Jonathan2251)）编写的《Tutorial: Creating an LLVM Backend for the Cpu0 Architecture》，基于LLVM 3。有关：[CPU0的处理器架构](http://ccckmit.wikidot.com/ocs:cpu0)。

9. 国内基于CPU0架构的编译器构建学习工程，难得的是基于LLVM8，https://zhuanlan.zhihu.com/p/351848328。<!-- 博主还有个专栏，《[一步步掌握 LLVM](https://www.zhihu.com/column/c_1250484713606819840)》目前看内容不是体系化。-->

## 仓库

1. [shining1984/llvm-examples](https://link.zhihu.com/?target=https%3A//github.com/shining1984/llvm-examples)
2. [nsumner/llvm-demo](https://github.com/nsumner/llvm-demo ) 基于LLVM camke方便地搭建demo环境
3. llvm-tutor

# 工具

1. [clang-format configurator (zed0.co.uk)](https://zed0.co.uk/clang-format-configurator/) 在线clang-format配置生成的网站

2. 在线编译器

   1、[godbolt.org](https://link.zhihu.com/?target=https%3A//isocpp.org/godbolt.org/) (Clang, GCC, Intel ICC, VC++)

   2、[Wandbox](https://link.zhihu.com/?target=http%3A//melpon.org/wandbox/)  (Clang, gcc -- includes Boost)

   3、[Online Visual Studio Compiler](https://link.zhihu.com/?target=http%3A//webcompiler.cloudapp.net/) (VC++)

   4、[Stacked-Crooked](https://link.zhihu.com/?target=http%3A//stacked-crooked.com/) (GCC)

   5、[Rextester](https://link.zhihu.com/?target=http%3A//rextester.com/runcode) (Clang, GCC, VC++)

   6、[ideone.com](https://link.zhihu.com/?target=http%3A//ideone.com/) (GCC, Clang)
   
3. 在线Graphviz DAG可视化

   1. Graphviz Visual Editor，http://magjac.com/graphviz-visual-editor/
   
4. [Verilog仿真](http://iverilog.icarus.com/)，[安装](https://iverilog.fandom.com/wiki/Installation_Guide#Installing_version_0.9.x_from_a_PPA_on_Karmic.2C_Lucid.2C_Maverick.2C_Natty.2C_Oneiric.2C_Precise.2C_...)

# 其他

1. [LLVM每日谈](https://www.zhihu.com/column/llvm-clang)
2. llvmweekly 每周动态跟踪
3. 在线浏览LLVM仓库代码：
   1. https://reviews.llvm.org/source/llvm-github/repository/main/



# 大牛博文

1. Eli，博客地址：[http://eli.thegreenplace.net](https://link.zhihu.com/?target=http%3A//eli.thegreenplace.net)
2. 博客园 Five100Miles LLVM随笔分类，https://www.cnblogs.com/Five100Miles/category/1438128.html

# 论文/专著

1. 向量化论文。Loop-Aware SLP in GCC，由 Ira Rosen、Dorit Nuzman和Ayal Zaks所著