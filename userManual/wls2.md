# wls2
## 定义  
Windows Subsystem for Linux（WSL）是一个用于在本地运行linux二进制可执行文件（ELF格式）的兼容层。与虚拟机相比，wsl没有虚拟硬件的过程，而是直接在windows上虚拟一个linux内核，模拟linux系统调用，以运行linux执行文件。因此效率要比虚拟机高，但是它使用的是自己实现的init进程而不是发行版的init进程，并且几乎没有实现任何系统服务，因此只适用于软件的开发，而不是作为桌面环境或生产性的服务器。  

WSL 2 是适用于 Linux 的 <strong>Windows 子系统体系结构</strong>的一个新版本，
它支持适用于 Linux 的 Windows 子系统在 Windows 上运行 ELF64 Linux 二进制文件。 它的主要目标是提高文件系统性能，以及添加完全的系统调用兼容性。

这一新的体系结构改变了这些 Linux 二进制文件与Windows 和计算机硬件进行交互的方式

## [原理](https://blog.csdn.net/jdbdh/article/details/88653434)  
### 组件
wsl实现的组件涉及到了用户和内核模式。在Windows NT内核模式中，LXCore，LXSS这两个驱动提供了linux内核调用的实现，即将linux调用转化为对应的windows NT内核调用；还提供了两种文件系统：VolFs（挂载在/目录上，支持linux文件系统所有特性）和DriverFs（挂载在/mnt/c，/mnt/d等等windows分区，主要为了支持系统间的互操作性）；驱动还会模拟内核的行为，对linux进程进行调度。

在用户模式下，windows提供了一种特殊的进程类型：Pico进程，来支持linux进程的运行。windows会 “放松” 对该类型进程的控制，主要交由linux虚拟内核调用和管理，即隔离性（因此需要系统的支持，低版本的系统不能使用wls的功能）。pico会将ELF二进制可执行文件装入到自己的地址空间，然后执行在linux虚拟内核提供的兼容层上。一个pico对应一个linux进程，并且pico进程也是windows的一种特殊进程，因此你可以在任务管理器上看到linux进程。

无论exe还是elf格式的二进制文件，原理上都可以在同架构的cpu上执行，只是结构不同操作系统不能解析罢了。而Pico能够解析ELF格式的二进制文件，只需要linux虚拟内核能够提供正确的系统调用，就能够运行大部分linux命令。

LXSS管理服务主要用于协调windows和linux进程之间的关系，和给于Bash.exe（并不是shell，只是我们访问wsl的入口）调用linux命令的接口。所有的运行的linux进程都会被加入到叫Linux实例（应该有LXSS记录的）中，只有第一次请求访问linux进程时才会创建Linux实例，才会创建init进程；当window关机时，会自动关闭linux实例，即关闭linux所有进程。

### wsl运行过程
linux中正常的启动过程是引导程序载入内核，内核初始化后载入init进程，init进程开启各项服务，将系统配置到用户可用的状态，如多用户登录、图形界面登录等。此时linux自启完成，接着等待用户的登录，并且登录方式多种，如控制台登录、ssh登录、虚拟终端登录等等。

而wsl并不是一个真正的系统，而只是windows的一部分，可以执行linux二进制可执行文件。内核也是虚拟出来的，但是具有一定隔离性，如linux进程由虚拟内核调度，也具有互操作性。

因此当windows自启结束并加载了上述LxCore、LXSS两个驱动后，已经能够提供linux进程系统调用了。此时wsl的init进程（windows自己实现的）并不会立刻运行，即暂时没有一个linux实例。只有当第一次访问wsl时（执行Bash.exe），才会创建linux实例，执行init服务进程（图中左边）。这个init进程会伴随linux实例，直到实例结束。然后再创建bash shell 和 另一个init 进程（图中右边），在本次会话结束时（关闭Bash.exe窗口）这两个进程结束。之后再通过Bash.exe连接wsl，都只会创建bash和右边的init进程。
### 文件系统
wsl只有两种windows设计的文件系统：VolFs和DriverFs。其中VolFs文件系统主要是为了支持linux文件系统的全部特性，如linux的文件权限、符号链接、不同于windows的文件名、文件名大小写敏感等等。

而DriveFs主要是为了挂载windows的分区，并且实现互操作性而存在。实际上就是NTFS文件系统的包装，能够让NTFS在linux中使用，即使也提供了大部分linux文件系统特性，但是限制很多

## 安装 WSL
### 先决条件
必须运行 Windows 10 版本 2004 及更高版本（内部版本 19041 及更高版本）或 Windows 11;
如果你运行的是旧版，或只是不想使用 install 命令并希望获得分步指引，请参阅[旧版 WSL 手动安装步骤](https://docs.microsoft.com/zh-cn/windows/wsl/install-manual)。
### 安装
在 powershell中： wsl --install  （wsl --install -d Ubuntu  ）   
此命令将启用所需的可选组件，下载最新的 Linux 内核，将 WSL 2 设置为默认值，并安装 Linux 发行版（默认安装 Ubuntu）。  
### [wsl命令](https://docs.microsoft.com/zh-cn/windows/wsl/basic-commands)  
``` javascript
// 安装 WSL 和 Linux 的 Ubuntu 发行版
wsl --install 
// 将 WSL 版本设置为 1 或 2
wsl --set-version <distribution name> <versionNumber> 
// 设置默认 Linux 发行版
wsl --set-default <Distribution Name> 
// 将目录更改为主页
wsl ~
```
## wsl2迁移ubuntu  
WSL2 (Ubuntu20.04LTS)后，其默认安装路径在 C:\Program Files\WindowsApps  
### 准备工作
1. 在powershell中 查看已安装的linux发行版本  
wsl -l --all -v 
2. 如果状态显示running，要先停止  
wsl --shutdown  
### 迁移  
1. 导出分发版为tar文件到暂存路径  
wsl --export Ubuntu d:\\wsl-ubuntu.tar  
2. 注销当前分发版  
wsl --unregister Ubuntu
3. 重新导入并安装WSL到最终路径  
wsl --import Ubuntu d:\\wsl2-ubuntu d:\\wsl-ubuntu.tar
4. 登录  
wsl Ubuntu

### 文件存储
若要在 Windows 文件资源管理器中打开 WSL 项目，  
请输入：explorer.exe .  
请确保在命令的末尾添加句点以打开当前目录。

将项目文件与计划使用的工具存储在相同的操作系统上。
若想获得最快的性能速度，请将文件存储在 WSL 文件系统中，前提是使用 Linux 工具在 Linux 命令行（Ubuntu、OpenSUSE 等）中处理这些文件。 如果是使用 Windows 工具在 Windows 命令行（PowerShell、命令提示符）中工作，请将文件存储在 Windows 文件系统中。 可以跨操作系统访问文件，但这可能会显著降低性能。

例如，在存储 WSL 项目文件时：

使用 Linux 文件系统根目录：\\wsl$\<DistroName>\home\<UserName>\Project
而不使用 Windows 文件系统根目录：C:\Users\<UserName>\Project 或 /mnt/c/Users/<UserName>/Project$


### 在wsl2中启动vscode
在wsl2, ubuntu中执行： code .  
[查看](https://docs.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-vscode)   


