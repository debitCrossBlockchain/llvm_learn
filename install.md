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

禁用VS Code更新，VS Code自动更新导致远程的VS Code Server版本也要更新，时间不能浪费在无意义的地方。

![禁用VS Code更新](F:\Workspace\Git\llvm-study\images\vs_code_setting_update_none.png)

VS Code可以在远程安装一些插件：C/C++等提高效率。

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
EXAM_OBJECTS=exam.o

default: $(HELLO)

%.o : $(SRC_DIR)/%.cpp
	@echo Compiling $*.cpp
	$(QUIET)$(CXX) -c $(CPPFLAGS) $(CXXFLAGS) $<

$(HELLO) : $(HELLO_OBJECTS)
	@echo Linking $@
	$(QUIET)$(CXX) -o $@ $(CXXFLAGS) $(LDFLAGS) $^ $(LLVMLIBS) $(SYSTEMLIBS)

exam : $(EXAM_OBJECTS)
	@echo homework $@
	$(QUIET)$(CXX) -o $@ $(CXXFLAGS) $(LDFLAGS) $^ $(LLVMLIBS) $(SYSTEMLIBS)

clean::
	$(QUIET)rm -f $(HELLO) $(HELLO_OBJECTS) $(EXAM_OBJECTS)

```
TODO 如果改这个Makefile以复用

### 通过编译安装

单独编译安装LLVM很简单，一次成功。

#### 通过CMake

编译参数更多地请参考llvm/docs/CMake.rst文档。

```bash
mkdir mybuilddir
cd mybuilddir
cmake path/to/llvm/source/root
make
```

通过如上步骤，即可编译安装LLVM。

编译安装LLVM+Clang

### 生成文档

LLVM的文档格式采用reStructuredText(ReST)格式写的，文件后缀名是`.rst`。HTML文档采用[Sphinx](http://sphinx-doc.org/) 文件构建系统生成。生成文档也要做些工作，可以直接阅读版本已经发布的[文档](join-in.md)。

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

从源安装，Ubuntu 16.04 目前是 3.8的版本

```bash
sudo apt install llvm clang
```



## LLVM 8

成功编译安装LLVM 8的环境，在Unix-like Systems上。

以我的编译环境为例，虚拟机RAM 2G，还需要足够大的交换Swap空间。否则几次编译都有这样的错误：

```bash
clang: error: unable to execute command: Killed
clang: error: assembler command failed due to signal (use -v to see invocation)
```

1. 添加Swap空间，参考[博客](https://blog.csdn.net/hktkfly6/article/details/52753320)

```bash
sudo fallocate -l 6G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```
   开机自动挂载swap：
   使用 vi 或 nano 在 /etc/fstab 文件底部添加如下内容：

   ```
/swapfile none swap sw 0 0
   ```
2. 其次以编译经验，编译llvm和clang相关工具，带Debug信息，至少磁盘空间还剩余四五十G。VMWare Ubuntu LVM扩容，可以参考这篇[博客](https://blog.csdn.net/u011730792/article/details/109965822)。

3. 下载源代码，这里通过[Gitee 极速下载](https://gitee.com/mirrors) **/** [LLVM](https://gitee.com/mirrors/LLVM)

   ```bash
   git clone https://gitee.com/mirrors/LLVM.git
   ```

   会比原始仓库https://github.com/llvm/llvm-project快很多。

4. 切换分支

   ```bash
   git checkout llvmorg-8.0.1 -b llvmorg-8.0.1
   ```

5. 设置一些软链接

   ```bash
   cd llvm/tools
   ln -s ../../clang clang
   cd clang/tools/
   ln -s ../../clang-tools-extra extra
   cd ../../../
   ```

6. 开始构建，可参考[Building LLVM with CMake](https://llvm.org/docs/CMake.html)、[Getting Started](https://clang.llvm.org/get_started.html)和[Hacking on Clang](https://clang.llvm.org/hacking.html#docs)

   ```bash
   mkdir build
   cd build/
   cmake -DCMAKE_BUILD_TYPE=Debug -DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra" ..
   make -j2
   ```

   `-DCMAKE_EXPORT_COMPILE_COMMANDS=ON`用于生成编译数据库。`DCMAKE_BUILD_TYPE=RelWithDebInfo`很占磁盘空间，没有一两百G不要全编。

> - `-DLLVM_ENABLE_PROJECTS='...'` — semicolon-separated list of the LLVM subprojects you’d like to additionally build. Can include any of: clang, clang-tools-extra, libcxx, libcxxabi, libunwind, lldb, compiler-rt, lld, polly, or debuginfo-tests.
>   For example, to build LLVM, Clang, libcxx, and libcxxabi, use `-DLLVM_ENABLE_PROJECTS="clang;libcxx;libcxxabi"`.
>
> - `-DCMAKE_INSTALL_PREFIX=directory` — Specify for *directory* the full pathname of where you want the LLVM tools and libraries to be installed (default `/usr/local`).
> - `-DCMAKE_BUILD_TYPE=type` — Valid options for *type* are Debug, Release, RelWithDebInfo, and MinSizeRel. Default is Debug.
>
> -- [Refer](https://llvm.org/docs/GettingStarted.html)

7. 测试一下

   ```bash
   unset LLVM_HOME
   export PATH=~/project/LLVM/llvm/build/bin:$PATH
   ```

   输出：

   ```bash
   $ clang --version
   clang version 8.0.1
   Target: x86_64-unknown-linux-gnu
   Thread model: posix
   InstalledDir: /root/project/LLVM/llvm/build/bin
   ```

   ## 编译常见错误

   1. `error: linker command failed with exit code 1 (use -v to see invocation)`

      编译`make clang-check -j2`时遇到的。

   2. 编译clang项目

      ```
      cmake -DCMAKE_BUILD_TYPE=Debug -DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra;libcxx;" -DLLVM_TARGETS_TO_BUILD="X86" -DBUILD_SHARED_LIBS=ON -DCMAKE_CXX_COMPILER=/usr/bin/g++ ..
      ```
   
      成功后，执行clang编译程序`stddef.h not found`以及要安装z3的库。
      
      注意配置文件/etc/ld.so.conf中有指定动态库搜索路径，添加库后还需要`ldconfig`重新配置动态加载库。
   
   ## 使用CMake构建LLVM项目
   
   参考[Embedding LLVM in your project](https://releases.llvm.org/8.0.1/docs/CMake.html#id14) 在源码外构建LLVM项目，链接使用上述编译的LLVM .a库。