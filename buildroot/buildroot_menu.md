整理Buildroot笔记，包含配置选项注释、目录结构分析、常用命令、构建示例、 使用技巧。

## 1.Buildroot基本介绍

Buildroot是Linux平台上一个构建嵌入式Linux系统的框架。
整个Buildroot是由Makefile(*.mk)脚本和Kconfig(Config.in)配置文件构成的，因此可以像配置Linux内核一样执行make menuconfig进行配置，编译出一个完整的、可以直接烧写到机器上运行的Linux系统文件(包含bootloader、kernel、rootfs以及rootfs中的各种库和应用程序)。
回想构建开源软件包的流程，工作流大致如下：
获取：获取源代码
解压：解压源代码
补丁：针对缺陷修复和增加的功能应用补丁
配置：根据环境准备构建过程
安装：复制二进制和辅助文件到它们的目标目录
打包：为在其它系统上安装而打包二进制和辅助文件

可以看到构建每个软件包的工作流几乎是相同的，Buildroot主要就是把这些重复操作自动化，用户只需勾选上所需软件包，便自动完成以上所有操作。
其次，U-boot、Linux Kernel的编译工作流的差不多，只是配置的自定义参数更多，在Buildroot设置好了，也就一并生成。

## 2.Buildroot配置选项

以Buildroot 2019.02.4，imx6ull配置为例，对配置选项进行注释。
执行**make menuconfig**进入一级配置菜单('一共10个')：

```
Target options --->
Build options --->
Toolchain --->
System configuration --->
Kernel --->
Target packages --->
Filesystem images --->
Bootloaders --->
Host utilities --->
Legacy config options --->
```
后面将依次对每个子菜单进行注释。约定()内为直接翻译或补充说明，[]内为示例选项含义。

### 2.1 Target options(目标选项)
```
===一级配置菜单：Target options ---> ===

Target options --->
Target Architecture (ARM (little endian)) ---> //目标处理器的架构和大小端模式 [ARM (little endian)]
Target Binary Format (ELF) ---> //目标二进制格式 [ELF]
Target Architecture Variant (cortex-A7) ---> //目标处理器核心类型 [cortex-A7]
Target ABI (EABIhf) ---> //目标应用程序二进制接口(Application Binary Interface) [EABIhf] ①
Floating point strategy (NEON/VFPv4) ---> //浮点运算策略 [NEON/VFPv4] ②
ARM instruction set (ARM) ---> //ARM的指令集设置 [ARM]
```

①：ABI是X86计算机上的，EABI是嵌入式平台上; EABI/EABIhf分别适用于armel和armhf两个不同的架构，armel和armh在对待浮点运算采取了不同的策略(有fpu的arm才能选择EABIhf)；
②：ARM浮点体系结构(VFP)，VFPvX为历史各版本，比如浮点运算指定为VFP4(vector floating point4)指令或neon向量浮点指令；

### 2.2 Build options(编译选项)

```
=== 一级配置菜单：Build options---> ===

Build options --->
Commands ---> //指定下载、解压命令参数选项
(wget --passive-ftp -nd -t 3) Wget command //用于常规FTP/HTTP下载压缩包 [被动传输模式;不创建目录;超时重试次数为3]
(svn --non-interactive) Subversion (svn) command //通过SSH下载压缩包 [禁用所有交互式提示]
(bzr) Bazaar (bzr) command //版本控制工具Bazaa
(git) Git command //版本控制工具Git
(cvs) CVS command //版本控制工具CVS
(cp) Local files retrieval command //本地文件拷贝命令
(scp) Secure copy (scp) command //基于ssh的安全的远程文件拷贝命令
(hg) Mercurial (hg) command //版本控制工具hg
(gzip -d -c) zcat command //zip包解压缩查看 [解压zip文件 解压到指定路径，源文件不消失]
(bzcat) bzcat command //bz2包解压缩查看
(xzcat) xzcat command //xz包解压缩查看
(lzip -d -c) lzcat command //lz包解压缩查看
() Tar options //bz2包解压缩查看
(/home/hceng/imx6ul_buildroot/configs/imx6ulevk_defconfig) Location to save buildroot config //指定配置文件保存路径
((TOPDIR)/dl) Download dir //指定文件下载保存路径 [./dl/] ★
((BASE_DIR)/host) Host dir //指定主机编译所需工具安装目录 [./output/host] ★
Mirrors and Download locations ---> //镜像和下载位置
() Primary download site
(http://sources.buildroot.net) Backup download site
(https://cdn.kernel.org/pub) Kernel.org mirror
(http://ftpmirror.gnu.org) GNU Software mirror
(http://rocks.moonscript.org) LuaRocks mirror
(http://cpan.metacpan.org) CPAN mirror (Perl packages)
(0) Number of jobs to run simultaneously (0 for auto) //指定编译时运行的CPU核心数 [0自动]
[ ] Enable compiler cache //使能编译器缓存
[ ] build packages with debugging symbols //启用带调试编译软件包
[] strip target binaries //binaries和libraries在打包到target目录的时候会被strip命令裁减掉调试信息   ★
() executables that should not be stripped //剥离时跳过可执行文件
() directories that should be skipped when stripping //剥离时跳过的目录
gcc optimization level (optimize for size) ---> //GCC优化等级 [优化大小]
[ ] Enable google-breakpad support //启动崩溃日志收集
libraries (shared only) ---> //库类型 [只共享库]
($(CONFIG_DIR)/local.mk) location of a package override file //包覆盖文件的位置  ★
() global patch directories //全局补丁目录
Advanced --->
[] paranoid check of library/header paths //检查库/头文件路径
[ ] Force the building of host dependencies //强制构建主机依赖
[ ] Make the build reproducible (experimental) //构建可重复(实验)
* Security Hardening Options * //安全加固选项
Stack Smashing Protection (None) ---> //堆栈粉碎保护 [无]
RELRO Protection (None) ---> //RELRO只读重定位(Relocation Read Only)保护 [无]
Buffer-overflow Detection (FORTIFY_SOURCE) (None) ---> //缓冲区溢出检测(强制源) [无]
```
###2.3 Toolchain(工具链)

```
===一级配置菜单： Toolchain --->  ===

Toolchain type (External toolchain) ---> //工具链类型 [外部工具链]
*** Toolchain External Options *** //外部工具链选项
Toolchain (Custom toolchain) ---> //工具链 [自定义工具链]
Toolchain origin (Toolchain to be downloaded and installed) ---> //工具链来源 [工具链将被下载安装]
  ( ) Toolchain to be downloaded and installed  
  (X) Pre-installed toolchain                                              
(https://releases.linaro.org/……) Toolchain URL //工具链下载链接 ①  ★

(bin) Toolchain relative binary path //工具链二进制文件相对路径 [bin目录]
($(ARCH)-linux-gnueabihf) Toolchain prefix //工具链前缀 [arm-linux-gnueabihf] ★
External toolchain gcc version (6.x) ---> //外部工具链GCC版本 [6.x]
External toolchain kernel headers series (4.6.x) ---> //外部工具链内核头文件系列 [4.6.x]
External toolchain C library (glibc/eglibc) ---> //外部工具链C库 [glibc/eglibc] ②
[*] Toolchain has SSP support? //工具链是否支持SSP? ③ ★
[*] Toolchain has RPC support? //工具链是否支持RPC? ④ ★
[*] Toolchain has C++ support? //工具链是否支持C++? ★
[*] Toolchain has Fortran support? //工具链是否支持Fortran? (一种编程语言)
() Extra toolchain libraries to be copied to target //复制额外工具链库到目标
[ ] Copy gdb server to the Target //复制GDB服务到目标
*** Host GDB Options *** //主机GDB选项
[ ] Build cross gdb for the host //为主机交叉编译GDB
*** Toolchain Generic Options *** //工具链通用选项
[ ] Copy gconv libraries //复制gconv库 (gconv库用于在不同的字符集之间进行转换)
[*] Enable MMU support //使能MMU支持  ★
() Target Optimizations //目标优化 (需设置前面的GCC优化等级)
() Target linker options //目标链接器选项 (构建目标时传递给链接器的额外选项)
[ ] Register toolchain within Eclipse Buildroot plug-in //在Eclipse Buildroot插件中注册工具链
```

①：受限每行字数，该处完整链接为：https://releases.linaro.org/components/toolchain/binaries/6.2-2016.11/arm-linux-gnueabihf/gcc-linaro-6.2.1-2016.11-x86_64_arm-linux-gnueabihf.tar.xz；
②：可选的有uClibc/uClibc-ng、glibc/eglibc、musl (experimental)，介绍如下：
glibc(GNU C Library):常用于桌面、服务器中的GNU/Linux类系统中的C语言标准库，支持多种系统平台，功能齐全，但也相对比较臃肿和庞大;
eglibc(Embedded glibc):glibc的一种变体，目的在于将glibc用于嵌入式系统;
uClibc/uClibc-ng:uClibc-ng是uClibc的分支，它们都是为嵌入式设计的小型的C语言标准库，适合标准和无MMU的Linux系统上运行;

musl:一个轻量级的C标准库，目前在buildroot里是实验阶段;
对比参考：Comparison of C/POSIX standard library implementations for Linux

③：堆栈粉碎保护(Stack Smashing Protection)；
④：远程过程调用(Remote Procedure Call)，主要用于NFS；

### 2.4 System configuration(系统配置)

System configuration ---> 系统配置

```shell
==== 一级配置菜单： System configuration  --->  ====

Root FS skeleton (default target skeleton) ---> //根文件系统框架 [典型目标框架]
-----------------------------
(X) default target skeleton 
( ) custom target skeleton     

(RV1126_RV1109) System hostname //系统主机名字(自取任意) [RV1126_RV1109]  ★就是命令提示行的前缀
(Welcome to RV1126_RV1109 Buildroot) System banner //系统开机提示 [Welcome to RV1126_RV1109 Buildroot] ★
Passwords encoding (sha-256) ---> //密码编码 [sha-256]  ★
Init system (BusyBox) ---> //初始化系统方案 [BusyBox] ①
-----------------------------
(X) BusyBox         
( ) systemV  
( ) systemd 
( ) None    
                           
/dev management (Dynamic using devtmpfs + eudev) ---> //dev管理方案 [Dynamic using devtmpfs + eudev] ②
-----------------------------
 ( ) Static using device table         
 ( ) Dynamic using devtmpfs only       
 ( ) Dynamic using devtmpfs + mdev     
 (X) Dynamic using devtmpfs + eudev    

(system/device_table.txt) Path to the permission tables //权限表路径 ★
system/device_table.txt                


[ ] support extended attributes in device tables //支持设备表中的扩展属性
[ ] Use symlinks to /usr for /bin, /sbin and /lib //是否将/bin,/sbin,/lib链接到/usr
[*] Enable root login with password    //使能root登陆密码         ★
(rockchip) Root password               //设置root密码            ★
/bin/sh (busybox default shell) --->   //选择shell类型 [bash] ③
-----------------------------
 (X) busybox' default shell  
 ( ) bash                    
 ( ) dash                    
 ( ) mksh                    
 ( ) zsh                     
 ( ) none                    

[*] Run a getty (login prompt) after boot ---> //启动后运行getty(登录提示)--->
-----------------------------
(ttyFIQ0) TTY port //设置TTY硬件端口  ★
   Baudrate (`1500000`) ---> // [设置波特率]
(vt100) TERM environment variable //TERM环境变量 (终端类型xterm、vt100)
() other options to pass to getty //传递给getty的其他选项


[*] remount root filesystem read-write during boot //在引导期间安装根文件系统支持读和写  ★
(eth0) Network interface to configure through DHCP //设置DHCP配置的网络接口 [eth0]
(/bin:/sbin:/usr/bin:/usr/sbin) Set the systems default PATH //设置系统的默认路径
[*] Purge unwanted locales //清除不需要的区域设置
(C en_US) Locales to keep //要保留的语言环境
() Generate locale data //生成区域设置数据
[ ] Enable Native Language Support (NLS) //启用本地语言支持（NLS）
-*- Install timezone info //安装时区信息  ★
(default) timezone list //时区清单 [典型]
(Etc/UTC) default local time //用户表的路径
() Path to the users tables
( 'board/rockchip/common/base board/rockchip/common/wifi board/rockchip/common/wifi board/rockchip/rv1126_rv1109/fs-overlay/ board/rockchip/rv1126_rv1109/fs-overlay-sysv/') Root filesystem overlay directories //根文件系统覆盖目录 ★
 
('build/post.sh') Custom scripts to run before creating filesystem images //在创建文件系统映像之前运行的自定义脚本★
() Custom scripts to run inside the fakeroot environment               //自定义脚本在fakeroot(模拟root权限)环境中运行
(board/……) Custom scripts to run after creating filesystem images     //创建文件系统映像后运行的自定义脚本 ④★
() Extra arguments passed to custom scripts //传递给自定义脚本的额外参数
```

①：可选选项有BusyBox、systemV、systemd、None：
BusyBox init:
1.不支持运行等级，设置的运行等级将被忽略，要使用运行等级，请使用sysvinit;
2.语法格式：<id>:<runlevels>:<action>:<process>
<id>:process执行所在的tty设备，内容为/dev目录中tty设备的文件名;
<runlevels>:此字段完全被忽略;
<action>:支持sysinit、respawn、askfirst、wait、once、restart、ctrlaltdel、shutdown;
<process>:指定要执行的进程及其命令行;
3.BusyBox init程序将在启动时读取/etc/inittab文件，以了解该做什么，默认inittab存储在./package/busybox/inittab;
4.inittab除了安装几个重要的文件系统之外，还要启动/etc/init.d/rcS中的shell脚本，并启动一个getty程序(提供一个登录提示);

systemV:
1.使用传统sysvinit程序，之前大多数台式机Linux发行版都使用该方案，现在有些变为了Upstart或Systemd;
2.在/ect目录下会生成init.d、rc0.d、rc1.d、rc2.d、rc3.d、rc4.d、rc5.d、rc6.d、rc.loacl;
init.d里面包含的是真正的服务脚本;
rcN.d里面是链接向init.d里脚本的软链接，N表示运行级别，进入哪个运行级别，就会执行对应rcN.d文件夹的脚本;
当所有的当前运行级别的脚本都运行完了之后，会运行rc.local;
3.脚本的命名规则：以[S|K]+NN+其它，以S开头的是启动脚本，以K开头的是停止脚本，init进程会按照S或者K后面的数字的顺序来启动或停止服务;
4.sysvinit还使用/etc/inittab文件(与BusyBox的语法略有不同)，默认inittab存储在./package/sysvinit/inittab;

systemd:
1.systemd是Linux的新一代init系统，以前的运行级别(runlevel)的概念被新的运行目标(target)所取代;
2.支持并行化任务;采用socket式与D-Bus总线式激活服务;按需启动守护进程(daemon);支持快照和系统恢复;
3.功能强大的同时，也带来了相当大数量的大型依赖：dbus，udev等;
Systemd 的简介和特点
②：/dev设备文件的管理方式，可选选项有四个：
Static using device table: 使用静态的设备表，/dev将根据system/device_table _dev.txt的内容创建设备，进入系统添加或删除设备时，无法自动更新；
Dynamic using devtmpfs only:在系统启动过程中，会动态生成/dev文件，进入系统添加或删除设备时，无法自动更新；
Dynamic using devtmpfs + mdev:在前面devtmpfs的基础上加入mdev用户空间实用程序，进入系统添加或删除设备时，可以自动更新，自动创建规则在/etc/mdev.conf;
Dynamic using devtmpfs + eudev:在前面devtmpfs的基础上加入eudev用户空间守护程序，eudev是udev的独立版本，是Systemd的一部分，提供更多的功能也更占用资源；
③：在Linux下编写shell脚本文件时，经常会看到在第一行中标注#!/bin/bash，这句话的意思是告诉系统强制用bash，避免出现一些不兼容的问题。因此，除了bash外，还有很多shell工具，比如这里可选busybox自带的shell、小巧但功能很少的dash、高效紧凑的mksh、功能强大体积也稍大的zsh。此外，可以通过ls -l /bin/sh查看当前使用的是何种shell工具。
④：受限每行字数，该处完整路径为board/freescale/common/imx/post-image.sh。

这里是如何产生sdcard.img，用于sd卡启动的原理部分。
针对我现在imx6ull的情况，board/freescale/common/imx目录下有两个文件值得关注：genimage.cfg.template和post-image.sh。
先来看genimage.cfg.template：
{% codeblock lang:shell %}

```
Minimal SD card image for the Freescale boards Template
We mimic the .sdcard Freescale’s image format:
* the SD card must have 1 kB free space at the beginning,
* U-Boot is dumped as is,
* a FAT partition at offset 8 MB is containing zImage/uImage and DTB files
* a single root filesystem partition is required (ext2, ext3 or ext4)
image boot.vfat {
vfat {
files = {
%FILES%
}
}
size = 16M
}
image sdcard.img {
hdimage {
}
partition u-boot {
in-partition-table = “no”
image = “%UBOOTBIN%”
offset = 1024
}
partition boot {
partition-type = 0xC
bootable = “true”
image = “boot.vfat”
offset = 8M
}
partition rootfs {
partition-type = 0x83
image = “rootfs.ext2”
}
}
{% endcodeblock %}
```

该配置文件显示会生成两个文件，一个boot.vfat，一个sdcard.img；
boot.vfat由"%FILES%"所表示内容组成(后面会得知是kernel+dtb);
sdcard.img有四个分区，第一个是空，第二个是偏移1024字节(1k)后，内容为"%UBOOTBIN%"(u-boot)，第三个为偏移8M后，存放前面生成的boot.vfat(kernel+dtb)，最后存放rootfs.ext2。
此时分区情况如下:

分区类型和数值的对应关系可通过该文章查询:List of partition identifiers for PCs or Listing of MBR/EBR Partition Types
此时将sd卡插入Windows电脑，可以发现只能识别存放boot.vfat(kernel+dtb)的分区，因为该分区为FAT32格式，Windows可以识别，而存放rootfs.ext2的分区为ext2/3/4，Windows是无法识别的，与生活常识是吻合的。
另外，如果想在SD卡创建其它自定义分区，可以再加一个partition：

```
partition user {
partition-type = 0xC
size = 10M
}
```

再来看看post-image.sh是如何解析genimage.cfg.template:

```
{% codeblock lang:shell %}
#!/usr/bin/env bashdtb_list extracts the list of DTB files from BR2_LINUX_KERNEL_INTREE_DTS_NAMEin ${BR_CONFIG}, then prints the corresponding list of file names for thegenimage configuration file

dtb_list()
{
local DTB_LIST="KaTeX parse error: Can't use function '\(' in math mode at position 47: …TREE_DTS_NAME="\̲(̲[\/a-z0-9 \-]*\…/\1/p’ ${BR2_CONFIG})"


for dt in $DTB_LIST; do
    echo -n "\"`basename $dt`.dtb\", "
done

linux_image extracts the Linux image format from BR2_LINUX_KERNEL_UIMAGE in${BR_CONFIG}, then prints the corresponding file name for the genimageconfiguration file

linux_image()
{
if grep -Eq “^BR2_LINUX_KERNEL_UIMAGE=y$” KaTeX parse error: Can't use function '\"' in math mode at position 28: …; then echo "\̲"̲uImage\"" elif…" ${BR2_CONFIG}; then
echo ““Image””
else
echo ““zImage””
fi
}

genimage_type()
{
if grep -Eq “^BR2_PACKAGE_FREESCALE_IMX_PLATFORM_IMX8M=y$” B R 2 C O N F I G ; t h e n e c h o " g e n i m a g e . c f g . t e m p l a t e i m x 8 " e l i f g r e p − E q " B R 2 T A R G E T U B O O T S P L = y {BR2_CONFIG}; then echo "genimage.cfg.template_imx8" elif grep -Eq "^BR2_TARGET_UBOOT_SPL=y BR2
C

ONFIG;thenecho"genimage.cfg.template
i

mx8"elifgrep−Eq"
B
R2
T

ARGET
U

BOOT
S

PL=y" ${BR2_CONFIG}; then
echo “genimage.cfg.template_spl”
else
echo “genimage.cfg.template”
fi
}

uboot_image()
{
if grep -Eq “^BR2_TARGET_UBOOT_FORMAT_DTB_IMX=y$” B R 2 C O N F I G ; t h e n e c h o " u − b o o t − d t b . i m x " e l i f g r e p − E q " B R 2 T A R G E T U B O O T F O R M A T I M X = y {BR2_CONFIG}; then echo "u-boot-dtb.imx" elif grep -Eq "^BR2_TARGET_UBOOT_FORMAT_IMX=y BR2
C

ONFIG;thenecho"u−boot−dtb.imx"elifgrep−Eq"
B
R2
T

ARGET
U

BOOT
F

ORMAT
I

MX=y" ${BR2_CONFIG}; then
echo “u-boot.imx”
fi
}

main()
{
local FILES="$(dtb_list) ( l i n u x i m a g e ) " l o c a l U B O O T B I N = " (linux_image)" local UBOOTBIN=" (linux
i

mage)"localUBOOTBIN="(uboot_image)"
local GENIMAGE_CFG=" ( m k t e m p − − s u f f i x g e n i m a g e . c f g ) " l o c a l G E N I M A G E T M P = " (mktemp --suffix genimage.cfg)" local GENIMAGE_TMP=" (mktemp−−suffixgenimage.cfg)"localGENIMAGE
T

MP="{BUILD_DIR}/genimage.tmp"


sed -e "s/%FILES%/${FILES}/" \
    -e "s/%UBOOTBIN%/${UBOOTBIN}/" \
    board/freescale/common/imx/$(genimage_type) > ${GENIMAGE_CFG}

rm -rf "${GENIMAGE_TMP}"

genimage \
    --rootpath "${TARGET_DIR}" \
    --tmppath "${GENIMAGE_TMP}" \
    --inputpath "${BINARIES_DIR}" \
    --outputpath "${BINARIES_DIR}" \
    --config "${GENIMAGE_CFG}"

rm -f ${GENIMAGE_CFG}

exit $?

}

main $@
{% endcodeblock %}
```

可以在main看到，FILES为dtb和kernel，UBOOTBIN为u-boot，再传入配置文件。
最后使用genimage生成，genimage在后面2.9Host utilities(主机工具)部分需要勾选上，它的作用是给定根文件系统树，生成多个文件系统和闪存镜像的工具。

### 2.5 Kernel(内核配置)

