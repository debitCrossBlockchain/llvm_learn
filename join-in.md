# 参与到LLVM开源项目开中去

LLVM有梳理其开源项目的[TODO List](https://www.llvm.org/OpenProjects.html) 这其中也有用于谷歌编程之夏。

## 阅读LLVM的文档

不同版本的指引文档不同，官网是最新版本的文档，以《Writing an LLVM Pass》为例，路径是：http://llvm.org/docs/WritingAnLLVMPass.html 。

而其他版本，以3.5.0为例，路径在：https://releases.llvm.org/3.5.0/docs/WritingAnLLVMPass.html 。

注意到替换url的小规律：

```
clang相关：
1. 从最新到指定版本的clang doc
http://clang.llvm.org/docs/LibASTMatchers.html
-->
https://releases.llvm.org/8.0.1/tools/clang/docs/LibASTMatchers.html
2. 从最新到指定版本的clang extra doc
http://clang.llvm.org/extra/clang-tidy/
-->
https://releases.llvm.org/8.0.1/tools/clang/tools/extra/docs/clang-tidy/
```



## Doxygen生成的API文档

https://clang.llvm.org/doxygen/



# 社区

- Clang前端[开发人员列表](https://lists.llvm.org/mailman/listinfo/cfe-dev)
- LLVM开发各模块总的[列表](https://lists.llvm.org/mailman/listinfo/llvmdev)