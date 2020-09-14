# 准备LLVM学习环境

[TOC]



## LLVM 3.5

书籍《Getting Started with LLVM Core Library》[^1]中是基于LLVM 3.4的，而附录是基于LLVM 3.5.0的。这里准备LLVM 3.5.0的环境。

- LLVM 3.5.0的[文档主页](https://releases.llvm.org/3.5.0/docs/index.html)

有很多种方式：自己编译安装、采用已编译的发行版本、Docker一键式部署安装环境。这里提供Docker镜像的方式，快速部署。其他方式也有尝试，详见……。

### 通过Docker 安装

| 镜像                                               |                                                              |
| -------------------------------------------------- | ------------------------------------------------------------ |
| rafaelauler/llvmbook<br>同jahentao/llvmbook:origin | 这是书《Getting Started with LLVM Core Library》[^1] Appendix中提供的LLVM 3.5学习镜像。比较大我下载了下来。<br>以防网络问题，提供了镜像tar包，下载导入即可：<br/>链接：https://pan.baidu.com/s/1W5HOxkMC5S9b6kB7mrV1Lw <br/>提取码：rt3u |
| jahentao/llvmbook:llvmbook-ssh                     | 在以上jahentao/llvmbook:origin镜像的基础上，安装了ssh，配合VS Code ssh远程开发。<br>启动容器时可以映射到8008端口。用户名root，密码123456。学习使用，不考虑安全问题。<br>以防网络问题，提供了镜像tar包，下载导入即可：<br>链接：https://pan.baidu.com/s/1drNXMFa_7v4XD_JsiNoRVA <br/>提取码：e7ol |

从容器启动镜像：

```bash
docker run --name llvmbook -p 8008:22 -itd jahentao/llvmbook:llvmbook-ssh
```

解释一下，`-p, --publish list`将容器22端口在主机8008端口公开，`-t, --tty`分配一个pseudo-TTY伪终端，`-d, --detach`容器在后台运行并打印容器ID，容器名指定为`llvmbook`。

不进入容器，启动ssh（llvmbook-ssh镜像已安装）

```bash
docker exec -d llvmbook service ssh start
```

或进入容器，启动ssh

```bash
docker exec -it llvmbook /bin/bash
```

```bash
root@5be544e81dcc:/# service ssh start
```

在Windows VS Code中连接虚拟机中的容器，密码`123456`。

![在Windows VS Code中连接虚拟机中的容器](F:\Workspace\Git\llvm-study\images\vs_code_ssh_connect_container_in_vm.png)

在VS Code中Remote - SSH插件能够轻松编辑远程文件

![在VS Code中ssh remote插件能够轻松编辑远程文件](F:\Workspace\Git\llvm-study\images\vs_code_ssh_remote_edit_file_easyly.png)

VS Code可以在远程安装一些插件：C/C++等。

关闭容器

```bash
docker stop llvmbook
```

启动容器

```bash
docker start llvmbook
```

进入容器后，测试clang是否安装好的小技巧：

```bash
root@5be544e81dcc:/workspace# install/bin/clang++ -o test -x c++ - <<< '#include <iostream>
> int main() { std::cout << "Hello World!\n"; return 0; }'
root@5be544e81dcc:/workspace# ./test
Hello World!
root@5be544e81dcc:/workspace#
```

clang++不会依赖文件扩展名确定语言，需要`-x`指定语言。更为详细地编译选项详见[compile-flag.md](compile-flag.md)。

从《Getting Started with LLVM Core Library》[^1]书中，也可以积累到编译clang小程序的Makefile，可以用于以后使用。

```makefile
LLVM_CONFIG?=llvm-config

ifndef VERBOSE
QUIET:=@
endif

SRC_DIR?=$(PWD)
LDFLAGS+=$(shell $(LLVM_CONFIG) --ldflags)
COMMON_FLAGS=-Wall -Wextra
CXXFLAGS+=$(COMMON_FLAGS) $(shell $(LLVM_CONFIG) --cxxflags) -fno-rtti
CPPFLAGS+=$(shell $(LLVM_CONFIG) --cppflags) -I$(SRC_DIR)
LLVMLIBS=$(shell $(LLVM_CONFIG) --libs bitreader support)
SYSTEMLIBS=$(shell $(LLVM_CONFIG) --system-libs)

HELLO=helloworld
HELLO_OBJECTS=hello.o

default: $(HELLO)

%.o : $(SRC_DIR)/%.cpp
	@echo Compiling $*.cpp
	$(QUIET)$(CXX) -c $(CPPFLAGS) $(CXXFLAGS) $<

$(HELLO) : $(HELLO_OBJECTS)
	@echo Linking $@
	$(QUIET)$(CXX) -o $@ $(CXXFLAGS) $(LDFLAGS) $^ $(LLVMLIBS) $(SYSTEMLIBS)

clean::
	$(QUIET)rm -f $(HELLO) $(HELLO_OBJECTS)
```

TODO 如果改这个Makefile以复用

### 生成文档

LLVM的文档格式采用reStructuredText(ReST)格式写的，文件后缀名是`.rst`。HTML文档采用[Sphinx](http://sphinx-doc.org/) 文件构建系统生成。

可以参考`SphinxQuickstartTemplate.rst`学习如何写该类型文档。可以在VS Code中安装插件reStructuredText。详细可参考[Sphinx Introduction for LLVM Developers](https://lld.llvm.org/sphinx_intro.html)。

要生成文档，需要安装sphinx-build。

```bash
root@5be544e81dcc:/# apt install sphinx3
```

先安装pip（不要安装esay_install，以废弃），再通过其安装sphinx-build。

```bash
root@5be544e81dcc:/# wget https://bootstrap.pypa.io/get-pip.py
root@5be544e81dcc:/# export http_proxy=http://192.168.3.8:11080
```

其中我设置了宿主机代理http://192.168.3.8:11080。

在镜像中，进入`/workspace/llvm/docs`路径，执行



[^1]: books/Getting_Started_with_LLVM_Core_Libraries.pdf	"Getting Started with LLVM Core Library"

## LLVM 3.8

尝试通过编译安装LLVM3.8的环境



LLVM 8

尝试通过编译安装LLVM 8的环境