```
=== 一级配置菜单：Kernel  ---> ===

[*] Linux Kernel //使能编译内核
*** Linux kernel in thumb mode may be broken with binutils >= 2.29 *** //binutils>=2.29可能会破坏thumb模式下的内核


Kernel version (Custom Git repository) ---> //内核版本 [自定义Git仓库]
(https://git.dev.tencent.com/……) URL of custom repository //自定义仓库网址 ①
(origin/master) Custom repository version //自定义仓库版本
() Custom kernel patches //自定义内核补丁
Kernel configuration (Using an in-tree defconfig file) ---> //内核配置 [使用项目内defconfig文件]
(imx_v7_ebf) Defconfig name //配置文件名字 (不要_defconfig)
() Additional configuration fragment files //其他配置片段文件
() Custom boot logo file path //自定义启动logo文件路径
Kernel binary format (zImage) ---> //内核二进制文件格式 [zImage] ②
Kernel compression format (gzip compression) ---> //内核压缩格式 [gzip压缩]
[*] Build a Device Tree Blob (DTB) //构建设备树二进制文件(DTB文件)
(imx6ull-14x14-evk imx6ull-14x14-ebf) In-tree Device Tree Source file names //项目里设备树源文件名 (不要.dts)
() Out-of-tree Device Tree Source file paths //项目外设备树源文件路径
[ ] Build Device Tree with overlay support //使能设备树文件覆盖支持
[*] Install kernel image to /boot in target //安装内核镜像到/boot目录
[*] Needs host OpenSSL //主机需要OpenSSL
[ ] Needs host libelf //主机需要libelf (用于读取，修改或创建ELF文件)
Linux Kernel Extensions ---> //Linux内核扩展
[ ] Adeos/Xenomai Real-time patch //Adeos/Xenomai实时时钟补丁
[ ] RTAI Real-time patch //RTAI实时时钟补丁
[ ] ev3dev Linux drivers //ev3dev Linux驱动
[ ] FB TFT drivers //FB TFT驱动
[ ] Aufs Filesystem Module patch //Aufs文件系统模块补丁

Linux Kernel Tools ---> //Linux内核工具
[ ] cpupower //用于检查、调整CPU省电相关功能
[ ] gpio //用于控制、监控GPIO,仅在4.8版本提供 ③
[ ] iio //用于控制、监控iio设备,仅在4.7版本提供
[ ] pci //用于测试特定PCI端点, 仅在4.20版本提供
[ ] perf //用于Linux性能分析 ★
[ ] selftests //用于内核自我测试
[ ] tmon //用于用户访问系统相关的热信息
```

①：受限每行字数，该处完整链接为https://git.dev.tencent.com/weidongshan/imx6ull_kernel.git；
②：分别介绍下这几个内核镜像格式，以及一些其它格式:
vmlinuz·:静态编译出来的最原始的ELF文件，包括了内核镜像、调试信息、符号表等内容；其中 “vm” 代表 “Virtual Memory”;
Image:将所有的符号和重定位信息都删除，只剩下二进制数据的内核代码，此时还没经过压缩;
zImage:是vmlinux加上解压代码(用于自解压)经过gzip压缩后的文件，适用于小内核，常见于ARM;
bzImage:是vmlinux加上解压代码(用于自解压)经过gzip压缩后的文件，适用于大内核，常见于x86，“bz”表示 “big zImage”;
uImage:是U-Boot专用的镜像文件，使用mkimage工具在zImage之前加上一个长度为0x40的头信息(tag)，在头信息内说明了该镜像文件的类型、加载位置、生成时间、大小等信息;
参考资料：linux内核镜像格式
③：使用新的ABI，弃用sysfs；



### 2.6 Target packages(目标包配置)
目标包配置内容有点多，二级目录如下：

```
==== 一级配置菜单：Target packages ===

Target packages --->
[*] BusyBox //使能编译BusyBox ★
(package/busybox/busybox.config) BusyBox configuration file to use? //设置BusyBox配置文件路径
() Additional BusyBox configuration fragment files //其他BusyBox配置片段文件
-*- Show packages that are also provided by busybox //列出部分busybox也提供的包
[ ] Enable SELinux support //SELinux支持(安全增强型Security-Enhanced Linux)
[ ] Individual binaries //每个应用程序作为单独二进制文件(为SELinux提供支持)
[ ] Install the watchdog daemon startup script //在启动脚本安装看门狗守护程序
[*] Rockchip BSP packages  --->          ★ 

Audio and video applications ---> //音频和视频应用
Compressors and decompressors ---> //压缩和解压
Debugging, profiling and benchmark ---> //调试、分析和基准测试
Development tools ---> //开发工具
Filesystem and flash utilities ---> //文件系统和闪存实用程序
Fonts, cursors, icons, sounds and themes ---> //字体，游标，图标，声音和主题
Games ---> //游戏
Graphic libraries and applications (graphic/text) ---> //图形库和应用程序(图形/文本)
Hardware handling ---> //硬件处理
Interpreter languages and scripting ---> //编程语言和脚本
Libraries ---> //库
Mail ---> //邮箱
Miscellaneous ---> //杂项
Networking applications ---> //网络应用
Package managers ---> //安装包管理
Real-Time ---> //实时时钟
Security ---> //安全
Shell and utilities ---> //Shell和程序
System tools ---> //系统工具
Text editors and viewers ---> //文版编辑和浏览
Libretro cores and retroarch --->
```
接下来对每个二级目录的子目录注释。

#### 2.6.1 Audio and video applications（音频和视频应用程序）

```
====Target packages====
Audio and video applications --->
[] alsa-utils ---> //ALSA声卡测试和音频编辑
[ ] atest //ALSA Asoc驱动测试工具
[ ] aumix //声卡混音器
[ ] bellagio //基于OpenMAX的多媒体访问
[ ] bluez-alsa //蓝牙音频ALSA后端
[ ] dvblast //MPEG-2/TS解复用和流媒体
[ ] dvdauthor //创作DVD视频光盘文件和目录结构
[ ] dvdrw-tools //创作蓝光光盘和DVD光盘媒体
[ ] espeak //用于英语和其他语言的语音合成器软件
[ ] faad2 //开源的MPEG-4和MPEG-2 AAC解码器
[] ffmpeg ---> //录制、转换以及流化音视频的完整解决方案
[ ] flac //开源无损音频编解码器
[ ] flite //小型、快速的TTS系统(TextToSpeech),即文字转语音
[ ] gmrender-resurrect //基于gstreamer的UPnP(DLNA)媒体渲染器
[ ] gstreamer 0.10 //开源多媒体框架0.10版本
[ ] gstreamer 1.x //开源多媒体框架1.x版本,与前面的0.10不兼容
[ ] jack2 //JACK音频连接套件(包含服务器和示例客户端)
* kodi needs python w/ .py modules, //Kodi开源媒体播放器软件应用程序的需要条件
a uClibc or glibc toolchain w/ C++, threads, wchar, dynamic library, gcc >= 4.8, *
* kodi needs an OpenGL EGL backend with OpenGL support *
[ ] lame //高质量的MPEG Audio Layer III(MP3)编码器
[ ] madplay //libmad的命令行前端,一个高质量的MPEG音频解码器
[ ] mimic //快速，轻量级的文本到语音引擎
* miraclecast needs systemd and //miraclecast无线屏幕投影的需要条件
a glibc toolchain w/ threads and wchar *
[ ] mjpegtools //录制视频和回放,简单的剪切和粘贴编辑以及音频和视频的MPEG压缩
[ ] modplugtools //支持播放MOD、S3M、XM等格式音乐文件
[ ] motion //监控摄像机视频信号的程序,可以检测物体运动
[*] mpd ---> //用于播放音乐的服务器端应用程序
[ ] mpd-mpc //MPD的简约命令行界面
[ ] mpg123 //MPEG音频播放器
[ ] mpv //MPlayer的一个分支,有些新功能
[ ] multicat //高效地操作多播流,特别是MPEG-2传输流
[ ] musepack //音频高品质压缩
[ ] ncmpc //功能齐全的MPD客户端
[ ] opus-tools //Opus编解码器命令行工具
[ ] pulseaudio //声音服务代理,可队声音进行操作再播放
[ ] sox //可录制、播放、格式转换、修改音频文件
[ ] squeezelite //Logitech媒体服务器客户端
[ ] tstools //处理MPEG数据的跨平台命令行工具
[ ] twolame //优化的MPEG Audio Layer 2(MP2)编码器
[ ] udpxy //将UDP流量转发到请求HTTP客户端的中继守护程序
[ ] upmpdcli //MPD音乐播放器前端
[ ] v4l2grab //用于从V4L2设备获取JPEG的程序
[ ] v4l2loopback //创建虚拟视频设备
[ ] vlc //可播放大多数多媒体文件以及DVD、音频CD、VCD和各种流媒体协议
[ ] vorbis-tools //用于Ogg格式文件的独立播放器、编码器和解码器,也可作为流媒体播放器
[ ] wavpack //提供无损，高质量的有损和独特的混合压缩模式
[ ] yavta //一个V4L2测试应用程序
[ ] ympd //MPD网页客户端
```

#### 2.6.2 Compressors and decompressors（解压缩）

```
====Target packages====

Compressors and decompressors --->
[ ] brotli //通用无损压缩库
-- bzip2 //免费的压缩工具
[ ] gzip //标准GNU压缩器
[ ] lz4 //快速的无损压缩
[ ] lzip //类似gzip或bzip2
[ ] lzop //与gzip相似
[ ] p7zip //Unix的7-zip命令行版本
[ ] pigz //是gzip的全功能替代品
[ ] pixz //是xz的并行索引版本
[ ] unrar //rar文件解压
[ ] unzip //zip文件解压
[] xz-utils //用于处理XZ压缩的命令行工具,包括xz、unxz、xzcat、xzgrep等
[ ] zip //压缩和解压zip文件
[ ] zstd //Zstandard或zstd的简短版本
```

#### 2.6.3 Debugging, profiling and benchmark

```
====Target packages====
[ ] blktrace //对通用块层(block layer)的I/O跟踪机制，它能抓取详细的I/O请求，发送到用户空间
[ ] bonnie++ //执行一系列简单的硬盘驱动器和文件系统性能测试
[ ] cache-calibrator //用于分析计算机(缓存)内存系统并提取有用信息，以及作为负载生成器进行实时测试
* clinfo needs an OpenCL provider * //查询OpenCL参数和是否安装正常
[ ] dhrystone //测量处理器运算能力的最常见基准程序之一，常用于处理器的整型运算性能的测量
[ ] dieharder //随机数/均匀偏差发生器测试仪，适用于测试软件RNG和硬件RNG
[ ] dmalloc //一个调试分配内存库，替代系统的malloc、realloc、calloc、free等
[ ] dropwatch //交互式监视和记录内核丢弃的数据包
[ ] dstat //取代vmstat、iostat、netstat、ifstat等，监控系统运行状况、基准测试、排除故障
[ ] dt //用于验证外围设备、文件系统、驱动程序或操作系统支持的任何数据流的正常运行
[ ] duma //检测意外的内存访问(Detect Unintended Memory Access)
[ ] fio //一种I/O工具，用于基准测试和压力/硬件验证
[ ] gdb //强大的UNIX下的程序调试工具
[ ] google-breakpad //用于崩溃日志收集
[ ] iozone //一个文件系统基准测试工具，测试不同的操作系统中文件系统的读写性能
[ ] kexec //用一个运行的内核去运行一个新内核，就像运行一个应用程序一样
[ ] ktap //基于脚本的Linux动态跟踪工具，允许用户跟踪Linux内核动态
[ ] kvm-unit-tests //为KVM提供单元测试
[ ] latencytop //专注于解决音频跳跃、桌面卡顿、服务器过载等延迟
[ ] lmbench //一种性能检测工具，提供内存、网络、内核等多方面的测试
[ ] lsof //列出当前系统打开文件的工具(LiSt Open Files)
[ ] ltp-testsuite //测试Linux内核和相关特性的工具集合
[ ] ltrace //能够跟踪进程的库函数调用,显示哪个库函数被调用
[ ] lttng-babeltrace //LTTng(Linux Trace Toolkit Next Generation)跟踪读写库、转换
[ ] lttng-modules //用于LTTng 2.x内核跟踪基础结构的Linux内核模块
[ ] lttng-tools //用于LTTng 2.x跟踪基础结构的用户空间实用程序
[ ] memstat //列出正在消耗虚拟内存的所有进程、可执行程序、共享库
[ ] netperf //网络性能基准工具
[ ] netsniff-ng //高性能的Linux网络分析器和网络工具包
[ ] nmon //监控系统的 CPU、内存、网络、硬盘、文件系统、NFS、高耗进程、资源等信息
[ ] oprofile //性能监测工具，从代码层面分析程序的性能消耗情况，找出程序性能的问题点
[ ] pax-utils //用于ELF 32/64二进制文件的ELF相关工具，可以检查文件的安全相关属性
[ ] pv //基于终端的工具，用于监控通过管道的数据进度
[ ] ramspeed/smp //用于测量多处理器计算机的缓存和内存性能
[ ] ramspeed //用于测量缓存和内存性能
[ ] rt-tests //用于测试Linux系统实时行为的程序集
[ ] spidev_test //基于spidev驱动程序的SPI测试程序
[ ] strace //用于诊断、调试和教学的Linux用户空间跟踪器
[ ] stress //用于POSIX系统的工作负载生成器
[ ] stress-ng //以各种可选择的方式对计算机系统进行压力测试
[ ] sysdig //从正在运行的Linux实例捕获系统状态和活动、然后保存、过滤和分析
[ ] tcf-agent //一个守护进程，它提供可供本地和远程客户端使用的TCF服务
[ ] tinymembench //内存基准测试程序
[ ] trace-cmd //帮助开发人员了解Linux内核的运行时行为，以便进行故障调试或性能分析
[ ] trinity //Linux系统调用模糊测试
[ ] uclibc-ng-test //编译并安装uClibc-ng测试套件
[ ] valgrind //用于调试和分析Linux程序的工具
[ ] vmtouch //用于学习和控制unix和unix类系统的文件系统缓存的工具
[ ] whetstone //测试双精度浮点数操作的速度和效率
```

#### 2.6.4 Development tool（开发工具）

```
====Target packages====
[ ] binutils //安装binutils(BinaryUtilities)二进制工具的集合，比如ld、as
[ ] bsdiff //创建补丁path或文件比较diff
[ ] check //单元测试框架
[ ] ctest //CTest是CMake集成的一个测试工具，可以自动配置、构建、测试、展现测试结果
[ ] cppunit //著名的JUnit框架的C++端口，用于单元测试
[ ] cunit //自动化测试框架
[ ] cvs //代码版本控制软件
[ ] cxxtest //C++的单元测试框架
[ ] diffutils //GNU diff，文件每行比较
[ ] dos2unix //将DOS格式(CRLF)的文本文件转换成UNIX格式(LF)
[ ] findutils //基本目录搜索工具，比如find、xargs、locate、updatedb等
[ ] flex //快速词法分析器生成器，用于生成在文本上执行模式匹配的程序的工具
[ ] gawk //一种特殊用途的编程语言，它是数据驱动的而不是程序的
[ ] gettext //提供一个框架来帮助其他GNU包生成多语言消息
[ ] git //代码版本控制软件
[ ] git-crypt //用于在git存储库中对文件进行透明加密和解密
[ ] gperf //一个“完美的哈希函数”生成器
[ ] grep //GNU正则表达式匹配器
[ ] jo //从shell输出JSON的命令行处理器
[ ] jq //类似用于JSON数据的sed，用来切片和过滤，映射和转换结构化数据
[ ] libtool //一个通用的库支持脚本，藏了在一致的可移植界面背后使用共享库的复杂性
[ ] make //用于控制程序源文件中程序的可执行文件和其他非源文件的生成
[ ] patch //获取补丁文件(包含差异列表)并将其应用于原始文件，生成修补版本
[ ] pkgconf //有助于为开发框架配置编译器和链接器标志的程序
[ ] sed //非交互式命令行文本编辑器
[ ] subversion //代码版本控制软件
[ ] tree //递归显示目录列表的命令
```

#### 2.6.5 Filesystem and flash utilities（文件系统和闪存实用程序）

```
====Target packages====
[ ] abootimg //直接通过文件映像或/dev块设备操作Android Boot Images的工具
[ ] aufs-util //aufs文件系统工具
[ ] autofs //自动挂载/卸载文件系统的守护进程
[ ] btrfs-progs //Btrfs文件系统工具
[ ] cifs-utils //CIFS文件系统工具
[ ] cpio //用于创建和提取的cpio存档的工具
[ ] cramfs //用于生成和检查cramfs文件系统的工具
[ ] curlftpfs (FUSE) //基于FUSE和libcurl访问FTP主机的文件系统
[ ] davfs2 //一个Linux文件系统驱动程序，允许挂载WebDAV资源，远程协作创作Web资源
[ ] dosfstools //用于创建和检查DOS FAT文件系统的工具
[*] e2fsprogs ---> //Ext2(及Ext3/4)文件系统工具集，包含了创建、修复、配置、调试等工具
[ ] debugfs //ext2/ext3/ext4文件系统调试工具(不仅限于下列几个工具)
[ ] e2image //保存关键的ext2/ext3ext4文件系统的元数据到文件中
[ ] e4defrag //用于ext4文件系统的在线碎片整理程序
[*] fsck //检查并修复特定Linux文件系统
[ ] fuse2fs //用于ext2/ext3/ext4文件系统的FUSE文件系统客户端
[ ] resize2fs //用于ext2/ext3/ext4文件系统容量调整
[ ] e2tools //用于读取、写入、操作ext2/ext3中的文件，使用ext2fs库访问文件系统
[ ] ecryptfs-utils //适用于Linux的POSIX兼容企业加密文件系统
[ ] exFAT (FUSE) //作为FUSE模块，GNU/Linux和其他类Unix系统的全功能exFAT文件系统
[ ] exfat-utils //exFAT文件系统工具
[ ] f2fs-tools //用于Flash-Friendly File System(F2FS)的工具
[ ] flashbench //用于识别SD卡和存储媒介属性的工具
[ ] fscryptctl //处理原始密钥并管理Linux文件系统加密策略的工具
[ ] fwup //可编写脚本的嵌入式Linux固件更新创建、运行工具
[ ] genext2fs //作为普通(非root)用户生成ext2文件系统
[ ] genpart //生成由命令行参数定义的16字节分区表条目，并将其转储到stdout
[ ] genromfs //生成ROMFS文件系统的工具
[ ] imx-usb-loader //通过USB在Freescale i.MX5/i.MX6和Vybrid SoC上下载和运行程序的工具
[ ] mmc-utils //MMC工具
[ ] mtd, jffs2 and ubi/ubifs tools //构建mtd、jffs2和ubi/ubifs工具
[ ] mtools //用于从Unix访问MS-DOS磁盘而不安装它们
[ ] nfs-utils //NFS服务工具
[ ] nilfs-utils //用于创建和管理NILFS2文件系统的工具
[ ] ntfs-3g //开源免费的读/写NTFS驱动程序，可以处理Windows的NTFS文件系统
[ ] sp-oops-extract //一个从MTD中提取OOPS/panic异常日志的工具
[ ] squashfs //生成SquashFS文件系统的工具
[ ] sshfs (FUSE) //基于SSH文件传输协议的FUSE文件系统客户端
[ ] sunxi-tools //用于全志Allwinner A10(又名sun4i)和A13(又名sun5i)设备的工具
[ ] udftools //用于创建UDF文件系统的工具
[ ] unionfs (FUSE) //一个用户空间unionfs的实现
[ ] xfsprogs //XFS文件系统工具和库
```

#### 2.6.6 Fonts, cursors, icons, sounds and themes（字体、光标、图标、声音和主题）

```
====Target packages====
*** Cursors *** //光标
[ ] comix-cursors //X11鼠标主题里一个略卡通的鼠标光标
[ ] obsidian-cursors //一个明亮、干净的鼠标光标集合
*** Fonts *** //字体
[ ] Bitstream Vera //Bitstream Vera字体系列
[ ] cantarell //一款当代人文主义无衬线字体，专为屏幕阅读而设计
[ ] DejaVu fonts //基于Vera字体的字体系列，提供更广泛的用途
[ ] font-awesome //是一套605个象形图标，可在网站上轻松扩展矢量图形
[ ] ghostscript-fonts //随Ghostscript(PDF软件)一起分发的字体，目前包括35种的gostScript字体
[ ] inconsolata //一种等宽字体，专为代码、清单等而设计
[ ] Liberation (Free fonts) //旨在替代Microsoft三种最常用字体：Times New Roman、Arial和Courier New
*** Icons *** //图标
[ ] google-material-design-icons //Google根据材料设计语言(Material Design)设计的官方图标集
[ ] hicolor icon theme //备用图标主题，用于显示图标主题中不可用的图标
*** Sounds *** //声音
[ ] sound-theme-borealis //北极星(borealis)的声音主题
[ ] sound-theme-freedesktop //默认桌面(freedesktop)的声音主题
*** Themes *** //主题
```

#### 2.6.7 Games

```
====Target packages====
[ ] chocolate-doom //一个复古游戏
[ ] flare-engine //Flare(Free Libre Action Roleplaying Engine)是一款简单的游戏引擎
[ ] gnuchess //一个西洋棋游戏
[ ] LBreakout2 //一款以Arkanoid形式出现的突破式街机游戏
[ ] LTris //LTris是使用SDL的俄罗斯方块克隆
*** minetest needs X11 and an OpenGL provider *** //一个开源体像素游戏，需要X11和OpenGL支持
[ ] OpenTyrian //是DOS射击游戏Tyrian的一个端口
[ ] prboom //一个Doom客户端，用于支持在较新的硬件上玩旧游戏
[ ] sl /终端/输入"sl"时，终端出现蒸汽火车穿过，一个玩笑命令
*** solarus needs OpenGL and a toolchain w/ C++, //larus需要OpenGL和一个带C++的工具链，gcc> = 4.8，NPTL，动态库
gcc >= 4.8, NPTL, dynamic library *** //一款动作角色扮演游戏(ARPG)引擎
[ ] stella //一款多平台Atari 2600 VCS仿真器
```

#### 2.6.8 Graphic libraries and applications (graphic/text)（图形库和应用程序（图形/文本））

