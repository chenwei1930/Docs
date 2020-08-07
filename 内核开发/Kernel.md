



# kerne

## 1 编译

RK3308 EVB 开发板配置与编译如下 RK3128H EVB board：
cd kernel;
make ARCH=arm rockchip_linux_defconfig;
make ARCH=arm rk3128h-box.img CROSS_COMPILE=arm-linux-gnueabihf- -j16
编译完成后，kernel 根目录，生成以下二个镜像文件.
kernel/
├── kernel.img
├── resource.img



4.3 KERNEL  编译步骤
进入工程目录根目录执行以下命令自动完成 Kernel 的编译及打包：
./build.sh kernel
编译后在kernel目录生成zboot.img, zboot.img包含zImage和resource.img, resource.img打包了
dtb 和 uboot\kernel 的 logo 文件

```
 ARCH=arm 代表32位机器，不加的话默认64位机器
```

配置文件目录

```
cw@SYS3:~/sdk/312x_i/kernel/arch/arm/configs$ ls rockchip_linux_defconfig 
rockchip_linux_defconfig

配置文件里条目以CONFIG_开头
CONFIG_CRC7=y
# CONFIG_XZ_DEC_X86 is not set
```







```
cw@SYS3:~/sdk/312x_i/kernel$ cat drivers/he
headset_observe/ hello/           
cw@SYS3:~/sdk/312x_i/kernel$ cat drivers/hello/hello.c
#include <linux/init.h>
#include <linux/module.h>
 
static int __init hello_init(void) {
    printk(KERN_ALERT "Hello, world\n");
    return 0;
}
 
static void __exit hello_exit(void) {
    printk(KERN_ALERT "Goodbye, cruel world\n");
}
 
MODULE_LICENSE("Dual BSD/GPL");
 
module_init(hello_init);
module_exit(hello_exit);


diff --git a/drivers/Kconfig b/drivers/Kconfig
index c7a3c412bce1..3c94b9ea5802 100644
--- a/drivers/Kconfig
+++ b/drivers/Kconfig
@@ -202,6 +202,8 @@ source "drivers/fpga/Kconfig"
 
 source "drivers/tee/Kconfig"
 
+source "drivers/hello/Kconfig"
+
 source "drivers/rkflash/Kconfig"
 
 source "drivers/rk_nand/Kconfig"
diff --git a/drivers/Makefile b/drivers/Makefile
index ee62edb593f1..23444200230b 100644
--- a/drivers/Makefile
+++ b/drivers/Makefile
@@ -58,6 +58,9 @@ obj-y                         += gpu/
 
 obj-$(CONFIG_CONNECTOR)                += connector/
 
+# chenwei hello world
+obj-$(CONFIG_HELLO_CHENWEI)                            += hello/
+
 # i810fb and intelfb depend on char/agp/
 obj-$(CONFIG_FB_I810)           += video/fbdev/i810/
 obj-$(CONFIG_FB_INTEL)          += video/fbdev/intelfb/
```



```
  733  make ARCH=arm menuconfig
  734  make ARCH=arm savedefconfig
  735  cp defconfig arch/arm/configs/rockchip_linux_defconfig
  
  
  cw@SYS3:~/sdk/312x_i/kernel$ git diff
diff --git a/arch/arm/configs/rockchip_linux_defconfig b/arch/arm/configs/rockchip_linux_defconfig
index cabffc153fd5..0e2bb0518e00 100644
--- a/arch/arm/configs/rockchip_linux_defconfig
+++ b/arch/arm/configs/rockchip_linux_defconfig
@@ -475,6 +475,7 @@ CONFIG_PHY_ROCKCHIP_INNO_VIDEO_COMBO_PHY=y
 CONFIG_ANDROID=y
 CONFIG_NVMEM=y
 CONFIG_ROCKCHIP_EFUSE=y
+CONFIG_HELLO_CHENWEI=m
 CONFIG_RK_NAND=y
 CONFIG_ROCKCHIP_SIP=y
 CONFIG_EXT4_FS=y
```





https://www.cnblogs.com/aaronLinux/p/5496559.html#t1

DTC为编译工具，它可以将.dts文件编译成.dtb文件。DTC的源码位于内核的scripts/dtc目录，内核选中CONFIG_OF，编译内核的时候，主机可执行程序DTC就会被编译出来。 即scripts/dtc/Makefile中

hostprogs-y := dtc

always := $(hostprogs-y) 

在内核的arch/arm/boot/dts/Makefile中，若选中某种SOC，则与其对应相关的所有dtb文件都将编译出来。在linux下，make dtbs可单独编译dtb。以下截取了TEGRA平台的一部分。

ifeq ($(CONFIG_OF),y)

dtb-$(CONFIG_ARCH_TEGRA) += tegra20-harmony.dtb \

tegra30-beaver.dtb \

tegra114-dalmore.dtb \

tegra124-ardbeg.dtb 

```
cw@SYS3:~/sdk/312x_i/kernel/scripts/dtc$ ls
checks.c                 dtc.o                     fdtput.c          modules.order
checks.o                 dtc-parser.tab.c          flattree.c        srcpos.c
data.c                   dtc-parser.tab.c_shipped  flattree.o        srcpos.h
data.o                   dtc-parser.tab.h          fstree.c          srcpos.o
dtc                      dtc-parser.tab.h_shipped  fstree.o          treesource.c
dtc.c                    dtc-parser.tab.o          include-prefixes  treesource.o
dtc.h                    dtc-parser.y              libfdt            update-dtc-source.sh
dtc-lexer.l              dt_to_config              livetree.c        util.c
dtc-lexer.lex.c          dtx_diff                  livetree.o        util.h
dtc-lexer.lex.c_shipped  fdtdump.c                 Makefile          util.o
dtc-lexer.lex.o          fdtget.c                  Makefile.dtc      version_gen.h
```

### 2.3. DTB

DTC编译*.dts生成的二进制文件(*.dtb)，bootloader在引导内核时，会预先读取*.dtb到内存，进而由内核解析。

```
cw@SYS3:~/sdk/312x_i/kernel$ ls arch/arm/kernel/head.S
arch/arm/kernel/head.S
```

在arch/arm/kernel/head.S中，有这样一段：

![img](resources/30145634_14285079263u2A.png)

_vet_atags定义在/arch/arm/kernel/head-common.S中，它主要对DTB镜像做了一个简单的校验。