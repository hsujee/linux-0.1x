# linux-0.1x

----------------------------------------------------------------------

这是可以在 Ubuntu 16.04 上编译、运行、调试的 Linux 内核源代码版本 0.1x（包括 Linux 0.11 和 Linux 0.12）。

## 1. Linux 环境设置

* 虚拟机
  
建议在虚拟机中安装 Ubuntu 16.04。

* 工具

Ubuntu 16.04 默认环境中安装编译工具。

```shell
# 安装编译调试工具链
sudo apt-get update
sudo apt-get install qemu gdb make git bin86 vim tree libc6-dev-i386
```

* 硬盘镜像

Linux-0.12 硬盘镜像文件获取（hdc-0.11.img）

请从以下网址下载：

```shell
http://www.oldlinux.org
https://oldlinux.superglobalmegacorp.com/Linux.old/bochs/

```

## 2. 编译调试

* 构建 Linux-0.1x

```shell
cd Linux-0.1x
make
```

> 注：编译时build.sh没有权限的问题，可通过如下命令解决。 `chmod 777 tools/build.sh`

* 在 qemu 上启动 Linux-0.1x

```shell
cd Linux-0.1x
make start
```

* 在 GDB 中调试 Linux-0.12

在 qemu 上运行 Linux-0.12，启动调试服务1234端口，再开始执行时立刻挂起。

```
cd Linux-0.1x
make debug
```

在调试 PC 上运行 GDB，如下所示：

```
gdb tools/system

(gdb)target remote :1234

(gdb)b main

(gdb)c

```

## 3. 更多帮助

想要查看更多编译选项，可查看帮助文档

```
make help
```

----------------------------------------------------------------------