```
====Target packages====
* Graphic applications * //图形应用
[ ] fswebcam //一个从V4L2获取图像的简洁的网络摄像头应用程序
[ ] ghostscript //文件通过它到打印机打印出来
[ ] glmark2 //glmark2，一个GPU压力测试软件
[ ] gnuplot //使用命令列界面，绘制数学函数图形、统计图表等等
[ ] jhead //用于操作一些数码相机使用的Exif jpeg标题中的设置和缩略图的程序
[ ] libva-utils //是VA-API(视频加速API)测试的集合
[ ] netsurf //一个紧凑的图形Web浏览器，旨在支持HTML5，CSS和JavaScript
[ ] pngquant //有损PNG压缩器，包含pngquant命令和libimagequant库
[ ] rrdtool //用于时间序列数据的高性能数据记录和图形系统
[ ] tesseract-ocr ---- //一个支持多种语言的OCR(光学字符识别)引擎，它可以直接使用或提供API
* Graphic libraries * //图形库
[ ] cegui06 //Crazy Eddie的GUI系统是一个免费的库，为图形API/引擎提供窗口和小部件
[ ] directfb //DirectFB是在Linux帧缓冲区(fbdev)抽象层之上实现的一组图形API
[ ] efl //Enlightenment Foundation Libraries，一个开源UI工具包
[ ] fbdump (Framebuffer Capture Tool) //一个从Linux内核帧缓冲设备捕获快照并将其作为PPM文件写出的简单工具
[ ] fbgrab //一个帧缓冲截图程序，捕获Linux frambuffer并将其转换为png图片
[ ] fbset //用于显示或更改帧缓冲设备的设置
[ ] fb-test-app //Linux framebuffer的测试套件
[ ] fbterm //用于Linux的快速终端仿真器，带有帧缓冲设备或VESA视频卡
[ ] fbv //一个帧缓冲控制台图形文件查看器，能够显示GIF，JPEG，PNG和BMP文件
[ ] freerdp //是远程桌面协议(RDP)的免费实现
[ ] imagemagick //一个用于创建，编辑和组合位图图像的软件套件
[ ] linux-fusion communication layer for DirectFB multi //DirectFB通信层允许多个DirectFB应用程序同时运行
[ ] mesa3d ---- //OpenGL规范的开源实现
[ ] ocrad //一个基于特征提取方法的OCR(光学字符识别)程序
[ ] psplash //用于实现开机动画、开机进度条
[ ] SDL //一个库，允许程序对视频帧缓冲、音频输出、鼠标和键盘进行低级访问
[ ] sdl2 //DirectMedia的第2层，与SDL不兼容
* Other GUIs * //其它GUI
[] Qt5 ---> //QT5框架
Qt5 version (Latest (5.11)) ---> //选择QT5版本
[ ] qt53d module //QT53d模块
-- qt5base //qt5base模块，包含基本的Qt库：QtCore、QtNetwork、QtGui、QtWidgets等
() Custom configuration options //自定义QT5编译选项
() Config file //指定类似src/corelib/global/qconfig-.h文件来启用/禁用的功能
[ ] Compile and install examples (with code) //编译并安装示例(含代码)
[ ] concurrent module //启用Qt5Concurrent库
[ ] MySQL Plugin //构建MySQL插件
[ ] PostgreSQL Plugin //构建PostgreSQL插件
SQLite 3 support (No sqlite support) ---> //启用SQLite3支持(不支持sqlite)
[ ] gui module //启用Qt5Gui库
[ ] DBus module //启用D-Bus模块
[ ] Enable ICU support //启用Qt5中的ICU支持，例如Qt5Webkit需要此功能
[ ] Enable Tslib support //启用Tslib插件
[ ] qt5canvas3d //Qt Canvas 3D模块提供一种从Qt Quick JavaScript进行类似于WebGL的3D绘图调用的方法
[ ] qt5charts //Qt图表模块提供了一组易于使用的图表组件
[ ] qt5connectivity //Qt Connectivity模块提供对Bluetooth/NFC外围设备的支持
[ ] qt5declarative //Qt Declarative模块提供了Qt QML和Qt Quick模块，用于使用QML语言开发UI
[ ] qt5enginio //Enginio是一种后端即服务解决方案，用于简化连接的和数据驱动的应用程序的后端开发
[ ] qt5graphicaleffects //Qt Graphical Effects模块提供了一组QML类型，用于向用户界面添加视觉特效
[ ] qt5imageformats //Qt Image Formats模块提供了用于其他图像格式的插件:TIFF、MNG、TGA、WBMP
[ ] qt5location //Qt Location API使用一些流行的定位服务提供的数据来创建可行的地图解决方案
[ ] qt5multimedia //Qt Multimedia模块，实现媒体播放以及使用摄像头和无线电设备
[ ] qt5quickcontrols //Qt Quick Controls模块提供了一组控件，可用于在Qt Quick中构建完整的界面
[ ] qt5quickcontrols2 //对应Qt Quick Controls 2模块
[ ] qt5script //Qt脚本支持使Qt应用程序可编写脚本，逐渐弃用，在新设计中由Qt QML模块替换
[ ] qt5scxml //Qt SCXML模块提供了从SCXML文件创建状态机的功能
[ ] qt5sensors //Qt Sensors API通过QML和C++接口提供对传感器硬件的访问
[ ] qt5serialbus //对应qt5serialbus模块
[ ] qt5serialport //Qt串行端口提供配置串行端口，I/O操作，获取和设置RS-232引脚排列的控制信号
[ ] qt5svg //Qt SVG提供了用于在小部件和其他绘画设备上渲染和显示SVG图纸的类
[ ] qt5tools //Qt Tools提供的工具可促进应用程序的开发和设计
[ ] qt5virtualkeyboard //Qt虚拟键盘是一个虚拟键盘框架，由C++后端和QML实现的UI前端组成
[ ] qt5wayland //对应qt5wayland模块
[ ] qt5webchannel //支持在服务器(QML/C++应用程序)和客户端(HTML/JavaScript或QML应用程序)之间进行对等通信
[ ] qt5webkit //提供WebView API，用于QML应用程序呈现动态Web内容，后继QtWebEngine需要OpenGL支持
[ ] qt5webengine //提供用于渲染HTML，XHTML和SVG文档的C++类和QML类型
[ ] qt5websockets //提供C++和QML接口，使Qt应用程序可以充当可处理WebSocket请求的服务器，也可充当的客户端
[ ] qt5xmlpatterns //Qt XML Patterns模块提供对XPath，XQuery，XSLT和XML Schema验证的支持
[ ] KF5 ---- //KF5是一组Qt框架插件，扩展了Qt
* QT libraries and helper libraries * //**QT库和帮助程序库***
[ ] cutelyst //一个基于Qt的C++ Web框架，它使用Catalyst(Perl)框架的简单实现
[ ] grantlee //Django模板框架的Qt实现
[ ] qextserialport //一个Qt库来管理串行端口
[ ] qjson //QJson是基于Qt的库，可将JSON数据映射到QVariant对象，反之亦然
[ ] quazip //QuaZIP是Gilles Vollant的ZIP/UNZIP软件包的简单C++包装，可用于访问ZIP档案，它使用Qt工具箱
[ ] qwt //Qwt是Qt GUI应用程序框架的图形扩展，它提供了2D绘图小部件等
[ ] tekui //一个轻量、独立、可移植的GUI工具包, 用lua和C开发
[ ] weston //是Wayland服务器的参考实现
[ ] X.org X Window System ---- //支持X11R7的库、服务器、驱动程序和应用程序
[ ] midori //一个轻量级浏览器
[ ] vte //Virtual Terminal Emulator，一个虚拟终端模拟器小部件
[ ] xkeyboard-config //X的键盘配置数据库
```

#### 2.6.9 Hardware handling

```
====Target packages====
Firmware ---> //固件
[ ] am33x-cm3 //Cortex-M3二进制文件用于在am335x上挂起和恢复
[ ] armbian-firmware //特定用于Armbian的固件
[ ] b43-firmware //b43内核驱动程序支持的Broadcom Wifi设备的固件
[ ] linux-firmware //为LAN，WLAN卡等设备提供了各种二进制固件文件
[ ] rpi-bt-firmware //Raspberry Pi 3和Zero W Broadcom BCM43438蓝牙模块固件
[ ] rpi-firmware //Raspberry Pi引导程序和GPU固件的预编译二进制文件
[ ] rpi-wifi-firmware //Raspberry Pi 3和Zero W Broadcom BCM43430 wifi模块NVRAM数据
[ ] sunxi script.bin board file //专用于linux-sunxi内核的一个已编译的.fex文件来进行硬件描述
[ ] ts4900-fpga //TS-4900的FPGA实现了clocks、UART MUX、GPIO
[ ] ux500-firmware //为Azurewave AW-NH580组合模块(wifi、bt、gps)提供了各种二进制文件
[ ] wilc1000-firmware //Atmel Wilc1000无线设备的固件
[ ] wilink-bt-firmware //TI的Wilink7和Wilink8(wl12xx/wl18xx)UART连接的蓝牙固件
[ ] zd1211-firmware //ZyDAS ZD1211/Atheros AR5007UG wifi设备的固件
[ ] a10disp //用于改变运行linux-sunxi内核的Allwinner ARM SOCs显示模式的程序
[ ] acpica //提供独立于操作系统外的高级配置和电源接口规范(ACPI)的参考实现
[ ] acpitool //一个小型、方便的命令行ACPI客户端，具有许多适用于Linux的功能
[ ] aer-inject //允许注入软件层面PCIE AER错误到正在运行的Linux内核
[ ] am335x-pru-package //TI AM335X PRU程序加载器
[ ] avrdude //avrdude是一个多平台的avr系列MCU的下载器
[ ] bcache tools //Bcache是Linux内核块层缓存，将快速的SSD充当慢速的HDD缓存
[ ] brltty //一个守护程序，为盲人提供对Linux控制台的访问
[ ] cbootimage //编译BCT(启动配置表)映像，将其放入Tegra的设备的启动闪存中
[ ] cc-tool //为Linux OS的Texas Instruments CC调试器提供支持
[ ] cdrkit //用于读取CD和DVD，清空CD-RW介质，创建ISO-9660文件系统映像等
[ ] cryptsetup //此工具有助于操纵dm-crypt和luks分区以进行磁盘加密
[ ] cwiid //用C语言编写的用于与Nintendo Wiimote接口的Linux工具
[ ] dhadi-linux //用于将Asterisk与电话硬件接口的开源设备驱动程序框架
[ ] dahdi-tools //用于管理和监视DAHDI设备的程序包
-- dbus //D-Bus消息总线系统
[ ] dbus-c++ //为D-Bus提供C ++ API
[ ] dbus-glib //D-Bus的GLib绑定
[ ] dbus-triggerd //在收到给定的dbus信号后触发shell命令的工具
[] devmem2 //读/写内存的任何位置数据
[ ] dfu-util //用于将固件下载和上传到通过USB连接的设备
[ ] dmraid //利用Linux内核的Device Mapper机制的磁盘阵列(RAID)
[ ] dt-utils //设备树转储和barebox操作的工具
[ ] dtv-scan-tables //数字电视扫描表
[ ] dump1090 //Dump1090是用于RTLSDR设备的简单模式S解码器
[ ] dvb-apps //安装少量的DVB测试和实用程序，包括szap和dvbscan
[ ] dvbsnoop //分析、查看、调试传输流(TS)、程序基本流(PES)、程序流(PS)
[ ] edid-decode //以人类可读的格式解码EDID数据
-- eudev //eudev是systemd-udev的一个分支
[] enable rules generator //启用持久性规则生成
[] enable hwdb installation //启用将硬件数据库安装到/etc/udev/hwdb.d
[ ] evemu //evemu记录并重放设备描述和事件
[ ] evtest //input输入子系统测试工具
[ ] fan-ctrl //一个守护程序，控制CPU风扇的速度
[ ] fconfig //从Linux获取/设置RedBoot配置参数
[ ] fis //从Linux操纵RedBoot分区表
[ ] flashrom //用于识别/读取/写入/验证和擦除闪存芯片的程序
[ ] fmtools //用于功率控制、调谐、音量控制、电台扫描的的fm
[ ] Freescale i.MX libraries ---- //为Freescale i.MX平台提供GPU或VPU提供硬件加速和一些硬件工具
[ ] fxload //一个USB自动下载工具
[ ] gadgetfs-test //usb gadgetfs测试程序
[ ] gpm //为虚拟控制台提供鼠标进行复制和粘贴操作
[ ] gpsd ---- //监视串口或USB，获取GPS或AIS模块数据，并可通过TCP端口查询
[ ] gptfdisk //GPT fdisk(由gdisk和sgdisk程序组成)是一种文本模式分区工具
[ ] gvfs //一个用户空间虚拟文件系统，可通过SFTP、SMB等访问远程数据
[ ] hdparm //获取/设置Linux IDE驱动器的硬盘参数
[ ] hwdata //获取各种硬件标识和配置数据
[ ] hwloc //获取系统中层次化的关键计算元素，比如：处理器内核，线程
[ ] i2c-tools //用于Linux的各种I2C工具集，比如总线探测、寄存器访问
[ ] input-event-daemon //对输入事件(例如按键，鼠标按钮和开关)执行用户定义的命令
[ ] iostat //I/O性能监视实用程序
[ ] ipmitool //为启用IPMI的设备提供了简单的命令行界面
[ ] irda-utils //用于控制IrDA栈用户空间程序
[ ] kbd //键表文件和键盘实用程序
[ ] lcdproc //LCD显示驱动程序守护程序和客户端
[ ] libuio //用于处理UIO(用户空间I/O)设备发现和绑定任务
[ ] libump //ARM通用内存提供程序用户空间库，Mali驱动程序所必需的
[ ] linuxconsoletools //将老式串行设备连接到Linux内核输入层的inputattach程序
[ ] linux-backports //许多来自最新内核的Linux驱动程序，反向移植到较旧的内核
[ ] lirc-tools //接收和发送最常见的IR遥控器的IR信号
[ ] lm-sensors //Linux的硬件运行状况监视软件包(温度、电压、风扇速度等)
[ ] lshw //Hardware Lister，用于提供有关机器硬件配置的详细信息的工具
[ ] lsscsi //列出SCSI设备(或主机)及其属性
[ ] lsuio //列出可用的用户空间I/O(UIO)设备
[ ] luksmeta //用于将元数据存储在LUKSv1标头中
[ ] lvm2 & device mapper //LVM2(Logical Volume Manager2)是Linux逻辑卷管理器的重写
[] mali-t76x //为ARM Mali Midgard T76X GPU安装二进制用户空间组件
[ ] mdadm //用于管理Linux软件RAID阵列的程序
[ ] memtester //用于测试内存子系统是否有故障
[ ] memtool //用于修改存储器映射的寄存器(/dev/mem)或字符设备(/dev/fb0)
[ ] minicom //一个有菜单界面的串口通信工具
[ ] nanocom //基于microcom的Linux和POSIX系统的轻量级串行终端
[ ] neard //Near Field Communication，NFC管理
[ ] nvme //与标准NVM Express(优化的PCI Express SSD接口)设备的交互程序
[ ] odroid-mali //为基于odroidc2的系统安装ARM Mali驱动程序
[ ] odroid-scripts //为基于odroidc2的系统安装脚本
[ ] ofono //用于移动电话(GSM/UMTS)开源程序，使用D-Bus API，3GPP标准
[ ] open2300 //从Lacrosse WS2300/WS2305/WS2310/WS2315气象站读写数据
[ ] openipmi //允许对设备进行远程监视和远程管理
[ ] openocd //Open On-Chip Debugger，开源片上调试器
* owl-linux is only supported //用于H＆D无线SPB104 SD-card WiFi SIP的Linux内核驱动程序
     on ARM9 architecture *
[ ] parted //磁盘分区和分区大小调整工具
[ ] pciutils //处理PCI总线的各种程序，提供诸如setpci和lspci之类的东西
[ ] pdbg //PowerPC FSI调试器，通过FSI对IBM Power8/9 CPU进行低级调试
[ ] picocom //一个极简的串口调试工具
[ ] pifmrds //使用Raspberry Pi的PWM的FM-RDS发送器
[ ] pigpio //用于控制Raspberry Pi通用输入输出(GPIO)的库
[ ] powertop //诊断功耗和电源管理问题的工具
[ ] pps-tools //每秒脉冲(Pulse per second )工具，提供timepps.h和其它PPS程序
[ ] pru-software-support //从TI的PRU软件支持包中提取的PRU固件示例
[ ] read-edid //一对用于从监视器读取EDID的工具
[ ] rng-tools //使用硬件随机数生成器(random number generators)的守护程序
[ ] rpi-userland //包含Raspberry Pi使用VideoCore驱动程序所需的库
[ ] rs485conf //使用的命令行选项显示和修改TTY设备的RS485配置参数
[ ] rtc-tools //用于操纵实时时钟设备
[ ] rtl8188eu //RTL8188EU USB Wi-Fi适配器的独立驱动程序
[ ] rtl8723bs //无线网卡rtl8723bs驱动程序
[ ] rtl8723bu //无线网卡rtl8723bu驱动程序
[ ] rtl8821au //无线网卡rtl8821au驱动程序
[ ] rtl8189fs //无线网卡rtl8189fs驱动程序
[ ] sane-backends //Scanner Access Now Easy，轻松访问扫描仪
[ ] sdparm //访问SCSI设备参数的程序
[ ] setserial //串口配置
[ ] sg3-utils //使用SCSI命令集的设备的程序
[ ] sigrok-cli //Sigrok-cli是sigrok软件套件的命令行前端
[ ] sispmctl //在Linux下使用GEMBIRD SiS-PM和mSiS(sispm)USB控制的电源插座设备
[ ] smartmontools //使用S.M.A.R.T.控制和监视存储系统
[ ] smstools3 //一个SMS网关软件，可以通过GSM调制解调器和手机发送和接收短消息
[ ] spi-tools //包含一些简单的命令行工具，以帮助使用Linux spidev设备
[ ] sredird //一个串行端口重定向器，可以通过网络共享一个串行端口
[ ] statserial //显示标准9针或25针串行端口上的信号表，并指示握手线的状态
[ ] stm32flash //通过UART或I2C为STM32 ARM微控制器提供的开源flash程序
[ ] sunxi-cedarx //CedarX是Allwinner的多媒体协处理技术，用于硬件加速视频和图像解码
[ ] sunxi-mali //为基于sunxi的系统(ARM Allwinner SoC的系统)安装ARM Mali驱动程序
[ ] sysstat //Linux的性能监视工具集合(sar、sadf、mpstat、iostat、pidstat、sa)
[ ] targetcli-fb //一个命令行界面，用于配置3.x Linux内核版本中的LIO通用SCSI目标
[ ] ti-gfx //使用SGX加速的TI开发板的图形库，支持AM335x、AM43xx等
[ ] ti-sgx-km //带有SGX GPU的TI CPU的内核模块
* ti-sgx-um needs the ti-sgx-km driver * //带有SGX5xx GPU的TI CPU的图形库,支持AM335x，AM437x等
[ ] ti-uim //TI wl12xx连接芯片的用户模式初始化管理器共享传输驱动程序
[ ] ti-utils //基于wl12xx驱动程序的TI无线解决方案的校准器和其它程序
[ ] triggerhappy //一个热键守护程序
[ ] u-boot tools //U-Boot引导程序的配套工具
[ ] ubus //IPC/RPC总线，允许进程之间进行通信，由ubusd、libubus、ubus组成
[ ] uccp420wlan //基于SoftMAC的Imagination Explorer RPU uccp420的WiFi驱动程序
[ ] udisks //提供了一个守护程序、D-Bus API及命令行工具，以管理磁盘/存储设备
[ ] uhubctl //在USB集线器上控制每个端口的USB电源
[ ] upower //用于枚举功率设备，侦听设备事件以及查询历史记录和统计信息
[ ] usb_modeswitch //一种模式切换工具，用于控制具有“多种模式”的USB设备
[ ] usb_modeswitch_data //包含udev规则和事件，以允许usb_modeswitch自动运行
[ ] usbmount //在插入USB大容量存储设备后会自动挂载，拔掉后自动卸载
[ ] usbutils //USB枚举程序
[ ] w_scan //用于对DVB和ATSC传输执行频率扫描
[ ] wf111 //Silicon Labs WF111 WiFi驱动程序和实用程序
[ ] wipe //用于从磁介质安全擦除文件的命令
[ ] xorriso //可创建、加载、处理和写入具有Rock Ridge扩展名的ISO 9660系统映像
[ ] xr819-xradio //SDIO WiFi芯片XR819的无线驱动程序
```
#### 2.6.10 Interpreter languages and scripting（解释器语言和脚本）

