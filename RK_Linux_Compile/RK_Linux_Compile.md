



RK Linux编译

## 1.1 下载SDK并编译，生成固件

1. shell的环境变量，只在当前shell有效，所以不要登入多个shell，会导致环境变量缺失编译失败。
2. 不要强制停止source envsetup.sh的执行，可能导致文件缺失，比如生成的makefile为空，这时候你要删除空的makefile再重新编译。如果已经输入数字错误，就在输入一个错误数字，按回车，脚本提示数字非法


```shell
1、同步代码
$.repo/repo/repo sync --no-tags

2、选择单板
$ source buildroot/build/envsetup.sh
You're building on Linux
Lunch menu...pick a combo:
1. rockchip_rk3308_release
2. rockchip_rk3308_debug
3. rockchip_rk3308_robot_release
Which would you like? [1
如选择 rockchip_rk3308_release，输入对应序号 （你的单板序号）。
注意：
这一步输错的话，建议多输入这个错误字符，再回车，强烈建议不要ctrl+c，否者可能造成编译过程中创建一半被中断，比如创建一个makefile空白，还没写入内容。编译就会遇到目标不存在等问题
注意：
shell的环境变量，只在当前shell有效，所以不要登入多个shell，会导致环境变量缺失编译失败。

3、编译
$ ./build.sh 全自动
make

4、完成编译后生成固件
$ ./mkfirmware.sh  执行 SDK 根目录下的 mkfirmware.sh 脚本

5、完成编译后
所有烧写所需的镜像将都会拷贝于 rockdev 目录。
rockdev
├── boot.img
├── misc.img
├── parameter.txt
├── recovery.img
├── MiniLoaderAll.bin（即 rk3308_loader_v1.17.101.bin）
├── oem.img
├── userdata.img
├── rootfs.img
├── trust.img
└── uboot.img
```



小技巧：编译之后，查看环境变量中各项配置：确定脚本执行的信息，以firefly为例

```
cw@SYS3:~/sdk/312x_i$ env
RK_MISC=wipe_all-misc.img   
RK_ARCH=arm        系统架构arch =arm 这说明是32位， 如果arch=arm64说明是64位
XDG_SESSION_ID=20953
RK_ROOTFS_TYPE=ext4   rootfs文件系统类型
TERM=xterm
SHELL=/bin/bash        
SSH_CLIENT=172.16.21.104 1493 22
RK_ROOTFS_IMG=rockdev/rootfs.ext4   rootfs使用的文件系统
LIBRARY_PATH=/usr/local/lib
RK_UBOOT_DEFCONFIG=rk3128            uboot使用的默认配置
OLDPWD=/home/cw/sdk/312x_i/kernel        
RK_CFG_PCBA=rockchip_rk3128_pcba
RK_PARAMETER=parameter-buildroot.txt
SSH_TTY=/dev/pts/47
USER=cw
RK_JOBS=12
RK_USERDATA_DIR=userdata_normal
RK_OEM_FS_TYPE=ext2
MAIL=/var/mail/cw
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
PWD=/home/cw/sdk/312x_i
RK_BOOT_IMG=zboot.img
LANG=en_US.UTF-8
RK_KERNEL_DTS=rk3128-fireprime          内核使用的dts
RK_KERNEL_DEFCONFIG=rockchip_linux_defconfig  内核defconfig
RK_USERDATA_FS_TYPE=ext2
TARGET_OUTPUT_DIR=/home/cw/sdk/312x_i/buildroot/output/rockchip_rk3128
SHLVL=1
HOME=/home/cw
RK_CFG_RECOVERY=rockchip_rk3128_recovery
RK_TARGET_PRODUCT=rk3128
RK_CFG_BUILDROOT=rockchip_rk3128
LOGNAME=cw
RK_OEM_DIR=oem_normal
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
SSH_CONNECTION=172.16.21.104 1493 10.10.10.190 22
RK_KERNEL_IMG=kernel/arch/arm/boot/zImage
XDG_RUNTIME_DIR=/run/user/1032
_=/usr/bin/env
cw@SYS3:~/sdk/312x_i$ 
```





## 1.2 烧写工具

请用SDK里面AndroidTool.exe ，不建议复制出来，SDK里面已经配置好了各子项名称路径，你可以直接选用。如

