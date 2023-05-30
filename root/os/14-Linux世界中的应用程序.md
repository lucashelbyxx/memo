**背景回顾**：我们已经了解操作系统作为 “状态机的管理者”，通过为应用程序提供 fork, execve, exit 等系统调用使得第一个进程可以逐步构建出一个完整的应用世界。

**本讲内容**：完整的应用世界到底是如何构建的？在这节课中，我们用一个 “最小” 的 Linux 系统解答这个问题：

- Linux 操作系统
- Linux 系统启动和 initramfs
- Linux 应用世界的构建



# Linux 操作系统

## 25 Aug 1991, The Birthday of Linux

> Hello, everybody out there using minix – I’m doing a (free) operating system (just a hobby, won’t be big and professional like gnu) for 386(486) AT clones. This has been brewing since April, and is starting to get ready.
>
> —— Linus Torvalds (时年 21 岁)

------

类似于 “我写了一个加强版的操作系统实验，现在与大家分享”

- 发布在 comp.os.minix
  - 因为还依赖 Minix 的工具链 (从零开始做东西是不现实的)
  - 跑的都是 GNU 的程序：gcc, bash, ...
- 从此改变世界
  - “Just for fun: the story of an accidental revolutionary”



# 构建最小 Linux 系统

## 一个想法

我们能不能控制 Linux Kernel 加载的 “第一个状态机”？

- 计算机系统没有魔法
- 你能想到的事就能实现



挑战 ChatGPT：

- 我希望用 QEMU 在给定的 Linux 内核完成初始化后，直接执行我自己编写的、静态链接的 init 二进制文件。我应该怎么做？



我们的真正壁垒

- 怎样问出好问题
- 怎样回答问出的问题



## 启动 Linux

熟悉的 QEMU，稍稍有些不熟悉的命令行选项

- 再次挑战 ChatGPT：如果我希望用 QEMU 启动我给定的 Linux 内核二进制文件 vmlinuz 和初始内存文件系统 initramfs，应该使用怎样的 QEMU 参数使得 initramfs 中的 /bin/hello 作为第一个执行的程序？

```
run:
# Run QEMU with the installed kernel and generated initramfs
    qemu-system-x86_64 \
      -serial mon:stdio \
      -kernel build/vmlinuz \
      -initrd build/initramfs.cpio.gz \
      -machine accel=kvm:tcg \
      -append "console=ttyS0 quiet rdinit=$(INIT)"
```



# initrd 之后

## initrd: 并不是我们实际看到的 Linux

只是一个内存里的小文件系统

- 我们 “看到” 的都是被 init 创造出来的
  - 加载剩余必要的驱动程序，例如网卡
  - 根据 fstab 中的信息挂载文件系统，例如网络驱动器
  - 将根文件系统和控制权移交给另一个程序，例如 systemd



## 构建一个 “真正” 的应用世界

```
int pivot_root(const char *new_root, const char *put_old);
```

- `pivot_root()` changes the root mount in the mount namespace of the calling process. More precisely, it moves the root mount to the directory `put_old` and makes `new_root` the new root mount. The calling process must have the `CAP_SYS_ADMIN` capability in the user namespace that owns the caller's mount namespace.
- 执行 `/usr/sbin` (Kernel 的 init 选项)
  - 看一看系统里的文件是什么吧
  - 计算机系统没有魔法 (一切都有合适的解释)



## 例子：[NOILinux Lite](https://zhuanlan.zhihu.com/p/619237809)

在 init 时多做一些事

```
export PATH=/bin
busybox mknod /dev/sda b 8 0
busybox mkdir -p /newroot
busybox mount -t ext2 /dev/sda /newroot
exec busybox switch_root /newroot/ /etc/init
```

- pivot_root 之后才加载网卡驱动、配置 IP
  - 这些都是 systemd 的工作
  - (你会留意到 tty 字体变了)
- 之后 initramfs 就功成身退，资源释放



## Take-away Messages

我们从 CPU Reset 后的 “硬件初始状态” 到操作系统加载完 init 进程后的 “软件初始状态”，从此以后，计算机系统中的一切都是由应用程序主导的，操作系统只是提供系统调用这一服务接口。正是系统调用 (包括操作系统中的对象) 这个稳定的、向后兼容的接口随着历史演化和积累，形成了难以逾越的技术屏障，在颠覆性的技术革新到来之前，另起炉灶都是非常困难的。