```
====Target packages====
[ ] 4th //Forth编译器，可将Forth语言转成其他语言的字节码和独立可运行程序
[ ] enscript //将ASCII文件转换为PostScript，HTML或RTF，生成文件或打印
[ ] erlang //Erlang是一种编程语言，主要用于开发并发和分布式系统
[ ] execline //execline是一种(非交互式)脚本语言，类似sh，但语法有很大不同
[ ] ficl //Ficl是一种编程语言解释器，将命令/宏/开发原型语言嵌入到其他系统中
[ ] gauche //Gauche是R7RS计划的实现，作为方便的脚本解释器被开发
[ ] guile //Guile是Scheme编程语言的解释器和编译器，该语言类似Lisp
[ ] haserl //Haserl是一个小的cgi包装器，使程序脚本可以嵌入到html文档中
[ ] jamvm //JamVM是新的Java虚拟机，符合JVM规范版本2
[ ] jimtcl //Jim Tcl是Tcl脚本语言的一种小型实现
[ ] lua //Lua是一种功能强大，快速，轻巧，可嵌入的脚本语言
[ ] luajit //LuaJIT实现了Lua 5.1定义的全部语言功能
[ ] micropython //Micro Python是Python 3编程语言的精简和快速实现
[ ] moarvm //专门为Rakudo Perl 6和NQP编译器工具链构建的虚拟机
[ ] mono //C＃和与Microsoft.NET二进制兼容的CLR的开源、跨平台实现
[ ] nodejs //基于V8的事件驱动的I/O服务器端JavaScript环境
[ ] perl //Practical Extraction and Report Language，实用报表提取语言
[] php //PHP是一种广泛使用的通用脚本语言，特别适合于Web开发
[] CGI interface //Common Gateway Interface，通用网关接口
[] CLI interface //Command Line Interface，命令行接口
[] FPM interface //FastCGI Process Manager，FastCGI流程管理器
Extensions ---> //php扩展
[ ] Calendar //日历和活动支持
[ ] Fileinfo //文件信息支持
[ ] OPcache //启用Zend OPcache加速器
[] Readline //行读取支持
[] Session //会话支持
* Compression extensions * //压缩扩展
[] bzip2 //bzip2格式读/写支持
[ ] phar //PHP存档支持
[ ] zip //zip格式读/写支持
[] zlib //zlib支持
* Cryptography extensions * //密码扩展*
[ ] hash //Hash加密算法
[ ] mcrypt //mcrypt加密扩展库支持
[] openssl //Open Secure Sockets Layer开源安全套接层协议
* Database extensions * //数据库扩展
[ ] DBA //Database Abstraction数据库抽象层
[ ] Mysqli //MySQL改进的扩展支持
[ ] SQLite3 //SQLite3支持
[ ] PDO //PHP数据对象支持
* Human language and character //人类语言和字符编码支持*
encoding support *
[ ] Gettext //文本获取支持
[ ] iconv //iconv字符集转换支持
[ ] intl //国际化支持
[ ] mbstring //多字节字符串支持
* Image processing * //图像处理**
[ ] EXIF //记录数码照片的属性信息和拍摄数据
[ ] GD //GD库，用于图像处理，制作验证码、缩略图、水印图等
* Mathematical extensions * //数学扩展
[ ] BC math //BCMath任意精度数学支持
[ ] GMP //GNU多精度支持
* Other basic extensions * //其它基本扩展
[] JSON //JavaScript对象序列化支持
[ ] Tokenizer //令牌生成器功能支持
* Other services * //其它服务
[ ] cURL //URL流的cURL
[ ] FTP //FTP支持
[ ] SNMP //SNMP支持
[ ] sockets //Sockets支持
* Process Control * //过程控制
[ ] PCNTL //Process control，过程控制支持
[] Posix //POSIX.1(IEEE 1003.1)功能支持
[ ] shmop //共享内存支持
[ ] sysvmsg //System V消息队列支持
[ ] sysvsem //System V信号灯支持
[ ] sysvshm //System V共享内存支持
* Variable and Type related * //变量和类型相关
[ ] Ctype //字符类型检查支持
[ ] Filter //输入过滤支持
* Web services * //网页服务
[ ] SOAP //SOAP支持
[ ] XML-RPC //XML-RPC支持
* XML manipulation * //XML操作
[ ] DOM //文档对象模型支持
[ ] libxml //libxml2支持
[ ] SimpleXML //SimpleXML支持
[ ] WDDX //WDDX支持
[ ] XML Parser //XML解析支持
[ ] XMLReader //XML读支持
[ ] XMLWriter //XML写支持
[ ] XSL //XSL转换支持
External php extensions ---> //外部php扩展
[ ] php-amqp //与任何符合AMQP的服务器通信
[ ] php-geoip //基于PHP GeoIP的IP地址映射到地理位置
[ ] php-gnupg //PHP扩展的gpgme库，开源的非对称信息加密系统
[ ] php-imagick //PHP扩展的ImageMagick库，用于读、写和处理主流图片文件
[ ] php-memcached //用于通过libmemcached库与memcached接口
[ ] php-ssh2 //libssh2库的PHP绑定
[ ] php-yaml //PHP YAML-1.1解析和生成
[ ] php-zmq //PHP的ZeroMQ消息传递绑定
-- python //python相关
python module format to install
(.pyc compiled sources only) ---> //python模块安装格式(.pyc仅编译源)
core python modules ---> //核心python模块
* The following modules are unusual or require extra libraries * //以下模块特殊或需要额外的库
[ ] bzip2 module //适用于Python的bzip2模块
[ ] bsddb module //适用于Python的bsddb模块
[ ] codecscjk module //适用于Python的中文/日文/韩文编解码器模块
[ ] curses module //Python的curses模块
[ ] ossaudiodev module //适用于Python的ossaudiodev模块
[ ] readline //Python的readline模块(在Python Shell中进行命令行编辑时需要)
[ ] ssl //适用于Python的_ssl模块(urllib等中的https必需)
[*] unicodedata module //Unicode字符数据库(由stringprep模块使用)
Python unicode database format //Python unicode数据库格式(通用字符集2字节UCS2)
(Universal Character Set 2-byte
(UCS2)) --->
[ ] sqlite module //SQLite数据库支持
[ ] xml module //Python的pyexpat和xml库
[ ] zlib module //Python中的zlib支持
[ ] hashlib module //Python中的hashlib支持
External python modules ---> //外部python扩展
[ ] python-alsaaudio //包含ALSA声卡的API
[ ] python-argh //Argh是argparse的智能包装器，argparse是一个命令行参数解析模块
[ ] python-arrow //更适用Python的日期和时间
[ ] python-asn1crypto //用于解析和序列化ASN.1结构
[ ] python-attrs //摆脱实现对象协议的繁琐工作，感受编写类的乐趣
[ ] python-autobahn //WebSocket客户端Hhh服务器库，WAMP实时框架
[ ] python-automat //用于Python有限状态机(特别是确定性有限状态机)表达
[ ] python-babel //一系列国际化Python应用程序的工具
[ ] python-backports-abc //对“collections.abc”模块的最新添加的反向移植
[ ] python-backports-shutil- //get_terminal_size函数的反向移植
get-terminal-size
[ ] python-bcrypt //跨平台文件加密工具
[ ] python-beautifulsoup4 //用于从HTML和XML文件中提取数据
[ ] python-bitstring //二进制数据的简单构造，分析和修改
[ ] python-bottle //快速，简单且轻量级的WSGI微型Web框架
[ ] python-can //为Python提供控制器区域网络支持
[ ] python-cbor //RFC 7049-简洁的二进制对象表示
[ ] python-certifi //用于提供Mozilla的CA Bundle的Python软件包
[ ] python-cffi //Python调用C代码的外函数接口
[ ] python-characteristic //没有样板的Python属性
[ ] python-chardet //适用于Python 2和3的通用编码检测器
[ ] python-cheetah //一个开源模板引擎和代码生成工具
[ ] python-cheroot //Cheroot是源自CherryPy的纯Python的高性能HTTP服务器
[ ] python-cherrypy //简约的Web框架
[ ] python-click //提供功能强大的命令行实用程序
[ ] python-coherence //用于数字生活的DLNA/UPnP媒体服务器和框架
[ ] python-configobj //一个简单但功能强大的配置文件(ini格式)读/写软件
[ ] python-configshell-fb //提供了一个框架来构建基于CLI的应用程序
[ ] python-constantly //提供符号常量支持的库
[ ] python-couchdb //用于CouchDB的Python客户端库
[ ] python-crc16 //用于CRC16校验的库
[ ] python-crcmod //用于生成计算循环冗余校验(CRC)的对象
[ ] python-crossbar //一个开源的WAMP应用路由器
[ ] python-cryptography //一个旨在向Python开发人员公开密码基元和配方的软件包
[ ] python-cssselect //解析CSS3选择器并将其转换为XPath 1.0
[ ] python-cssutils //用于Python的CSS级联样式表库
[ ] python-daemon //生成规范的Unix守护进程
[ ] python-dataproperty //用于从数据提取属性的Python库
[ ] python-dateutil //标准Python日期时间模块的扩展
[ ] python-decorator //实现装饰器更好地使用Python
[ ] python-dialog //UNIX对话框程序和大多数兼容程序的Python接口
[ ] python-dicttoxml //将Python字典或其它本机数据类型转换为有效的XML字符串
[ ] python-django //一个高级Python Web框架
[ ] python-docopt //轻松创建漂亮的命令行界面
[ ] python-docutils //用于将文档处理为有用的格式，比如HTML、XML、LaTeX
[ ] python-dominate //用于使用DOM API创建和处理HTML文档
[ ] python-dpkt //快速、简单的数据包创建/解析，带有基本TCP/IP协议的定义
[ ] python-ecdsa //ECDSA加密签名库
[ ] python-engineio //Engine.IO服务器
[ ] python-enum //Python中强大的枚举类型
[ ] python-enum34 //Python 3.4枚举反向移植到2.x
[ ] python-flask //Flask是基于Werkzeug，Jinja 2的Python微框架
[ ] python-flask-cors //Flask扩展添加了一个装饰器以支持CORS
[ ] python-flask-babel //Flask-Babel是Flask的扩展
[ ] python-flask-jsonrpc //支持Flask网站的基本JSON-RPC实现
[ ] python-flask-login //Flask-Login提供Flask的用户会话管理
[ ] python-flup //提供适用于Python的WSGI模块集合
[ ] python-futures //从Python 3.2反向移植concurrent.futures包
[ ] python-gobject //GLib/GObject库的Python绑定
[ ] python-gunicorn //用于UNIX的Python WSGI HTTP服务器
[ ] python-h2 //基于HTTP/2状态机的协议实现
[ ] python-hpack //Python HPACK标头压缩
[ ] python-html5lib //基于WHATWG HTML规范的HTML解析器
[ ] python-httplib2 //一个全面的HTTP客户端库
[ ] python-humanize //将数据转化为对应方便人类阅读的库
[ ] python-hyperframe //适用于Python的HTTP/2框架层
[ ] python-hyperlink //Python功能齐全，准确的URL
[ ] python-ibmiotf //适用于IBM Watson IoT Platform的Python客户端
[ ] python-id3 //通过界面读取和操作MP3文件的ID3信息标签
[ ] python-idna //指定的程序中国际化域名(IDNA)协议的库
[ ] python-incremental //用于对Python项目进行版本控制的库
[ ] python-iniparse //适用于Python的INI解析器，与标准库的ConfigParser API兼容
[ ] python-iowait //与平台无关的模块，用于I / O完成事件
[ ] python-ipaddr //Python中的IPv4/IPv6操作库
[ ] python-ipaddress //适用于旧版Python的Python 3.3的IP地址
[ ] python-ipy //用于处理IPv4和IPv6地址和网络的类和工具
[ ] python-ipython //用于多种编程语言的交互式计算的命令外壳
[ ] python-ipython-genutils //IPython残余实用程序
[ ] python-iso8601 //解析ISO 8601日期的简单模块
[ ] python-itsdangerous //可将数据传递到不受信任的环境并安全可靠地恢复数据
[ ] python-jaraco-classes //Python类构造的程序函数
[ ] python-jinja2 //用纯Python编写的模板引擎
[ ] python-jsonschema //适用于Python的JSON模式验证的实现
[ ] python-json-schema-validator //JSON模式验证器
[ ] python-keyring //提供了一种从Python访问系统密钥环服务的简便方法
[ ] python-libconfig //Libconfig是用于处理结构化配置文件的简单库
[ ] python-lmdb //读写LMDB数据库，Lightning Memory-Mapped Database
[ ] python-logbook //Python的日志记录替代
[ ] python-lxml //支持HTML和XML的解析，支持XPath解析方式，而且解析效率非常高
[ ] python-m2r //Markdown到reStructuredText转换器
[ ] python-mad //MAD库是一个高质量的MPEG解码器
[ ] python-mako //mako模板，有比Jinja2更快的解析速度和更多的语法支持
[ ] python-markdown //Markdown的Python实现
[ ] python-markdown2 //Markdown的快速，完整的Python实现
[ ] python-markupsafe //为Python实现XML/HTML/XHTML Markup安全字符串
[ ] python-mbstrdecoder //多字节字符串解码器
[ ] python-meld3 //HTML/XML模板系统
[ ] python-mistune //最快的Markdown解析器，具有渲染器功能
[ ] python-more-itertools //除itertools之外，还有更多的可迭代操作例程
[ ] python-msgpack //用于读取和写入MessagePack数据
[ ] python-mutagen //处理音频元数据的Python模块
[ ] python-mwclient //MediaWiki API客户端
[ ] python-mwscrape //将呈现的文章从MediaWiki API下载到CouchDB
[ ] python-netaddr //用于Python的网络地址处理库
[ ] python-netifaces //从Python可移植地访问网络接口
[ ] python-nfc //用于近场通信的Python模块
[ ] python-numpy //使用Python进行科学计算的基本软件包
[ ] python-oauthlib //OAuth请求签名逻辑的通用、符合规范的全面实现
[ ] python-paho-mqtt //为客户端类提供了对MQTT v3.1和v3.1.1的支持
[ ] python-pam //Pluggable Authentication Module，可插拔认证模块
[ ] python-paramiko //SSH2协议库
[ ] python-pathlib2 //面向对象的文件系统路径
[ ] python-pathpy //将路径对象实现为一实体，可调用路径对象上对文件操作
[ ] python-pathtools //文件系统通用程序
[ ] python-pathvalidate //用于验证/清除字符串，如filename/variable-name
[ ] python-pexpect //允许脚本生成子应用程序并对其进行控制
[ ] python-pickleshare //带有并发支持的微型“搁置式”数据库
[ ] python-pillow //一个很流行的图像处理库
[ ] python-portend //TCP端口监视实用程序
[ ] python-posix-ipc //访问POSIX进程间信号，共享内存和消息队列
[ ] python-priority //HTTP/2优先级树的Python实现
[ ] python-prompt-toolkit //用于在Python中构建功能强大的交互式命令行的库
[ ] python-protobuf //Google协议缓冲区的Python实现
[ ] python-psutil //用于获取Python中正在运行的进程和系统利用率信息
[ ] python-psycopg2 //用来操作postgreSQL数据库的第三方库
[ ] python-ptyprocess //用在伪终端(pty)中启动子流程，并与流程及其pty进行交互
[ ] python-pudb //一个全屏、基于控制台的Python调试器
[ ] python-pyasn //能够非常快速的IP地址查找
[ ] python-pyasn-modules //基于ASN.1的协议模块的集合
[ ] python-pycli //用于在Python中制作简单、准确的命令行应用程序框架
[ ] python-pycparser //Python中的C解析器
[ ] python-pycrypto //PyCrypto是密码算法和协议的集合
[ ] python-pydal //数据库抽象层，使用指定的方式为数据库后端实时动态生成SQL
[ ] python-pyelftools //用于解析和分析ELF文件和DWARF调试信息
[ ] python-pyftpdlib //极其快速且可扩展的Python FTP服务器库
[ ] python-pygame //简化使用Python编写游戏等多媒体软件的过程
[ ] python-pygments //Pygments是用Python编写的语法高亮包
[ ] python-pyicu //包装ICU C ++ API的Python扩展
[ ] python-pyinotify //在Linux上使用inotify监视文件系统事件
[ ] python-pylibftdi //包含python语言绑定libftdi
[ ] python-pylru //least recently used(LRU)缓存的实现
[ ] python-pymysql //一个纯Python MySQL客户端库，它遵循DB-API 2.0
[ ] python-pynacl //Python绑定到网络和密码学(NaCl)库
[ ] python-pyopenssl //OpenSSL库相关的Python包装器模块
[ ] python-pyparsing //客户端代码使用提供的类库直接在Python代码中构造语法
[ ] python-pyparted //可以编写与磁盘分区表和文件系统交互的应用程序
[ ] python-pypcap //pypcap模块是C libpcap库的面向对象包装
[ ] python-pyqrcode //QR代码生成器，带有SVG，EPS，PNG和终端输出
[ ] python-pyqt5 //Qt 5的Python绑定
[ ] python-pyratemp //用于执行html模板的Python库
[ ] python-pyro //一个Python库，表示PYthon远程对象
[ ] python-pyroute2 //Python Netlink库-Linux网络/netns/无线/ipset配置
[ ] python-pysendfile //用于sendfile(2)系统调用的Python接口
[ ] python-pysmb //Python编写的实验性SMB/CIFS库
[ ] python-pysnmp //SNMP引擎实现
[ ] python-pysnmp-apps //用于SNMP管理的命令行工具集合
[ ] python-pysnmp-mibs //预编译和打包的IETF和IANA MIB，以简化与PySNMP库的使用
[ ] python-pysocks //Python SOCKS客户端模块
[ ] python-pytablereader //用于从文件/URL加载具有各种数据格式的结构化表数据
[ ] python-pytablewriter //用于以各种格式编写表：CSV、HTML、JSON、Excel等
[ ] python-pytrie //trie数据结构的纯Python实现
[ ] python-pytz //世界时区定义
[ ] python-pyudev //Python绑定到libudev(Linux的设备和硬件管理以及信息库)
[ ] python-pyusb //从Python访问通用串行总线(USB)
[ ] python-pyxb //为与XMLSchema定义的数据结构相对应的类生成Python代码
[ ] python-pyyaml //包含与libyaml API的绑定
[ ] python-pyzmq //用于zeromq的python语言绑定
[ ] python-raven //Raven是Sentry(https://getsentry.com)的客户端
[ ] python-remi //GUI库，将应用程序的界面转换为HTML，以在Web浏览器中呈现
[ ] python-requests //Apache2许可的HTTP库
[ ] python-requests-oauthlib //OAuthlib身份验证对请求的支持
[ ] python-requests-toolbelt //python-requests的高级用户工具
[ ] python-rpi-gpio //用于控制Raspberry Pi上的GPIO
[ ] python-rtslib-fb //用于配置LIO通用SCSI目标的基于对象的Python库
[ ] python-scandir //一个更好、更快的目录迭代器
[ ] python-schedule //使用简单、人性化的语法按预定的时间间隔定期运行Python函数
[ ] python-sdnotify //systemd服务通知协议(sd_notify)的纯Python实现
[ ] python-secretstorage //Python与FreeDesktop.org Secret Service API的绑定
[ ] python-see //dir()的人性化替代
[ ] python-serial //用于访问串行端口的Python库
[ ] python-service-identity //pyOpenSSL的服务身份验证
[ ] python-setproctitle //用于自定义流程标题的Python模块
[ ] python-setuptools //下载、构建、安装、升级和卸载Python软件包
[ ] python-sh //Python子进程替换，可以像调用一个程序一样调用任何程序
[ ] python-shutilwhich //Python 3.3的shutil.which函数的复制和粘贴反向移植
[ ] python-simplegeneric //简单的通用函数(类似于Python自己的len()、pickle.dump()等)
[ ] python-simplejson //简单、快速、可扩展的JSON编码器/解码器
[ ] python-simplesqlite //用于简化SQLite数据库操作：表创建，数据插入和获取数据等
[ ] python-singledispatch //把整体方案拆分成，多个小的模块
[ ] python-six //提供了一些程序函数，用于消除Python2/3版本之间的差异
[ ] python-smbus-cffi //允许SMBus通过Linux主机上的I2C/dev接口进行访问
[ ] python-socketio //Socket.IO服务器
[ ] python-sortedcontainers //Python排序容器类型：SortedList，SortedDict和SortedSet
[ ] python-spidev //用于通过spidev内核驱动程序从用户空间与SPI设备进行接口
[ ] python-systemd //用于本地访问systemd设备的Python模块
[ ] python-tabledata //用于表示pytablewriter/pytablereader/SimpleSQLite的表格数据
[ ] python-tempora //与日期和时间有关的对象和例程
[ ] python-typepy //在运行时用于变量类型检查器/验证器/转换器的Python库
[ ] python-thrift //Apache Thrift RPC系统的Python绑定
[ ] python-tomako //将Mako用作Tornado的模板引擎的最简单方法
[ ] python-toml //一个解析toml编写的包
[ ] python-tornado //一个Python Web框架和异步网络库
[ ] python-traitlets //用于Python应用程序的配置系统
[ ] python-treq //用于在使用Twisted时发出HTTP请求
[ ] python-twisted //一个用Python编写的事件驱动的网络引擎
[ ] python-txaio //asyncio/Twisted/Trollius之间的兼容性API
[ ] python-txtorcon //基于Twisted的Tor控制器客户端，具有状态跟踪和配置抽象
[ ] python-u-msgpack //轻量级MessagePack序列化器和反序列化器
[ ] python-ubjson //通用二进制JSON编码器/解码器
[ ] python-ujson //UltraJSON是用纯C语言编写的超快速JSON编码器/解码器
[ ] python-urllib3 //具有线程安全连接池、文件发布支持、健全性等功能的Python HTTP库
[ ] python-urwid //Urwid是Python的控制台用户界面库
[ ] python-versiontools //version中使用的普通元组的智能替换
[ ] python-watchdog //Python API和Shell程序，用于监视文件系统事件
[ ] python-wcwidth //可测量宽字符代码的终端列单元格的数量
[ ] python-web2py //全功能Web应用框架，用于开发快速、安全以及可移植Web应用
[ ] python-webpy //适用于Python的Web框架，功能强大而又简单
[ ] python-werkzeug //适用于Python的WSGI实用程序库
[ ] python-whoosh //一个快速的纯Python全文索引，搜索和拼写检查库
[ ] python-ws4py //实现了RFC 6455中定义的WebSocket协议
[ ] python-wsaccel //ws4py和AutobahnPython的加速器
[ ] python-xlib //Python X库旨在成为Python程序的全功能X客户端库
[ ] python-xlrd //供开发人员从Microsoft Excel(tm)电子表格文件中提取数据的库
[ ] python-xlsxwriter //用于创建Excel XLSX文件的Python模块
[ ] python-xlutils //用于同时需要xlrd和xlwt的Excel文件的实用程序
[ ] python-xlwt //创建与MS Excel 97/2000/XP/2003 XLS文件兼容的电子表格文件的库
[ ] python-xmltodict //xmltodict是另一个简易的库，它致力于将XML变得像JSON
[ ] python-zope-interface //提供了面向对象编程语言中的接口(interface)实现
[ ] ruby //面向对象的脚本语言
[ ] tcl //Tool Command Language，一种简单的文本语言
```


#### 2.6.11 Libraries（待完善）

