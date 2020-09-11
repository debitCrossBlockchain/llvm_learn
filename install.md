# 准备LLVM学习环境

## LLVM 3.5

书籍《Getting Started with LLVM Core Library》[^1]中是基于LLVM 3.4的，而附录是基于LLVM 3.5的。这里准备LLVM 3.5的环境。

有很多种方式：自己编译安装、采用已编译的发行版本、Docker一键式部署安装环境。这里提供Docker镜像的方式，快速部署。其他方式也有尝试，详见……。

### 通过Docker 安装

| 镜像                                               |                                                              |
| -------------------------------------------------- | ------------------------------------------------------------ |
| rafaelauler/llvmbook<br>同jahentao/llvmbook:origin | 这是书《Getting Started with LLVM Core Library》[^1] Appendix中提供的LLVM 3.5学习镜像。比较大我下载了下来。<br>以防网络问题，提供了镜像tar包，下载导入即可：<br/>链接：https://pan.baidu.com/s/1W5HOxkMC5S9b6kB7mrV1Lw <br/>提取码：rt3u |
| jahentao/llvmbook:llvmbook-ssh                     | 在以上jahentao/llvmbook:origin镜像的基础上，安装了ssh，配合VS Code ssh远程开发。<br>启动容器时可以映射到8008端口。用户名root，密码123456。学习使用，不考虑安全问题。<br>以防网络问题，提供了镜像tar包，下载导入即可：<br>链接：https://pan.baidu.com/s/1drNXMFa_7v4XD_JsiNoRVA <br/>提取码：e7ol |

测试clang是否安装好的小技巧：

```bash
root@5e11819410b3:/workspace# install/bin/clang++ -o test -x c++ - <<< '#include <iostream>
> int main() { std::cout << "Hello World!\n"; return 0; }'
root@5e11819410b3:/workspace# ./test
Hello World!
root@5e11819410b3:/workspace#
```

clang++不会依赖文件扩展名确定语言，需要`-x`指定语言。

clang++ 的一些编译选项讲解：

| 编译选项  | 含义                                                         |      |
| --------- | ------------------------------------------------------------ | ---- |
| -fno-rtti | 如果使用的llvm库编译时没有链接RTTI，添加该选项可以避免链接错误 |      |
|           |                                                              |      |
|           |                                                              |      |



更为详细地编译选项详见：……。



[^1]: books/Getting_Started_with_LLVM_Core_Libraries.pdf	"Getting Started with LLVM Core Library"



## LLVM 3.8

尝试通过编译安装LLVM3.8的环境



LLVM 8

尝试通过编译安装LLVM 8的环境