Windows工具：[AndroidTool](http://www.t-firefly.com/doc/download/page/id/4.html#windows_22)

提示：AndroidTool_v2.35版本：升级MBR分区的Ubuntu固件
            AndroidTool_v2.58版本：升级GPT分区的Ubuntu固件

![image-20200309223410774](RK_Linux_Compile.assets/image-20200309223410774.png)

### 1.2.1 启动模式介绍

Rockchip 平台硬件运行的几种模式如表所示，只有当设备处于 Maskrom，及 Loader
模式下，才能够烧写固件，或对板上固件进行更新操作。

| 模式            | 工具烧录 | 介绍                                                         |
| --------------- | -------- | ------------------------------------------------------------ |
| Maskrom         | 支持     | Flash 在未烧录固件时，芯片会引导进入 Maskrom 模式，可以<br/>进行初次固件的烧写；开发调试过程中若遇到 Loader 无法正常<br/>启动的情况，也可进入 Maskrom 模式烧写固件。 |
| Loader          | 支持     | Loader 模式下，可以进行固件的烧写、升级。可以通过工具单<br/>独烧写某一个分区镜像文件，方便调试。 |
| Recovery        | 不支持   | 系统引导 recovery 启动，主要作用是升级、恢复出厂设置类操<br/>作 |
| Normal<br/>Boot | 不支持   | 系统引导 rootfs 启动，加载 rootfs，大多数的开发都是在这个<br/>模式在调试的 |

进入烧写模式方式以下几种方法：
1. 未烧录过固件，上电，进入 Maskrom 模式。
2. 烧录过固件，按住 recovery 按键上电或复位，系统将进入 Loader 固件烧写模式。
3. 烧录过固件，按住 Maskrom 按键上电或复位，系统将进入 MaskRom 固件烧写模式。
4. 烧录过固件，上电或复位后开发板正常进入系统后，瑞芯微开发工具上显示“发现一个 **ADB**
**设备”或“发现一个 MSC 设备”**，然后点击工具上的按钮**“切换”，进入 Loader 模式。**
5. 烧录过固件，可在串口或 adb 命令行模式下，输入 reboot loader 命令，进入 Loader 模
式。

- Normal 模式

Normal 模式就是正常的启动过程，各个组件依次加载，正常进入系统。

- maskrom（不推荐）

```
如果芯片没烧写过，上电就是maskrom模式。这种模式用于拯救砖头机器。比如bootloader无法启动。无法进入loader正常下载。需要通过在板子上找对应的T13 C155 焊点，短接后通电，进入MASKROM模式，这些点需要问板子的生产商。
MaskRom 模式是设备变砖的最后一条防线。强行进入 MaskRom 涉及硬件操作，有一定风险， 因此仅在设备进入不了 Loader 模式情况下，方可尝试 MaskRom 模式。
```

- loder模式（推荐）

  ```
  是刷固件模式。这个模式可以刷各种镜像image，。按住recover按键再通电，通过uboot的检测进入这个模式
  ```

### 1.2.2 烧写方法

#### hotkey ##

为了用户开发方便，rockchip 定义了一些快捷键用于调试或触发某些操作。快捷键主要通过串口输入实
现：
开机长按 ctrl+c：进入 U-Boot 命令行模式；
开机长按 ctrl+d：进入 loader 烧写模式；
开机长按 ctrl+b：进入 maskrom 烧写模式；
开机长按 ctrl+f：进入 fastboot 模式；
开机长按 ctrl+m：打印 bidram/system 信息；
开机长按 ctrl+i：使能内核 initcall_debug；
开机长按 ctrl+p：打印 cmdline 信息；
开机长按 ctrl+s："Starting kernel..."之后进入 U-Boot 命令行；
开机反复按机器的 power button：进入 loader 烧写模式。但是用户需要先使能：

```
CONFIG_PWRKEY_DNL_TRIGGER_NUM
```

这是一个 int 类型的宏，用户根据实际情况定义（可理解为：灵敏度）。当连续按 power button 的次
数超过定义值后，U-Boot 会进入 loader 烧写模式。默认值为 0，表示禁用该功能。

- Maskrom模式下烧写

```
1、 进入Maskrom
如果没有烧录过系统的芯片，上电就是maskrom模式
或者reboot 命令重启，开机马上按'ctrl+c'进入uboot命令选择界面，help查看帮助，‘rbrom’进入Maskrom
或者reboot 命令重启， ctrl+b：进入 maskrom 烧写模式；

2、按上图图片，直接烧写
```

- Loader模式下烧写

```
采用loader烧写，说明芯片已经烧写过固件有loader和parameter在上面，所以可以单模块烧写，比如值烧写rootfs

1、 进入loader
方法一
reboot loader就会进入loader模式
方法二
reboot重启Ctrl+C进入uboot命令行输入  rockusb 0 mmc 0就会进入loder模式
或者 reboot重启 开机长按 ctrl+d：进入 loader 烧写模式
2、烧写
采用loader烧写，说明芯片已经烧写过固件有loader和parameter在机器，所以可以单模块烧写，比如值烧写rootfs
```

单模块烧写

```
在Maskrom下单模块烧写，并且你烧写过paramenter，那么这时候，单模块烧写，你就要选上（loader+单模块），比如说，你编译了builroot这时候生成的是rootfs，这时候loader+rootfs。这两项选上再烧写。

在loader模式下单模块烧写,你烧写过paramenter，那么会有分区信息，这时候就可以单模块烧写。在loader模式下，你只编译了buildroot生成的rootfs，那么只需要烧写 勾选上rootfs，其他不用选，烧写。
```

### 1.2.3 固件文件

固件文件一般有两种：

- 单个统一固件 update.img, 将启动加载器、参数和所有分区镜像都打包到一起，用于固件发布。
- 多个分区镜像,如 kernel.img, boot.img, recovery.img 等，在开发阶段生成。

#### 1.2.3.1 烧写统一固件 update.img

烧写统一固件 update.img 的步骤如下:

- 切换至”升级固件”页。
- 按”固件”按钮，打开要升级的固件文件。升级工具会显示详细的固件信息。
- 按”升级”按钮开始升级。
- 如果升级失败，可以尝试先按”擦除Flash”按钮来擦除 Flash，然后再升级。

注意：***如果你烧写的固件laoder版本与原来的机器的不一致，请在升级固件前先执行”擦除Flash”。***

![img](RK_Linux_Compile.assets/win_tool_upgrade_v2.58.png)

#### 1.2.3.2 烧写分区映像

烧写分区映像的步骤如下：

- 切换至”下载镜像”页。
- 勾选需要烧录的分区，可以多选。
- 确保映像文件的路径正确，需要的话，点路径右边的空白表格单元格来重新选择。
- 点击”执行”按钮开始升级，升级结束后设备会自动重启。

 ![img](RK_Linux_Compile.assets/win_3128_tool_download.png)

### 1.2.4 各分区镜像功能

uboot：对应的是uboot.img。  uboot 属于bootloader的一种，是用来引导启动内核的，它的最终目的就是，从flash中读出内核，放到内存中，启动内核
trust：对应的是trust.img， 其中含有ATF以及休眠唤醒相关的文件。安全保护使用。
misc: misc 分区映像，对应misc.img，负责启动模式切换和急救模式的参数传递。
resource: 资源映像，对应的是resource.img，内含开机图片和内核的设备树信息。
kernel: 内核映像，对应的是kernel.img
boot: Android 的初始文件映像，即ramdisk，负责初始化并加载 system 分区，对应的是boot.img
recovery:急救模式映像，对应的是recovery.img
system: Android 的 system 分区映像，ext4 文件系统格式，对应的是system.img



分析编译的镜像：

- boot.ing 是编译内核kernel生成的，

- uboot.img、MiniLoaderAll.bin和trust是编译uboot生成的，

- recovery和rootfs是编译buildroot生成的

- userdata.img是用户数据镜像


```shell
cw@SYS3:~/sdk/312x_i/rockdev$ ls -l

boot.img -> ../kernel/zboot.img
MiniLoaderAll.bin -> ../u-boot/rk3128_loader_v2.12.256.bin
misc.img -> ../device/rockchip/rockimg/wipe_all-misc.img
oem.img
parameter.txt -> ../device/rockchip/rk3128/parameter-buildroot.txt
recovery.img -> ../buildroot/output/rockchip_rk3128_recovery/images/recovery.img
rootfs.ext4 -> ../buildroot/output/rockchip_rk3128/images/rootfs.ext2
rootfs.img -> ../buildroot/output/rockchip_rk3128/images/rootfs.ext2
trust.img -> ../u-boot/trust.img
uboot.img -> ../u-boot/uboot.img
update.img
userdata.img
```

这是制作镜像的相关脚本

```
cw@SYS3:~/sdk/312x_i/device/rockchip/common$ ls 
.
..
build.sh
gen_patches_body.sh
mk-buildroot.sh
mk-debian.sh
mkfirmware.sh
mk-fitimage.sh
mk-image.sh
mk-multi-npu_boot.sh
mk-ramdisk.sh
mk-toolchain.sh
rkflash.sh
Version.mk
```
### 1.2.5  recovery模式和普通模式

[root@buildroot:/]# 这个代表是recovery模式，该模式下不能自己加载驱动。

如何从recovery模式切换为正常模式：

misc烧写一个空的.Z:\sdk\312x_i\device\rockchip\rockimg\blank-misc.im

[root@rk312x:/]#这个是正常模式

## 2 编译

### 2.1 编译Buildroot  source脚本只用来编译rootfs

- 声明buildroot环境变量

source envsetup.sh  #选择开发板，如rk3128

- 声明kernel、uboot等的环境变量

  ./build.sh device/rockchip/rv1126_rv1109/BoardConfig-tb.mk  这样切换单板

```
檵 14:18:28
我们source之后一般还需要./build.sh device/rockchip/rv1126_rv1109/BoardConfig-tb.mk  这样切换单板吗

林刘迪铭 14:19:00
source 只是选buildroot的配置，其他的kernel uboot什么的，需要切这个mk

林刘迪铭 14:19:19
正常版本用BoardConfig.mk就是，tb是快速开机的 
```

#### 2.1.1 source

```
source envsetup.sh  #选择开发板，如rk3128
```

生成连接指向配置

cw@SYS3:~/sdk/3126i/device/rockchip$ ls -al
lrwxrwxrwx  1 cw cw   21 Mar  5 15:41 .BoardConfig.mk -> rk3128/BoardConfig.mk

执行编译命令时，将会根据 `.mk` 文件进行编译。 对 Buildroot 相关配置进行说明 ：

```shell
cw@SYS3:~/sdk/3126i/device/rockchip$ vim .BoardConfig.mk
1 #!/bin/bash
2
3 # Target arch
4 export RK_ARCH=arm
5 # Uboot defconfig
6 export RK_UBOOT_DEFCONFIG=rk3128
7 # Kernel defconfig
8 export RK_KERNEL_DEFCONFIG=rockchip_linux_defconfig
9 # Kernel dts
10 export RK_KERNEL_DTS=rk3128-fireprime
11 # boot image type
12 export RK_BOOT_IMG=zboot.img
13 # kernel image path
14 export RK_KERNEL_IMG=kernel/arch/arm/boot/zImage
15 # parameter for GPT table
16 export RK_PARAMETER=parameter-buildroot.txt
17 # Buildroot config
18 export RK_CFG_BUILDROOT=rockchip_rk3128

# Buildroot 根文件系统配置文件
# 文件路径在 `buildroot/configs/rockchip_rk3128_defconfig`

19 # Recovery config
20 export RK_CFG_RECOVERY=rockchip_rk3128_recovery

# recovery 模式下根文件系统配置文件（可省略）
# 文件路径在 `buildroot/configs/rockchip_rk3288_recovery_defconfig`

21 # Pcba config
22 export RK_CFG_PCBA=rockchip_rk3128_pcba
23 # Build jobs
24 export RK_JOBS=12
25 # target chip
26 export RK_TARGET_PRODUCT=rk3128
27 # Set rootfs type, including ext2 ext4 squashfs
28 export RK_ROOTFS_TYPE=ext4
29 # rootfs image path
30 export RK_ROOTFS_IMG=rockdev/rootfs.${RK_ROOTFS_TYPE}

# Buildroot 根文件系统镜像路径
# 本例中，文件路径在 `buildroot/output/rockchip_rk3128/images/rootfs.ext4`
# 注：该文件路径将在首次编译根文件系统后生成

31 # Set oem partition type, including ext2 squashfs
32 export RK_OEM_FS_TYPE=ext2
33 # Set userdata partition type, including ext2, fat
34 export RK_USERDATA_FS_TYPE=ext2
35 #OEM config
36 export RK_OEM_DIR=oem_normal
37 #userdata config
38 export RK_USERDATA_DIR=userdata_normal
39 #misc image
40 export RK_MISC=wipe_all-misc.img

```

Linux export 命令用于设置或显示环境变量。

在 shell 中执行程序时，shell 会提供一组环境变量。export 可新增，修改或删除环境变量，供后续执行的程序使用。export 的效力仅限于该次登陆操作。（所以环境变量只在当前secureCRT窗口shell有效，再开一个窗口shell就没了）

```
export [-fnp][变量名称]=[变量设置值]
```

**参数说明**：

- -f 　代表[变量名称]中为函数名称。
- -n 　删除指定的变量。变量实际上并未删除，只是不会输出到后续指令的执行环境中。
- -p 　列出所有的shell赋予程序的环境变量。

列出当前所有的环境变量

```
# export -p //列出当前的环境变量值
declare -x HOME=“/root“
declare -x LANG=“zh_CN.UTF-8“
declare -x LANGUAGE=“zh_CN:zh“
```

#### 2.1.2  make

怎么编译buildroot修改的模块

```
make menuconfig    //# 进入图形化配置界面，选择所需模块，保存退出。斜杆搜索，空格或者y选上
make savedeconfig   //保存到配置文件 'buildroot/configs/rockchip_rk3128_defconfig'

举个例子，你看到如下改变
cw@SYS3:~/sdk/3126i/buildroot$ git diff
diff --git a/configs/rockchip_rk3128_defconfig b/configs/rockchip_rk3128_defconfig
index 4232fac868..06a4728bc3 100644
--- a/configs/rockchip_rk3128_defconfig
+++ b/configs/rockchip_rk3128_defconfig
@@ -16,8 +16,13 @@
 #include "qt_app.config"
+#include "video_gst_rtsp.config"


make rkwifibt-dirclean //清除掉之前的
make rkwifibt-rebuild //重新编译
再make 即可（实际就是等价于与./build.sh rootfs）。还要./mkfirmware.sh


cw@SYS3:~/sdk/3126i$ make savedefconfig
cw@SYS3:~/sdk/3126i$ ./build.sh rootfs （或者直接make，等价的）
cw@SYS3:~/sdk/3126i$ ./mkfirmware.sh  （打包固件）
```

### 2.2 编译Kernel

四条编译命令

```shell
cd kernel
kernel$ make ARCH=arm rockchip_linux_defconfig
kernel$ make ARCH=arm menuconfig             //这条命令生成了.config文件
kernel$ make ARCH=arm savedefconfig          //条命令下生成上defconfig文件
scripts/kconfig/conf  --savedefconfig=defconfig Kconfig
kernel$ cp defconfig arch/arm/configs/rockchip_linux_defconfig

//这条命令下生产了defconfig文件arch/arm/configs/rockchip_linux_defconfig
```

原理分析

```
1. 为什么指定ARCH=arm，不加的话影响是啥？

注意（为什么指定ARCH=arm，不加的话影响是啥）：
arch是说明用的是32位的机器，如RK3126、RK2128、RK3128

cw@SYS3:~/sdk/3328/kernel$make menuconfig ARCH=arm
注意kernel对于32位，make menuconfig和make savedefconfig都必须加上ARCH=arm， menuconfig配置后save在拷贝到arch/arm/configs/rockchip_linux_defconfig。


如果不加 ARCH=arm的话，默认是64位，这时候，这时候你git diff下发现rockchip_linux_defconfig会有很大的改动。加 ARCH=arm的话，就是32位机器，你git diff下发现rockchip_linux_defconfig就是刚才菜单的那些修改。
你看下下面文件搜索就会明白
cw@SYS3:~/sdk/3126i/kernel$ ag -g "rockchip_linux_defconfig"
arch/arm/configs/rockchip_linux_defconfig
arch/arm64/configs/rockchip_linux_defconfig
```

### 2.3编译UBOOT

./make.sh --help  

编译命令： 

./make.sh [board] // [board]：configs/[board]_defconfig文件。  

1. 首次编译 

```
make.sh rk3399 // build for rk3399_defconfig ./make.sh evb-rk3399 // build for evb-rk3399_defconfig ./make.sh firefly-rk3288 // build for firefly-rk3288_defconfig
```

编译完成后的提示： 

```
Platform RK3399 is build OK, with new .config(make evb-rk3399_defconfig)
```



2. 二次编译 

无论 32 位或 64 位平台，如果想基于当前".confifig"进行二次编译，则不需要指定[board]： 

```
./make.sh
```

编译完成后的提示： 

```
...... Platform RK3399 is build OK, with exist .config
```

3.2.4 固件生成 

1. 编译完成后，最终打包生成的固件都在 U-Boot 根目录下：trust、uboot、loader。 

```
./uboot.img 
./trust.img 
./rk3126_loader_v2.09.247.bin
```

2. 根据固件打包的过程信息可以知道 bin 和 INI 文件的来源。 

uboot.img： 

 ```
load addr is 0x60000000! // U-Boot的运行地址会被追加在打包头信息里 pack input rockdev/rk3126/out/u-boot.bin pack file size: 478737 crc = 0x840f163c uboot version: v2017.12 Dec 11 2017 pack uboot.img success! pack uboot okay! Input: rockdev/rk3126/out/u-boot.bin
 ```

loader： 

```
out:rk3126_loader_v2.09.247.bin fix opt:rk3126_loader_v2.09.247.bin merge success(rk3126_loader_v2.09.247.bin) pack loader okay! Input: /home/guest/project/rkbin/RKBOOT/RK3126MINIALL.ini
```

trust.img： 

```
load addr is 0x68400000! // trust的运行地址会被追加在打包头信息里 pack file size: 602104 crc = 0x9c178803 trustos version: Trust os pack ./trust.img success! trust.img with ta is ready pack trust okay! Input: /home/guest/project/rkbin/RKTRUST/RK3126TOS.ini
```

注意：make clean/mrproper/distclean 会把编译阶段的中间文件都清除，包括 bin 和 img 文件。 

请用户不要把重要的 bin 或者 img 文件放在 U-Boot 的根目录下。

### 2.4 自动编译

#### 2.4.1 全自动编译

./build.sh   全自动编译会编译并打包固件 `update.img`，生成固件目录 `rockdev/`：

#### 2.4.2 部分编译

- 编译 kernel:      ./build.sh kernel

- 编译 u-boot:      ./build.sh uboot

- 编译 rootfs:        编译 Buildroot 根文件系统，将会在 `buildroot/output` 生成编译输出目录：

  ./build.sh buildroot 注：确保作为普通用户编译 Buildroot 根文件系统，避免不必要的错误。编译过程中会自动下载所需软件包，请保持联网状态

#### 2.4.3 更新链接&打包固件

- 更新链接

为确保 `rockdev/` 目录下文件链接正确，**更新各部分镜像链接**：

```
./mkfirmware.sh
```

- 打包固件

将 `rockdev` 目录的**各部分镜像打包成一个固件** `update.img`：

```
./build.sh updateimg
```



## 3  buildroot 与source envsetup.sh

### 3.1 编译buildroot

客户按实际编译环境配置好编译依赖后，按照以下步骤配置完后，执行 make 即可。
$ source buildroot/build/envsetup.sh
You're building on Linux
Lunch menu...pick a combo:

1. rockchip_rk3308_release
2. rockchip_rk3308_debug
3. rockchip_rk3308_robot_release
4. rockchip_rk3308_robot_debug
5. rockchip_rk3308_mini_release
Which would you like? [1]
如选择 rockchip_rk3308_release，输入对应序号 1。
$ make
完成编译后执行 SDK 根目录下的 mkfirmware.sh 脚本生成固件
$ ./mkfirmware.sh
所有烧写所需的镜像将都会拷贝于 rockdev 目录。

### 3.1 注意 1 source envsetup.sh禁止ctrl+c

envsetup.sh只是将某个单板的相关配置source进当前shell的环境变量

【注意 1】：build.sh不能强制ctrl+c，如果你输入错误的数字才发现错了，就再随便输入几个字母，回车

source envsetup.sh 执行的文件会生成Makefile等文件，比如你发下你填错数字了，千万不要ctrl+c，这时候比如他正在生成makefile后，可是你强制停止了，文件还没有写入任何内容，这时候你ctrl就终止了，可是make执行的就是这个空白的makefile。可能错误如下：

```
2020-03-12T20:06:26 >>>   Generating root filesystem image rootfs.tar
2020-03-12T20:06:26 rm -rf /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs
2020-03-12T20:06:26 mkdir -p /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs
2020-03-12T20:06:26 echo '#!/bin/sh' > /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 echo "set -e" >> /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 echo "chown -h -R 0:0 /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/target" >> /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 printf '    - - input -1 * - - - Input device groupnn' >> /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/users_table.txt
2020-03-12T20:06:26 PATH="/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/host/bin:/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/host/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin" /home/cw/sdk/3126i/buildroot/support/scripts/mkusers /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/users_table.txt /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/target >> /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 cat system/device_table.txt > /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/device_table.txt
2020-03-12T20:06:26 printf '    /bin/busybox                     f 4755 0  0 - - - - -n /dev/console c 622 0 0 5 1 - - -nn' >> /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/device_table.txt
2020-03-12T20:06:26 echo "/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/host/bin/makedevs -d /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/device_table.txt /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/target" >> /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 printf '    (cd /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/target; find -print0 | LC_ALL=C sort -z | tar  -cf /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/images/rootfs.tar --null --no-recursion -T - --numeric-owner)n' >> /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 chmod a+x /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 rm -f /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/target/THIS_IS_NOT_YOUR_ROOT_FILESYSTEM
2020-03-12T20:06:26 PATH="/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/host/bin:/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/host/sbin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin" /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/host/bin/fakeroot -- /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/fakeroot.fs
2020-03-12T20:06:26 rootdir=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/target
2020-03-12T20:06:26 table='/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/build/buildroot-fs/device_table.txt'
2020-03-12T20:06:26 /usr/bin/install -m 0644 support/misc/target-dir-warning.txt /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128_recovery/target/THIS_IS_NOT_YOUR_ROOT_FILESYSTEM
2020-03-13T08:23:50 make: *** No targets.  Stop.
2020-03-13T09:15:54 make: *** No targets.  Stop.
2020-03-13T09:16:16 make: *** No targets.  Stop.
2020-03-13T09:16:36 make: *** No targets.  Stop.

cw@SYS3:~/sdk/3126i$ vi Makefile
2020-03-13T09:55:47 make: *** No targets.  Stop.
2020-03-13T10:04:06 make: *** No targets.  Stop.
```



```
cw@SYS3:~/sdk/3126i$ vi Makefile
  1 ### DO NOT EDIT THIS FILE ###
  2 ifeq ($(TARGET_OUTPUT_DIR),)
  3 $(error "Please use "source buildroot/build/envsetup.sh" to select a buildroot config")
  4 endif
  5
  6 O=$(TARGET_OUTPUT_DIR)//原因就是这个输出在output/rk3128/下的makefile由于ctrl+c，里面是空白的，还没有写入，把这个文件删除了就好
  7 include $(O)/Makefile
  //1696  cd buildroot/output/rockchip_rk3128/  rm Makefile
  8 ### DO NOT EDIT THIS FILE ###
```

解决

```
原因就是这个输出在output/rk3128/下的makefile由于ctrl+c强制停止脚本，里面是空白的，还没有写入，把这个文件删除了就好

 ~/sdk/3126i_study/buildroot/output/rockchip_rk3128/Makefile 这个makefile是自动生成的。所以强制停止
  # Automatically generated by /home/cw/sdk/3126i_study/buildroot/support/scripts/mkmakefile: don't edit
```

### 3.2 注意 2复制工程要删除重编译
 【注意 2】：你复制了A工程为B，在B工程下执行.build.sh会报错，因为用的还是A工程的路径。

解决：删除output/单板下

```

2020-03-13T10:26:28 /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/images/rootfs.ext2: ***** FILE SYSTEM WAS MODIFIED *****
2020-03-13T10:26:28 /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/images/rootfs.ext2: 5929/32768 files (0.1% non-contiguous), 72692/113478 blocks
2020-03-13T10:26:28 /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/host/sbin/tune2fs -m 5 /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/images/rootfs.ext2
2020-03-13T10:26:28 tune2fs 1.43.9 (8-Feb-2018)
2020-03-13T10:26:28 Setting reserved blocks percentage to 5% (5673 blocks)
2020-03-13T10:26:28 /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/host/sbin/resize2fs -M /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/images/rootfs.ext2
2020-03-13T10:26:28 resize2fs 1.43.9 (8-Feb-2018)
2020-03-13T10:26:28 Resizing the filesystem on /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/images/rootfs.ext2 to 106117 (4k) blocks.
2020-03-13T10:26:28 The filesystem on /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/images/rootfs.ext2 is now 106117 (4k) blocks long.
2020-03-13T10:26:28
```

### 3.3 切换单板与脚本的关系

根目录脚本envsetup.sh 下面有下面这句话，使用的是软连接 根目录/device/rockchip/.BoardConfig.mk

````
#注意一下，这个软件链，应该是手动在device/rockchip下面配置的
	source ${TOP_DIR}/device/rockchip/.BoardConfig.mk
	echo “source 文件里面的变量${TOP_DIR}/device/rockchip/.BoardConfig.mk 这里指定了每个的设备树，板载配置等”

````

看下这个软连接指向啥，这个软连接是是一个特定单板的芯片

```
root@c:/home/c/linux/v2/device/rockchip# ls -al
总用量 108
drwxr-xr-x 26 c    c    4096 3月  15 14:43 .
drwxr-xr-x  3 c    c    4096 1月  30 22:06 ..
lrwxrwxrwx  1 root root   21 3月  15 14:43 .BoardConfig.mk -> rk3128/BoardConfig.mk
drwxr-xr-x  2 c    c    4096 3月  15 01:41 common
drwxr-xr-x  2 c    c    4096 3月  15 00:01 .git
```

所以切换单板的操作：

```
root@c:/home/c/linux/v2/device/rockchip# ln -sf rk3128/BoardConfig.mk .BoardConfig.mk
root@c:/home/c/linux/v2# source envsetup.sh   #envsetup.sh 会source .BoardConfig.mk 里面的环境变量
root@c:/home/c/linux/v2# ./build.sh


如下可以看到，编译的时候，就单板已切换
processing option: allsave
============================================
TARGET_ARCH=arm
TARGET_PLATFORM=rk3128
TARGET_UBOOT_CONFIG=evb-rk3128
TARGET_KERNEL_CONFIG=rockchip_linux_defconfig
TARGET_KERNEL_DTS=rk3128-fireprime
TARGET_TOOLCHAIN_CONFIG=
TARGET_BUILDROOT_CONFIG=rockchip_rk3128
TARGET_RECOVERY_CONFIG=rockchip_rk3128_recovery
TARGET_PCBA_CONFIG=rockchip_rk3128_pcba
TARGET_RAMBOOT_CONFIG=
=============================
```

【错误实例】

如果3128切换为3308。你没有重新生成软链接指向3308的配置，哪怕你重新source之后，./build.sh还是使用的是3128的配置。



【./build.sh rootfs】

```
执行的就是删除相关文件，执行source命令和make命令
```

 全自动编译脚本，降低人工编译可能出现的误操作，该 SDK 中集成了全自动化编译脚本，方便固件编译、备份。

```

1）脚本原始文件存放于：
device/rockchip/common/build.sh
2）在 repo sync 的时候，通过 manifest 中的 copy 选项拷贝至工程根目录下：
3）修改 device/rockchip/rkxx(芯片平台)/BoardConfig.mk 脚本中的特定变量以编出对应
产品固件。
如 RK3308 平台，可修改 device/rockchip/rk3308/BoardConfig.mk 文件：
#buildroot defconfig
LUNCH=rockchip_rk3308_release
#uboot defconfig
UBOOT_DEFCONFIG=evb-rk3308
#kernel defconfig
KERNEL_DEFCONFIG=rk3308_linux_defconfig
#kernel dts
KERNEL_DTS= rk3308-evb-dmic-pdm-v11
以下变量请按实际项目情况，对应修改：
LUNCH 变量指定 Buildroot 编译 defconfig。
KERNEL_DTS 变量指定编译 kernel 的产品板极配置。
4）执行自动编译脚本：
./build.sh
该脚本会自动配置环境变量，编译 U-Boot，编译 Kernel，编译 Buildroot，编译 Recovery
继而生成固件。
5）脚本生成内容:
脚本会将编译生成的固件拷贝至：
IMAGE/RK3308-EVB-DMIC-PDM-V11_****_RELEASE_TEST/IMAGES 目录下，具体路
径以实际生成为准。每次编译都会新建目录保存，自动备份调试开发过程的固件版本，并存放固件
版本的各类信息
```

build.sh可编译单独模块, 使用命令`./build.sh -h`查看帮助


```
$./build.sh -h
====USAGE: build.sh modules====
uboot -build uboot
kernel -build kernel
rootfs -build default rootfs, currently build buildroot as default
buildroot -build buildroot rootfs
yocto -build yocto rootfs, currently build ros as default
ros -build ros rootfs
debian -build debian rootfs
pcba -build pcba
recovery -build recovery
all -build uboot, kernel, rootfs, recovery image
cleanall -clean uboot, kernel, rootfs, recovery
firmware -pack all the image we need to boot up system
updateimg -pack update image
save -save images, patches, commands used to debug
default -build all modules
如单独编译 kernel，只需要执行以下命令：
./build.sh kernel
如单独编译 kernel，只需要执行以下命令：
./build.sh rootfs
```

###  3.3 source 的作用

如果你没有 source envsetup.sh 的话会怎么样:	会说你没有指定目标

```
c@c:~/linux/v1$ ./build.sh kernel
./build.sh: 行 9: /home/c/linux/v1/device/rockchip/.BoardConfig.mk: 没有那个文件或目录
processing option: kernel
============Start build kernel============
TARGET_ARCH          =
TARGET_KERNEL_CONFIG =
TARGET_KERNEL_DTS    =
==========================================
Makefile:660: arch//Makefile: 没有那个文件或目录
make: *** 没有规则可制作目标“arch//Makefile”。 停止。
====Build kernel failed!====
```

有 source envsetup.sh 的话会怎么样:

```
c@c:~/linux/v1$ source envsetup.sh
Top of tree: /home/c/linux/v1

You're building on Linux
Lunch menu...pick a combo:

0. non-rockchip boards
22. rockchip_rk3036
23. rockchip_rk3036_recovery
24. rockchip_rk3126c
25. rockchip_rk3126c_dpf
26. rockchip_rk3126c_recovery
27. rockchip_rk3128

Which would you like? [0]: 27
-bash: /home/c/linux/v1/device/rockchip/.BoardConfig.mk: 没有那个文件或目录
===========================================

#TARGET_BOARD=rk3128
#OUTPUT_DIR=output/rockchip_rk3128
#CONFIG=rockchip_rk3128_defconfig

===========================================
make: 进入目录“/home/c/linux/v1/buildroot”
  GEN     /home/c/linux/v1/buildroot/output/rockchip_rk3128/Makefile
/home/c/linux/v1/buildroot/build/defconfig_hook.py -m /home/c/linux/v1/buildroot/configs/rockchip_rk3128_defconfig /home/c/linux/v1/buildroot/output/rockchip_rk3128/.rockchipconfig
BR2_DEFCONFIG='' KCONFIG_AUTOCONFIG=/home/c/linux/v1/buildroot/output/rockchip_rk3128/build/buildroot-config/auto.conf KCONFIG_AUTOHEADER=/home/c/linux/v1/buildroot/output/rockchip_rk3128/build/buildroot-config/autoconf.h KCONFIG_TRISTATE=/home/c/linux/v1/buildroot/output/rockchip_rk3128/build/buildroot-config/tristate.config BR2_CONFIG=/home/c/linux/v1/buildroot/output/rockchip_rk3128/.config HOST_GCC_VERSION="7" BUILD_DIR=/home/c/linux/v1/buildroot/output/rockchip_rk3128/build SKIP_LEGACY= BR2_DEFCONFIG=/home/c/linux/v1/buildroot/configs/rockchip_rk3128_defconfig /home/c/linux/v1/buildroot/output/rockchip_rk3128/build/buildroot-config/conf --defconfig=/home/c/linux/v1/buildroot/output/rockchip_rk3128/.rockchipconfig Config.in
/home/c/linux/v1/buildroot/output/rockchip_rk3128/.rockchipconfig:77:warning: override: reassigning to symbol BR2_PACKAGE_MPP
/home/c/linux/v1/buildroot/output/rockchip_rk3128/.rockchipconfig:78:warning: override: reassigning to symbol BR2_PACKAGE_MPP_ALLOCATOR_DRM
/home/c/linux/v1/buildroot/output/rockchip_rk3128/.rockchipconfig:80:warning: override: reassigning to symbol BR2_PACKAGE_LINUX_RGA
/home/c/linux/v1/buildroot/output/rockchip_rk3128/.rockchipconfig:266:warning: override: reassigning to symbol BR2_PACKAGE_RKWIFIBT
#
# configuration written to /home/c/linux/v1/buildroot/output/rockchip_rk3128/.config
#
make: 离开目录“/home/c/linux/v1/buildroot”
```

## 4 build.sh

为了提高编译的效率，降低人工编译可能出现的误操作，该 SDK 中集成了全自动化编译脚本，
方便固件编译、备份。

### 4.1原理

1. 该全自动化编译脚本原始文件存放于：
   device/rockchip/common/build.sh

2. 在 repo sync 的时候，通过 manifest 中的 copy 选项拷贝至工程根目录下：

3. **修改 device/rockchip/rkxx(芯片平台)/.BoardConfig.mk 脚本中的特定变量以编出对应**

   (换芯片要重新修改这个软连接)

4. 产品固件。
   如 RK3308 平台，可修改 device/rockchip/rk3308/BoardConfig.mk 文件：

   ```
   #buildroot defconfig
   LUNCH=rockchip_rk3308_release
   #uboot defconfig
   UBOOT_DEFCONFIG=evb-rk3308
   #kernel defconfig
   KERNEL_DEFCONFIG=rk3308_linux_defconfig
   #kernel dts
   KERNEL_DTS= rk3308-evb-dmic-pdm-v11
   以下变量请按实际项目情况，对应修改：
   LUNCH 变量指定 Buildroot 编译 defconfig。
   KERNEL_DTS 变量指定编译 kernel 的产品板极配置。
   ```

5. 执行自动编译脚本：
   ./build.sh
   该脚本会自动配置环境变量，编译 U-Boot，编译 Kernel，编译 Buildroot，编译 Recovery
   继而生成固件。

6. 脚本生成内容:
   脚本会将编译生成的固件拷贝至：
   IMAGE/RK3308-EVB-DMIC-PDM-V11__RELEASE_TEST/IMAGES 目录下，具体
   路径以实际生成为准。**每次编译都会新建目录保存，(如果你硬盘空间不够大，可以删除掉)自动备份调试开发过程的固件版本**，并存放固件版本的各类信息

### 4.2单模块编译

$./build.sh -h  //帮助文档

- uboot             编译uboot
- kernel            编译内核
- rootfs             编译default rootfs, currently build buildroot as default
- buildroot        编译buildroot rootfs
- yocto              编译yocto rootfs, currently build ros as default
- ros                  编译 ros rootfs
- debian             编译 debian rootfs
- pcba                编译pcba
- recovery          编译 recovery
- all -build uboot, kernel, rootfs, recovery image
- cleanall -clean uboot, kernel, rootfs, recovery
- firmware -pack all the image we need to boot up system
- updateimg -pack update image   打包update镜像
- save -save images, patches, commands used to debug
- default -build all modules  默认编译所有的模块


```shell
#!/bin/bash

unset RK_CFG_TOOLCHAIN

CMD=`realpath $0`
COMMON_DIR=`dirname $CMD`
TOP_DIR=$(realpath $COMMON_DIR/../../..)
BOARD_CONFIG=$TOP_DIR/device/rockchip/.BoardConfig.mk
source $BOARD_CONFIG
source $TOP_DIR/device/rockchip/common/Version.mk

//`./build.sh -h`命令时候，打印出帮助
function usage()
{
	echo "Usage: build.sh [OPTIONS]"
	echo "Available options:"
	echo "BoardConfig*.mk    -switch to specified board config"
	echo "uboot              -build uboot"
	echo "kernel             -build kernel"
	echo "modules            -build kernel modules"
	echo "toolchain          -build toolchain"
	echo "rootfs             -build default rootfs, currently build buildroot as default"
	echo "buildroot          -build buildroot rootfs"
	echo "ramboot            -build ramboot image"
	echo "multi-npu_boot     -build boot image for multi-npu board"
	echo "yocto              -build yocto rootfs"
	echo "debian             -build debian9 stretch rootfs"
	echo "distro             -build debian10 buster rootfs"
	echo "pcba               -build pcba"
	echo "recovery           -build recovery"
	echo "all                -build uboot, kernel, rootfs, recovery image"
	echo "cleanall           -clean uboot, kernel, rootfs, recovery"
	echo "firmware           -pack all the image we need to boot up system"
	echo "updateimg          -pack update image"
	echo "otapackage         -pack ab update otapackage image"
	echo "save               -save images, patches, commands used to debug"
	echo "allsave            -build all & firmware & updateimg & save"
	echo ""
	echo "Default option is 'allsave'."
}

#c@c:~/linux/v1$ ag -t "RK_UBOOT_DEFCONFIG"
#device/rockchip/common/build.sh
#43:     echo "TARGET_UBOOT_CONFIG=$RK_UBOOT_DEFCONFIG"
#48:     cd u-boot && ./make.sh $RK_UBOOT_DEFCONFIG && cd -
#271:    echo "TARGET_UBOOT_CONFIG=$RK_UBOOT_DEFCONFIG"
#376:    echo "UBOOT:  defconfig: $RK_UBOOT_DEFCONFIG" >> $STUB_PATH/build_cmd_info
#device/rockchip/rk3399/BoardConfig_debian.mk
#6:export RK_UBOOT_DEFCONFIG=rk3399
#device/rockchip/rk3399/BoardConfig.mk
#6:export RK_UBOOT_DEFCONFIG=rk3399
#device/rockchip/rk3326/BoardConfig.mk
#6:export RK_UBOOT_DEFCONFIG=evb-rk3326
#device/rockchip/rk3326/BoardConfig_32bit.mk
#6:export RK_UBOOT_DEFCONFIG=evb-rk3326
#device/rockchip/rk3326/BoardConfig_robot64.mk
#6:export RK_UBOOT_DEFCONFIG=evb-rk3326
#device/rockchip/rk3326/BoardConfig_robot64_no_gpu.mk
#6:export RK_UBOOT_DEFCONFIG=evb-rk3326
//编译uboot  ，RK_UBOOT_DEFCONFIG是source 的时候生成的环境变量，指定了uboot的默认芯片
function build_uboot(){
	echo "============Start build uboot============"
	echo "TARGET_UBOOT_CONFIG=$RK_UBOOT_DEFCONFIG"
	echo "========================================="
	if [ -f u-boot/*_loader_*.bin ]; then    #如果这个是文件件，就删除
		rm u-boot/*_loader_*.bin
	fi
	cd u-boot && ./make.sh $RK_UBOOT_DEFCONFIG && cd -
	# $? 是上一个程序执行是否成功的标志，如果执行成功则$? 为0，否则 不为0

	if [ $? -eq 0 ]; then
		echo "====Build uboot ok!===="
	else
		echo "====Build uboot failed!===="
		exit 1
	fi
}

//编译kernel,前面source声明了
# TARGET_ARCH 目标架构
# RK_KERNEL_DEFCONFIG 内核默认配置
#TARGET_KERNEL_DTS 使用的dts
function build_kernel(){
	echo "============Start build kernel============"
	echo "TARGET_ARCH          =$RK_ARCH"
	echo "TARGET_KERNEL_CONFIG =$RK_KERNEL_DEFCONFIG"
	echo "TARGET_KERNEL_DTS    =$RK_KERNEL_DTS"
	echo "=========================================="
	cd $TOP_DIR/kernel && make ARCH=$RK_ARCH $RK_KERNEL_DEFCONFIG && make ARCH=$RK_ARCH $RK_KERNEL_DTS.img -j$RK_JOBS && cd -
	if [ $? -eq 0 ]; then
		echo "====Build kernel ok!===="
	else
		echo "====Build kernel failed!===="
		exit 1
	fi
}
//编译kernel模块
前面source声明了
# TARGET_ARCH 目标架构
# RK_KERNEL_DEFCONFIG 内核默认配置
function build_modules(){
	echo "============Start build kernel modules============"
	echo "TARGET_ARCH          =$RK_ARCH"
	echo "TARGET_KERNEL_CONFIG =$RK_KERNEL_DEFCONFIG"
	echo "=================================================="
	cd $TOP_DIR/kernel && make ARCH=$RK_ARCH $RK_KERNEL_DEFCONFIG && make ARCH=$RK_ARCH modules -j$RK_JOBS && cd -
	if [ $? -eq 0 ]; then
		echo "====Build kernel ok!===="
	else
		echo "====Build kernel failed!===="
		exit 1
	fi
}

//编译toolchain交叉工具链,前面source声明了
# RK_CFG_TOOLCHAIN
function build_toolchain(){
	echo "==========Start build toolchain =========="
	echo "TARGET_TOOLCHAIN_CONFIG=$RK_CFG_TOOLCHAIN"
	echo "========================================="
	[[ $RK_CFG_TOOLCHAIN ]] \
		&& /usr/bin/time -f "you take %E to build toolchain" $COMMON_DIR/mk-toolchain.sh $BOARD_CONFIG \
		|| echo "No toolchain step, skip!"
	if [ $? -eq 0 ]; then
		echo "====Build toolchain ok!===="
	else
		echo "====Build toolchain failed!===="
		exit 1
	fi
}

function build_buildroot(){
	echo "==========Start build buildroot=========="
	echo "TARGET_BUILDROOT_CONFIG=$RK_CFG_BUILDROOT"
	echo "========================================="
	/usr/bin/time -f "you take %E to build builroot" $COMMON_DIR/mk-buildroot.sh $BOARD_CONFIG
	if [ $? -eq 0 ]; then
		echo "====Build buildroot ok!===="
	else
		echo "====Build buildroot failed!===="
		exit 1
	fi
}

function build_ramboot(){
	echo "=========Start build ramboot========="
	echo "TARGET_RAMBOOT_CONFIG=$RK_CFG_RAMBOOT"
	echo "====================================="
	/usr/bin/time -f "you take %E to build ramboot" $COMMON_DIR/mk-ramdisk.sh ramboot.img $RK_CFG_RAMBOOT
	if [ $? -eq 0 ]; then
		echo "====Build ramboot ok!===="
	else
		echo "====Build ramboot failed!===="
		exit 1
	fi
}

function build_multi-npu_boot(){
	if [ -z "$RK_MULTINPU_BOOT" ]; then
		echo "=========Please set 'RK_MULTINPU_BOOT=y' in BoardConfig.mk========="
		exit 1
	fi
	echo "=========Start build multi-npu boot========="
	echo "TARGET_RAMBOOT_CONFIG=$RK_CFG_RAMBOOT"
	echo "====================================="
	/usr/bin/time -f "you take %E to build multi-npu boot" $COMMON_DIR/mk-multi-npu_boot.sh
	if [ $? -eq 0 ]; then
		echo "====Build multi-npu boot ok!===="
	else
		echo "====Build multi-npu boot failed!===="
		exit 1
	fi
}

function build_yocto(){
	if [ -z "$RK_YOCTO_MACHINE" ]; then
		echo "This board doesn't support yocto!"
		exit 1
	fi

	echo "=========Start build ramboot========="
	echo "TARGET_MACHINE=$RK_YOCTO_MACHINE"
	echo "====================================="

	cd yocto
	ln -sf $RK_YOCTO_MACHINE.conf build/conf/local.conf
	source oe-init-build-env
	cd ..
	bitbake core-image-minimal -r conf/include/rksdk.conf

	if [ $? -eq 0 ]; then
		echo "====Build yocto ok!===="
	else
		echo "====Build yocto failed!===="
		exit 1
	fi
}

function build_debian(){
	cd debian

	if [ "$RK_ARCH" == "arm" ]; then
		ARCH=armhf
	fi
	if [ "$RK_ARCH" == "arm64" ]; then
		ARCH=arm64
	fi

	if [ ! -e linaro-stretch-alip-*.tar.gz ]; then
		echo "\033[36m Run mk-base-debian.sh first \033[0m"
		RELEASE=stretch TARGET=desktop ARCH=$ARCH ./mk-base-debian.sh
	fi

	VERSION=debug ARCH=$ARCH ./mk-rootfs-stretch.sh

	./mk-image.sh
	cd ..
	if [ $? -eq 0 ]; then
		echo "====Build Debian9 ok!===="
	else
		echo "====Build Debian9 failed!===="
		exit 1
	fi
}

function build_distro(){
	echo "===========Start build debian==========="
	echo "TARGET_ARCH=$RK_ARCH"
	echo "RK_DISTRO_DEFCONFIG=$RK_DISTRO_DEFCONFIG"
	echo "========================================"
	cd distro && make $RK_DISTRO_DEFCONFIG && /usr/bin/time -f "you take %E to build debian" $TOP_DIR/distro/make.sh && cd -
	if [ $? -eq 0 ]; then
		echo "====Build debian ok!===="
	else
		echo "====Build debian failed!===="
		exit 1
	fi
}

function build_rootfs(){
	rm -f $RK_ROOTFS_IMG

	case "$1" in
		yocto)
			build_yocto
			ROOTFS_IMG=yocto/build/tmp/deploy/images/$RK_YOCTO_MACHINE/rootfs.img
			;;
		debian)
			build_debian
			ROOTFS_IMG=debian/linaro-rootfs.img
			;;
		distro)
			build_distro
			ROOTFS_IMG=yocto/output/images/rootfs.$RK_ROOTFS_TYPE
			;;
		*)
			build_buildroot
			ROOTFS_IMG=buildroot/output/$RK_CFG_BUILDROOT/images/rootfs.$RK_ROOTFS_TYPE
			;;
	esac

	[ -z "$ROOTFS_IMG" ] && return

	if [ ! -f "$ROOTFS_IMG" ]; then
		echo "$ROOTFS_IMG not generated?"
	else
		mkdir -p ${RK_ROOTFS_IMG%/*}
		ln -rsf $TOP_DIR/$ROOTFS_IMG $RK_ROOTFS_IMG
	fi
}

function build_recovery(){
	echo "==========Start build recovery=========="
	echo "TARGET_RECOVERY_CONFIG=$RK_CFG_RECOVERY"
	echo "========================================"
	/usr/bin/time -f "you take %E to build recovery" $COMMON_DIR/mk-ramdisk.sh recovery.img $RK_CFG_RECOVERY
	if [ $? -eq 0 ]; then
		echo "====Build recovery ok!===="
	else
		echo "====Build recovery failed!===="
		exit 1
	fi
}

function build_pcba(){
	echo "==========Start build pcba=========="
	echo "TARGET_PCBA_CONFIG=$RK_CFG_PCBA"
	echo "===================================="
	/usr/bin/time -f "you take %E to build pcba" $COMMON_DIR/mk-ramdisk.sh pcba.img $RK_CFG_PCBA
	if [ $? -eq 0 ]; then
		echo "====Build pcba ok!===="
	else
		echo "====Build pcba failed!===="
		exit 1
	fi
}

function build_all(){
	echo "============================================"
	echo "TARGET_ARCH=$RK_ARCH"
	echo "TARGET_PLATFORM=$RK_TARGET_PRODUCT"
	echo "TARGET_UBOOT_CONFIG=$RK_UBOOT_DEFCONFIG"
	echo "TARGET_KERNEL_CONFIG=$RK_KERNEL_DEFCONFIG"
	echo "TARGET_KERNEL_DTS=$RK_KERNEL_DTS"
	echo "TARGET_TOOLCHAIN_CONFIG=$RK_CFG_TOOLCHAIN"
	echo "TARGET_BUILDROOT_CONFIG=$RK_CFG_BUILDROOT"
	echo "TARGET_RECOVERY_CONFIG=$RK_CFG_RECOVERY"
	echo "TARGET_PCBA_CONFIG=$RK_CFG_PCBA"
	echo "TARGET_RAMBOOT_CONFIG=$RK_CFG_RAMBOOT"
	echo "============================================"
	build_uboot
	build_kernel
	build_toolchain && \
	build_rootfs ${RK_ROOTFS_SYSTEM:-buildroot}
	build_recovery
	build_ramboot
}

function build_cleanall(){
	echo "clean uboot, kernel, rootfs, recovery"
	cd $TOP_DIR/u-boot/ && make distclean && cd -
	cd $TOP_DIR/kernel && make distclean && cd -
	rm -rf $TOP_DIR/buildroot/output
	rm -rf $TOP_DIR/yocto/build
	rm -rf $TOP_DIR/distro/output
	rm -rf $TOP_DIR/debian/binary
}

function build_firmware(){
	./mkfirmware.sh $BOARD_CONFIG
	if [ $? -eq 0 ]; then
		echo "Make image ok!"
	else
		echo "Make image failed!"
		exit 1
	fi
}

function build_updateimg(){
	IMAGE_PATH=$TOP_DIR/rockdev
	PACK_TOOL_DIR=$TOP_DIR/tools/linux/Linux_Pack_Firmware
	if [ "$RK_LINUX_AB_ENABLE"x = "true"x ];then
		echo "Make Linux a/b update.img."
		build_otapackage
		source_package_file_name=`ls -lh $PACK_TOOL_DIR/rockdev/package-file | awk -F ' ' '{print $NF}'`
		cd $PACK_TOOL_DIR/rockdev && ln -fs "$source_package_file_name"-ab package-file && ./mkupdate.sh && cd -
		mv $PACK_TOOL_DIR/rockdev/update.img $IMAGE_PATH/update_ab.img
		cd $PACK_TOOL_DIR/rockdev && ln -fs $source_package_file_name package-file && cd -
		if [ $? -eq 0 ]; then
			echo "Make Linux a/b update image ok!"
		else
			echo "Make Linux a/b update image failed!"
			exit 1
		fi

	else
		echo "Make update.img"
		cd $PACK_TOOL_DIR/rockdev && ./mkupdate.sh && cd -
		mv $PACK_TOOL_DIR/rockdev/update.img $IMAGE_PATH
		if [ $? -eq 0 ]; then
			echo "Make update image ok!"
		else
			echo "Make update image failed!"
			exit 1
		fi
	fi
}

function build_otapackage(){
	IMAGE_PATH=$TOP_DIR/rockdev
	PACK_TOOL_DIR=$TOP_DIR/tools/linux/Linux_Pack_Firmware

	echo "Make ota ab update.img"
	source_package_file_name=`ls -lh $PACK_TOOL_DIR/rockdev/package-file | awk -F ' ' '{print $NF}'`
	cd $PACK_TOOL_DIR/rockdev && ln -fs "$source_package_file_name"-ota package-file && ./mkupdate.sh && cd -
	mv $PACK_TOOL_DIR/rockdev/update.img $IMAGE_PATH/update_ota.img
	cd $PACK_TOOL_DIR/rockdev && ln -fs $source_package_file_name package-file && cd -
	if [ $? -eq 0 ]; then
		echo "Make update ota ab image ok!"
	else
		echo "Make update ota ab image failed!"
		exit 1
	fi
}

function build_save(){
	IMAGE_PATH=$TOP_DIR/rockdev
	DATE=$(date  +%Y%m%d.%H%M)
	STUB_PATH=Image/"$RK_KERNEL_DTS"_"$DATE"_RELEASE_TEST
	STUB_PATH="$(echo $STUB_PATH | tr '[:lower:]' '[:upper:]')"
	export STUB_PATH=$TOP_DIR/$STUB_PATH
	export STUB_PATCH_PATH=$STUB_PATH/PATCHES
	mkdir -p $STUB_PATH

	#Generate patches
	$TOP_DIR/.repo/repo/repo forall -c "$TOP_DIR/device/rockchip/common/gen_patches_body.sh"

	#Copy stubs
	$TOP_DIR/.repo/repo/repo manifest -r -o $STUB_PATH/manifest_${DATE}.xml
	mkdir -p $STUB_PATCH_PATH/kernel
	cp $TOP_DIR/kernel/.config $STUB_PATCH_PATH/kernel
	cp $TOP_DIR/kernel/vmlinux $STUB_PATCH_PATH/kernel
	mkdir -p $STUB_PATH/IMAGES/
	cp $IMAGE_PATH/* $STUB_PATH/IMAGES/

	#Save build command info
	echo "UBOOT:  defconfig: $RK_UBOOT_DEFCONFIG" >> $STUB_PATH/build_cmd_info
	echo "KERNEL: defconfig: $RK_KERNEL_DEFCONFIG, dts: $RK_KERNEL_DTS" >> $STUB_PATH/build_cmd_info
	echo "BUILDROOT: $RK_CFG_BUILDROOT" >> $STUB_PATH/build_cmd_info

}

function build_allsave(){
	build_all
	build_firmware
	build_updateimg
	build_save
}

#=========================
# build targets
#=========================
如果./build.sh的参数是包含help或-h，显示帮助
if echo $@|grep -wqE "help|-h"; then
	usage
	exit 0
fi

OPTIONS="$@"
for option in ${OPTIONS:-allsave}; do
	echo "processing option: $option"
	case $option in
		BoardConfig*.mk)
			CONF=$TOP_DIR/device/rockchip/$RK_TARGET_PRODUCT/$option
			echo "switching to board: $CONF"
			if [ ! -f $CONF ]; then
				echo "not exist!"
				exit 1
			fi

			ln -sf $CONF $BOARD_CONFIG
			;;
		buildroot|debian|distro|yocto)
			build_rootfs $option
			;;
		recovery)
			build_kernel
			;&
		*)
			eval build_$option || usage
			;;
	esac
done

```

device/rockchip/common/mk-buildroot.sh

```shell
  1 #!/bin/bash
  2
  3 COMMON_DIR=$(cd `dirname $0`; pwd)
  4 if [ -h $0 ]
  5 then
  6         CMD=$(readlink $0)
  7         COMMON_DIR=$(dirname $CMD)
  8 fi
  9 cd $COMMON_DIR
 10 cd ../../..    #回到SDK根目录
 11 TOP_DIR=$(pwd)  #回到SDK根目录
 12 BOARD_CONFIG=$1
 13 source $BOARD_CONFIG
 14 if [ -z $RK_CFG_BUILDROOT ]
 15 then
 16         echo "RK_CFG_BUILDROOT is empty, skip building buildroot rootfs!"
 17         exit 0
 18 fi
 19 source $TOP_DIR/buildroot/build/envsetup.sh $RK_CFG_BUILDROOT
 20 $TOP_DIR/buildroot/utils/brmake
 21 if [ $? -ne 0 ]; then
 22     echo "log saved on $TOP_DIR/br.log"
 23     tail -n 100 $TOP_DIR/br.log
 24     exit 1
 25 fi
 26 echo "log saved on $TOP_DIR/br.log. pack buildroot image at: $TOP_DIR/buildroot/output/$RK_CFG_BUILDROOT/images/rootfs.$RK_ROOTFS_TYPE"
~
```



##  6 Buildroot 原理介绍

### 6.1 为什么要使用buildroot？

  （文件系统搭建，强烈建议直接用buildroot，官网[http://buildroot.uclibc.org/]上有使用教程非常详细）文件系统通常要包含很多第三方软件，比如busybox，udhcpc,tftp，apache，sqlite，PHP，iptable,DNS等，为了避免繁杂的移植工作。buildroot应运而生。通过menuconfig配置我们需要的功能，不需要的功能去掉，再执行make指令编译，buildroot就会自动从指定的服务器上下载源码包,自动编译,自动搭建成我们所需要的嵌入式根文件系统。

- buildroot/package/：下面放着应用软件的配置文件，每个应用软件的配置文件有Config.in和soft_name.mk，其中soft_name.mk(这种其实就Makefile脚本的自动构建脚本)文件可以去下载应用软件的包。

- buildroot/output/：是编译出来的输出文件夹，里面的build/目录存放着解压后的各种软件包编译完后的现场。

  - host：是由各类源码编译后在你主机上运行的工具(build for host)的安装目录,如arm-linux-gcc就是安装在这里.

    - 编译出来的主机工具在host/usr下
- 根目录所需要的库及一些基本目录就在host/< tuple >/sysroot/或host/usr/< tuple >/sysroot/里 (< tuple >:arm-buildroot-linux-gnueabi),如果是外部toolchain,比如lirano的就在libc里,名字不一样而已　　

  - build：所有源码包解压出来的文件存放地和编译的发生地

  - staging：软链接到host/< tuple >/sysroot/ 就是上面说到的文件系统需要的库等目录,方便查看

  - target： 目录是用来制作rootfs的，里面放着Linux系统基本的目录结构，以及各种编译好的应用库和bin可执行文件。不能用来nfs mount到开发板,因为buildroot不是root权权运行的,所以现dev/,etc/等一些文件无法创建,所以目录还不完整,要用images/里的rootfs.tar解压出来的根文件目录才能mount.使用如下命令：sudo tar -C /destination/of/extraction -xf images/rootfs.tar

  - Images：目录下就是最终生成的可烧写到板子上的各种image。

- buildroot/dl：存放下载的源码包及应用软件的压缩包

- buildroot/fs：放各种文件系统的源代码

- buildroot/fs/skeleton：放生成文件系统镜像的地方，及板子里面的系统

- buildroot/linux： 存放着Linux kernel的自动构建脚本。

- buildroot/configs：放置开发板的一些配置参数

- buildroot/dl：目录存放从官网上下载的开源软件包，第一次下载后，下次就不会再去从官网下载了，而是从dl/目录下拿开源包，以节约时间。**本身下载通常都是很慢的,你可以手动找到相关包下载后放到这里就OK了,make时会自动检测这个目录.**

- buildroot/docs： 存放相关的参考文档。

- buildroot/arch： 目录存放CPU架构相关的配置脚本，如arm/mips/x86 ，这些CPU相关的配置，在制作工具链，编译boot和内核时很关键。

- buildroot/board：存放了一些默认开发板的配置补丁之类的

- buildroot/boot：

- buildroot/build：

- buildroot/support：

- buildroot/system：这里就是根目录的主要骨架了和相关的启动初始化配置,当制作根目录时就是将此处的文件cp到output里去.然后再安装toolchain的动态库和你勾选的package的可执行文件之类的.

- buildroot/toolchain：

### buildroot目录介绍

  解压之后，我们可以看到以下的目录情况：

```
├── arch:   存放CPU架构相关的配置脚本，如arm/mips/x86,这些CPU相关的配置，在制作工具链时，编译uboot和kernel时很关键.
├── board   存放了一些默认开发板的配置补丁之类的
├── boot
├── CHANGES
├── Config.in
├── Config.in.legacy
├── configs:  放置开发板的一些配置参数.
├── COPYING
├── DEVELOPERS
├── dl:       存放下载的源代码及应用软件的压缩包.
├── docs:     存放相关的参考文档.
├── fs:       放各种文件系统的源代码.
├── linux:    存放着Linux kernel的自动构建脚本.
├── Makefile
├── Makefile.legacy
├── output: 是编译出来的输出文件夹.
│   ├── build: 存放解压后的各种软件包编译完成后的现场.
│   ├── host: 存放着制作好的编译工具链，如gcc、arm-linux-gcc等工具.
│   ├── images: 存放着编译好的uboot.bin, zImage, rootfs等镜像文件，可烧写到板子里, 让linux系统跑起来.
│   ├── staging
│   └── target: 用来制作rootfs文件系统，里面放着Linux系统基本的目录结构，以及编译好的应用库和bin可执行文件. (buildroot根据用户配置把.ko .so .bin文件安装到对应的目录下去，根据用户的配置安装指定位置)
├── package：下面放着应用软件的配置文件，每个应用软件的配置文件有Config.in和soft_name.mk，其中soft_name.mk(这种其实就Makefile脚本的自动构建脚本)文件可以去下载应用软件的包。
├── README
├── support
├── system
└── toolchain
```



### make命令解析

  通过make help可以看到buildroot下make的使用细节，包括对package、uclibc、busybox、linux以及文档生成等配置：

```
Cleaning:
  clean                  - delete all files created by build------------------清理
  distclean              - delete all non-source files (including .config)

Build:
  all                         - make world----------------------------编译整个系统
  toolchain              - build toolchain------------------------------------------编译工具链

Configuration:
  menuconfig             - interactive curses-based configurator-------------对整个buildroot进行配置
  savedefconfig          - Save current config to BR2_DEFCONFIG (minimal config)--------保存menuconfig的配置

Package-specific:---------------------------------------------------------对package配置
  <pkg>                  - Build and install <pkg> and all its dependencies---------单独编译对应APP
  <pkg>-source           - Only download the source files for <pkg>
  <pkg>-extract          - Extract <pkg> sources
  <pkg>-patch            - Apply patches to <pkg>
  <pkg>-depends          - Build <pkg>'s dependencies
  <pkg>-configure        - Build <pkg> up to the configure step
  <pkg>-build            - Build <pkg> up to the build step
  <pkg>-show-depends     - List packages on which <pkg> depends
  <pkg>-show-rdepends    - List packages which have <pkg> as a dependency
  <pkg>-graph-depends    - Generate a graph of <pkg>'s dependencies
  <pkg>-graph-rdepends   - Generate a graph of <pkg>'s reverse dependencies
  <pkg>-dirclean         - Remove <pkg> build directory--------------------清除对应APP的编译目录
  <pkg>-reconfigure      - Restart the build from the configure step
  <pkg>-rebuild          - Restart the build from the build step------------单独重新编译对应APP

busybox:
  busybox-menuconfig     - Run BusyBox menuconfig

uclibc:
  uclibc-menuconfig      - Run uClibc menuconfig

linux:
  linux-menuconfig       - Run Linux kernel menuconfig--------------------配置Linux并保存设置
  linux-savedefconfig    - Run Linux kernel savedefconfig
  linux-update-defconfig - Save the Linux configuration to the path specified
                             by BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE

Documentation:
  manual                 - build manual in all formats
  manual-pdf             - build manual in PDF
  graph-build            - generate graphs of the build times-----------对编译时间、编译依赖、文件系统大小生成图标
  graph-depends          - generate graph of the dependency tree
  graph-size             - generate stats of the filesystem size

```



### 5.2 rockchp buildroot

Buildroot 编译输出结果保存在 `output` 目录，具体目录由配置文件决定，本例保存在 `buildroot/output/rockchip_rk3288` 目录，后续可以在该目录执行 `make` 编译根文件系统。采用全自动编译方式时，默认会生成 `buildroot/output/rockchip_rk3288_recovery` 目录，这是 `recovery` 的编译输出目录。

###  5.3 Buildroot编译输出

**buildroot的编译流程是先从dl/xxx.tar下解压出源码到output/build/xxx,然后它利用本身的配置文件(如果有的话)覆盖output/build/xxx下的配置文件,在开始编译连接完成后安装到output/相应文件夹下。**

###  Buildroo的编译输出存放在output/.下面

cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128$ du -h --max-depth=1
13G   ./build
830M  ./images
211M  ./target
893M  ./host
15G   .

- `build/` 包含所有的源文件，包括 Buildroot 所需主机工具和选择的包，这个目录包含所有 模块源码。 存放除交叉编译器之外的所有组件，每个组件自己有一个文件夹
- `host/`   包含host所需要的各种安装包（为运行buildroot所需要的各组件，及交叉编译器）
- `images/` 包含压缩好的根文件系统镜像文件，kernel image, bootloader and root filesystem等images
- `staging/` 这个目录类似根文件系统的目录结构，包含编译生成的所有头文件和库，以及其他开发文件，不过他们没有裁剪，比较庞大，不适用于目标文件系统。
- `target/` 包含完整的根文件系统，对比 `staging/`，它没有开发文件，不包含头文件，二进制文件也经过 `strip` 处理。 **包含几乎与目标根文件系统一样的目录，尚缺少的是设备文件/dev**（Buildroot不是运行在也不想运行在root权限下），因而这个目录也不能作为目标系统的根文件系统，你应该使用images/下的其中一个作为目标系统的根文件系统

###  5.4  模块配置

默认编译好的根文件系统不一定满足我们的需求，我们可能需要增加一些第三方包，或者修改包的配置选项，Buildroot 支持图形化方式去做选择配置：

```
cd buildroot/output/rockchip_rk3288/

# 进入图形化配置界面，选择所需模块，保存退出
make menuconfig

# 保存到配置文件 'buildroot/configs/rockchip_rk3288_defconfig'
make savedefconfig

#编译 Buildroot 根文件系统
make
```

需要了解的是：

- 进行编译时，Buildroot 根据配置，会自动从网络获取相关的软件包，包括一些第三方库，插件，实用工具等，放在dl/目录。
- 软件包会解压在 `output/build/` 目录下，然后进行编译。
- 如果要修改软件包的源码，可以通过打补丁的方式进行修改，补丁集中放在 `package/` 目录，Buildroot 会在解压软件包时为其打上相应的补丁。

###  5.6 Buildroot Overlay

文件系统覆盖是指在目标文件系统编译完成后将文件覆盖到文件系统目录。通过这种方式，我们可以简单的添加或修改一些文件：

- 本例覆盖目录 `buildroot/board/rockchip/rk3288/fs-overlay`
- 公有覆盖目录 `buildroot/board/rockchip/common`

例：`buildroot/board/rockchip/rk3288/fs-overlay/etc/firmware` 将覆盖文件系统的 `/etc/firmware` 文件。


###  5.7 什么是rootfs

Linux中的rootfs，就是那些文件夹和文件，包括什么根文件目录’/’系统相关的配置文件目录/etc存放系统启动相关配置的/etc/init存放系统相关的工具 /sbin存在用户的工具/usr/bin

如下target下就是开发板的根文件系统主要（不包含dev）：

```
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/target$ ls
bin             dev   lib      media  oem   rockchip_test  sbin    system                            tmp       usr
busybox.config  etc   lib32    misc   opt   root           sdcard  THIS_IS_NOT_YOUR_ROOT_FILESYSTEM  udisk     var
data            init  linuxrc  mnt    proc  run            sys     timestamp                         userdata
```



| `dirclean`  | 删除整个包构建目录                                           |
| ----------- | ------------------------------------------------------------ |
| `reinstall` | 重新运行安装命令                                             |
| `rebuild`   | 重新运行编译命令 - 这只有在使用该`OVERRIDE_SRCDIR`功能（重新指定源码包位置）时才有意义，或者直接在编译目录中修改文件时才有意义 |



5.7 编译结果

- 编译结果位于output/images

包含一个或多个不同格式的rootfs

一个linux内核，可能一个或多个设备树块

一个或多个bootloader镜像

安装镜像的时候，不同设备不一样，没有一个标准，buildroot提供一些工具来产生USB/SDCARD/FLASH

-  make list-defconfigs

  列出所有buildroot/config/下所有的defconfig

环境变量O = output

### 5.8 官方

### Build tree: $(O)

output/

▶ Global output directory
▶ Can be customized for out-of-tree build by passing O=<dir>
▶ Variable: O (as passed on the command line)
▶ Variable: BASE_DIR (as an absolute path)

### Build tree: $(O)/build

Build tree: $(O)/build
▶ output/
	▶ build/
		▶buildroot-config/
		▶busybox-1.22.1/
		▶host-pkgconf-0.8.9/
		▶kmod-1.18/
		▶build-time.log
▶ Where all source tarballs are extracted
▶ Where the build of each package takes place
▶ In addition to the package sources and object files, stamp files are created by
Buildroot
▶ Variable: BUILD_DIR

### Build tree: $(O)/host

​	▶ host/
​		▶lib
​		▶bin
​		▶sbin
​		▶<tuple>/sysroot/bin
​		▶<tuple>/sysroot/lib
​		▶<tuple>/sysroot/usr/lib
​		▶<tuple>/sysroot/usr/bin
​	▶ Contains both the tools built for the host (cross-compiler, etc.) and the sysroot of
the toolchain
​	▶ Variable: HOST_DIR
​	▶ Host tools are directly in host/
​	▶ The sysroot is in host/<tuple>/sysroot/usr
​	▶ <tuple> is an identifier of the architecture, vendor, operating system, C library and
ABI. E.g: arm-unknown-linux-gnueabihf.
​	▶ Variable for the sysroot: STAGING_DIR

### Build tree: $(O)/staging

​	▶ staging/
​	▶ Just a symbolic link to the sysroot, i.e. to host/<tuple>/sysroot/.
​	▶ Available for convenience

### Build tree: $(O)/target

​	▶ target/
​		▶bin/
​		▶etc/
​		▶lib/
​		▶usr/bin/
​		▶usr/lib/
​		▶usr/share/
​		▶usr/sbin/
​	▶THIS_IS_NOT_YOUR_ROOT_FILESYSTEM
​	▶...
​	▶ The target root filesystem
​	▶ Usual Linux hierarchy
​	▶ Not completely ready for the target: permissions, device files, etc.
​	▶ Buildroot does not run as root: all files are owned by the user running Buildroot, not
setuid, etc.
​	▶ Used to generate the final root filesystem images in images/
​	▶ Variable: TARGET_DIR

​	▶ images/
​		▶zImage
​		▶armada-370-mirabox.dtb
​		▶rootfs.tar
​		▶rootfs.ubi
​		▶ Contains the final images: kernel image, bootloader image, root filesystem image(s)
​		▶ Variable: BINARIES_DIR

​		▶ graphs/
​		▶ Visualization of Buildroot operation: dependencies between packages, time to build
the different packages
​		▶ make graph-depends
​		▶ make graph-build
​		▶ make graph-size
​		▶ Variable: GRAPHS_DIR
​		▶ See the section Analyzing the build later in this training.



​	▶ legal-info/
​		▶manifest.csv
​		▶host-manifest.csv
​		▶licenses.txt
​		▶licenses/
​		▶sources/
▶ Legal information: license of all packages, and their source code, plus a licensing
manifest
▶ Useful for license compliance
▶ make legal-info
▶ Variable: LEGAL_INFO_DIR

## 7 Buildroot 瑞芯微介绍

### 7.1 编译

```
$source envsetup.sh
$make menuconfig
$ make savedefconfig
$ make  （或./build.sh rootfs 其实就是mak）
$ ./mkfirmware.sh        生成固件所烧写镜像
```

### 7.2 切换单板（申明环境变量，用于 envsetup.sh使用）

source是改的是rootfs的配置，

切换.BoardConfig.mk是一个软链接，他指向linux不同芯片的默认配置

```
root@c:/home/c/linux/v2/device/rockchip# ln -sf rk3128/BoardConfig.mk .BoardConfig.mk
A<- B 
root@c:/home/c/linux/v2# source envsetup.sh
root@c:/home/c/linux/v2# ./build.sh
```

不同的芯片，申明了不同的环境变量表示了dts等配置

```shell
cw@SYS3:~/sdk/312x_i/device/rockchip$ cat .BoardConfig.mk 
#!/bin/bash

# Target arch
export RK_ARCH=arm
# Uboot defconfig
export RK_UBOOT_DEFCONFIG=rk3128
# Kernel defconfig
export RK_KERNEL_DEFCONFIG=rockchip_linux_defconfig
# Kernel dts
export RK_KERNEL_DTS=rk3128-fireprime
# boot image type
export RK_BOOT_IMG=zboot.img
# kernel image path
export RK_KERNEL_IMG=kernel/arch/arm/boot/zImage
# parameter for GPT table
export RK_PARAMETER=parameter-buildroot.txt
# Buildroot config
export RK_CFG_BUILDROOT=rockchip_rk3128
# Recovery config
export RK_CFG_RECOVERY=rockchip_rk3128_recovery
# Pcba config
export RK_CFG_PCBA=rockchip_rk3128_pcba
# Build jobs
export RK_JOBS=12
# target chip
export RK_TARGET_PRODUCT=rk3128
# Set rootfs type, including ext2 ext4 squashfs
export RK_ROOTFS_TYPE=ext4
# rootfs image path
export RK_ROOTFS_IMG=rockdev/rootfs.${RK_ROOTFS_TYPE}# Buildroot 根文件系统镜像路径
# Set oem partition type, including ext2 squashfs
export RK_OEM_FS_TYPE=ext2
# Set userdata partition type, including ext2, fat
export RK_USERDATA_FS_TYPE=ext2
#OEM config
export RK_OEM_DIR=oem_normal
#userdata config
export RK_USERDATA_DIR=userdata_normal
#misc image
export RK_MISC=wipe_all-misc.img
cw@SYS3:~/sdk/312x_i/device/rockchi
```



选择开发板对应的配置文件。配置文件会链接到 `device/rockchip/.BoardConfig.mk`，查看该文件可确认当前所使用的配置文件：

```
./build.sh firefly-rk3288.mk

# 文件路径在 `device/rockchip/rk3288/firefly-rk3288.mk`

```

`.mk` 文件默认配置为编译 Buildroot 固件，下面对 Buildroot 相关配置进行说明：

```
# Buildroot config
export RK_CFG_BUILDROOT=rockchip_rk3288     # Buildroot 根文件系统配置文件

# 文件路径在 `buildroot/configs/rockchip_rk3288_defconfig`
# Recovery config
export RK_CFG_RECOVERY=rockchip_rk3288_recovery     # recovery 模式下根文件系统配置文件（可省略）

# 文件路径在 `buildroot/configs/rockchip_rk3288_recovery_defconfig`
# rootfs image path
export RK_ROOTFS_IMG=buildroot/output/$RK_CFG_BUILDROOT/images/rootfs.$RK_ROOTFS_TYPE   # Buildroot 根文件系统镜像路径

# 本例中，文件路径在 `buildroot/output/rockchip_rk3288/images/rootfs.ext4`
# 注：该文件路径将在首次编译根文件系统后生成

```

执行编译命令时，将会根据 `.mk` 文件进行编译。7.3 编译原理

 针对output/**build**/某个包进行了修改，需要单独重新编译该软件包，直接编译Buildroot是不起效果的。

output/包/包含以下文件追踪编译过程。

```
.stamp_configured 配置
.stamp_downloaded 下载
.stamp_extracted 解压
.stamp_patched 打上补丁
.stamp_staging_installed 编译 
.stamp_target_installe 安装
```

#### 7.3.1 完全重建buildroot

目标架构改变、二进制格式、浮点、
工具链或其版本改变
你加入的包对其他包邮依赖、
删除一个包、文件系统架构

```
$make clean all
$make
```

#### 7.3.2 不完全重建buildroot

新增一个包无需全部重新编译，但是如果新增的是一个库，且别其他文件所引用，则需一起重新编
译，或者全部重编。

BuildRoot 如何单独编译某个一包？

1. 如果修改了package源码，在编译前运行 make < package >-dirclean。


```
make <package> -dirclean //删除output/build/package 这个文件夹
make                     //自动对这个包重新编译
```

2. 如果只是修改output 目录下代码，只需要启动编译的步骤，编译前运行 make < package >-rebuild千万不要make <package> -dirclean ，这样会删除整个output/<package>/这个文件夹，修改全没了。

```
 make <package> -rebuild
 make 或 make <package>
```

###7.4 拷贝文件到开发板根文件系统

开发根文件系统则存放在output/target，内核镜像、uboot、压缩的根文件系统存放在output/images下target

- 方法一：手动复制到output/target下

```
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128$ ag -g "sink-test"
build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/sink/sink-test

cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128$cp build/intel-wds-ece955a9947e8d5848223c849d2c0f3f928078d4/sink/sink-test target/

cw@SYS3:~/sdk/3126i$  make
cw@SYS3:~/sdk/3126i$  ./mkfirmware.sh
```

- 方法二：在包的配置mk文件，设置istall阶段自动拷贝

intel-wds编译后，想把它在output/包/下生成的某个可执行文件复制到开发板根文件系统（根文件系统则存放在output/target，也就是target目录，注意target目录就是开发板下面的除了dev目录的所有文件）
package/intel-wds/intel-wds.mk 在配置文件里面修改，编译后install把某个文件一起拷贝到开发板目录

```c
define INTEL_WDS_INSTALL_TARGET_CMDS
	cp -f $(@D)/sink/sink-test $(TARGET_DIR)/usr/bin/
endef
```

### 7.5 output下修改保存为补丁

如何给package 增加一个patch,生成补丁后，拷贝到对应的buildroot/package/对应包下。buildroot在解压源代码的时候，自动打上这个目录下的所有补丁，注意补丁的格式和序号。打补丁的过程，make编译的时可以看到log。

以rkwifibt 为例,

````
1. cd buildroot/output/rockchip_rk3308_release/build/rkwifibt-1.0.0
2. 初始化仓库
git init
3. 添加将要修改的文件到 git 仓库
git add wifi_start.sh
git commit -s
4. 修改文件
5. 提交修改文件
git add wifi_start.sh
git commit -s
6. 生成补丁
git format-patch
````

举例buildroot/package/connma，make的时候解压在ouput文件夹下面，解压后，会自动打上这些patch

```
cw@SYS3:~/sdk/3126i/buildroot/package/connman ls
0001-tethering-Reorder-header-includes.patch  0002-nat-build-failure.patch  Config.in  connman.hash  connman.mk  S45connman
```

## 8 Buildroot命令

### 8.1 编译类命令

| make menuconfig            | make menuconfig   RK SDK支持 make nconfig          RK SDK有点残废make xconfig          RK SDK不支持 make gconfig          RK SDK不支持 |
| -------------------------- | ------------------------------------------------------------ |
|                            | make 配置后编译:                                             |
| make                       | make 配置后编译:                                             |
| make 2>&1 \| tee build.log | 编译时候保存日志（亲测ok,用于分析出错原因有用）              |
| make clean                 | 删除所有build output，只保留配置文件                         |
| make distclean             | 删除一切，包括所有配置文件，下载文件                         |
| make V=1                   | 详细构建，默认情况下，Buildroot隐藏在生成期间运行的许多命令，只展示最重要的。要获得完全详细的生成，请传递V=1： |
| make graph-size            | 分析文件系统大小组成，文件大小，包大小                       |
| make graph-depends         | 生成全部软件依赖图                                           |


### 8.2 分析类命令

### make graph-size

分析文件系统大小组成，文件大小，包大小

make graph-size produces:
▶ file-size-stats.csv, CSV               每个原始文件的大小
▶ package-size-stats.csv, CSV        每个包的大小
▶ graph-size.pdf,                             生成每个包消耗size的饼图

```
cw@SYS3:~/sdk/3126i$ make graph-size
umask 0022 && make -C /home/cw/sdk/3126i/buildroot O=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128 graph-depends
Getting targets
Getting dependencies for ['QLauncher', 'alsa-config', 'alsa-lib', 'alsa-plugins', 'alsa-utils', 'android-tools', 'bash', 'busybox', 'cairo', 'camera_engine_rkisp', 'connman',  -libzlib', 'host-xlib_libX11', 'host-xlib_libxkbfile', 'host-mpfr', 'host-xorgproto', 'host-libopenssl', 'host-xlib_libXdmcp', 'host-xlib_libXau', 'host-xlib_xtrans', 'host-libxcb', 'host-libxslt', 'host-xcb-proto', 'host-libpthread-stubs']
dot  -Tpdf \
        -o /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/graphs/graph-depends.pdf \
        /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/graphs/graph-depends.dot

```

![](RK_Linux_Compile.assets/make_graph-size.png)





file-size-stats.csv 文件大小，举例：

|           File name           | Package name | File size | Package size | File size in package (%) | File size in system (%) |      |      |
| :---------------------------: | ------------ | --------- | ------------ | ------------------------ | ----------------------- | ---- | ---- |
| usr/share/zoneinfo/posix/GMT0 | tzdata       | 127       | 1173112      | 0                        | 0                       |      |      |
|       usr/bin/sink-test       | intel-wds    | 153692    | 372916       | 41.2                     | 0.1                     |      |      |

package-size-stats.csv 包大小，举例：

| Package name | Package size | Package size in system (%) |      |      |
| ------------ | ------------ | -------------------------- | ---- | ---- |
| intel-wds    | 372916       | 0.2                        |      |      |



### make graph-depends

生成全部软件依赖图

buildroot的库会根据依赖关系被自动下载，通过此图也可以了解某些某块被谁依赖。

```
cw@SYS3:~/sdk/3126i$ make graph-depends
umask 0022 && make -C /home/cw/sdk/3126i/buildroot O=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128 graph-depends
Getting targets
Getting dependencies for ['QLauncher', 'alsa-config', 'alsa-lib', 'alsa-plugins', 'alsa-utils', 'android-tools', 'bash', 'busybox', 'cairo', 'camera_engine_rkisp', 'connman', 'coreutils', 'dbus', 'deviceio_release', 'dhrystone', 'dnsmasq', 'dosfstools', 'dropbear', 'e2fsprogs', 'eudev', 'evtest', 'expat', 'faad2', 'fontconfig', 'freetype', 'glibc',  -flex', 'host-libglib2', 'host-cmake', 'host-gawk', 'host-pixman', 'udev', 'host-gettext', 'host-lzo',  -gcc-final', 'host-pcre', 'host-gmp', 'host-binutils', 'host-attr', 'host-libxml-parser-perl', 'host-openssl', 'host-mpc', 'host-expat', 'host-kmod', 'host-bzip2', 'host-zic', 'host-libffi', 'host-m4', 'host-libxml2', 'host-libzlib', 'host-xlib_libX11', 'host-xlib_libxkbfile', 'host-mpfr', 'host-xorgproto', 'host-libopenssl', 'host-xlib_libXdmcp', 'host-xlib_libXau', 'host-xlib_xtrans', 'host-libxcb', 'host-libxslt', 'host-xcb-proto', 'host-libpthread-stubs']
dot  -Tpdf \
        -o /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/graphs/graph-depends.pdf \
        /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/graphs/graph-depends.dot
```



![](RK_Linux_Compile.assets/make_graph-depends.png)

### make <包名>-graph-depends

生成指定包的依赖图

```
cw@SYS3:~/sdk/3126i$ make intel-wds-graph-depends 
umask 0022 && make -C /home/cw/sdk/3126i/buildroot O=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128 intel-wds-graph-depends
Getting dependencies for ['intel-wds']
Getting dependencies for ['toolchain', 'skeleton', 'host-cmake', 'libglib2', 'host--zlib', 'skeleton-init-common', 'readline', 'host-pcre', 'host-gmp', 'host-libxml2', 'glibc', 'host-binutils', 'host-ncurses', 'host-mpfr', 'host-mpc', 'host-libzlib', 'host-qemu', 'linux-headers', 'host-gcc-initial', 'host-gawk', 'host-python', 'host-pixman', 'host-expat', 'host-openssl', 'host-libopenssl']
dot  -Tpdf -o /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/graphs/intel-wds-graph-depends.pdf /home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/graphs/intel-wds-graph-depends.dot
```

![image-20200316193828443](RK_Linux_Compile.assets/image-20200316193828443.png)









### make graph-build 

可以明白整个编译流程时间都耗在哪里，针对性进行分析优化，有利于提高编译效率。

执行make graph-build会生成如下文件：

▶ make graph-build generates several graphs in $(O)/graphs/:
▶ build.hist-build.pdf, build time in build order                                    安装编译顺序
▶ build.hist-duration.pdf, build time by duration                                 按照耗时从大到小排列。
▶ build.hist-name.pdf, build time by package name                           按照字母排序
▶ build.pie-packages.pdf, pie chart of the per-package build time     每个包的编译时间饼图
▶ build.pie-steps.pdf, pie chart of the per-step build time                  编译顺序下，每一步的编译时间



![img](RK_Linux_Compile.assets/1083701-20190614102518315-1402017455.png)

其中比较有参考意义的文件是build.hist-duration.pdf文件，按照耗时从大到小排列。



```
cw@SYS3:~/sdk/3126i/buildroot$ cd ..
cw@SYS3:~/sdk/3126i$ make graph-build 
umask 0022 && make -C /home/cw/sdk/3126i/buildroot O=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128 graph-build
./support/scripts/graph-build-time --type=histogram --order=name --input=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/build/build-time.log --.pie-packages.pdf 
./support/scripts/graph-build-time --type=pie-steps --input=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/build/build-time.log --output=/home/cw/sdk/3126i/buildroot/output/rockchip_rk3128/graphs/build.pie-steps.pdf 
```

```
cw@SYS3:~/sdk/3126i/buildroot/output/rockchip_rk3128/graphs$ ls -al
-rw-r--r-- 1 cw cw 55482 Mar 16 19:15 build.hist-build.pdf
-rw-r--r-- 1 cw cw 55209 Mar 16 19:15 build.hist-duration.pdf
-rw-r--r-- 1 cw cw 55291 Mar 16 19:15 build.hist-name.pdf
-rw-r--r-- 1 cw cw 20547 Mar 16 19:15 build.pie-packages.pdf
-rw-r--r-- 1 cw cw 15303 Mar 16 19:15 build.pie-steps.pdf
```

![image-20200316192054362](RK_Linux_Compile.assets/image-20200316192054362.png)



###  RK SDK不支持的几个命令

```

Buildroot自带文档
▶ 网站文档 https://buildroot.org/docs.html (PDF,
HTML, text)
▶ 目录树下文档 docs/manual in the Buildroot sources
▶ 是 Asciidoc 格式
▶ The manual can be built with:
▶ make manual or just make manual-html, make manual-pdf, make manual-epub,
make manual-text, make manual-split-html
▶ A number of tools need to be installed on your machine, see the manual itself.
  
make manual //生成所有格式的手册 主机需要安装acsiidos软件还有需要w3m等
make manual-pdf //生成pdf的手册
make manual-split-html
make manual-text
```



## 8 UBOOT

### 8.1 Rockchip U-Boot 简介

Rockchip U-Boot 简介
Rockchip U-Boot next-dev 分支是 Rockchip 从 U-Boot 官方的 v2017.09 正式版本中切出
来进行开发的版本。目前在该平台上已经支持 RK 所有主流在售芯片。
  支持芯片：rk3288、rk3036、rk312x、rk3288、rk3308、rk3326、rk3399、px30
等；
  支持 RK Android 平台的固件启动；
  支持最新 Android AOSP(如 GVA)固件启动；
  支持 Linux Distro 固件启动；
  支持 Rockchip miniloader 和 SPL/TPL 两种 pre-loader 引导；
  支持 LVDS、EDP、MIPI、HDMI 等显示设备；
  支持 Emmc、Nand Flash、SPI NOR flash、SD 卡、U 盘等存储设备启动；
  支持 FAT, EXT2, EXT4 文件系统；
  支持 GPT, RK parameter 分区格式；
  支持开机 logo 显示、充电动画显示，低电管理、电源管理；
  支持 I2C、PMIC、CHARGE、GUAGE、USB、GPIO、PWM、GMAC、EMMC、NAND、
中断等驱动；
  支持 RockUSB 和 Google Fastboot 两种 USB gadget 烧写 EMMC；
  支持 Mass storage, ethernet, HID 等 USB 设备；
  支持使用 Kernel 的 dtb；
  支持 dtbo 功能

### 8.2 rkbin

U-Boot 根目录下生成 trust.img、uboot.img、loader 等相关固件

rkbin 工程主要存放了 Rockchip 不开源的 bin 文件（trust、loader 等）、脚本、打包工具等，
所以 rkbin 只是一个“工具包”工程 。
rkbin 工程需要和 U-Boot 工程保持同级目录关系，否则编译时会报找不到 rkbin 仓库。当在
U-Boot 工程执行编译的时候，编译脚本会从 rkbin 仓库里索引相关的 bin 文件和打包工具，最后
在 U-Boot 根目录下生成 trust.img、uboot.img、loader 等相关固件

### 8.3 gcc 工具链
默认使用的编译器是 gcc-linaro-6.3.1 版本：
32 位编译器：gcc‐linaro‐6.3.1‐2017.05‐x86_64_arm‐linux‐gnueabihf
64 位编译器：gcc‐linaro‐6.3.1‐2017.05‐x86_64_aarch64‐linux‐gnu
默认使用 Rockchip 提供的工具包：prebuilts，请保证它和 U-Boot 工程保持同级目录关系。

如果需要更改编译器路径，可以修改编译脚本./make.sh 里的内容：

````
GCC_ARM32=arm‐linux‐gnueabihf‐
GCC_ARM64=aarch64‐linux‐gnu‐
TOOLCHAIN_ARM32=../prebuilts/gcc/linux‐x86/arm/gcc‐linaro‐6.3.1‐
2017.05‐x86_64_arm‐linuxgnueabihf/
bin
TOOLCHAIN_ARM64=../prebuilts/gcc/linux‐x86/aarch64/gcc‐linaro‐6.3.1‐
2017.05‐x86_64_aarch64‐
linux‐gnu/bin
````

### 8.4 编译

编译命令：
./make.sh [board] ‐ ‐ ‐ ‐                         [board]的名字来源是：configs/[board]_defconfig 文件。
命令范例：

```
./make.sh evb‐ rk3308 ‐ ‐ ‐ ‐ build for evb‐ rk3308_defconfig
./make.sh firefly‐ rk3288 ‐ ‐ ‐ ‐ build for firefly‐ rk3288_defconfig
```



## 9 编译

### 9.1 U-Boot  编译步骤
RK3128H EVB 开发板：
cd u-boot;
make rk3128x_box_defconfig
make;
编译完成后，U-Boot 根目录，生成以下三个镜像文件：
$:~/u-boot$ tree
├── rk3128x_loader_v1.06.238.bin
├── trust_with_ta.img
└── uboot.img

### 9.2 Kernel  编译步骤
RK3308 EVB 开发板配置与编译如下 RK3128H EVB board：
cd kernel;
make ARCH=arm rockchip_linux_defconfig;
make ARCH=arm rk3128h-box.img CROSS_COMPILE=arm-linux-gnueabihf- -j16
编译完成后，kernel 根目录，生成以下二个镜像文件.
kernel/
├── kernel.img
├── resource.img


### 9.3 Buildroot  编译步骤

1. 客户按实际编译环境配置好 JDK 环境变量后，按照以下步骤配置完后，执行 make 即可。
   cd buildroot
   make rockchip_rk3128H_defconfig
   make
2. Create the ext4 image(rootfs.img)
    ./mk-image.sh
    完成编译后，执行 SDK 根目录下的 mkfirmware.sh 脚本生成固件，所有烧写所需的镜像将
    都会拷贝于 rockdev/目录。
    rockdev/
    ├── kernel.img
    ├── parameter.txt
    ├── resource.img
    ├── MiniLoaderAll.bin
    ├── data.img
    ├── cfg.img
    ├── rootfs.img
    ├── trust.img
    └── uboot.img
    得到了所有镜像文件后，为了方便烧写及量产，通常可手动将这些单独的镜像通过脚本打包
    成为 update.img。

## 11 paramete文件分区说明

### 11.0 起始地址与分区大小计算公式

定义

```
分区大小@分区起始地址
0x00100000@0x0005a000(rootfs) @符号之前的数值是分区大小，@符号之后的数值是分区的起始位置，
```

介绍数字单位都是块（512字节）

```
计算公式：  数字*512字节/1024/1024
0x00002000 计算后是4MB
0x00004000 计算后是8MB
计算技巧： 在16进制下除 0x800 ， 结果再转10进制
```

为什么以块(512字节)为单位，以flash为例:这是由于存储驱动有关系，flash是块设备，读写512字节每块。

![image-20200316192054362](RK_Linux_Compile.assets/512byte.png)





### 11.1 介绍

Rockchip android系统平台使用parameter文件来配置一些系统参数，比如固件版本，存储器分区信息等。
Parameter文件是非常重要的系统配置文件，最好在能了解清楚各个配置功能时再做修改，避免出现parameter文
件配置异常造成系统不能正常工作的问题。
Parameter文件大小有限制，最大不能超过64KB。如图，烧录包括parameter.

[分区说明](  http://opensource.rock-chips.com/wiki_Boot_option)

参考《Rockchip Parameter File Format》

1. 分区大小@分区地址格式

0x00100000@0x0005a000(rootfs) **@符号之前的数值是分区大小，@符号之后的数值是分区的起始位置，**

1. 编译出来的大小超过paramenter定义，会编译报错
2. 修改分区的大小是16进制，并同步修改后面分区的起始气质
3. 现在一般是GPT分区模式分区模式

```
图一：GPT分区模式 （瑞芯微电子一般使用这个）
图二：传统cmdline分区模式
```

### 11.1 分区表举例rk3128/parameter-buildroot.txt

```
cw@SYS3:~/sdk/312x_i/rockdev$ more /home/cw/sdk/312x_i/device/rockchip/
FIRMWARE_VER: 8.1
MACHINE_MODEL: RK3128
MACHINE_ID: 007
MANUFACTURER: RK3128
MAGIC: 0x5041524B
ATAG: 0x00200800
MACHINE: 3128
CHECK_MASK: 0x80
PWR_HLD: 0,0,A,0,1
TYPE: GPT
CMDLINE: mtdparts=rk29xxnand:0x00002000@0x00004000(uboot),0x00002000@0x00006000(trust),0x00002000@0x00008000(misc),0x00010000@0x0000a000(boot),0x00010000
@0x0001a000(recovery),0x00010000@0x0002a000(backup),0x00020000@0x0003a000(oem),0x00100000@0x0005a000(rootfs),-@0x0015a000(userdata:grow)
uuid:rootfs=614e0000-0000-4b53-8000-1d28000054a9
```

### 11.2 分区表定义


| FIRMWARE_VER:8.1     | 固件版本，打包updata.img时会使用到，升级工具会根据这个识别固件版本。 |
| -------------------- | ------------------------------------------------------------ |
| MACHINE_MODEL:RK3326 | 机器型号，打包updata.img使用，不同的项目，可以自己修改，用于升级工具显示。在recovery里面升级固件时 |
| MACHINE_ID:007       | 产品开发ID，可以为字符和数字组合，打包updata.img使用，不同的项目使用不同的ID，可以用于识别机器机 型。在recovery里面升级固件时可以用于判断固件是否匹配。 |
| MANUFACTURER: rk3326 | 厂商信息，打包updata.img使用，可以自己修改，用于升级工具显示。 |
| MAGIC: 0x5041524B    | 魔数MAGIC，不能修改，一些新的AP使用DTS，这一项没有用，为了兼容，不要删除或修改。 |
| ATAG: 0x60000800     | ATAG，不能修改，一些新的AP使用DTS，这一项没有用，为了兼容，不要删除或修改。 |
| MACHINE: 3226        | 内核识别用，不能修改，这个定义和内核匹配。RK29xx识别码：MACHINE: 2929RK292x识别码：MACHINE: 2928RK3066识别码：MACHINE: 3066RK3326识别码：MACHINE: 3326 |
| CHECK_MASK: 0x80     | 保留，不能修改。                                             |
| TYPE: GPT            | 指定该文件CMDLINE里面定义的分区用于创建GPT使用，不会烧录到NVM（NAND，EMMC等）存储器件里面。 |
| CMDLINE：            | console=ttyFIQ0 androidboot.console=ttyFIQ0，串口定义。      |

![image-20200624151316516](RK_Linux_Compile.assets/image-20200624151316516.png)

```

3.10. CMDLINE：
console=ttyFIQ0 androidboot.console=ttyFIQ0，串口定义。
initrd=0x62000000,0x00800000，第一个参数是boot.img加载到sdram的位置，第二个参数为ramdisk的大小，
目前ramdisk大小没有限制。
androidboot.xxx的定义在android启动时使用，有些平台会在kernel的dts里面定义，这部分定义一般不用修改，
只用用发布SDK默认的就可以了。
MTD分区定义说明：
mtdparts=rk29xxnand:0x00002000@0x00002000(uboot),0x00002000@0x00004000(trust),0x00002000@0x0
0006000(misc),
0x00008000@0x00008000(resource),0x00010000@0x00010000(kernel),0x00010000@0x00020000(boot),0x0
0020000@0x00030000(recovery),
0x00038000@0x00050000(backup),0x00002000@0x00088000(security),0x00100000@0x0008a000(cache),0x
00400000@0x0018a000(system),
0x00008000@0x0058a000(metadata),0x00080000@0x00592000(vendor),0x00080000@0x00612000(oem),0x
00000400@0x00692000(frp),-@0x00692400(userdata)
分区定义说明：
1、为了兼容性，目前RK所有AP都是用rk29xxnand做标识。
2、单个分区说明：
例如：0x00002000@0x00008000(boot)，@符号之前的数值是分区大小，@符号之后的数值是分区的起始位置，
括号里面的字符是分区的名字。所有数值的单位是sector，1个sector为512Bytes.上例中，boot分区起始位置为
0x8000 sectors位置，大小为0x2000 sectors(4MB).
3、为了性能，每个分区起始地址需要32KB（64 sectors）对齐，大小也需要32KB的整数倍。
4、如果使用sparse格式的镜像，升级时会擦除数据，为了兼容性更好，对应的分区最好按4MB对齐，大小也按
4MB整数倍配置。
名称 Parameter定义地址 EMMC逻辑地址 NAND逻辑地址 大小
GPT -- 0 0 32KB
LOADER -- 0x40 0x40 4MB-32KB
保留 -- 0x2000 0x2000 4MB
UBOOT 0x4000 0x4000 0x4000 4MB
TRUST 0x6000 0x6000 0x6000 4MB
名称 Parameter定义地址 EMMC逻辑地址 NAND逻辑地址 大小
保留 -- 0 0 32KB
LOADER -- 0x40 0x40 4MB-32KB
parameter -- 0x2000 0x0 4MB
UBOOT 0x2000 0x4000 0x2000 4MB
TRUST 0x4000 0x6000 0x4000 4MB
5、使用GPT分区时，parameter里面定义的地址，都是真实的逻辑地址（LBA），例如uboot定义在0x4000，那
么烧录到EMMC和NAND里面时，逻辑地址也是0x4000.
最后一个分区需要指定grow参数，工具会把剩余的空间都分配给最后一个分区。
6、使用传统cmdline分区时，如果是EMMC颗粒，0-4MB的空间是保留存放loader的，parameter里面定义的分区
都需要加上4MB，例如uboot定义在0x2000，实际烧录到EMMC里面时，和使用GPT分区时烧录的逻辑地址是一样
的，也是0x4000。如果是NAND颗粒，为了和原来产品兼容，所有地址都是真实逻辑地址，例如uboot定义在
0x2000，实际烧录到NAND里面是，逻辑地址也是0x2000，和使用GPT时不一样。
注：NAND FLASH的机器，0x40有可能会写loader的镜像，和parameter在同一个4MB空间内，有效的数据是相
互错开存放的，不会覆盖。
```




### 11.3 分区表和编译关系

编译时候遇到生成的rootfs大于parameter文件限制的情况

```
tune2fs 1.43.9 (8-Feb-2018)
Setting maximal mount count to -1
Setting interval between checks to 0 seconds
create uboot.img...done.
create trust.img...done.
create loader...done.
create boot.img...done.
/home/cw/sdk/312x_i/device/rockchip/rk3128/parameter-buildroot.txt
0x00002000@0x00004000(uboot),0x00002000@0x00006000(trust),0x00002000@0x00008000(misc),0x00010000@0x0000a000(boot),0x00010000@0x0001a000(recovery),0x00010000@0x0002a000(backup),0x00020000@0x0003a000(oem),0x00100000@0x0005a000(rootfs),-@0x0015a000(userdata:grow)
 error: rootfs image size exceed parameter! 
```
看下rootfs的我大小

```
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128/images$ ls -alh
total 1.4G
drwxr-xr-x 2 cw cw 4.0K Jun 24 09:32 .
drwxrwxr-x 6 cw cw 4.0K Jun 24 09:31 ..
-rw-r--r-- 1 cw cw 311M Jun 24 09:31 rootfs.cpio
-rw-r--r-- 1 cw cw 172M Jun 24 09:32 rootfs.cpio.gz
-rw-r--r-- 1 cw cw 660M Jun 24 09:32 rootfs.ext2
lrwxrwxrwx 1 cw cw   11 Jun 24 09:32 rootfs.ext4 -> rootfs.ext2
-rw-r--r-- 1 cw cw 173M Jun 24 09:32 rootfs.squashfs
-rw-r--r-- 1 cw cw 314M Jun 24 09:32 rootfs.tar

,0x00100000@0x0005a000(rootfs) = 
```

- 查看实际rootfs.ext2 占据了660M字节

- 0x00100000@0x0005a000(rootfs) ，计算：（0x00100000块）*512字节/1024/1024 = 512M字节。

所以需要修改parameter的rootfs大小，这里改为0x00200000@0x0005a000(rootfs)   即1024M字节
注意：du -sh 与ls -alh 插看文件大小会不一样，建议使用 ls -alh



如果parameter文件配置错误，可能导致启动卡死，如下图，修改rootfs为0x1000000（8G大小），超过了合法的方位，于是开机的时候死机，这时候断电重启动ctrl+c输入rbrom重新进入maskrom，重新烧录合法的估计。

![image-20200624163709370](RK_Linux_Compile.assets/image-20200624163709370.png)

## 12 DDR改128M

1. 将DDR初始化的固件存放于rkbin/bin/rk31/rk3128_ddr_128MB_v1.00.bin
2. 修改对应的配置文件RK3128MINIALL.ini，制定新的路径

```shell
cw@SYS3:~/sdk/312x_i/rkbin$ git diff
diff --git a/RKBOOT/RK3128MINIALL.ini b/RKBOOT/RK3128MINIALL.ini
index befd74c..b512589 100644
--- a/RKBOOT/RK3128MINIALL.ini
+++ b/RKBOOT/RK3128MINIALL.ini
@@ -5,7 +5,7 @@ MAJOR=2
 MINOR=56
 [CODE471_OPTION]
 NUM=1
-Path1=bin/rk31/rk3128_ddr_300MHz_v2.12.bin
+Path1=bin/rk31/rk3128_ddr_128MB_v1.00.bin
 Sleep=1
 [CODE472_OPTION]
 NUM=1
@@ -14,7 +14,7 @@ Path1=bin/rk31/rk3128_usbplug_v2.57.bin
 NUM=2
 LOADER1=FlashData
 LOADER2=FlashBoot
-FlashData=bin/rk31/rk3128_ddr_300MHz_v2.12.bin
+FlashData=bin/rk31/rk3128_ddr_128MB_v1.00.bin
 FlashBoot=bin/rk31/rk312x_miniloader_v2.57.bin
 [OUTPUT]
 PATH=rk3128_loader_v2.12.256.bin
cw@SYS3:~/sdk/312x_i/rkbin$ 
```

3.  执行脚本制作loader

```shell
   312x_i/rkbin$ tools/boot_merger --replace tools/rk_tools/ ./ ./RKBOOT/RK3128MINIALL.ini
```

312x_i/rkbin/RKTRUST/RK3126TOS.ini 修改如下内容，重新生成trust， 然后在u-boot下执行./make.sh

```
[TOS_BIN_PATH]                                                                   
TOSTA=bin/rk31/rk3126_tee_laddr_v1.00.bin
ADDR=0x1800000
OUTPUT=trust_laddr.img
```

关于rkbin下的配置文件，修改后,重新编译u-boot

```
cw@SYS3:~/sdk/312x_i/u-boot$ ./make.sh
  CHK     include/config/uboot.release
  CHK     include/generated/timestamp_autogenerated.h
  UPD     include/generated/timestamp_autogenerated.h
  CHK     include/config.h
  CFG     u-boot.cfg
  CHK     include/generated/version_autogenerated.h
  CHK     include/generated/generic-asm-offsets.h
  CHK     include/generated/asm-offsets.h
  HOSTCC  tools/mkenvimage.o
  HOSTCC  tools/fit_image.o
  HOSTCC  tools/image-host.o
  HOSTCC  tools/dumpimage.o
  HOSTCC  tools/mkimage.o
  HOSTCC  tools/rockchip/boot_merger.o
  HOSTCC  tools/rockchip/loaderimage.o
  HOSTLD  tools/mkenvimage
  HOSTLD  tools/loaderimage
  HOSTLD  tools/dumpimage
  HOSTLD  tools/mkimage
  HOSTLD  tools/boot_merger
  CC      drivers/usb/gadget/f_fastboot.o
  CC      cmd/version.o
  CC      lib/display_options.o
  CC      common/main.o
  LD      cmd/built-in.o
  LD      common/built-in.o
  LD      lib/built-in.o
  LD      drivers/usb/gadget/built-in.o
  LD      u-boot
  OBJCOPY u-boot.srec
  OBJCOPY u-boot-nodtb.bin
  SYM     u-boot.sym
make[2]: 'arch/arm/dts/rk3126-evb.dtb' is up to date.
  COPY    u-boot.dtb
  CAT     u-boot-dtb.bin
  COPY    u-boot.bin
  ALIGN   u-boot.bin
  CFGCHK  u-boot.cfg

 load addr is 0x60000000!
pack input u-boot.bin 
pack file size: 715440(698 KB)
crc = 0x540e3c8e
uboot version: U-Boot 2017.09-gcd1c982e9a #cw (Jul 02 2020 - 14:10:53)
pack uboot.img success! 
pack uboot okay! Input: u-boot.bin

 load addr is 0x61800000!
pack input /home/cw/sdk/312x_i/rkbin/bin/rk31/rk3126_tee_laddr_v1.00.bin 
pack file size: 619256(604 KB)
crc = 0x5dac0a49
trustos version: Trust os
pack trust_laddr.img success! 
pack trust okay! Input: /home/cw/sdk/312x_i/rkbin/RKTRUST/RK3128TOS.ini
out:rk3128_loader_v2.12.256.bin
fix opt:rk3128_loader_v2.12.256.bin
merge success(rk3128_loader_v2.12.256.bin)
/home/cw/sdk/312x_i/u-boot
pack rk3128_loader_v2.12.256.bin okay! Input: /home/cw/sdk/312x_i/rkbin/RKBOOT/RK3128MINIALL.ini

Platform RK3128 is build OK, with exist .config
```

```\
Making /home/cw/sdk/312x_i/rockdev/userdata.img from /home/cw/sdk/312x_i/device/rockchip/userdata/userdata_normal with size(5M)
0+0 records in
0+0 records out
0 bytes copied, 1.9893e-05 s, 0.0 kB/s
mke2fs 1.43.9 (8-Feb-2018)
Discarding device blocks: done                            
Creating filesystem with 5120 1k blocks and 1280 inodes

Allocating group tables: done                            
Writing inode tables: done                            
Copying files into the device: done
Writing superblocks and filesystem accounting information: done

tune2fs 1.43.9 (8-Feb-2018)
Setting maximal mount count to -1
Setting interval between checks to 0 seconds
create uboot.img...done.
 error: /home/cw/sdk/312x_i/u-boot/trust.img not found! 
create loader...done.
create boot.img...done.
/home/cw/sdk/312x_i/device/rockchip/rk3128/parameter-buildroot.txt
0x00002000@0x00004000(uboot),0x00002000@0x00006000(trust),0x00002000@0x00008000(misc),0x00010000@0x0000a000(boot),0x00010000@0x0001a000(recovery),0x00010000@0x0002a000(backup),0x00020000@0x0003a000(oem),0x00200000@0x0005a000(rootfs),-@0x0025a000(userdata:grow)
 Image: image in rockdev is ready 
Make image ok!
Make update.img
start to make update.img...
Android Firmware Package Tool v1.66
------ PACKAGE ------
Add file: ./package-file
Add file: ./package-file done,offset=0x800,size=0x28f,userspace=0x1
Add file: ./Image/MiniLoaderAll.bin
Add file: ./Image/MiniLoaderAll.bin done,offset=0x1000,size=0x3194e,userspace=0x64
Add file: ./Image/parameter.txt
Add file: ./Image/parameter.txt done,offset=0x33000,size=0x20b,userspace=0x1
Add file: ./Image/trust.img
Error:<AddFile> open file failed,err=2!
------ FAILED ------
Press any key to quit:
mv: cannot stat '/home/cw/sdk/312x_i/tools/linux/Linux_Pack_Firmware/rockdev/update.img': No such file or directory
Make update image failed!
```



## 13 启动流程

  This chapter introduce the generic boot flow for Rockchip Application Processors, including the detail about what image we may use in Rockchip platform for kind of boot path:

  \- use U-Boot TPL/SPL from upsream or rockchip U-Boot, fully source code;

  \- use Rockchp idbLoader which is combinded by Rockchip ddr init bin and miniloader bin from Rockchip [rkbin project](https://github.com/rockchip-linux/rkbin);

  ```
  +--------+----------------+----------+-------------+---------+
  | Boot   | Terminology #1 | Actual   | Rockchip    | Image   |
  | stage  |                | program  |  Image      | Location|
  | number |                | name     |   Name      | (sector)|
  +--------+----------------+----------+-------------+---------+
  | 1      |  Primary       | ROM code | BootRom     |         |
  |        |  Program       |          |             |         |
  |        |  Loader        |          |             |         |
  |        |                |          |             |         |
  | 2      |  Secondary     | U-Boot   |idbloader.img| 0x40    | pre-loader
  |        |  Program       | TPL/SPL  |             |         |
  |        |  Loader (SPL)  |          |             |         |
  |        |                |          |             |         |
  | 3      |  -             | U-Boot   | u-boot.itb  | 0x4000  | including u-boot and atf
  |        |                |          | uboot.img   |         | only used with miniloader
  |        |                |          |             |         |
  |        |                | ATF/TEE  | trust.img   | 0x6000  | only used with miniloader
  |        |                |          |             |         |
  | 4      |  -             | kernel   | boot.img    | 0x8000  |
  |        |                |          |             |         |
  | 5      |  -             | rootfs   | rootfs.img  | 0x40000 |
  +--------+----------------+----------+-------------+---------+
  ```

  Then when we talking about boot from eMMC/SD/U-Disk/net, they are in different concept:

  - Stage 1 is always in boot rom, it loads stage 2 and may load stage 3(when SPL_BACK_TO_BROM option enabled).
  - Boot from SPI flash means firmware for stage 2 and 3(SPL and U-Boot only) in SPI flash and stage 4/5 in other place;
  - Boot from eMMC means all the firmware(including stage 2, 3, 4, 5) in eMMC;
  - Boot from SD card means all the firmware(including stage 2, 3, 4, 5) in SD card;
  - Boot from U-Disk means firmware for stage 4 and 5(not including SPL and U-Boot) in Disk, optionally only including stage 5;
  - Boot from net/tftp means firmeware for stage 4 and 5(not including SPL and U-Boot) on the network;

 ![img](RK_Linux_Compile.assets/894px-Rockchip_bootflow20181122.jpg)



## 12   系统启动流程 

Bootloader很小，一般在几十KB甚至几百KB，负责做最基本的系统初始化，并把Kernel从存储设备（EMMC/NAND）中拷贝到内存（DDR）中，kernel一般几MB到十几MB、负责控制所有的硬件和系统的调度，根文件系统和system属于用户空间的应用，根文件系统一般只有几MB，负责初始化一个最基本的上层运行环境，为system挂载打基础，system里面是主要的应用，大小几百MB设置几GB，主要的应用和库都包含在里面



##13 busybox

- BR2_PACKAGE_BUSYBOX_CONFIG.

- make busybox-menuconfig

```
cw@SYS3:~/sdk/312x_i$ ag -g busybox.config
distro/package/busybox/busybox.config
buildroot/board/rockchip/rv1108/busybox.config
buildroot/board/rockchip/rk3308/busybox.config
buildroot/board/rockchip/common/base/busybox.config
buildroot/board/rockchip/common/tinyrootfs/busybox.config
buildroot/board/rockchip/common/robot/base/busybox.config

buildroot/output/rockchip_rk3128/build/buildroot-config/br2/package/busybox/config.h
buildroot/output/rockchip_rk3128/build/buildroot-config/br2/package/busybox/config/fragment/files.h
buildroot/output/rockchip_rk3128/target/busybox.config
```

cw@SYS3:~/sdk/312x_i/buildroot$ make busybox-menuconfig



只在开启的时候有效

If you already have a BusyBox configuration file, you can directly specify this file in the Buildroot configuration, using
BR2_PACKAGE_BUSYBOX_CONFIG.

make busybox-menuconfig //进入output/build目录下的busybox目录执行相应的Makefile文件，出现busybox配置菜单  

```
cw@SYS3:~/sdk/312x_i$ busybox
BusyBox v1.22.1 (Ubuntu 1:1.22.0-15ubuntu1.4) multi-call binary.
BusyBox is copyrighted by many authors between 1998-2012.
Licensed under GPLv2. See source distribution for detailed
copyright notices.

Usage: busybox [function [arguments]...]
   or: busybox --list[-full]
   or: busybox --install [-s] [DIR]
   or: function [arguments]...

        BusyBox is a multi-call binary that combines many common Unix
        utilities into a single executable.  Most people will create a
        link to busybox for each function they wish to use and BusyBox
        will act like whatever it was invoked as.

Currently defined functions:
        [, [[, acpid, adjtimex, ar, arp, arping, ash, awk, basename,
        blockdev, brctl, bunzip2, bzcat, bzip2, cal, cat, chgrp, chmod,
        chown, chpasswd, chroot, chvt, clear, cmp, cp, cpio, crond, crontab,
        cttyhack, cut, date, dc, dd, deallocvt, depmod, devmem, df, diff,
        dirname, dmesg, dnsdomainname, dos2unix, dpkg, dpkg-deb, du,
        dumpkmap, dumpleases, echo, ed, egrep, env, expand, expr, false,
        fdisk, fgrep, find, fold, free, freeramdisk, fstrim, ftpget, ftpput,
        getopt, getty, grep, groups, gunzip, gzip, halt, head, hexdump,
        hostid, hostname, httpd, hwclock, id, ifconfig, ifdown, ifup, init,
        insmod, ionice, ip, ipcalc, kill, killall, klogd, last, less, ln,
        loadfont, loadkmap, logger, login, logname, logread, losetup, ls,
        lsmod, lzcat, lzma, lzop, lzopcat, md5sum, mdev, microcom, mkdir,
        mkfifo, mknod, mkswap, mktemp, modinfo, modprobe, more, mount, mt,
        mv, nameif, nc, netstat, nslookup, od, openvt, passwd, patch, pidof,
        ping, ping6, pivot_root, poweroff, printf, ps, pwd, rdate, readlink,
        realpath, reboot, renice, reset, rev, rm, rmdir, rmmod, route, rpm,
        rpm2cpio, run-parts, sed, seq, setkeycodes, setsid, sh, sha1sum,
        sha256sum, sha512sum, sleep, sort, start-stop-daemon, stat,
        static-sh, strings, stty, su, sulogin, swapoff, swapon, switch_root,
        sync, sysctl, syslogd, tac, tail, tar, taskset, tee, telnet,
        telnetd, test, tftp, time, timeout, top, touch, tr, traceroute,
        traceroute6, true, tty, tunctl, udhcpc, udhcpd, umount, uname,
        uncompress, unexpand, uniq, unix2dos, unlzma, unlzop, unxz, unzip,
        uptime, usleep, uudecode, uuencode, vconfig, vi, watch, watchdog,
        wc, wget, which, who, whoami, xargs, xz, xzcat, yes, zcat

```



```
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128$ cd host/
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128/host$ ^C
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128/host$ cd ..
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128$ ag -g rtc_demo*
build/rtc_demo-1.0.0/rtc_demo.c
build/rtc_demo-1.0.0/Makefile
build/rtc_demo-1.0.0/LICENSE
build/rtc_demo-1.0.0/rtc_demo.h
target/usr/lib/librtc_demo.so
host/arm-buildroot-linux-gnueabihf/sysroot/usr/lib/librtc_demo.so
host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/rtc_demo.h
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128$ rm target/usr/lib/librtc_demo.so  host/arm-buildroot-linux-gnueabihf/sysroot/usr/lib/librtc_demo.so host/arm-buildroot-linux-gnueabihf/sysroot/usr/include/rtc_demo.h
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128$ 
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128$ 
cw@SYS3:~/sdk/312x_i/buildroot/output/rockchip_rk3128$ 
```

## 14 字体问题

1、查找内核下面的

```

rk3308_robot_defconfig
201:CONFIG_VFAT_FS=m

lsk_defconfig
198:CONFIG_VFAT_FS=y

rockchip_linux_defconfig
501:CONFIG_VFAT_FS=y
502:CONFIG_FAT_DEFAULT_CODEPAGE=936
503:CONFIG_FAT_DEFAULT_IOCHARSET="utf8"

```



```
ISO/IEC 8859-1:1998，又称Latin-1或“西欧语言”
发布时间 2014-12-31
ISO 8859-1，正式编号为ISO/IEC 8859-1:1998，又称Latin-1或“西欧语言”，是国际标准化组织内ISO/IEC 8859的第一个8位字符集。它以ASCII为基础，在空置的0xA0-0xFF的范围内，加入96个字母及符号，藉以供使用附加符号的拉丁字母语言使用。曾推出过 ISO 8859-1:1987 版。

此字符集支持部分于欧洲使用的语言，包括阿尔巴尼亚语、巴斯克语、布列塔尼语、加泰罗尼亚语、丹麦语、荷兰语、法罗语、弗里西语、加利西亚语、德语、格陵兰语、冰岛语、爱尔兰盖尔语、意大利语、拉丁语、卢森堡语、挪威语、葡萄牙语、里托罗曼斯语、苏格兰盖尔语、西班牙语及瑞典语。

英语虽然没有重音字母，但仍会标明为ISO/IEC 8859-1编码。除此之外，欧洲以外的部分语言，如南非荷兰语、斯瓦希里语、印尼语及马来语、菲律宾他加洛语等也可使用ISO/IEC 8859-1编码。
```



![1](F:\github\Docs\RK_Linux_Compile.assets/1.png)

## 15 kernel

```shell
cd kernel
make ARCH=arm rockchip_linux_defconfig       //32位机器才加ARCH=arm
make ARCH=arm menuconfig
vi .config
make ARCH=arm savedefconfig   把.config格式改为savedefconfig格式，格式不一样，所以转换下
vi defconfig
diff defconfig arch/arm/configs/rockchip_linux_defconfig
cp defconfig arch/arm/configs/rockchip_linux_defconfig             
git diff
git checkout
git diff
git checkout arch/
rm .config
```



### 15.1 新加设备树

- 设备树文件路径

  Linux Kernel 目前支持多平台使用 dts，RK 平台的 dts 文件存放于：

ARM:arch/arm/boot/dts/
ARM64 :arch/arm64/boot/dts/rockchip

- dts和dtsi

```shell
一般 dts 文件的命名规则为”soc-board_name.dts”，如 rk3308-evb-dmic-i2s-v10.dts。
soc 指的是芯片名称，board_name 一般是根据板子丝印来命名。
如果你的板子是一体板，则只需要一个 dts 文件来描述即可。

rk3308-ai-va-v10.dts
└── rk3308.dtsi

如果硬件设计上是核心板和底板的结构，或者产品有多个产品形态，可以把公用的硬件描述放
在 dtsi 文件，而 dts 文件则描述不同的硬件模块，并且通过 include "xxx.dtsi"将公用的硬件描述
包含进来。
├── rk3308-evb-amic-v10.dts
│ ├── rk3308-evb-ext-v10.dtsi
│ └── rk3308-evb-v10.dtsi
│ └── rk3308.dtsi
└── rk3308-evb-dmic-i2s-v10.dts
├── rk3308-evb-ext-v10.dtsi
└── rk3308-evb-v10.dtsi
└── rk3308.dtsi
```

- dts  语法的几个说明

dts 语法可以像 c/c++一样，通过#include xxx.dtsi 来包含其他公用的 dts 数据。dts 文件
将继承包含的 dtsi 文件的所有设备节点的属性和值。
如 property 在多个 dts/dtsi 文件被定义，它的值最终为 dts 的定义。所有和芯片相关的控制
器节点都会被定义在 soc.dtsi，如需使能该设备功能，需要在 dts 文件中设置其 status
为”okay"。

### 1  创建 dts  文件

```
diff --git a/arch/arm/boot/dts/Makefile b/arch/arm/boot/dts/Makefile
index 16859011d6b0..1b3c3c383142 100644
--- a/arch/arm/boot/dts/Makefile
+++ b/arch/arm/boot/dts/Makefile
@@ -524,6 +524,7 @@ dtb-$(CONFIG_ARCH_ROCKCHIP) += \
        rk3126-bnd-m88-emmc.dtb \
        rk3126-evb.dtb \
        rk3126-linux.dtb \
+       rk3126-linux-dpf.dtb \
        rk3128-fireprime.dtb \
        rk3128h-box.dtb \
        rk3128h-box-avb.dtb \
```

### 2 修改 dts  所在目录的 Makefile

```shell

diff --git a/arch/arm/boot/dts/rk3126-linux-dpf.dts b/arch/arm/boot/dts/rk3126-linux-dpf.dts
new file mode 100755
index 000000000000..6b7bcc86a3fe
--- /dev/null
+++ b/arch/arm/boot/dts/rk3126-linux-dpf.dts
@@ -0,0 +1,481 @@
+// SPDX-License-Identifier: (GPL-2.0+ OR MIT)
+/*
+ * Copyright (c) 2019 F
+ 
+ 
+ uzhou Rockchip Electronics Co., Ltd
+ */
+
+/dts-v1/;
+#include <dt-bindings/gpio/gpio.h>
+#include <dt-bindings/input/input.h>
+#include <dt-bindings/pinctrl/rockchip.h>

```

编译 kenrel 的时候可以直接 make dts-name.img（如 rk3308-evb-amic-v10.img），即
可生成对应的 resource.img（包含 dtb 数据）。

### 3 修改编译脚本,指向最新的设备树

```
cw@SYS3:~/3126c_inner/device/rockchip$ ls -al
total 92
drwxrwxr-x 22 cw cw 4096 May 30  2019 .
drwxrwxr-x  3 cw cw 4096 May 30  2019 ..
lrwxrwxrwx  1 cw cw   22 May 30  2019 .BoardConfig.mk -> rk3126c/BoardConfig.mk
drwxrwxr-x  2 cw cw 4096 Jun 30  2019 common
drwxrwxr-x  2 cw cw 4096 Jul  2  2019 .git
-rw-rw-r--  1 cw cw   16 May 30  2019 .gitignore
drwxrwxr-x  6 cw cw 4096 May 30  2019 oem
drwxrwxr-x  2 cw cw 4096 May 30  2019 px30
drwxrwxr-x  2 cw cw 4096 May 30  2019 px3se
drwxrwxr-x  3 cw cw 4096 May 30  2019 rk1808
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3036
drwxrwxr-x  2 cw cw 4096 Jun 30  2019 rk3126c
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3128
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3128h
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3229
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3288
drwxrwxr-x 16 cw cw 4096 May 30  2019 rk3308
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3326
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3328
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3399
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3399pro
drwxrwxr-x  2 cw cw 4096 May 30  2019 rk3399pro-npu
drwxrwxr-x  2 cw cw 4096 Jun 26  2019 rockimg
drwxrwxr-x  4 cw cw 4096 May 30  2019 userdata
cw@SYS3:~/3126c_inner/device/rockchip$ vim .BoardConfig.mk 
cw@SYS3:~/3126c_inner/device/rockchip$ 
cw@SYS3:~/3126c_inner/device/rockchip$ git d
diff --git a/rk3126c/BoardConfig.mk b/rk3126c/BoardConfig.mk
index cff7bb2..ee248ce 100755
--- a/rk3126c/BoardConfig.mk
+++ b/rk3126c/BoardConfig.mk
@@ -7,7 +7,7 @@ export RK_UBOOT_DEFCONFIG=rk3126
 # Kernel defconfig
 export RK_KERNEL_DEFCONFIG=rockchip_linux_defconfig
 # Kernel dts
-export RK_KERNEL_DTS=rk3126-linux
+export RK_KERNEL_DTS=rk3126-linux-dpf
 # boot image type
 export RK_BOOT_IMG=zboot
```

重新编译即可 ./build.sh kernel



## 16 TRUST

ARM TrustZone [1]技术是所有 Cortex-A 类处理器的基本功能，是通过 ARM 架构安全扩展引入的。这些扩展可在 

供应商、平台和应用程序中提供一致的程序员模型，同时提供真实的硬件支持的安全环境。



目前 Rockchip 平台上的 64 位 SoC 平台上使用的是 ARM Trusted Firmware + OP-TEE OS 的组合；32 位 SoC 平 

台上使用的是 OP-TEE OS。 



Rockchip 所有的平台都启用了 ARM TrustZone 技术，在整个 TrustZone 的架构中 U-Boot 属于Non-Secure World，所以无法访问任何安全的资源（如：某些安全 memory、安全 efuse...）。 Trustzone 是 ARM 公司为了解决可能遇到的软硬件安全问题提出的一种硬件解决方案。基于 Trustzone 这种硬件架构设计的软硬件，能在很大程度和范围内保证系统的安全性，使软硬件破解都变得相对很困难。

```
如果把上述这种阶段定义映射到 Rockchip 的平台各级固件上，对应关系为：Maskrom（BL1）、
Loader（BL2）、Trust（BL31：ARM Trusted Firmware + BL32：OP-TEE OS）、U-Boot（BL33）。

Android 系统的固件启动顺序：
Maskrom -> Loader -> Trust -> U-Boot -> kernel -> Android
```

## 16.1 运行内存 

ARM Trusted Firmware 运行在 DRAM 起始偏移 0M~2M 的空间，以 0x10000（64KB）作为程序入口地址。 

OP-TEE OS 运行在 DRAM 起始偏移 132M~148M 之间（结束地址依各平台需求而定）以 0x08400000（132M） 

作为入口地址。 

## 16.2 生命周期 

Trust 自上电初始化之后就始终常驻于内存之中，完成着自己的使命。