```
====一级菜单 Target packages====
====Libraries==========
Audio/Sound ---> //音频&声卡
-- alsa-lib ---> //Advanced Linux Sound Architecture(ALSA)，提供音频和MIDI功能
[ ] aubio //一种用于从音频信号中提取属性信息的工具
[ ] audiofile //读写许多常见格式的音频文件
[ ] bcg729 //ITU G729 Annex A/B语音编解码器的编码器和解码器的开源实现
[ ] celt051 //CELT格式的超低延迟音频编解码器
[ ] fdk-aac //为数字音频实现MPEG高级音频编码(AAC)编码和解码方案的软件
[ ] libao //跨平台的音频库，允许程序在各种平台上使用简单的API输出音频
[ ] libasplib //Achim的信号处理库，用于数字信号处理
[ ] libbroadvoice //16和32语音编解码器的库
[ ] libcdaudio //提供在播放音频CD时控制CD-ROM操作的功能
[ ] libcddb //用于访问CDDB服务器(freedb.org)上的数据
[ ] libcdio //GNU CD输入和控制库
[ ] libcdio-paranoia //带有纠错功能的CD输入和控制库
[ ] libcodec2 //低比特率语音编解码器(700到3200bit/s之间)
[ ] libcue //CUE工作表解析器库
[ ] libcuefile //Musepack的提示文件库
[ ] libebur128 //用于实现响度标准化的EBU R 128标准
[ ] libg7221 //ITU G.722.1和Annex C宽带语音编解码器的库
[ ] libgsm //GSM 06.10有损语音压缩的共享库
[ ] libid3tag //MAD项目中的ID3标签阅读库
[ ] libilbc //internet Low Bitrate Codec(iLBC)免费的语音编解码器
[ ] liblo //POSIX系统的开放声音控制协议的实现
[ ] libmad //高品质的MPEG音频解码器，适合没有浮点单元的系统
[ ] libmodplug //MOD音乐文件解码器
[ ] libmpd //用于访问Music Player守护程序的高级客户端库
[ ] libmpdclient //提供用于连接Music Player Daemon (MPD)的API
[ ] libreplaygain //重播增益库
[ ] libsamplerate //计算音频的响度，并根据ReplayGain标准建议dB调整
[ ] libsidplay2 //能播放所有C64单声道和立体声文件格式
[ ] libsilk //一种音频压缩格式和音频编解码器
[ ] libsndfile //用于通过标准库接口读取和写入包含采样声音文件的C库
[ ] libsoundtouch //用于更改音频流或音频文件的速度，音调和播放速率的音频处理库
[ ] libsoxr //用于快速，高质量的一维采样率转换
[ ] libvorbis //Vorbis开源音频解码器Ogg Vorbis的库
[ ] mp4v2 //MP4v2库提供了读取，创建和修改mp4文件的功能
[ ] openal //提供在虚拟3D环境中播放音频的功能
[ ] opencore-amr //自适应多速率窄带和宽带(AMR-NB和AMR-WB)语音编解码器的实现库
[ ] opus //旨在通过Internet进行交互式语音和音频传输
[ ] opusfile //用于解码和基本处理所有Ogg Opus音频流
[ ] portaudio //一个免费的，跨平台的开源音频I/O库
[ ] sbc //音频编解码器，用于连接蓝牙高质量音频设备，例如耳机或扬声器
[ ] spandsp //许多用于电话的DSP功能的库
[ ] speex //Speex是一种开放源代码/免费软件，专为语音设计的音频压缩格式
[ ] speexdsp //Speex的DSP库
[ ] taglib //一个用于读取和编辑几种流行音频格式的元数据的库
[ ] tinyalsa //一个可与Linux内核中的ALSA接口交互的库
[ ] tremor (fixed point vorbis decoder) //Tremor是Ogg Vorbis解码器的定点实现。
[ ] vo-aacenc //包含Advanced Audio Coding(AAC)音频编解码器的编码器实现
[ ] webrtc-audio-processing //基于Google的WebRTC实现的AudioProcessing库


Compression and decompression ---> //压缩和解压缩 ★
[ ] libarchive //用于读取和写入各种流存档格式
[ ] libsquish //使用DXT标准(也称为S3TC)压缩图像
[ ] libzip //用于读取，创建和修改zip存档
-- lzo //用ANSI C编写的可移植无损数据压缩库
[ ] minizip //用于解压zip文件
[ ] snappy //一个高速、兼容性强的压缩/解压缩库
[ ] szip //Szip是Extended-Rice无损压缩算法的实现
-- zlib support //选择所需的Zlib库提供程序
zlib variant (zlib) ---> //zlib:标准(解压缩)库 | zlib-ng:zlib的改进版

Crypto ---> //加密  ★
[ ] beecrypt //一个通用的加密库
[ ] botan //C ++的加密库
[ ] CA Certificates //包括CA证书的PEM文件，以允许基于SSL的程序检查SSL连接的真实性
[ ] cryptodev //选择所需的cryptodev实现
[ ] gcr //用于加密UI和访问PKCS＃11模块的库
[ ] gnutls //一个安全的通信库，用于实现SSL和TLS协议及其周围的技术
[ ] libassuan //实现Assuan协议，用于大多数较新的GnuPG组件之间的IPC
[ ] libgcrypt //LibGCrypt是GNU的基本密码库
[ ] libgpg-error //一个小型库，包含大多数GnuPG相关软件共享的错误代码和描述
[ ] libgpgme //GnuPG Made Easy(GPGME)是一个库，旨在简化应用对GnuPG的访问
[ ] libkcapi //Linux内核Crypto API用户空间接口库
[ ] libksba //CMS和X.509库
[ ] libmcrypt //一个提供统一接口以访问多种加密算法的库
[ ] libmhash //为哈希算法提供统一的接口的免费库
[ ] libnss //Network Security Services(NSS)，用于安全的客户端和服务器应用开发
[ ] libscrypt //一个实现安全密码哈希功能“scrypt”的库
[ ] libsecret //用于存储和检索密码和其他机密的库
[ ] libsha1 //一个提供SHA1实现的微型库
[ ] libsodium //一个现代且易于使用的加密库
[ ] libssh //在客户端和服务器端实现SSHv2和SSHv1协议
[ ] libssh2 //实现Internet草案定义的SSH2协议的客户端C库
[ ] libtomcrypt //一个相当全面的模块化便携式密码工具
[ ] libuecc //很小的椭圆曲线密码库
[ ] mbedtls //提供在(嵌入式)产品中包含加密和SSL/TLS功能
[ ] nettle //一个低级别的密码库
-- openssl support //选择所需的SSL库提供程序
ssl library (openssl) --->//选择OpenSSL或LibreSSL
openssl //实现安全套接字层(SSL v2/v3)和传输安全性(TLS v1)以及功能全面的通用加密库
[ ] openssl binary //将openssl二进制文件和关联的帮助程序脚本安装到目标文件系统
[ ] openssl additional engines //安装其他加密引擎库
[ ] rhash //用于计算各种哈希和，例如CRC32，MD4，MD5，SHA1，SHA256，SHA512等
[ ] tinydtls //用于数据报传输层安全性(DTLS)的库，涵盖客户端和服务器状态机
[ ] tpm2-tss //Trusted Computing Group's(TCG)和TPM2 Software Stack(TSS)的OSS实现
[ ] trousers //TCG Software Stack(TSS)，用于符合TPM规范1.2版的受信任平台模块
[ ] ustream-ssl //ustream SSL包装器
[ ] wolfssl //一种轻量级，可移植，基于C语言的SSL/TLS库

Database ---> //数据库  ★
[ ] berkeleydb //伯克利数据库，一个非常常见的数据库应用程序库
[ ] cppdb //一个SQL连接库，旨在提供与平台和数据库无关的连接API，类似于JDBC，ODBC
[ ] gdbm //一组使用可扩展哈希的数据库例程
[ ] hiredis //一个轻量级的访问redis数据库的c客户端
[ ] kompexsqlite //SQLite的开源C++包装器库
[ ] leveldb //Google编写的快速键值存储库，提供了从字符串键到字符串值的有序映射
[ ] libgit2 //是Git核心方法的可移植的纯C实现，用于将Git功能构建到应用程序中
[ ] mongodb //一个跨平台的面向文档的数据库(NoSQL)
-- mysql support //选择所需的mysql提供程序
mysql variant (oracle mysql) ---> //选择oracle mysql服务器或mariadb服务器
[ ] oracle mysql server //在目标服务器上安装MySQL服务器
[ ] postgresql //一个功能强大的开源对象关系数据库系统
[ ] redis //一个开源的高级键值存储，也被称为数据结构服务器
* sqlcipher conflicts with sqlite * //**sqlcipher与sqlite冲突*
-*- sqlite //SQLite是一个小型C库，实现了独立的，可嵌入的，零配置的SQL数据库引擎
[ ] Additional query optimizations (stat3) //向ANALYZE命令和查询计划程序添加其它逻辑
[ ] Enable convenient access to meta-data about tables and queries //启用其他API来访问有关表和查询的元数据
[ ] Enable version 3 of the full-text search engine //全文搜索引擎v3添加到构建中
[ ] Enable the JSON extensions for SQLite //SON扩展添加到构建中
[ ] Enable sqlite3_unlock_notify() interface //启用sqlite3_unlock_notify()接口及其附带的功能
[ ] Set the secure_delete pragma on by default //更改secure_delete编译指示的默认设置
[ ] Disable fsync //关闭fsync()强制数据库立即存储，牺牲掉电以提高性能
[ ] unixodbc //unixODBC Project的目标是开发和推广unixODBC，使其成为非Windows平台的ODBC标准

Filesystem ---> //文件系统*  ★
[ ] gamin //文件变更监视器
-- libconfig //用于处理结构化配置文件的简单库
-- libconfuse //用C编写的配置文件解析器库
-- libfuse //FUSE (Filesystem in UserSpacE)
[ ] liblockfile //NFS安全锁定库
[ ] libnfs //NFS用户空间实现
[ ] libsysfs //一组基于sysfs的实用程序
[ ] lockdev //用于锁定设备的库
[ ] physfs //PhysicsFS，便携、灵活的文件I/O抽象

Graphics ---> //图形  ★
[ ] assimp //Open Asset Import Library(assimp)导入各种3D格式以统一,作为通用的3D模型转换器
[ ] at-spi2-atk //包含将ATK桥接搭配At-spi2 Dbus服务的库
[ ] at-spi2-core //是GNOME辅助功能项目的一部分
[ ] atk //提供了一套由其他工具集和应用程序实现的辅助功能接口
[ ] atkmm //ATK的C++绑定
[ ] bullet //碰撞检测和刚体动力学库
[ ] cairo //一个2D图形库与多输出设备的支持
[ ] cairomm //cairo的C++绑定
[ ] chipmunk //一个简单、轻便、快速、便携的2D刚体物理C语言编写的库
[ ] exiv2 //一个C++库和命令行程序来管理、读写图像(Exif，IPTC和XMP)的元数据
[ ] exempi //XMP(Extensible Metadata Platform)可扩展元数据平台的实现
[ ] fontconfig //配置和定制字体访问的库
[ ] freetype //一个自由、高品质和便携的字体引擎。
[] gd ---> //一个图形库，允许代码快速绘制直线、圆弧、文字、剪裁图片，结果将保存为PNG文件
[ ] gdk-pixbuf //图像加载器和定标器
-- giflib //一个库，用于读取和写入GIF图像
[ ] granite //个扩展GTK+提供了一些有用的小工具和类便于应用开发
[ ] graphite2 //提供复杂的书写系统的跨平台的渲染
[ ] gtkmm3 //GTK 3的C++绑定
[ ] harfbuzz //一个OpenType字体文本整形引擎
[ ] ijs //用于实现光栅的页面图像的传输的协议的库
[ ] imlib2 //提供程序加载，保存并以各种格式的图像
[ ] irrlicht //一个开源的高性能实时3D图形引擎
[ ] jasper //JPEG-2000解码器
-- jpeg support //选择所需的JPEG库类型
jpeg variant (jpeg-turbo) ---> //JPEG变种(jpeg或jpeg-turbo)
[ ] kms++ //一个C++11库内核模式设置。此外，还包括简单的测试工具KMS
[ ] lcms2 //Little Color Management Software(CMS)一个色彩管理引擎，特别注重精度和性能
[ ] lensfun //纠正一些文物和用于存储镜头配置文件数据库的库
[ ] leptonica //图像处理和图像分析库
[ ] libart //高性能的2D图形库
[ ] libdmtx //读取和写入的现代ECC200各种数据矩阵条码
-- libdrm ---> //Direct Rendering Manager(直接渲染管理)
-- radeon //安装AMD/ATI显卡驱动
[ ] amdgpu //安装AMD GPU驱动程序
-- nouveau //安装NVIDIA显卡驱动程序
[ ] omap (experimental) //安装TI OMAP驱动程序的API(实验用)
-- etnaviv (experimental) //安装Etnaviv/Vivante驱动程序的API(实验用)
[ ] exynos (experimental) //安装Samsung Exynos驱动程序的API(实验用)
[ ] freedreno //安装Qualcomm Snapdragon驱动程序
[ ] tegra (experimental) //安装NVIDIA Tegra驱动程序的API(实验用)
-- vc4 //安装VC4(Raspberry Pi)驱动程序
[ ] Install test programs //安装libdrm测试程序
[ ] libepoxy //处理的OpenGL函数指针管理的库
[ ] libexif //允许解析EXIF文件，并从这些标签读取数据。
[ ] libfm //一个基于glib/gio的库，提供一些文件管理程序和gtk+/glib中缺少的相关小部件
[ ] libfm-extra //libfm扩展包包括一个库和通过菜单缓存获取所需的其他文件
[ ] libfreeglut //一个替代OpenGL的实用工具包(GLUT)库
[ ] libfreeimage //支持流行的图形图像格式(PNG、BMP、JPEG、TIFF)的开源库
[ ] libgeotiff //一个开源库读、写GeoTIFF的信息标签
[ ] libglew //OpenGL Extension Wrangler Library(GLEW)一个跨平台的C/C ++扩展加载库
[ ] libglfw //OpenGL的上下文创建窗口和接收输入事件的一个开源多平台库
[ ] libglu //Mesa OpenGL实用库
[ ] libgta //一种便携式库，实现了Generic Tagged Array(GTA)通用标记阵列的文件格式
[ ] libgtk3 //GTK+3的图形用户界面库
[ ] libmediaart //负责管理、提取和处理媒体缓存
[ ] libmng //参考库读取、显示、撰写和检查多图像网络图形
-- libpng //处理(Portable Network Graphics)PNG可移植网络图形的库
[ ] libqrencode //用于在QR码符号数据进行编码一个C库
[ ] libraw //一个原始图像处理库
[ ] librsvg //Scalable Vector Graphics(SVG)可伸缩矢量图形的渲染库
[ ] libsoil //用于上传纹理到OpenGL的一个微小的C库
[ ] libsvg //文件或缓冲区的SVG内容解析器，没有做任何渲染，而是提供基于函数的接口
[ ] libsvg-cairo //提供呈现来自文件或缓冲区SVG内容的功能
[ ] libsvgtiny //SVG的微型实现
[ ] libva //Video Acceleration(VA)视频加速API
[ ] libvips //2D图像处理库
[ ] menu-cache //小型库用于应用程序菜单集成
[ ] opencv-2.4 ---- //Open Source Computer Vision(OpenCV)一种用于实时计算机视觉编程的库
[] opencv3 ---> //OpenCV开源计算机视觉
* OpenCV modules * //OpenCV模块
[ ] calib3d //opencv_calib3d(相机校准和3D重建)模块
[ ] features2d //opencv_features2d(2D特征框架)模块
[ ] flann //opencv_flann(聚类和多维空间搜索)模块
[ ] highgui //opencv_highgui(高级GUI和媒体I/O)模块
[ ] imgcodecs //opencv_imgcodecs(图像编解码器)模块
[ ] imgproc //opencv_imgproc(图像处理)模块
[ ] ml //opencv_ml(机器学习)模块
[ ] objdetect //opencv_objdetect(对象检测)模块
[ ] photo //opencv_photo(计算摄影)模块
[ ] shape //opencv_shape(形状描述和匹配)模块
[ ] stitching //opencv_stitching(图像拼接)模块
[ ] superres //opencv_superres(超大分辨率)模块
[ ] ts //opencv_ts(测试)模块
[ ] videoio //opencv_videoio(媒体I/O)模块
[ ] video //pencv_video(视频分析)模块
[ ] videostab //opencv_videostab(视频稳定)模块
* Test sets * //测试设置
[ ] build tests //构建测试
[ ] build performance tests//构建性能测试
* 3rd party support * //第三方支持
[ ] ffmpeg support //ffmpeg支持
gstreamer support (none) ---> //gstreamer-0.10或1.x支持
[ ] jpeg2000 support //jpeg2000支持
[ ] jpeg support //jpeg支持
[ ] png support //png支持
[ ] tiff support //tiff支持
[ ] v4l support //linux v4l支持
* Install options * //安装选项
[ ] install extra data //安装所使用的CV库和/或演示应用程序的各种数据
[ ] openjpeg //一个开源的JPEG2000编解码器
[ ] pango //用于高质量地渲染国际化的文字
[ ] pangomm //pango的C++绑定
[ ] pixman //Cairo像素管理
[ ] poppler //一个基于xpdf的-3.0代码库PDF渲染库
[ ] tiff ---- //处理Tag Image File Format(TIFF)标签图像文件格式图像
[ ] waffle //一个C库，用于在运行时选择OpenGL API和窗口系统
[ ] wayland //旨在作为X的更简单替代品，更易于开发和维护
[ ] webkitgtk //用于处理视频内容的GStreamer GL元素
[ ] webp //一种新的图像格式，在网络上提供无损和有损压缩的图像
[ ] woff2 //对于WOFF2字体文件格式参考实现，通常用于Web字体
[ ] zbar //QR和条码扫描仪
[ ] zxing-cpp //条形码图像处理的Java实现库，提供编译C++端口

Hardware handling ---> //硬件处理  ★
    [ ] acsccid
    [ ] bcm2835
    [ ] c-periphery
    [ ] ccid
    [ ] dtc (libfdt)
    [ ] gnu-efi
    [ ] hackrf
    [ ] hidapi
    [ ] lcdapi
    [ ] let-me-create
    [ ] libaio
    [ ] libatasmart
    [ ] libcec
    [ ] libfreefare
    [ ] libftdi
    [ ] libftdi1
    [ ] libgphoto2
        * libgpiod needs kernel headers >= 4.8 *
    [ ] libgudev
    [ ] libhid
    [ ] libiio
    [ ] libinput
    [ ] libiqrf
    [ ] libllcp
    [ ] libmbim
    [ ] libnfc
    [ ] libpciaccess
    [ ] libphidget
    [ ] libpri
    [ ] libqmi
    [ ] libraw1394
    [ ] librtlsdr
    [ ] libserial
    [ ] libserialport
    [ ] libsigrok
    [ ] libsigrokdecode
    [ ] libsoc
    [ ] libss7
    -- libusb
    [ ] build libusb examples
    [ ] libusb-compat
    [ ] libusbgx
    [ ] libv4l
    [ ] libxkbcommon
    [ ] mraa
    [ ] mtdev
    [ ] ne10
    [ ] neardal
    [ ] owfs
    [ ] pcsc-lite
    [ ] tslib
    [ ] urg
    [ ] wiringpi
 
Javascript ---> //Javascript  ★
    [ ] angularjs
    [ ] bootstrap
    [ ] duktape
    [ ] explorercanvas
    [ ] flot
    [ ] jQuery
    [ ] jsmin
    [ ] json-javascript
    
JSON/XML ---> //JSON/XML  ★
    [ ] benejson
    [ ] cJSON
    -- expat
    [ ] ezxml
    [ ] jansson
    [ ] jose
    [ ] jsmn
    [ ] json-c
    [ ] json-for-modern-cpp
    -- json-glib
    [ ] jsoncpp
    [ ] libbson
    [ ] libfastjson
    [ ] libjson
    [ ] roxml
    [ ] libucl
    -- libxml2
    [ ] libxml++
    [ ] libxmlrpc
    [ ] libxslt
    [ ] libyaml
    [ ] Mini-XML
    [ ] pugixml
    [ ] rapidjson
    [ ] rapidxml
    [ ] raptor
    [ ] tinyxml
    [ ] tinyxml2
    [ ] valijson
    [ ] xerces-c++
    [ ] yajl
    [ ] yaml-cpp
    
Logging ---> //日志  ★
    [ ] eventlog
    [ ] glog
    [ ] liblog4c-localtime
    [ ] liblogging
    [ ] log4cplus
    [ ] log4cpp
    [ ] log4cxx
    [ ] opentracing-cpp
    [ ] zlog
    
Multimedia ---> //多媒体 ★
    [ ] bitstream
    [ ] kvazaar
    [ ] libaacs
    [ ] libamcodec
    [ ] libass
    [ ] libbdplus
    [ ] libbluray
    [ ] libdcadec
    [ ] libdvbcsa
    [ ] libdvbpsi
    [ ] libdvbsi
    [ ] libdvdcss
    [ ] libdvdnav
    [ ] libdvdread
    [ ] libebml
    [ ] libhdhomerun
        * libimxvpuapi needs an i.MX platform with VPU support *
    [ ] libmatroska
    [ ] libmms
    [ ] libmpeg2
    [ ] libogg
    [ ] libopenh264
    [ ] libopusenc
    [ ] libplayer
    [ ] libtheora
    [ ] libvpx
    [ ] libyuv
    [ ] live555
    [ ] mediastreamer
    [ ] x264
    [ ] x265
    
Networking ---> //网络 ★
    [ ] agent++
    [ ] alljoyn
    [ ] alljoyn-base
    [ ] alljoyn-tcl
    [ ] alljoyn-tcl-base
    [ ] azmq
    [ ] azure-iot-sdk-c
    [ ] batman-adv
    [ ] c-ares
    [ ] canfestival
    [ ] cgic
    [ ] cppzmq
    [ ] curlpp
    [ ] czmq
    [ ] daq
    [ ] davici
    [ ] filemq
    [ ] flickcurl
    [ ] freeradius-client
    [ ] geoip
    [ ] glib-networking
    [ ] grpc
    [ ] gssdp
    [ ] gupnp
    [ ] gupnp-av
    [ ] gupnp-dlna
    [ ] ibrcommon
    [ ] ibrdtn
    [ ] libcgi
    [ ] libcgicc
    [ ] libcoap
    [ ] libcpprestsdk
    -- libcurl
    [ ] curl binary
    [ ] enable verbose strings
         SSL/TLS library to use (OpenSSL) --->
    [ ] libdnet
    [ ] libeXosip2
    [ ] libfcgi
    [ ] libgsasl
    [ ] libhttpparser
    [ ] libidn
    [ ] libidn2
    [ ] libiscsi
    [ ] libkrb5
    [ ] libldns
    [ ] libmaxminddb
    [ ] libmbus
    [ ] libmemcached
    [ ] libmicrohttpd
    [ ] libminiupnpc
    [ ] libmnl
    [ ] libmodbus
    [ ] libnatpmp
    [ ] libndp
    [ ] libnet
    [ ] libnetfilter_acct
    [ ] libnetfilter_conntrack
    [ ] libnetfilter_cthelper
    [ ] libnetfilter_cttimeout
    [ ] libnetfilter_log
    [ ] libnetfilter_queue
    [ ] libnfnetlink
    [ ] libnftnl
    [ ] libnice
    -- libnl
    [ ] install tools
    [ ] liboauth
    [ ] liboping
    [ ] libosip2
    [ ] libpagekite
    [ ] libpcap
    [ ] libpjsip
    [ ] librsync
    [ ] libshairplay
    [ ] libshout
    [ ] libsocketcan
    [ ] libsoup
    [ ] libsrtp
    [ ] libstrophe
    [ ] libtirpc
    [ ] libtorrent
    [ ] libtorrent-rasterbar
    [ ] libupnp
    [ ] libupnp18
    [ ] libupnpp
    [ ] liburiparser
    [ ] libvncserver
    [ ] libwebsock
    [ ] libwebsockets
    [ ] lksctp-tools
    [ ] mongoose
    [ ] nanomsg
    [ ] libneon
    [ ] nghttp2
    [ ] norm
    [ ] nss-myhostname
    [ ] nss-pam-ldapd
    [ ] omniorb
    [ ] openldap
    [ ] openmpi
    [ ] openpgm
    [ ] openzwave
    [ ] oRTP
    [ ] paho-mqtt-c
    [ ] qdecoder
    [ ] qpid-proton
    [ ] rabbitmq-c
    [ ] librtmp
    [ ] slirp
    [ ] snmp++
    [ ] sofia-sip
    [ ] thrift
    [ ] usbredir
    [ ] snmp++
    [ ] sofia-sip
    [ ] thrift
    [ ] usbredir
    [ ] wampcc
    [ ] websocketpp
    [ ] zeromq
    [ ] zmqpp
    [ ] zyre
    
Other ---> //其它 ★
    [ ] apr
    [ ] apr-util
    [ ] armadillo
    [ ] atf
    [ ] bctoolbox
    [ ] bdwgc
    [ ] boost
    [ ] capnproto
    [ ] clang
    [ ] cblas/clapack
    [ ] classpath
    [ ] cmocka
    [ ] cppcms
    [ ] cracklib
    [ ] dawgdic
    [ ] ding-libs
    [ ] eigen
    [ ] elfutils
    [ ] ell
    [ ] fftw
    [ ] flann
    [ ] flatbuffers
    [ ] flatcc
    [ ] gconf
    [ ] gflags
    [ ] glibmm
    [ ] glm
    [ ] gmp
    [ ] gsl
    [ ] gtest
    [ ] jemalloc
    [ ] lapack/blas
    [ ] libargtable2
    [ ] libatomic_ops
    [ ] libb64
    [ ] libbsd
    [ ] libcap
    [ ] libcap-ng
    [ ] libcgroup
    [ ] libclc
    [ ] libcofi
    [ ] libcorrect
    [ ] libcroco
    [ ] libcrossguid
    [ ] libcsv
    [ ] libdaemon
    [ ] libee
    [ ] libev
    [ ] libevdev
    [ ] libevent
    -- libffi
    [ ] libgee
    -- libglib2
    [ ] libglob
    -- libical
    [ ] libite
    [ ] liblinear
    [ ] libloki
    [ ] libnpth
    [ ] libnspr
    [ ] libpfm4
    [ ] libplist
    -- libpthread-stubs
    [ ] libpthsem
    [ ] libpwquality
    [ ] libseccomp
    [ ] libsigc++
    [ ] libsigsegv
    [ ] libspatialindex
    [ ] libtasn1
    [ ] libtommath
    [ ] libtpl
    [ ] libubox
    [ ] libuci
    [ ] libunwind
    [ ] liburcu
    [ ] libuv
    [ ] lightning
    [ ] linux-pam
    [ ] liquid-dsp
    -- llvm
    [ ] AMDGPU backend
    [ ] lttng-libust
    [ ] mpc
    [ ] mpdecimal
    [ ] mpfr
    [ ] mpir
    [ ] msgpack
    [ ] mtdev2tuio
    [ ] openblas
    [ ] orc
    [ ] p11-kit
    [ ] poco
    [ ] protobuf
    [ ] protobuf-c
    [ ] qhull
    [ ] qlibc
    [ ] riemann-c-client
    [ ] shapelib
    [ ] skalibs
    [ ] sphinxbase
    [ ] riemann-c-client
    [ ] shapelib
    [ ] skalibs
    [ ] sphinxbase
    [ ] tinycbor
    [ ] xapian
    
Security ---> //安全 ★
    [ ] libselinux
    [ ] libsemanage
    [ ] libsepol
    [ ] safeclib

Text and terminal handling ---> //文字和终端处理  ★
    [ ] augeas
    [ ] enchant
    [ ] fmt
    [ ] icu
    [ ] libcli
    [ ] libedit
    [ ] libenca
    [ ] libestr
    [ ] libfribidi
    [ ] libunistring
    [ ] linenoise
    -- ncurses
    [ ] enable wide char support
    [ ] ncurses programs
    () additional terminfo files to install
    [ ] newt
    -- pcre
    [ ] 16-bit pcre
    [ ] 32-bit pcre
    -- UTF-8/16/32 support in pcre
    -- Unicode properties support in pcre
    [] pcre2
    [] 16-bit pcre2
    [ ] 32-bit pcre2
    [ ] popt
    -- readline
    [ ] slang
    [ ] tclap
    [ ] ustr
```

#### 2.6.12 Mail

```
====Target packages====
====Mail====

[ ] dovecot //开源的IMAP和POP3电子邮件服务器
[ ] exim //消息传输代理(MTA)
[ ] fetchmail //将邮件从POP和IMAP移至本地计算机的客户端后台驻留程序
[ ] heirloom-mailx //可作终端邮件阅读器、邮件编写程序和SMTP客户端
[ ] libesmtp //用于通过SMTP发送电子邮件的库
[ ] msmtp //SMTP客户端
[ ] mutt //一个基于文本的复杂邮件用户代理Mail User Agent(MUA)
```
#### 2.6.13 Miscellaneous （其他）

```
====Target packages====
====Miscellaneous====

​```
[ ] aespipe //AES加密和解密数据块
[ ] bc //一种任意精度的数字处理语言，其语法与C相似
[ ] clamav //用于检测木马、病毒、恶意软件和其他恶意威胁的防病毒引擎
[*] collectd ---> //收集的
match plugins ---> //匹配插件
[ ] empty counter //匹配当前为零的计数器值
[ ] hashed //使用主机名的哈希函数匹配值
[ ] regex //根据正则表达式按其标识符匹配值
[ ] timediff //匹配带有无效时间戳记的值
[ ] value //根据其数据源的值选择值
misc plugins ---> //杂项插件
[ ] aggregation //允许使用一或多个合并函数将多个值聚合为单个值
[*] logfile //将日志消息写入文件或STDOUT/STDERR
[ ] logstash //写入格式为logstash JSON事件的日志消息
[ ] notify_email //向配置的收件人发送带有通知消息的电子邮件
[ ] notify_nagios //将通知发送到Nagios，作为被动检查结果
[*] syslog //登录到标准UNIX日志记录机制
[ ] threshold //根据配置的阈值检查值，如果值超出范围，则创建通知
read plugins ---> //阅读插件
[ ] apache //收集Apache的mod_status信息
[ ] apcups //从apcupsd收集UPS统计信息
[ ] battery //收集电池电量，消耗的电流和电压
[ ] bind //收集BIND DNS统计信息
[ ] ceph //来自Ceph分布式存储系统的统计信息
[ ] chrony //从实时NTP服务器收集NTP数据
[ ] cgroups //收集CGroups CPU使用率统计信息
[ ] conntrack //收集Linux连接跟踪表中的条目数
[ ] contextswitch //收集由操作系统完成的上下文切换的数量
[ ] cpu //收集CPU在各种状态下花费的时间
[ ] cpufreq //收集当前的CPU频率
[ ] cpusleep //测量深度睡眠模式下CPU花费的时间
[ ] curl //使用libcurl读取文件，然后根据配置解析它们
[ ] curl-json //使用cURL库查询JSON数据，使用YAJL根据用户配置进行解析
[ ] curl-xml //使用libcurl读取文件并将其解析为XML
[ ] df //收集文件系统使用情况信息
[ ] disk //收集硬盘和分区的性能统计信息
[ ] dns //使用libpcap收集DNS流量的统计信息
[ ] drbd //收集单个drbd资源统计信息
[ ] entropy //收集系统上的可用熵
[ ] ethstat //收集单个drbd资源统计信息
[ ] exec //执行脚本并读回该程序打印到STDOUT的值
[ ] fhcount //文件处理统计
[ ] filecount //计算目录及其所有子目录中的文件数
[ ] fscache //收集有关网络文件系统和基于文件系统的缓存结构信息
[ ] gps //报告GPS接收器看到的卫星数量和精度
[ ] hugepages //报告Linux上已使用和空闲的大页面数
[ ] interface //收集有关网络接口流量的信息
[ ] ipc //IPC计数器：使用的信号量，共享内存中分配的段数等
[ ] iptables //从iptables数据包过滤器收集统计信息。
[ ] ipvs //从LVS项目的传输层负载均衡器IP虚拟服务器(IPVS)中提取统计信息
[ ] irq //收集中断数量
[ ] load //收集系统负载
[ ] md //收集软件RAID设备信息
[ ] memcachec //从memcache守护程序查询和解析数据
[ ] memcached //从memcached守护程序收集统计信息
[ ] memory //收集物理内存利用率
[ ] modbus //通过Modbus/TCP从Modbus"从机"读取寄存器值
[ ] mysql //连接到MySQL数据库并发出"显示状态"命令
[ ] netlink //获取接口、qdiscs、类和过滤器的统计数据
[ ] nfs //收集有关网络文件系统使用情况的信息
[ ] nginx //收集nginx守护程序处理的请求数以及按状态收集的当前连接数
[ ] ntpd //查询NTP服务器并提取参数
[ ] olsrd //从olsrd读取有关网状网络的信息
[ ] openldap //从OpenLDAP的cn=Monitor子树中读取监视信息
[ ] openvpn //读取OpenVPN的状态文件以收集统计信息
[ ] ping //使用ICMP"回显请求"确保网络延迟
[ ] postgresql //连接PostgreSQL数据库执行SQL语句，将返回的值转换为"值列表"
[ ] processes //收集按状态分组的进程数
[ ] protocols //收集有关网络协议的信息
[ ] sensors //从流明传感器收集数据
[ ] serial //收集串行接口上的流量
[ ] SMART //收集SMART统计信息，特别是负载周期计数，温度和坏扇区
[ ] StatsD //StatsD网络协议，允许客户端报告"事件"
[ ] snmp //从SNMP设备读取值
[ ] swap //收集当前写入磁盘的内存量
[ ] table //解析表状结构的纯文本文件
[ ] tail //尾巴日志文件，每一行都有一个或多个匹配项
[ ] tail csv //根据(尾部)CSV格式的文件，解析每一行并提交提取的值
[ ] tcpconns //计算与指定端口之间的TCP连接数
[ ] thermal //读取ACPI热区信息
[ ] uptime //跟踪系统正常运行时间
[ ] users //计算当前登录的用户数
[ ] vmem //收集有关虚拟内存子系统的信息
[ ] wireless //收集WLAN卡的信号质量，功率和噪声比
[ ] zookeeper //从Zookeeper的MNTR命令读取数据
target plugins ---> //目标插件
[ ] notification //创建和发送通知
[ ] replace //使用正则表达式替换标识符的各个部分
[ ] scale //用任意数字缩放(乘)值
[ ] set //设置(覆盖)标识符的整个部分
write plugins ---> //写插件
[ ] csv //将值以逗号分隔值格式写入纯文本文件
[ ] graphite //将收集的数据写入Carbon(Graphite's)存储API
[ ] mqtt //向MQTT代理发送指标和/或从MQTT代理接收指标
[ ] network //从其他收集的实例发送/接收值
[ ] rrdtool //将值写入RRD文件
[ ] riemann //将数据发送到流处理和监视系统Riemann
[ ] unixsock //打开UNIX域套接字并接受连接，可将命令发送到守护程序并接收信息
[ ] write_http //使用HTTP POST和PUTVAL将收集的值发送到Web服务器
[ ] write_log //将数据写入日志
[ ] write_prometheus //使用嵌入式HTTP服务器以与Prometheus的collectd_exporter兼容
[ ] write_sensu //通过Sensu客户端本地TCP套接字将数据发送到流处理和监视系统Sensu
[ ] write_tsdb //发送数据OpenTSDB，可扩展的无主数据库，无共享状态时间序列数据库
[ ] domoticz //一个家庭自动化系统，监视和配置各种设备，如：灯、开关、传感器等
[ ] empty //在伪终端(PTY)会话下运行进程和应用程序
[ ] gnuradio //提供信号处理模块来实现软件无线电
[ ] Google font directory //Google字体提供的字体文件
[ ] gqrx //使用GNU Radio和Qt GUI工具箱实现的开源无线电(SDR)接收器
[ ] gsettings-desktop-schemas //包含GSettings模式的集合，用于由桌面的各个组件共享的设置
[ ] haveged //一个易用、不可预测的随机数生成器，基于 HAVEGE 算法
[ ] linux-syscall-support (lss) //提供系统调用的头文件
[ ] mcrypt //允许开发人员使用各种加密功能，而无需对其代码进行大幅度更改
[ ] mobile-broadband-provider-info //移动宽带提供商数据库
[ ] proj //可将地理经度和纬度坐标转换为笛卡尔坐标(反之亦然)
[ ] QEMU //一种通用的开源计算机仿真器和虚拟器
[ ] qpdf //一个命令行程序，可对PDF文件进行结构化，内容保留的转换
[ ] shared-mime-info //包含常见类型的核心数据库以及用于扩展它的update-mime-database命令
[ ] taskd //任务管理同步守护程序
[ ] util-macros //包含M4被所有的使用宏的Xorg包
```

#### 2.6.14 Networking applications （待完善）

```
====Target packages     =====
====Networking applications ====
[ ] aircrack-ng
[ ] aoetools
[ ] apache
[ ] argus
[ ] arp-scan
[ ] arptables
[ ] asterisk
[ ] atftp
[ ] autossh
[ ] avahi
[ ] axel
[ ] babeld
[ ] bandwidthd
[ ] batctl
[ ] bcusdk
[ ] bind
[ ] bird
[ ] bluez-tools
[ ] bluez-utils
[*] bluez-utils 5.x
[*] build OBEX support
[*] build CLI client
[*] install deprecated tool
[*] build experimental obexd plugin
[*] build health plugin
[*] build midi profile
[*] build nfc plugin

[*] build health plugin
[*] build midi profile
[*] build nfc plugin
[*] build sap plugin
[*] build sixaxis plugin
[ ] build tests
[*] bmon
[ ] boa
[ ] boinc
[ ] brcm-patchram-plus
[ ] bridge-utils
[ ] bwm-ng
[ ] c-icap
[*] can-utils
[ ] cannelloni
[ ] chrony
[ ] civetweb
[ ] connman
*** connman-gtk needs libgtk3 and a glibc or uClibc toolchain w/ wchar, threads, resolver, d
[ ] conntrack-tools
[ ] corkscrew
[ ] crda
[ ] ctorrent
[ ] cups
[ ] dante
[ ] darkhttpd
[ ] dehydrated
[ ] dhcp (ISC)
[ ] dhcpcd
[ ] dhcpdump
[ ] dnsmasq
[ ] drbd-utils
[ ] dropbear
[ ] ebtables
*** ejabberd needs erlang, toolchain w/ C++ ***
[ ] ethtool
[ ] faifa
[ ] fastd
[ ] fcgiwrap
[ ] flannel
[ ] fping
[ ] freeswitch
*** gerbera needs a toolchain w/ C++, threads, gcc >= 7 ***
[ ] gesftpserver
[ ] glorytun
*** gupnp-tools needs libgtk3 ***
[ ] hans
[ ] haproxy
[ ] hiawatha
[ ] hostapd
[ ] httping
[ ] i2pd
[ ] ibrdtn-tools
[ ] ibrdtnd
[ ] ifenslave
[ ] ifplugd
[ ] iftop
[ ] ifupdown
[*] ifupdown scripts
[ ] igd2-for-linux
[ ] igh-ethercat
[ ] igmpproxy
[ ] inadyn
[ ] iodine
[ ] iperf
[*] iperf3
[ ] iproute2
[ ] ipsec-tools
[ ] ipset
[ ] iptables
[ ] iptraf-ng
[ ] iputils
[ ] irssi
[*] iw
[ ] iwd
[ ] janus-gateway ----
[ ] keepalived
[ ] kismet
[ ] knock
[ ] leafnode2
[ ] lft
[ ] lftp
[*] lighttpd
[*] openssl support
[*] zlib support
[*] bzip2 support
[*] pcre support
[*] webdav support
[ ] linknx
[ ] links
[ ] linphone
[ ] linux-zigbee
[ ] linuxptp
[ ] lldpd
[*] lrzsz
[ ] lynx
[ ] macchanger
[ ] memcached
[ ] mii-diag
[ ] mini-snmpd
[ ] minidlna
[ ] minissdpd
[*] mjpg-streamer
[ ] modem-manager
[ ] mongrel2
[ ] monkey
[ ] mosh
[ ] mosquitto
[ ] mrouted
[ ] mtr
[ ] nbd
[ ] ncftp
[ ] ndisc6 tools
[ ] netatalk
[ ] netcat
[ ] netcat-openbsd
[ ] netplug
[ ] netsnmp
[ ] netstat-nat
[ ] net-tools
[ ] network-manager
[ ] nfacct
[ ] nftables
[ ] nginx ----
[ ] ngircd
[ ] ngrep
[ ] nload
[ ] nmap
[ ] noip
[ ] ntp
[ ] nuttcp
[ ] odhcp6c
[ ] odhcploc
[ ] olsr
[ ] open-lldp
[ ] open-plc-utils
[ ] openntpd
[*] openobex
[ ] openresolv
[*] openssh
[ ] openswan
[ ] openvpn
[ ] p910nd
[ ] phidgetwebservice
[ ] phytool
[ ] pimd
[ ] pixiewps
[ ] pound
[ ] pppd
[ ] pptp-linux
[ ] privoxy
[ ] proftpd
*** prosody needs the lua interpreter, dynamic library ***
[ ] proxychains-ng
[ ] ptpd
[ ] ptpd2
[ ] pure-ftpd
[ ] putty
[ ] quagga
*** rabbitmq-server needs erlang ***
[ ] radvd
[ ] reaver
[ ] rp-pppoe
[ ] rpcbind
[ ] rsh-redone
[ ] rsync
[ ] rtorrent
[ ] rtptools
[ ] rygel
[ ] s6-dns
[ ] s6-networking
[ ] samba4
[ ] sconeserver ----
[ ] ser2net
[ ] shadowsocks-libev
[ ] shairport-sync
[ ] shellinabox
[ ] smcroute
[ ] sngrep
[ ] snort
[ ] socat
[ ] socketcand
[ ] softether
[ ] spawn-fcgi
[ ] spice protocol
[ ] squid
[ ] sshpass
[ ] sslh
[ ] strongswan ----
[ ] stunnel
[ ] tcpdump
[ ] tcping
[ ] tcpreplay
[ ] tftpd
[ ] thttpd
[ ] tinc
[ ] tinyhttpd
[ ] tor
[ ] traceroute
[ ] transmission
[ ] tunctl
[ ] tvheadend
[ ] udpcast
[ ] uhttpd
[ ] ulogd
[ ] ushare
[ ] ussp-push
[ ] vde2
[ ] vdr
[ ] vnstat
[ ] vpnc
[ ] vsftpd
[ ] vtun
[ ] wavemon
[*] wget
[ ] whois
[ ] wireguard
[ ] wireless-regdb
[ ] wireless tools
[ ] wireshark
[*] wpa_supplicant
[*] Enable nl80211 support
[*] Enable AP mode
[*] Enable Wi-Fi Display
[*] Enable mesh networking
[*] Enable autoscan
[*] Enable EAP
[*] Enable HS20
[*] Enable syslog support
[*] Enable WPS
[*] Install wpa_cli binary
[*] Install wpa_client shared library
[*] Install wpa_passphrase binary
[*] Enable support for old DBus control interface
[*] Enable support for new DBus control interface
[*] Introspection support
[ ] wpan-tools
[ ] xinetd
[ ] xl2tp
[ ] xtables-addons
[ ] znc
```

#### 2.6.15 Package managers

```
====Target packages     =====
====Package managers ====


* ------------------------------------------------------- *
* Please note: *
* - Buildroot does not generate binary packages, * //Buildroot不会生成二进制软件包
* - Buildroot does not install any package database. * //Buildroot不会安装任何软件包数据

---

* It is up to you to provide those by yourself if you * //如果想使用任何软件包管理器，需要自己提供
* want to use any of those package managers. *

---

* See the manual: *
* http://buildroot.org/manual.html#faq-no-binary-packages *
* ------------------------------------------------------- *
[ ] opkg //Opkg是基于ipkg的轻量级软件包管理系统，类似于apt/dpkg
[ ] rpm //RPM软件包管理器
```

#### 2.6.16 Real-Time

```
==== Target packages    =====
==== Real-Times          ====

[ ] Xenomai Userspace //Linux实时时钟框架
```
#### 2.6.17 Security

```
====Target packages  =====
====Security  ====
[ ] checkpolicy //SELinux(Security-Enhanced Linux)策略编译器
[ ] paxtest //PaX回归测试套件
[ ] policycoreutils //SELinux策略程序的集合(包含load_policy、newrole、run_init、secon、semodule、sestatus等)
[ ] refpolicy //一个完整的SELinux策略，可以用作各种系统的系统策略，也可以用作创建其他策略的基础
[ ] restorecond //一个守护程序，它监视文件创建，然后为该文件设置默认的SELinux文件上下文
[*] selinux-python ---> //selinux的python模块
[ ] audit2allow //该模块安装两个程序:audit2allow和audit2why
[ ] sepolgen //一个Python模块，可让生成初始的SELinux策略模块模板
[ ] semodule-utils //包含用于处理selinux模块的工具:semodule_deps、semodule_expand、semodule_link等
[ ] setools //SELinux策略分析软件包：apol、sediff-SELinux、sedta、seinfoflow-SELinux等
```
####  2.6.18 Shell and utilities

```
====Target packages  =====
====Shell and utilities  ====
* Shells * //命令解析器
-- bash //标准的GNU Bourne shell
[ ] dash //非常小的/bin/sh的POSIX兼容shell
[ ] mksh //MirBSD Korn Shell，一个紧凑、快速、可靠、安全、又不断扩展的shell
[ ] zsh //bash、ksh和tcsh的许多有用功能已合并到zsh中，又增加了许多原始功能
* Utilities * //实用工具
[ ] at //在未来的某个时间点定期执行一次任务(一条/多条命令组合，或脚本)
[] bash completion //添加bash完成基础结构
[ ] ccrypt //用于加密和解密文件和流的程序
[ ] dialog //从shell脚本显示对话框
[ ] dtach //屏幕的分离的功能的工具，可以随意地连接到各会话上
[ ] easy-rsa //RSA证书生成、管理工具
[ ] file //通过扫描二进制数据中的已知模式来识别文件格式的程序
[ ] gnupg //GNU Privacy Guard(GnuPG或GPG)，是包含数字签名和证书的加密工具
[ ] gnupg2 //gnupg的新版本
[ ] inotify-tools //用于监视文件系统事件并对其采取行动
[ ] lockfile programs //锁文件的程序
[ ] logrotate //一个简单的轮转日志的程序
[ ] logsurfer //用于实时监视系统日志并报告事件发生情况的程序
[ ] pdmenu //用于Unix的全屏菜单系统
[] pinentry ---> //简单的PIN或短语输入对话框的集合
-- pinentry-ncurses //pinentry-ncurses工具
[ ] pinentry-gtk2 //pinentry-gtk2工具
[ ] pinentry-qt5 //pinentry-qt5工具
[ ] ranger //支持VI快捷键的控制台文件管理器
[ ] screen //一个终端切换的软件，可用于终端会话恢复
[ ] sudo //旨在允许sysadmin向用户授予有限的root特权并记录root活动
[ ] time //GNU时间程序
[ ] tini //一个简单但有效的初始化二进制文件，可充当容器的PID 1
[ ] tmux //一个终端多路复用软件，使单个终端可以访问和控制多个终端
[ ] which //“which”命令程序
[ ] xmlstarlet //命令行XML工具包
[ ] xxhash //一种非常快速的哈希算法，运行速度受限RAM
```

#### 2.6.19 System tools

```
====Target packages  =====
====System tools  ====
[ ] acl //POSIX访问控制列表，用于为文件和目录定义更细粒度的自由访问权限
[ ] android-tools //包含fastboot和adb实用程序，可使用这些协议与目标设备进行交互
[ ] atop //用于Linux的ASCII全屏性能监视器
-- attr //操纵文件系统扩展属性的命令
[ ] audit //用于存储和搜索Linux 2.6内核中的审计子系统生成的审计记录
[ ] cgroupfs-mount //cgroupfs的挂载和卸载脚本
[ ] circus //运行并观看多个进程和套接字
[ ] coreutils //所有基本的文件/文本/Shell工具
[ ] cpuload //在一定时间内获得直观的CPU负载视图
[ ] daemon //其他进程转换为守护程序
[ ] dc3dd //GNU dd程序的补丁程序
[ ] dcron //基于时间的作业调度程序，具有类似于anacron的功能
[ ] ddrescue //一种数据恢复工具
[ ] debianutils //特定于Debian的其他程序：add-shell installkernel ischroot等
[ ] docker-cli //一个平台，可作为轻量级容器构建，交付和运行应用程序
[ ] docker-compose //Docker的多容器编排
[ ] docker-containerd //控制runC的守护程序
[ ] docker-engine //docker引擎
[ ] docker-proxy //一个容器网络模型，提供一致的编程接口和应用程序所需的网络抽象
[ ] efibootmgr //Linux用户空间应用程序，用于修改Intel可扩展固件接口(EFI)引导管理器
[ ] efivar //设置EFI变量的工具和库
[ ] emlog //一个Linux内核模块，可轻松访问进程的最新(且仅最新)输出
[ ] ftop //可以监视所有打开的文件和文件系统的进度
[ ] getent //用来察看系统的数据库中的相关记录，判定用户是否存在等
[ ] htop //用于Linux的交互式文本模式进程查看器
[ ] iotop //IBM Power RAID设备的系统程序
[ ] iprutils //IBM Power RAID设备的系统实用程序
[ ] irqbalance //一个守护程序，可帮助平衡由所有系统cpus的中断产生的cpu负载
-- keyutils //用于控制Linux内核中内置的密钥管理系统
-- kmod //处理内核模块
[ ] kmod utilities //安装kmod模块程序:depmod、insmod、lsmod、modinfo、modprobe、rmmod
[ ] kvmtool //用于托管KVM guest虚拟机的轻量级工具
[ ] libostree //OSTree是用于基于Linux的操作系统的升级系统
[ ] lxc //Linux Containers，一种基于容器的操作系统层级的虚拟化技术
[ ] mender //用于嵌入式Linux设备的开源无线(OTA)软件更新程序
[ ] monit //用于管理和监视UNIX系统上的进程，程序，文件，目录和文件系统
[ ] ncdu //具有ncurses接口的磁盘使用情况分析器
[ ] nut //Network UPS Tools(NUT)，为电力设备提供支持，例如不间断电源等
[ ] pamtester //用于测试可插入身份验证模块(PAM)功能的微型程序
[ ] polkit //用于定义和处理授权的工具包
[ ] procps-ng //标准的信息程序和流程处理工具，如kill、ps、uptime、free、top等
[ ] procrank_linux //Android平台开发人员常用的工具，用于找出实际使用了多少内存
[ ] psmisc //与/proc相关程序，如pstree，fuser，killall
[ ] pwgen //GPL的小型密码生成器，它创建的密码可以很容易地被人记住
[ ] quota //执行磁盘配额系统
[ ] quotatool //为命令行操作文件系统配额
[] rauc //自动更新控制器，它支持通过网络(OTA)或从磁盘更新嵌入式Linux系统
[] network support //此选项支持使用libcurl通过网络更新固件
[] JSON output support //此选项启用对以JSON格式提供输出的支持
[ ] rsyslog //功能强大且灵活的syslog实现
[ ] runc //一个CLI工具，用于根据OCP规范生成和运行容器
[ ] s6 //用于daemontools和runit中的所有进程监视，和进程和守护程序的各种操作
[ ] s6-linux-init //一组用于在Linux内核上创建基于s6的初始化系统的简约工具
[ ] s6-linux-utils //一组特定于Linux的简约系统程序
[ ] s6-portable-utils //一组小型的通用Unix实用程序，通常执行常见的任务，例如cut和grep
[ ] s6-rc //基于s6的系统的服务管理器
[ ] scrub //以迭代方式在文件或磁盘设备上写入模式，从而使清除的数据更不易恢复
[ ] scrypt //使用scrypt进行密钥派生的文件加密程序
[ ] smack //Smack(Simplified Mandatory Access Control Kernel)强制访问控制内核
[ ] start-stop-daemon //用于控制系统级进程的创建和终止
[ ] supervisor //一种允许其用户控制类UNIX操作系统上的多个进程的客户机/服务器系统
[] swupdate //一种可靠的方式来更新嵌入式系统上的软件
[] syslogd & klogd //系统日志守护程序syslogd和klogd
[ ] syslog-ng //增强的日志守护程序，支持多种输入和输出方法
-- sysvinit //所有进程的父级/sbin/init的System V样式实现
[ ] tar //tar打包和还原
[ ] tpm-tools //用于管理和诊断TPM(Trusted Platform Module)的工具
[ ] tpm2-abrmd //用于实现TCG的TPM2访问代理(TAB)和资源管理器(RM)的守护程序
[ ] tpm2-tools //用于管理和诊断TPM2(Trusted Platform Module 2)的工具
[ ] unscd //用于处理正在运行的程序的passwd，组和主机的查找，并为下次查询缓存结果
-- util-linux ---> //各种有用/必要的linux库和实用程序
-- libblkid //包含用于块设备识别和标记提取的例程
[] libfdisk //包含操作分区表的例程
-- libmount //包含用于块设备挂载和卸载的例程
[ ] libsmartcols //包含以表格形式进行屏幕输出的例程
-- libuuid //含用于生成在本地系统之上可访问对象的唯一标识符的例程
[ ] basic set //安装基本的util-linux二进制文件
[ ] agetty //替代Linux Getty
[ ] bfs //SCO bfs文件系统支持
[ ] cal //显示日历或日历的一部分
[ ] chfn/chsh //更改登录shell，真实用户名和信息
[ ] chmem //设置在线或离线的特定大小或范围的内存
[ ] cramfs utilities //压缩ROM文件系统的实用程序(fsck.cramfs，mkfs.cramfs)
[ ] eject //弹出可移动媒体
[ ] fallocate //将空间预分配给文件
[ ] fdformat //低级格式化软盘
[ ] fsck //检查并修复linux文件系统
[ ] hwclock //查询或设置硬件时钟(RTC)
[ ] ipcrm //删除某些IPC资源
[ ] ipcs //显示有关IPC设施的信息
[ ] kill //向进程发送信号
[ ] last //显示最近登录用户的列表
[ ] line //读一行
[ ] logger //在系统日志中输入消息
[ ] login //在系统上开始会话
[ ] losetup //设置和控制loop设备
[ ] lslogins //显示有关系统中已知用户的信息
[ ] lsmem //列出可用内存的范围及其在线状态
[ ] mesg //控制对终端的写访问
[ ] minix //inix文件系统支持
[ ] more //文件浏览过滤器，供crt查看
[ ] mount/umount //挂载/卸载文件系统
[ ] mountpoint //查看目录是否为挂载点
[ ] newgrp //登录新群组
[ ] nologin //拒绝登录
[ ] nsenter //输入另一个进程的名称空间
[ ] pg //逐页浏览文本文件
[ ] partition utilities //分区实用程序(addpart，delpart，partx)
[ ] pivot_root //更改根文件系统
[ ] raw //构建Linux原始字符设备
[ ] rename //重命名文件
[ ] rfkill //用于启用和禁用无线设备的工具
[ ] runuser //运行具有替代用户名和组ID的命令
[ ] scheduling utilities //计划任务程序(chrt，ionice，taskset)
[ ] setpriv //使用不同的Linux特权设置运行程序
[ ] setterm //设置终端属性
[ ] su //切换到其它用户
[ ] sulogin //单用户登录
[ ] switch_root //切换到另一个文件系统，将其作为根文件系统
[ ] tunelp //为lp设备设置各种参数
[ ] ul //下划线
[ ] unshare //运行某些与父级不共享的名称空间的程序
[ ] utmpdump //以原始格式转储UTMP和WTMP文件
[ ] uuidd //UUID生成守护程序
[ ] vipw //辑密码、组、影子密码或影子组文件
[ ] wall //向每个人的终端发送消息
[ ] wdctl //显示硬件看门狗状态
[ ] write //向其他用户发送消息
[ ] zramctl //设置和控制zram设备
[ ] xen //可构建Xen虚拟机管理程序和工具堆栈
[*] xvisor ---> //一个开放源代码的Type-1虚拟机管理程序
Xvisor configuration (Using an in-tree defconfig file) --->
(generic-v7) Defconfig name //要使用的Xvisor defconfig文件的名称
[ ] Create U-Boot image of Xvisor //创建可从Das U-Boot加载的Xvisor映像文件
[ ] Build test device-tree blobs //构建测试设备树blob
```

#### 2.6.20 Text editors and viewers

```
====Target packages  =====
====Text editors and viewers  ====
[ ] ed //面向行的文本编辑器，通常在脚本中使用，而不是直接调用
[ ] joe //JOE是易于使用的，基于终端的全功能屏幕编辑器
[ ] less //出色的文本文件查看器
[ ] mc //GNU Midnight Commander是一个视觉文件管理器
[ ] nano //一个不错的基于ncurses的编辑器
[ ] uemacs //一个小的emacs
[*] vim //VIM文本编辑器
[*] install runtime //安装VIM runtime(语法突出显示+宏)，将大约11MB的数据添加到/usr/share/
```


### 2.7 Filesystem images(文件系统)

```
====Target packages  =====
====Filesystem images ====

Filesystem images --->
[ ] axfs root filesystem //XFS格式根文件系统
[ ] btrfs root filesystem //btrfs格式根文件系统
[ ] cloop root filesystem for the target device //clop方式压缩根文件系统
[ ] cpio the root filesystem (for use as an initial RAM filesystem) //cpio方式压缩根文件系统(用作初始RAM文件系统)
[ ] cramfs root filesystem //cramf格式根文件系统
[] ext2/3/4 root filesystem //ext2/3/4格式根文件系统
ext2/3/4 variant (ext4) ---> //ext4格式根文件系统
() filesystem label //文件系统标签
(200M) exact size //根文件系统空间大小
(0) exact number of inodes (leave at 0 for auto calculation) //确切的inode数(从0开始自动计算)
(5) reserved blocks percentage //保留块百分比 (保留的供root使用,默认5%)
(-O ^64bit) additional mke2fs options //额外的mke2fs选项 [禁用64位文件系统]
Compression method (no compression) ---> //压缩方式 [无压缩]
[ ] f2fs root filesystem //f2fs格式根文件系统
[ ] initial RAM filesystem linked into linux kernel //初始RAM文件系统链接到Linux内核
[ ] jffs2 root filesystem //jffs2格式根文件系统
[ ] romfs root filesystem //romfs格式根文件系统
[ ] squashfs root filesystem //squashfs格式根文件系统
[] tar the root filesystem //tar压缩格式根文件系统
Compression method (no compression) ---> //压缩方式 [无压缩]
() other random options to pass to tar //传递给tar的其他选项
[ ] ubi image containing an ubifs root filesystem //ubifs格式根文件系统包含ubi镜像
[ ] ubifs root filesystem //ubifs格式根文件系统
[ ] yaffs2 root filesystem //yaffs2格式根文件系统
```

借此机会，把几个文件系统给记录一下。

- 1.什么是文件系统
  文件系统，就是文件的储存方式。简单说，它就是一个门牌系统，为储存设备划分门牌号，每个文件分配一个门牌，然后就能按照门牌找到文件。
  没有文件系统的硬盘，就是一块荒地。如果有人住在那里，你只能说那里有人住，精确位置你说不出来。只有划分了路牌，你才能说出，这个人住在"人民路15号"，这样才能精确定位。文件系统就是路牌的划分方法。
  储存设备都需要指定文件系统，计算机才能读写。所谓"格式化"，就是为硬盘安装文件系统。不同的操作系统有不同的文件系统，Linux使用ext4，OSX使用HFS+，Windows使用NTFS，Solaris和Unix使用ZFS。如果计算机不认识某个文件系统，就会显示这块盘无法读写。 

- 2.Windows的文件系统
  Windows里的文件系统主要有三种：FAT32、NTFS、exFAT。
  FAT32是最老的文件系统，所有操作系统都支持，兼容性最好。但是，它是为32位计算机设计的，单个文件不能超过2的(32-1)次方个字节，也就是不能超过4GB，分区不能超过8TB。也就是为什么一些U盘无法一次性拷贝4G以上的大文件；
  NTFS是Windows的默认文件系统，用来替换FAT32，Windows的系统盘只能使用这个文件系统；
  exFAT可以看作是FAT32的64位升级版，ex就是extended的缩写，表示"扩展的FAT32"，功能不如NTFS，但是解决了文件和分区的大小问题，最大分区可以到8PB； 

- 3.分区表
  硬盘分区，是指一块硬盘上面，同时存在多个文件系统。
  每个文件系统管理的区域，就称为一个分区(partition),比如一块100GB的硬盘，可以一半是NTFS分区，另一半是exFAT分区。
  硬盘必须先分区，才能指定每个区的文件系统。分区大小、起始位置、结束位置、文件系统等信息，都储存在分区表里面。
  分区表也分成两种所谓硬盘分区，就是指一块硬盘上面，同时存在多个文件系统。每个文件系统管理的区域，就称为一个分区（partition）。比如，一块 100 GB 的硬盘，可以一半是 NTFS 分区，另一半是 exFAT 分区。
  硬盘必须先分区，才能指定每个区的文件系统。分区大小、起始位置、结束位置、文件系统等信息，都储存在分区表里面。
  分区表也分成两种格式：MBR 和 GPT。前者是传统格式，兼容性好；后者更现代，功能更强大。一般来说，都推荐使用 GPT格式：MBR和GPT。前者是传统格式，兼容性好；后者更现代，功能更强大。一般来说，都推荐使用GPT。
- 4.buildroot中提到的几种文件系统
  xfs:XFS最早针对IRIX操作系统开发，是一个高性能的日志型文件系统，能够在断电以及操作系统崩溃的情况下保证文件系统数据的一致性。它是一个64位的文件系统，后来进行开源并且移植到了Linux操作系统中。XFS引入分配组、B+树、extent等方法来改进海量高并发小文件场景下性能。
  btrfs:通常念成Butter FS，Better FS或B-treeFS，由Oracle于2007年宣布并进行中的COW(copy-on-write式)文件系统。继ext3/4文件系统之后，比较强大的Linux文件系统，具有快照，内建磁盘阵列(RAID)支持，支持子卷等功能，允许在线调整文件系统大小。
  cramfs:一个压缩式的文件系统，它并不需要一次性地将文件系统中的所有内容都解压缩到内存之中，而只是在系统需要访问某个位置的数据的时侯，马上计算出该数据在cramfs中的位置，将其实时地解压缩到内存之中，然后通过对内存的访问来获取文件系统中需要读取的数据。
  romfs:一种简单的只读文件系统，主要是用来当做初始文件系统来使用的，在嵌入式linux或是uclinux中通常使用这中文件系统来作为引导系统的文件系统，甚至uclinux有时就直接把ROMFS作为其根文件系统，而不是将其作为系统启动中的过渡文件系统。
  squashfs:一个只读的文件系统，它可以将整个文件系统压缩在一起，存放在某个设备，某个分区或者普通的文件中。如果将其压缩到一个设备中，那么可以将其直接mount起来使用，而如果它仅仅是个文件的话，您可以将其当为一个loopback设备使用。
  f2fs:Flash-Friendly File System是一种闪存文件系统，主要由三星集团研发，适合Linux内核使用。此文件系统起初是为了NAND闪存的存储设备设计(诸如固态硬盘、eMMC和SD卡)，这些设备广泛存在于自移动设备至服务器领域。
  jffs2:磁盘文件系统(ext2, FAT)设计运行在磁盘上，在运行在闪存上时，需要闪存转换层(Flash Translation Layer), 它的功能就是将底层的闪存模拟成一个具有512字节扇区大小的标准块设备(block device)，从而模仿磁盘。这势必带来写操作性能的下降，更好的解决问题的方法就是实现一个特别针对闪存的文件系统，而JFFS2就是一个这样的文件系统。
  yaffs2:Yet Another Flash File System，是一种类似于JFFS/JFFS2的专门为Flash设计的嵌入式文件系统。与JFFS相比，它减少了一些功能， 因此速度更快、占用内存更少。
  ubifs:无序区块映像文件系统(Unsorted Block Image File System)，一种用于固态硬盘存储设备的文件系统，它与LogFS相互竞争，是JFFS2的后继文件系统之一。UBIFS在设计与性能上均较YAFFS2、JFFS2更适合大容量的NAND flash。
- 5.其它提到名词解释
  cloop:cloop是一个Linux内核模块，支持压缩回环文件系统。有了它，您可以安装压缩文件系统，如块设备，并在访问时无缝解压缩其数据。
  cpio:cpio是一个非常古老的归档工具。已逐渐被tar替代，但是有些功能是tar不存在的。

参考资料：
exFAT 文件系统指南
如何选择文件系统：EXT4、Btrfs 和 XFS
深入理解ext4等Linux文件系统
JFFS2 文件系统及新特性介绍 - IBM
UBI文件系统详细介绍

### 2.8 Bootloaders(引导程序)

```
==== 一级配置菜单：Bootloaders ===

Bootloaders --->
[ ] afboot-stm32 //STM32平台的一个非常小的引导加载程序
[ ] Barebox //Barebox引导程序,以前叫做U-Boot v2
[ ] grub2 //GRUB是一个多重引导加载程序
[ ] mxs-bootlets //适用于飞思卡尔iMX23/iMX28 SoC的Stage1引导加载程序
[ ] s500-bootloader //适用于Actions Semiconductor S500 SoC的第一级引导加载程序
[ ] ts4800-mbrboot //TS4800板的第一级引导加载程序
[] U-Boot //U-boot
Build system (Legacy) ---> //构建系统 [遗留]
(mx6ull_14x14_evk) U-Boot board name //U-Boot单板名称
U-Boot Version (Custom Git repository) ---> //U-Boot版本 [自定义Git仓库]
(https://git.dev.tencent.com/……) URL of custom repository //自定义仓库网址 ①
(origin/master) Custom repository version //自定义仓库版本
() Custom U-Boot patches //自定义U-Boot补丁
[] U-Boot needs dtc //U-Boot设备树可用
[ ] U-Boot needs pylibfdt //U-Boot Python libfdt库可用
[ ] U-Boot needs OpenSSL //U-Boot OpenSSL可用
[ ] U-Boot needs lzop //U-Boot lzop解压缩可用
U-Boot binary format ---> //U-Boot二进制文件格式
[ ] u-boot.ais //TI定义的格式,OMAP-L1系列处理器
[ ] u-boot.bin //常规二进制格式bin
[ ] u-boot-dtb.bin //bin格式,包含设备树dtb
[ ] u-boot-dtb.img //img格式,包含设备树dtb
[ ] u-boot-dtb.imx //imx格式,包含设备树dtb
[ ] u-boot.img //img格式,在bin格式上加入包含地址信息的头部
[*] u-boot.imx //imx格式,在bin格式上加入包含DRAM配置信息等的头部
[ ] u-boot-nand.bin //bin格式,适合nand
[ ] u-boot.kwb (Marvell) //适用Marvell系列芯片
[ ] u-boot.elf //可执行和链接格式(executable and link format)
[ ] u-boot.sb (Freescale i.MX28) //适用飞思卡尔i.MX28
[ ] u-boot.sd (Freescale i.MX28) //适用飞思卡尔i.MX28
[ ] u-boot.nand (Freescale i.MX28) //适用飞思卡尔i.MX28
[ ] Custom (specify below) //自定义[选定后在下面指定]
[ ] Install U-Boot SPL binary image //安装SPL到U-Boot
[ ] Environment image ---- //镜像环境
[ ] Generate a U-Boot boot script //生成U-Boot启动脚本
```
①：受限每行字数，该处完整链接为：https://git.dev.tencent.com/weidongshan/imx6ull_uboot.git；



### 2.9 Host utilities(主机工具)

```
==== 一级配置菜单：Host utilities  ===
Host utilities --->
[ ] host aespipe //AES加密或解密管道
[ ] host android-tools //包含fastboot和adb实用程序
[ ] host btrfs-progs //btrfs文件系统实用程序
[ ] host cargo //Rust编程语言的包管理器
[ ] host cbootimage //将BCT(引导配置表)镜像写入基于Tegra的设备的Flash中
[ ] host checkpolicy //SELinux编译器
[ ] host checksec //检查可执行文件的属性
[ ] host cmake //构建，测试和打包软件
[ ] host cramfs //生成和检查cramfs文件系统
[ ] host cryptsetup //操作dm-crypt和luks分区以进行磁盘加密
[ ] host dfu-util //下载固件并将其上传到通过USB连接的设备
[ ] host dos2unix //在CRLF和LF之间转换文本文件行结尾
-- host dosfstools //创建和检查DOS FAT文件系统
-- host dtc //设备树文件编译、反编译、查看
-- host e2fsprogs //EXT2/3/4文件系统实现工具
[ ] host e2tools //读写、操作ext2/ext3文件系统中的文件
[ ] host f2fs-tools //用于Flash-Friendly File System的工具(F2FS)
[ ] host faketime //伪造的系统时间到程序
[ ] host fwup //可编写脚本的嵌入式Linux固件更新创建者和运行者
[ ] host genext2fs //生成ext2文件系统作为普通(非root)用户
[] host genimage //给定根文件系统树,生成多个文件系统和闪存映像的工具
[ ] host genpart //生成由命令行参数定义的16字节分区表条目
[ ] host gnupg //GNU Privacy Guard(GnuPG或GPG)加密软件
[ ] host gptfdisk //由gdisk和sgdisk程序组成的文本模式分区工具,用于全局唯一标识符(GUID)分区表(GPT)磁盘
[ ] host imx-mkimage //imx镜像制作工具
[ ] host imx-usb-loader //Freescale i.MX5x/6x/7x/8x和Vybrid SoC USB上下载和执行代码的工具
[ ] host jq //创建/编辑/合并/检查JSON文件
[ ] host jsmin //删除JavaScript文件中注释和不必要的空格
[ ] host lpc3250loader //LPC3250平台上加载/刻录程序
[ ] host lttng-babeltrace //跟踪读写库以及跟踪转换器的应用程序
[ ] host mfgtools //使用Freescale UTP协议通过USB将固件编程到i.MX板
[ ] host mkpasswd //随机密码生成工具
[ ] host mtd, jffs2 and ubi/ubifs tools //构建mtd、jffs2和ubi/ubifs工具
[] host mtools //用于从Unix访问MS-DOS磁盘而不安装它们
[ ] host mxsldr //通过串行下载协议在Freescale i.MX23和i.MX28 SoC上下载和执行代码的工具
[ ] host omap-u-boot-utils //用于TI OMAP平台生成特定的U-Boot签名镜像文件等的工具
[ ] host openocd //OpenOCD-开源片上调试器
[ ] host opkg-utils //用于opkg包管理器的帮助程序脚本
[ ] host parted //GNU分区调整大小程序
[ ] host pkgconf //为开发框架配置编译器和链接器标志的程序
[ ] host pru-software-support //为某些TI处理器上的PRU单元提供了有用的标头和库,例如:AM3358
[ ] host pwgen //密码生成器
[ ] host python-cython //用于编写Python语言的C扩展的Cython编译器
[ ] host python-lxml //用于处理XML和HTML
[ ] host python-six //Six是Python2和3兼容库,目的是编写兼容两个Python版本的Python代码
[ ] host python-xlrd //用于从Microsoft Excel电子表格文件中提取数据的库
[ ] host qemu //一个通用的开源机器模拟器和虚拟器
[ ] host raspberrypi-usbboot //用于Raspberry Pi通过USB启动
[ ] host rauc //用于生成由目标rauc服务处理的固件包
[ ] host rcw //供复位配置字(RCW)编译器,用于构建NXP QoriQ/LS PBL/RCW二进制文件
[ ] host rustc //Rust语言的编译器
[ ] host sam-ba //用于Atmel SAM3、SAM7和SAM9 Soc的编程
[ ] host squashfs //生成SquashFS文件系统的工具
[ ] host sunxi-tools //用于Allwinner A10(又名sun4i)和A13(又名sun5i)设备的工具
[ ] host swig //用于将C和C ++编写的程序与各种高级编程语言连接起来
[ ] host tegrarcm //用于在恢复模式下将代码发送到Tegra设备
[ ] host ti-cgt-pru //为某些TI处理器上的PRU单元提供代码生成工具,例如:AM3358
-- host u-boot tools //用于Das U-Boot引导程序的配套工具
[ ] Flattened Image Tree (FIT) support //拼合镜像(FIT)支持
-*- host util-linux //各种有用/必要的Linux库和实用程序,像mkfs、mkswap、swapon、fdisk、mount，dmesg等
[ ] host utp_com //用于通过飞思卡尔的UTP协议向硬件发送命令(类似于MFGTools)
[ ] host vboot utils //Chromium OS验证了启动实用程序
[ ] host xorriso //将符合POSIX标准的文件系统中的文件对象复制到Rock Ridge增强的ISO 9660文件系统中
[ ] host zip //zip压缩包解压和压缩
[ ] host zstd //快速无损压缩方式
```
### 2.10 Legacy config options(旧版配置选项)

```
==== 一级配置菜单： Legacy config options ===
```

一些陆续被移除buildroot的配置选项，忽略。

## 3.Buildroot目录结构

使用一个编译过imx6ull的buildroot，在此基础上分析目录结构。
执行dir -AFl --group-directories-first可按先目录、后文件的顺序显示buildroot根目录的所有文件。

在分析目录内容之前，先简单了解下*.mk和Config.in/Config.in.host 这两类文件，能加快后面分析、理解。
Config.in/Config.in.host:包含配置信息，即执行make menuconfig，出现选项内容，Config.in用于目标，Config.in.host用于主机;
*.mk:根据前面的配置信息执行相应动作;

```
arch/ //存放CPU架构相关的配置文件Config.in.arm、Config.in.x86等，以及架构相关的构建脚本
board/ //存放某个具体单板紧密相关的文件，比如内核配置文件、sd卡制作脚本、rootfs覆盖文件等
boot/ //存放各种Bootloaders相关的的补丁.patch、校验文件.hash、构建脚本.mk、配置选项Config.in
configs/ //存放各个单板的buildroot配置文件
dl/ //存放所有下载的程序、源码压缩包、补丁等
docs/ //存放PDF、HTML格式的buildroot详细说明
fs/ //存放各种文件系统的构建脚本.mk和配置选项Config.in
linux/ //存放Linux的构建脚本.mk和配置选项Config.in
output/ //存放编译后的各种文件，包括所有软件的解压、编译后的现场、交叉编译工具链、生成的内核、根文件系统镜像等
package/ //存放所有软件包的构建脚本.mk、配置选项Config.in，以及软件下载、解压、编译的构建脚本pkg-.mk
support/ //存放一些为bulidroot提供功能支持的脚本、配置文件
system/ //存放制作根文件系统的配置文件，设备节点的模块等
toolchain/ //存放制作各种交叉编译工具链的构建脚本.mk和配置选项Config.in
utils/ //存放一些buildroot的实用脚本和工具
CHANGES //buildroot修改日志
.config //make menuconfig后生成的最终配置文件
Config.in //所有Config.in的入口，也是Build options的提供者
Config.in.legacy //Legacy config options的提供者
.config.old //上次buildroot的备份，执行make oldconfig将.config.old替换.config
COPYING //buildroot版权信息
DEVELOPERS //开发人员列表，N后面是开发人员名字，F后面是开发的软件
Makefile //顶层Makefile
Makefile.legacy //旧的Makefile，为了支持向后兼容
README //buildroot简单说明
```
打开Config.in，可以看到如下内容：
```
source "arch/Config.in"
menu "Build options"
menu "Commands"
config BR2_WGET
string "Wget command"
default "wget --passive-ftp -nd -t 3"
    …… //省略Build options部分内容
source "toolchain/Config.in"
source "system/Config.in"
source "linux/Config.in"
source "package/Config.in"
source "fs/Config.in"
source "boot/Config.in"
source "package/Config.in.host"
source "Config.in.legacy"
source "$BR2_BUILD_DIR/.br2-external.in"
```
结合执行make menuconfi显示的一级菜单，可以发现本Config.in串联起了所有Config.in，在用户配置完后，生成.config，.*mk就根据.config的配置内容，进行对应的构建。

配置选项文件路径

Target optionsarch/Config.inBuild options./Config.inToolchaintoolchain/Config.inSystem configurationsystem/Config.inKernellinux/Config.inTarget packagespackage/Config.inFilesystem imagesfs/Config.inBootloadersboot/Config.inHost utilitiespackage/Config.in.hostLegacy config options./Config.in.legacy

以上目录中，有几个需要详细说明下，下面开始分析。

### 3.1 board目录

在The Buildroot user manual的9.1. Recommended directory structure章节，有该目录的推荐结构。
根据手册内容，在该目录下创建自定义文件。
①：在board/目录下新建hceng/(自定义公司名)，再在hceng/下创建imx6ull/(单板名)；
②：在imx6ull/下，应存放uboot、linux、busybox等的配置文件，在分别设置好BR2_TARGET_UBOOT_CUSTOM_CONFIG_FILE、BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE、BR2_PACKAGE_BUSYBOX_CONFIG后，如果修改了配置，分别执行make uboot-update-defconfig、make linux-update-defconfig、make busybox-update-config可以更新该目录下的配置文件。这里的uboot、linux是通过git下载，选择的自带的配置文件，BR2_TARGET_UBOOT_CUSTOM_CONFIG_FILE、BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE也就没有指定，因此没采用把配置文件放在该目录的方法。busybox使用默认配置文件路径package/busybox/busybox.config，也没有移动处理。
③：除配置文件，还应放sd卡制作脚本等，参考board/freescale/common/imx/和board/freescale/imx6ulevk/目录，将前面提到用于sd卡制作的genimage.cfg.template和post-image.sh复制到本目录下；最后，在System configuration->Custom scripts to run after creating filesystem images设置脚本路径board/hceng/imx6ull/post-image.sh；
④：除了配置文件、sd卡制作脚本，该目录还要存放根文件系统覆盖文件，新建目录rootfs_overlay/，里面可以创建/etc/init.d/S01xx，用于放置开机启动脚本。最后，在System configuration->Root filesystem overlay directories设置覆盖文件路径board/hceng/imx6ull/rootfs_overlay；
⑤：除此之外，还可以放各类patches/补丁文件，并在配置文件指定路径；
可以看出，board/就是存放某个具体单板紧密相关的文件，比如内核配置文件、sd卡制作脚本、rootfs覆盖文件等，设置后的目录结构如下：

```shell
{% codeblock %}
board/
└── hceng
└── imx6ull
├── genimage.cfg.template
├── post-image.sh
└── rootfs_overlay
└── etc
├── init.d
└── S01xx
{% endcodeblock %}3.2 output目录
```
output目录下有以下几个目录：

```
build:存放着解压后的各种软件包编译完后的现场；
graphs:执行make graph-build等命令后，生成的相关图形化文档；
host:制作好的交叉编译工具链；
images:各种最终生成的镜像；
staging:包含了根文件系统的层次结构，编译生成的所有头文件和库，以及其他开发文件，体积较大；
target:已经rootfs_overlay/处理过的根文件系统，但没有创建/dev/下的设备节点；3.3 package目录
package目录是存放所有软件包构建脚本*.mk、配置选项Config.in的目录，这里添加一个自定义APP，来理解实现原理。
```

###  3.2 添加自定义APP

①：在package/目录下，创建一个hceng_app目录；
②：在hceng_app/目录下，创建文件Config.in和hceng_app.mk，注意*.mk的文件名不能乱取，只能是app名字，且为小写；
Config.in的内容如下：

```c
config BR2_PACKAGE_HCENG_APP
bool "hceng_app"
help
This is a demo to add local app.
```

这里的配置变量BR2_PACKAGE_HCENG_APP不能乱取，只能是**BR2_PACKAGE_+APP**名字大写；
bool是变量的类型，选中为ture编译，不选中为false不编译，与Linux Kernel的Kconfig文件规则是一样的；
help是在配置界面，按下h出现的帮助提示信息；
hceng_app.mk的内容如下：

```c
################################################################################
#
# hceng_app
#
################################################################################

# HCENG_APP_VERSION = 1.0.0
# HCENG_APP_SITE_METHOD = local
# HCENG_APP_SITE = $(CURDIR)/dl/hceng_app

HCENG_APP_VERSION = master
HCENG_APP_SITE_METHOD = git
HCENG_APP_SITE = $(call github,hceng,hceng_app,$(HCENG_APP_VERSION))

HCENG_APP_ALWAYS_BUILD = YES
HCENG_APP_INSTALL_TARGET = YES

HCENG_APP_CFLAGS =
HCENG_APP_LDFLAGS =
OUT_BIN = hceng_app
HCENG_APP_MAKE_FLAGS += \
CROSS_COMPILE="$(CCACHE) $(TARGET_CROSS)" \
CC=$(TARGET_CC) \
OUT_BIN=$(OUT_BIN) \
AR=$(TARGET_AR) \
STRIP=$(TARGET_STRIP) \
CFLAGS=$(HCENG_APP_CFLAGS) \
LDFLAGS=$(HCENG_APP_LDFLAGS) \
STAGING_DIR=$(STAGING_DIR) \
TARGET_DIR=$(TARGET_DIR)

define HCENG_APP_BUILD_CMDS
#$(MAKE) clean -C $(@D)
$(MAKE) $(HCENG_APP_MAKE_FLAGS) -C $(@D)
endef

define HCENG_APP_INSTALL_TARGET_CMDS
$(INSTALL) -D -m 0755 $(@D)/$(OUT_BIN) $(TARGET_DIR)/usr/bin
endef

$(eval $(generic-package))
```
需要注意的几点：

- Ⅰ.严格按照示例的格式，1-5行的样式不能省略，第3行为app名字；-
- Ⅱ.7-8行和11-13行，为获取app的两种方式，二选一即可。7-8行是本地获取，需要提前将app文件放在HCENG_APP_SITE指定的路径，这里为dl/hceng_app；11-13行是git获取，需要提前将app文件放到git仓库，注意HCENG_APP_SITE的格式，它会调用package/pkg-download.mk，三个参数依次为用户名、包名、版本号；
- Ⅲ.HCENG_APP_BUILD_CMDS是构建过程，给应用源码的Makefile传递编译、链接参数，并调用应用源码的Makefile执行；
  Ⅳ.HCENG_APP_INSTALL_TARGET_CMDS是编译完成后，自动安装到指定目录；

③：在package/Config.ini中添加：

```
menu "Private package"
source "package/hceng_app/Config.in"
endmenu
```

新创建了一个菜单Private package，显示的配置文件为前面创建的package/hceng_app/Config.in；

④：创建app程序和Makefile
APP参考：

```
#include <stdio.h>
void main(void)
{
printf("Hello world.\n");
}
```
Makefile参考:

```
OBJS = hceng_app.o
CC = $(CROSS_COMPILE)gcc
OUT_BIN = hceng_app
dep_file = .$@.d

all: $(OBJS)
    $(CC) $(LDFLAGS) -g -o $(OUT_BIN) $^

%.o: %.c
    $(CC) $(CFLAGS) -Wp,-MD,$(dep_file) -c -o $@ $<

clean:
    rm -rf *.o
    rm -rf $(OUT_BIN)
    rm -f $(shell find -name "*.d")
```

根据自己需求，将这两个文件放在本地(hceng_app.mk指定的路径)或者git仓库。

⑤：编译
执行make menuconfig，勾选上自定义APPTarget packages ---> Private package ---> [*] hceng_app；
然后执行make hceng_app或make all，将编译和安装自定义APP，可以在output/target/usr/bin/下看到安装的自定义APP，最后重新烧写系统，即可使用自定义APP。

## 4.buildroot常用命令
在buildroot根目录执行make help，即可获得buildroot常用命令的提示信息，内容如下：

```
Cleaning: - 清理
make clean - 删除编译产生的文件
make distclean - 删除所有非源码文件—(包括.config)
Build: - 编译
make all - 编译所有
make toolchain - 编译工具链
make sdk - 编译sdk(Software Development Kit)
Configuration: - 配置
make menuconfig - 基于curses的buildroot配置界面(常用)
make nconfig - 基于ncursesbuildroot配置界面
make xconfig - 基于Qt的buildroot配置界面
make gconfig - 基于GTK的buildroot配置界面
make oldconfig - 解决所有.config中未解决的符号问题(symbols)
make syncconfig - 和oldconfig类似，但会额外更新依赖
make olddefconfig - 和syncconfig类似，但会将新的symbols设为默认值
make randconfig - 所有选项随机配置
make defconfig - 所有选项都询问，如果设置有BR2_DEFCONFIG，则使用它的配置
make savedefconfig - 把当前配置保存到BR2_DEFCONFIG
make update-defconfig - 类似savedefconfig
make allyesconfig - 所有配置选项都选择yes
make allnoconfig - 所有配置选项都选择no
make alldefconfig - 所有配置选项都选择default
make randpackageconfig - 软件包选项都选择随机
make allyespackageconfig - 软件包选项都选择yes
make allnopackageconfig - 软件包选项都选择no
Package-specific: - 具体的包操作
make <pkg> - 编译、安装该pkg以及其依赖
make <pkg>-source - 下载该pkg所有文件
make <pkg>-extract - 解压该pkg(解压后放在output/build/pkg名字目录下)
make <pkg>-patch - 给该pkg打补丁
make <pkg>-depends - 编译pkg的依赖
make <pkg>-configure - 编译pkg到配置这一步
make <pkg>-build - 编译pkg到构造这一步
make <pkg>-show-depends - 显示该pkg的所有依赖
make <pkg>-show-rdepends - 显示依赖该pkg的所有包
make <pkg>-show-recursive-depends - 递归显示该pkg的所有依赖
make <pkg>-show-recursive-rdepends - 递归显示依赖该pkg的所有包
make <pkg>-graph-depends - 图形化显示该pkg的所有依赖
make <pkg>-graph-rdepends - 图形化显示依赖该pkg的所有包
make <pkg>-dirclean - 清除该pkg目录(清除解压目录output/build/pkg名字)
make <pkg>-reconfigure - 从配置这一步开始重新编译pkg
make <pkg>-rebuild - 从构造这一步开始重新编译pkg
busybox: - busybox相关
make busybox-menuconfig - 配置busybox界面
uclibc: - uclibc相关
make uclibc-menuconfig - 配置uclibc界面
linux: - linux相关
make linux-menuconfig - 配置linux内核界面
make linux-savedefconfig - 保存linux内核配置
make linux-update-defconfig - 保存配置到BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE指定路径
Documentation: - 文档
make manual - 生成各种格式的帮助手册
make manual-html - 生成HTML格式的帮助手册
make manual-split-html - 生成split HTML格式的帮助手册
make manual-pdf - 生成PDF格式的帮助手册
make manual-text - 生成text格式的帮助手册
make manual-epub - 生成ePub格式的帮助手册
make graph-build - 生成图形化查看编译时间文件
make graph-depends - 生成图形化查看所有依赖文件
make graph-size - 生成图形化查看文件系统大小文件
make list-defconfigs - 显示拥有的默认配置的单板列表
Miscellaneous: - 杂项
make source - 下载所有要离线编译的源码到dl路径
make external-deps - 列出使用的所有外部包(make show-targets的详细版)
make legal-info - 显示所有包的license合规性
make printvars - 打印所有内部指定的变量值
make V=0|1 - 设置编译打印信息(0:安装编译 1:打印编译信息)
make O=dir - 指定所有文件(包括.config)输出目录
```

## 5.构建示例

### 5.1准备buildroot

以stm32mp157为例(ST首款运行Linux的处理器)，展示如何使用buildroot来构建系统的思路。
开发板使用的是米尔科技生产的MYC-YA157C。
在开始之前，思考从哪里获取支持MYC-YA157C的buildroot。

①.开发板厂家提供
购买了某个开发板厂家的开发板，看其提供的资料有没有buildroot，如果有，请无脑直接使用开发板厂家的，能够为你剩下一大堆时间去做其它的。
如果开发板厂家没有提供，比如米尔提供的是Yocto构建系统，目前没有buildroot，继续参考下面的方法。

②.buildroot官网
如果使用的是芯片原厂的公版/比较火的开发板，很可能buildroot有原生支持。
但buildroot不可能对某个开发板一直维护，因此每个开发板都有生命周期，比如firefly-rk3288开发板，在2016.02加入buildroot，2018.03被移除不再维护，因此我们如果要使用现成的配置文件，就得下载2016.02至2018.03这个时间段之间的buildroot。
可以通过查询buildroot的log msg来获取这些变化信息。

我手里这款目前来看，buildroot原生支持没戏。

③.Github搜索
作为全球最大的“同性交友”网站，可以去搜搜板子名字，看是否有类似的buildroot仓库，有的话clone下来验证试试。
我手里这款还没批量开卖，Github自然没有。

找了一圈，目前应该没有直接支持MYC-YA157C的buildroot，因此需要自己尝试添加该开发板。
添加开发板也不是从头开始添加，先尝试找找类似的板子，节约配置时间。
在GitHub上，找到一个支持stm32mp157的仓库，在buildroot官网的log msg也搜到了在2019.08添加了stm32mp157_dk的配置文件(STM32MP157C-DK是STM32的原厂公板)，到目前也没被移除。
这里准备在官方最新buildroot，添加MYC-YA157C单板。5.2准备交叉编译工具链、U-boot、Linux Kernel

①.交叉编译工具链
可以使用开发板厂家提供资料的交叉编译工具链，也可以尝试使用stm32mp157_dk的配置文件所使用的交叉编译工具链。

②.U-boot、Linux Kernel
建议直接使用开发板厂家所提供U-boot、Linux Kernel。
如果主线U-boot、Linux Kernel有对MYC-YA157C的原生支持，或者知道MYC-YA157C和STM32MP157C-DK2公版的差异且能移植，才考虑用其它的U-boot、Linux Kernel。
本文主要是分析buildroot，对U-boot、Linux Kernel的移植不深究，因此这里直接使用开发板厂家所提供U-boot、Linux Kernel。

找到开发板厂家所提供U-boot，创建个uboot-stm32mp目录，将U-boot源码放在里面，压缩为uboot-stm32mp.tar.gz，同时，根据提供的文档，得知U-boot配置文件为stm32mp15_basic_defconfig。
同理，对提供的Linux Kernel进行类似操作，得到linux-stm32mp.tar.gz，得知内核配置文件为multi_v7_defconfig，设备树文件为stm32mp157c-myir.dts。
注意，源码解压出来一定要有个总文件夹包含源码，不能解压后当前目录就是源码，不然后面buildroot使用时会出错。5.3设置buildroot
①.下载官方buildroot，使用公板配置文件
获取主线buildroot:

```
hceng@machine:~/buildroot$ git clone git://git.buildroot.net/buildroot
hceng@machine:~/buildroot$ git pull
```
查询公板的配置文件:

```
hceng@machine:~/buildroot$ ls configs/*stm32mp157*
configs/stm32mp157_dk_defconfig
```
或
```
hceng@machine:~/buildroot$ make list-defconfigs | grep "stm32mp157"
stm32mp157_dk_defconfig - Build for stm32mp157_dk
```
使用公板的配置文件:
```
make stm32mp157_dk_defconfig
```

②.按需修改配置
执行make menuconfig进入配置界面，挨个进行修改。

Target options --->

```
和CPU紧密相关，这里和公板使用的同一CPU，这里就不需要修改。
```

Build options --->
修改新配置文件保存路径和名字：

```
原：(/work/buildroot/configs/stm32mp157_dk_defconfig)) Location to save buildroot config
新：(/work/buildroot/configs/stm32mp157_myir_defconfig)) Location to save buildroot config
```

Toolchain --->

```
交叉编译工具链可以考虑是开发板厂家提供的，这里先暂时使用公板配置文件的，后面编译如果出了问题再回来修改。
```

System configuration --->
设置根文件系统目录要覆盖/添加的文件路径：

```
原：(board/stmicroelectronics/stm32mp157-dk/overlay/)) Root filesystem overlay directories
新：(board/stmicroelectronics/stm32mp157-myir/overlay/)) Root filesystem overlay directories
注:配置中出现的新目录/文件，后面统一再创建。
```

设置制作SD卡启动的脚本配置信息路径：

```
原：(-c board/stmicroelectronics/stm32mp157-dk/genimage.cfg) Extra arguments passed to custom scripts
新：(-c board/stmicroelectronics/stm32mp157-myir/genimage.cfg) Extra arguments passed to custom scripts
```


Kernel --->
内核的来源可以有多种，比如从kernel官网自动下载，从自定义github仓库下载，或者使用本地内核包。
这里选择本地包的方式，以及设置对应的内核配置文件：

```
原： Kernel version (Custom tarball) --->
($(call github,STMicroelectronics,linux,v4.19-stm32mp-r1.5)/linux-v4.19-stm32mp-r1.5.tar) URL of custom kernel tarball
() Custom kernel patches
Kernel configuration (Using a custom (def)config file) --->
(board/stmicroelectronics/stm32mp157-dk/linux.config) Configuration file path

新： Kernel version (Custom tarball) --->
(dl/linux/linux-stm32mp.tar.gz)/linux-v4.19-stm32mp-r1.5.tar
() Custom kernel patches
Kernel configuration (Using an in-tree defconfig file) --->
(multi_v7) Defconfig name
```
以及选择对应设备树文件：

```
原：(stm32mp157c-dk2) In-tree Device Tree Source file names
新：(stm32mp157c-myir) In-tree Device Tree Source file names
```

Target packages --->
软件包根据需求添加，这里保持默认即可。


Filesystem images --->
根文件系统看了下，也没啥需要特殊修改的，保持默认。

Bootloaders --->
U-boot也是有多种来源方式，这里选择本地包的方式：

```
原： U-Boot Version (Custom version) --->
($(call github,STMicroelectronics,u-boot,v4.19-stm32mp-r1.5)/linux-v4.19-stm32mp-r1.5.tar.gz) URL of custom kernel tarball
() Custom U-Boot patches
U-Boot configuration (Using an in-tree board defconfig file) --->
(stm32mp15_basic) Board defconfig
(board/stmicroelectronics/stm32mp157-dk/uboot-fragment.config) Additional configuration fragment files
新： U-Boot Version (Custom tarball) --->
(dl/uboot/uboot-stm32mp.tar.gz) URL of custom U-Boot tarball
() Custom U-Boot patches
U-Boot configuration (Using an in-tree board defconfig file) --->
(stm32mp15_basic) Board defconfig
() Additional configuration fragment files
```

Host utilities --->
一般保持默认，编译过程中遇到主机缺什么，再来勾选上。

Legacy config options
不用看。
最后，保存，退出，执行make savedefconfig将会生成configs/stm32mp157_myir_defconfig。
使用公板的配置文件后，很多配置就可以默认即可，主要关注的是Toolchain、Kernel、Bootloaders。

③.为配置准备文件
前面对配置做了一系列修改，新增了不少目录和文件，接下来要依次添加。

准备board目录的单板紧密相关的文件
参考stm32mp157-dk/目录，生成自己板子目录：

```
cp board/stmicroelectronics/stm32mp157-dk/ board/stmicroelectronics/stm32mp157-myir/ -r
```

修改overlay/boot/extlinux/extlinux.conf:

```
原：
label stm32mp15-buildroot
kernel /boot/zImage
devicetree /boot/stm32mp157c-dk2.dtb
append root=/dev/mmcblk0p4 rootwait

新：
label stm32mp15-buildroot
kernel /boot/zImage
devicetree /boot/stm32mp157c-myir.dtb
append root=/dev/mmcblk0p4 rootwait
```
在生成根文件系统时，board/stmicroelectronics/stm32mp157-myir/overlay/boot/extlinux/extlinux.conf将会添加到根文件系统的/boot/extlinux/下，U-boot在启动内核前，会读取extlinux.conf文件，得知内核、设备树所在路径。
准备U-boot和Linux Kernel

```
mkdir dl/uboot/
cp uboot-stm32mp.tar.gz dl/uboot/

mkdir dl/linux/
cp linux-stm32mp.tar.gz dl/linux/
```
###  5.4 编译、烧写
执行make all，编译完后，将在output/images/生成以下文件：

```
rootfs.ext2 rootfs.ext4 sdcard.img stm32mp157c-myir.dtb u-boot.img u-boot-spl.stm32 zImage
```
随后将SD卡接入Linux主机，将sdcard.img写入SD卡(/dev/sdb)：

```
sudo dd if=output/images/sdcard.img of=/dev/sdb
```

写入完成后，将SD卡插入板子上，启动开关选择SD卡启动，上电即可看到打印输出：

## 6.使用技巧

①.添加启动脚本
很多时候，需要系统启动后自动运行一些程序，这就需要添加启动脚本。

在前面的board/stmicroelectronics/stm32mp157-myir/下有个overlay目录，就是切入点。
可以利用overlay会覆盖根文件系统相关目录的特性，加上初始化系统方案采用systemV来实现。
在overlay里创建个etc/init.d目录，再在目录里创建S35app之类的文件，开机后即会自动运行该脚本。

②.调整根文件系统大小
在前面示例里，Filesystem images ---> (120M) exact size，即根文件系统空间设置的120MB，使用一个8G的SD卡烧写后启动，执行df -h看到整个根文件系统也只是120MB，无法扩展。
如果改为Filesystem images ---> (4096M) exact size，生成的sdcard.img文件也将是4G，烧写会非常的慢。

一个可行的方案：
设置exact size任然为120MB，修改board/stmicroelectronics/stm32mp157-myir/genimage.cfg，将rootfs分区的size改为4096M。
这样生成的sdcard.img文件大小不会变太大，进入系统后，执行resize2fs /dev/mmcblk0p4(rootfs在SD卡第4分区)，即可看到系统空间变为了4G。
再将resize2fs /dev/mmcblk0p4命令添加到启动脚本里，通过一定的判断，在首次进入系统时执行扩容即可。
另外，如果提示没有resize2fs命令，在Target packages --->Filesystem and flash utilities --->[*] e2fsprogs --->[*] resize2fs勾选上即可。

